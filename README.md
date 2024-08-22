import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.doThrow;

public class VAPTeamControllerTest {

    @Mock
    private GroupSetupService groupSetupService;

    @InjectMocks
    private VAPTeamController vapTeamController;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGetSuggestionListAndSelectedUserGroups_whenExceptionThrown_shouldReturnBadRequest() {
        // Arrange
        String groupName = "someGroupName";
        Exception ex = new Exception("some error message");

        // Mock the service to throw an exception
        doThrow(ex).when(groupSetupService).getSuggestionListAndSelectedUserGroups(groupName);

        // Act
        ResponseEntity<?> response = vapTeamController.getSuggestionListAndSelectedUserGroups(groupName, null);

        // Assert
        assertEquals(ResponseEntity.badRequest().body("some error message"), response);
    }
}