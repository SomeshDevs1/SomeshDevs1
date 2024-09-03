import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.util.CollectionUtils;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class TaskConfigurationServiceTest {

    @Mock
    private TaskConfigUtil taskConfigUtil;

    @Mock
    private FundRulesUtil fundRulesUtil;

    @Mock
    private TaskListRepository taskListRepository;

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @Mock
    private FundRuleRepository fundRuleRepository;

    @InjectMocks
    private TaskConfigurationService taskConfigurationService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testSaveTaskConfiguration_Success() throws LinkServiceException {
        // Arrange
        TaskConfigurationDTO taskConfiguration = new TaskConfigurationDTO();
        taskConfiguration.setPerimeter(LinkFundConstants.MFS);
        List<Integer> fundCodes = List.of(1, 2);
        taskConfiguration.setFundCodes(fundCodes);
        List<FundTasksDTO> fundTasks = List.of(new FundTasksDTO());
        taskConfiguration.setFundTasks(fundTasks);

        List<TaskConfig> tasksConfigsToDB = new ArrayList<>();
        List<FundRule> fundRulesToDB = new ArrayList<>();
        List<TaskList> masterTaskList = List.of(new TaskList());

        when(taskListRepository.findAll()).thenReturn(masterTaskList);
        when(taskConfigRepository.saveAll(tasksConfigsToDB)).thenReturn(tasksConfigsToDB);

        // Act
        Boolean result = taskConfigurationService.saveTaskConfiguration(taskConfiguration);

        // Assert
        assertTrue(result);
        verify(taskConfigUtil).preChecksBeforeSave(fundTasks);
        verify(taskConfigUtil, times(fundCodes.size())).updateTaskConfigTable(eq(taskConfiguration), eq(tasksConfigsToDB), eq(masterTaskList), anyInt(), any());
        verify(fundRulesUtil, times(fundCodes.size())).updateFundRulesTable(eq(tasksConfigsToDB), eq(taskConfiguration), eq(fundRulesToDB), eq(masterTaskList), anyInt());
        verify(fundRuleRepository).saveAll(fundRulesToDB);
        verify(taskConfigRepository).saveAll(tasksConfigsToDB);
    }

    @Test
    void testSaveTaskConfiguration_NoTaskConfiguration() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = null;

        // Act & Assert
        assertThrows(LinkServiceException.class, () -> taskConfigurationService.saveTaskConfiguration(taskConfiguration));
    }

    @Test
    void testSaveTaskConfiguration_EmptyFundTasks() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = new TaskConfigurationDTO();
        taskConfiguration.setFundTasks(new ArrayList<>());

        // Act & Assert
        LinkServiceException exception = assertThrows(LinkServiceException.class, () -> taskConfigurationService.saveTaskConfiguration(taskConfiguration));
        assertEquals("The Fund Tasks are Empty", exception.getMessage());
    }

    @Test
    void testSaveTaskConfiguration_EmptyMasterTaskList() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = new TaskConfigurationDTO();
        List<FundTasksDTO> fundTasks = List.of(new FundTasksDTO());
        taskConfiguration.setFundTasks(fundTasks);

        when(taskListRepository.findAll()).thenReturn(new ArrayList<>());

        // Act & Assert
        LinkServiceException exception = assertThrows(LinkServiceException.class, () -> taskConfigurationService.saveTaskConfiguration(taskConfiguration));
        assertEquals("There is no tasks available in taskListRepository", exception.getMessage());
    }

    @Test
    void testSaveTaskConfiguration_FailureToSave() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = new TaskConfigurationDTO();
        List<FundTasksDTO> fundTasks = List.of(new FundTasksDTO());
        taskConfiguration.setFundTasks(fundTasks);
        List<TaskConfig> tasksConfigsToDB = new ArrayList<>();
        List<FundRule> fundRulesToDB = new ArrayList<>();
        List<TaskList> masterTaskList = List.of(new TaskList());

        when(taskListRepository.findAll()).thenReturn(masterTaskList);
        when(taskConfigRepository.saveAll(tasksConfigsToDB)).thenReturn(new ArrayList<>()); // Simulating a failure to save

        // Act
        Boolean result = taskConfigurationService.saveTaskConfiguration(taskConfiguration);

        // Assert
        assertFalse(result);
    }
}