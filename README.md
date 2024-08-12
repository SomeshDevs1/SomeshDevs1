import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.server.ResponseStatusException;

import java.util.Collections;
import java.util.List;

public class UserControllerTest {

    @Mock
    private UserAuthService userAuthSvc;

    @Mock
    private UserGroupSetUpService userGroupSetUpService;

    @InjectMocks
    private UserController userController;

    @Mock
    private Authentication auth;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetUserGroupInfosSuccess() throws Exception {
        // Mocking user details
        UserDto mockUser = new UserDto(); // Assuming UserDto is the expected return type
        when(userAuthSvc.getUserDetails(auth, VAPConstants.VAC_ID)).thenReturn(mockUser);
        
        // Mocking validation (assuming no exception means valid user)
        doNothing().when(UserUtils.class);
        UserUtils.validate(mockUser, VAPConstants.VAC_MODULE_NAME, VAPConstants.ADMIN_ROLE, true);

        // Mocking group infos
        List<UserGroupInfoDto> mockGroupInfos = Collections.emptyList(); // Assuming empty list for simplicity
        when(userGroupSetUpService.getUserGroupInfos()).thenReturn(mockGroupInfos);

        // Test
        ResponseEntity<List<UserGroupInfoDto>> response = userController.getUserGroupInfos(auth);
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(mockGroupInfos, response.getBody());
    }

    @Test
    void testGetUserGroupInfosUnauthorized() {
        // Mocking exception for unauthorized user
        when(userAuthSvc.getUserDetails(auth, VAPConstants.VAC_ID))
            .thenThrow(new VAPServiceException("Unauthorized"));

        // Test
        ResponseStatusException exception = assertThrows(ResponseStatusException.class, () -> {
            userController.getUserGroupInfos(auth);
        });

        assertEquals(HttpStatus.UNAUTHORIZED, exception.getStatus());
    }

    @Test
    void testGetUserGroupInfosBadRequest() throws Exception {
        // Mocking user details
        UserDto mockUser = new UserDto(); // Assuming UserDto is the expected return type
        when(userAuthSvc.getUserDetails(auth, VAPConstants.VAC_ID)).thenReturn(mockUser);

        // Mocking validation
        doNothing().when(UserUtils.class);
        UserUtils.validate(mockUser, VAPConstants.VAC_MODULE_NAME, VAPConstants.ADMIN_ROLE, true);

        // Mocking exception in service
        when(userGroupSetUpService.getUserGroupInfos()).thenThrow(new RuntimeException("Bad Request"));

        // Test
        ResponseStatusException exception = assertThrows(ResponseStatusException.class, () -> {
            userController.getUserGroupInfos(auth);
        });

        assertEquals(HttpStatus.BAD_REQUEST, exception.getStatus());
    }
}