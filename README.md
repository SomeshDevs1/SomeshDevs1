import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class YourServiceTest {

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @Mock
    private FundServiceForLink fundServiceforLink;

    @InjectMocks
    private YourService yourService; // Replace with the actual service class name

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testSavellfsTaskCutoffbyFund() throws LinkServiceException {
        // Arrange
        WfsTaskCutOffDto dto1 = new WfsTaskCutOffDto();
        dto1.setTaskId(101);
        dto1.setFundId(1);
        dto1.setUserType("TypeA");
        dto1.setCutoff(/* initialize cutoff time */);
        
        List<WfsTaskCutOffDto> wfsTaskConfiguration = Arrays.asList(dto1);
        String modifiedBy = "user123";
        
        FundDetailForLinkDto fundDetail = new FundDetailForLinkDto();
        fundDetail.setCountryOriginCode("US");

        TaskConfig taskConfig = new TaskConfig();
        taskConfig.setUserCuttoff(/* set initial cutoff time */);
        taskConfig.setUserType("TypeA");

        when(fundServiceforLink.getFundDetailById(dto1.getFundId())).thenReturn(fundDetail);
        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdandUserType("WFS", dto1.getTaskId(), dto1.getFundId(), dto1.getUserType()))
            .thenReturn(taskConfig);
        when(taskConfigRepository.saveAll(anyList())).thenReturn(Arrays.asList(taskConfig));
        
        // Act
        Boolean result = yourService.savellfsTaskCutoffbyFund(wfsTaskConfiguration, modifiedBy);
        
        // Assert
        assertTrue(result);
        assertEquals(modifiedBy, taskConfig.getModifiedBy());
        verify(taskConfigRepository, times(1)).findByPerimeterAndTaskIdAndFundIdandUserType("WFS", dto1.getTaskId(), dto1.getFundId(), dto1.getUserType());
        verify(fundServiceforLink, times(1)).getFundDetailById(dto1.getFundId());
        verify(taskConfigRepository, times(1)).saveAll(anyList());
    }

    @Test
    void testSavellfsTaskCutoffbyFund_EmptyInput() throws LinkServiceException {
        // Arrange
        List<WfsTaskCutOffDto> wfsTaskConfiguration = new ArrayList<>();
        String modifiedBy = "user123";
        
        // Act
        Boolean result = yourService.savellfsTaskCutoffbyFund(wfsTaskConfiguration, modifiedBy);
        
        // Assert
        assertFalse(result);
        verify(taskConfigRepository, never()).saveAll(anyList());
        verify(fundServiceforLink, never()).getFundDetailById(anyInt());
    }
}

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.Arrays;
import java.util.List;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class YourServiceTest {

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @Mock
    private FundServiceForLink fundServiceforLink;

    @InjectMocks
    private YourService yourService; // Replace with the actual service class name

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testFetchWfsTaskCutoffbyFund() throws LinkServiceException {
        // Arrange
        List<Integer> fundIds = Arrays.asList(1, 2, 3);
        Integer taskId = 101;
        String area = "SampleArea";
        String field = "SampleField";
        
        TaskConfig taskConfig1 = new TaskConfig(/* initialize with necessary data */);
        taskConfig1.setFundId(1);
        taskConfig1.setUserCuttoff(/* initialize with necessary data */);
        taskConfig1.setUserType("TypeA");
        
        TaskList taskList = new TaskList();
        taskList.setTaskName("SampleTask");
        taskConfig1.setTaskList(taskList);
        
        FundDetailForLinkDto fundDetail = new FundDetailForLinkDto();
        fundDetail.setCountryOriginCode("US");

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds))
            .thenReturn(Arrays.asList(taskConfig1));
        when(fundServiceforLink.getFundDetailById(fundIds.get(0)))
            .thenReturn(fundDetail);
        
        // Act
        List<WfsTaskCutoffDto> result = yourService.fetchWfsTaskCutoffbyFund(fundIds, taskId, area, field);
        
        // Assert
        assertNotNull(result);
        assertEquals(1, result.size());
        WfsTaskCutoffDto dto = result.get(0);
        assertEquals("SampleTask", dto.getTaskName());
        assertEquals(taskId, dto.getTaskId());
        assertEquals(1, dto.getFundId());
        assertEquals("US", dto.getCountryCode()); // Assuming you are returning this value in the DTO
        // Add more assertions based on your logic and expected output
        
        verify(taskConfigRepository, times(1)).findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds);
        verify(fundServiceforLink, times(1)).getFundDetailById(fundIds.get(0));
    }
}

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.anyInt;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Optional;

