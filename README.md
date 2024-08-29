import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.ArrayList;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class TaskSetupServiceImplTest {

    @Mock
    private AutomationListRepository automationRepo;

    @InjectMocks
    private TaskSetupServiceImpl taskSetupService;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testGetAutomationList_Success() {
        // Prepare mock data
        AutomationList automation = new AutomationList();
        automation.setAutomationName("TestAutomation");

        List<AutomationList> automationList = new ArrayList<>();
        automationList.add(automation);

        // Mock the repository call
        when(automationRepo.findAll()).thenReturn(automationList);

        // Call the method under test
        List<AutomationListDTO> result = taskSetupService.getAutomationList();

        // Assert the result
        assertNotNull(result);
        assertEquals(1, result.size());
        assertEquals("TestAutomation", result.get(0).getAutomationName());
    }

    @Test
    public void testGetAutomationList_EmptyList() {
        // Mock the repository call to return an empty list
        when(automationRepo.findAll()).thenReturn(new ArrayList<>());

        // Call the method under test and expect an exception
        Exception exception = assertThrows(LinkServiceException.class, () -> {
            taskSetupService.getAutomationList();
        });

        // Assert the exception message
        assertEquals("There is error while getting the AutomationList", exception.getMessage());
    }
}