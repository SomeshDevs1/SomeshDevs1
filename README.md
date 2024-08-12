import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

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

public class VAPTeamControllerTest {

    @Mock
    private UserAuthService userAuthSvc;

    @Mock
    private UserGroupSetUpService userGroupSetUpService;

    @InjectMocks
    private VAPTeamController controller;

    @Mock
    private Authentication auth;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetUserGroupInfosSuccess() throws Exception {
        // Arrange
        UserDto mockUser = mock(UserDto.class);
        List<UserGroupInfoDto> mockGroupInfos = Collections.emptyList();
        
        when(userAuthSvc.getUserDetails(auth, VAPConstants.VAC_ID)).thenReturn(mockUser);
        doNothing().when(UserUtils.class, "validate", mockUser, VAPConstants.VAC_MODULE_NAME, VAPConstants.ADMIN_ROLE, true);
        when(userGroupSetUpService.getUserGroupInfos()).thenReturn(mockGroupInfos);
        
        // Act
        ResponseEntity<List<UserGroupInfoDto>> response = controller.getUserGroupInfos(auth);
        
        // Assert
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(mockGroupInfos, response.getBody());
    }

    @Test
    void testGetUserGroupInfosBadRequest() throws Exception {
        // Arrange
        UserDto mockUser = mock(UserDto.class);
        
        when(userAuthSvc.getUserDetails(auth, VAPConstants.VAC_ID)).thenReturn(mockUser);
        doNothing().when(UserUtils.class, "validate", mockUser, VAPConstants.VAC_MODULE_NAME, VAPConstants.ADMIN_ROLE, true​⬤