public class TaskSetupServiceImplTest {

    @InjectMocks
    private TaskSetupServiceImpl taskSetupServiceImpl;

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @Mock
    private FundRepo fundRepo;

    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testCheckTasksAlreadyConfigured_TasksAlreadyConfigured() {
        // Arrange
        List<Integer> fundCodes = Arrays.asList(1, 2);
        String perimeter = "WFS";

        TaskConfig taskConfig = new TaskConfig();
        List<TaskConfig> taskListsByFund = Arrays.asList(taskConfig);

        Fund fund = new Fund();
        fund.setCode("FundCode1");

        when(taskConfigRepository.findByFundIdAndPerimeter(anyInt(), anyString())).thenReturn(taskListsByFund);
        when(fundRepo.findById(anyInt())).thenReturn(Optional.of(fund));

        // Act & Assert
        LinkServiceException exception = assertThrows(LinkServiceException.class, () -> {
            taskSetupServiceImpl.checkTasksAlreadyConfigured(fundCodes, perimeter);
        });

        // Validate the exception message
        assertTrue(exception.getMessage().contains("Tasks are already configured for the fund:"));
    }

    @Test
    public void testCheckTasksAlreadyConfigured_NoTasksConfigured() throws Exception {
        // Arrange
        List<Integer> fundCodes = Arrays.asList(1, 2);
        String perimeter = "WFS";

        // Mocking repository to return empty task lists
        when(taskConfigRepository.findByFundIdAndPerimeter(anyInt(), anyString())).thenReturn(new ArrayList<>());
        when(fundRepo.findById(anyInt())).thenReturn(Optional.empty()); // Fund not found scenario

        // Act
        boolean result = taskSetupServiceImpl.checkTasksAlreadyConfigured(fundCodes, perimeter);

        // Assert
        assertFalse(result, "No tasks should be configured, method should return false.");
        verify(taskConfigRepository, times(2)).findByFundIdAndPerimeter(anyInt(), anyString());
        verify(fundRepo, times(2)).findById(anyInt());
    }
}

import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class TaskSetupServiceImplTest {

    @InjectMocks
    private TaskSetupServiceImpl taskSetupServiceImpl;

    @Mock
    private TaskConfigUtil taskConfigUtil;

    @Mock
    private FundServiceForLink fundServiceForLink;

    @Mock
    private FundRepo fundRepo;

    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testDeleteTaskConfig_Success() throws Exception {
        // Arrange
        Integer taskNumber = 1;
        TaskConfigurationDTO taskConfiguration = new TaskConfigurationDTO();
        List<Integer> fundCodes = Arrays.asList(100, 200);
        taskConfiguration.setFundCodes(fundCodes);

        FundTasksDTO fundTask = new FundTasksDTO();
        List<FundTasksDTO> fundTasks = new ArrayList<>();
        fundTasks.add(fundTask);
        taskConfiguration.setFundTasks(fundTasks);

        FundDetailForLinkDto fundDetail = new FundDetailForLinkDto();
        fundDetail.setFundId(100);
        fundDetail.setFundGroups(Arrays.asList("Group1"));
        fundDetail.setTeams(Arrays.asList("Team1"));
        fundDetail.setClientId(12345);

        when(taskConfigUtil.deleteTaskConfig(any(), anyList(), any(FundTasksDTO.class))).thenReturn(true);
        when(taskConfigUtil.getWfsFundGroupId(anyList())).thenReturn("Group1");
        when(taskConfigUtil.getWfsTeamId(anyList())).thenReturn("Team1");
        when(fundServiceForLink.getFundDetailById(anyInt())).thenReturn(fundDetail);

        // Act
        Boolean result = taskSetupServiceImpl.deleteTaskConfig(taskNumber, taskConfiguration);

        // Assert
        assertTrue(result, "The deleteTaskConfig method should return true.");
        verify(taskConfigUtil).deleteTaskConfigfromDB(any(), anyList(), any(FundTasksDTO.class));
        verify(taskConfigUtil).sortTaskOrderAfterDelete(anyInt(), anyList());
    }
}