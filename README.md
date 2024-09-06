package com.sgss.ast.fvs.vap.link.fund.api.util;

import static org.mockito.Mockito.*;

import java.util.Arrays;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.beans.factory.annotation.Value;

@ExtendWith(MockitoExtension.class)
class TaskSetUpUtilServiceTest {

    @InjectMocks
    private TaskSetUpUtilService taskSetUpUtilService;

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @Mock
    private AutomationListRepository automationListRepository;

    @Mock
    private FundRulesUtilService fundRulesUtilService;

    @Mock
    private FundServiceForLink fundServiceForLink;

    @Mock
    private CurrentTaskRunRepository currentTaskRunRepository;

    @Mock
    private CurrentTaskConfigRepository currentTaskConfigRepository;

    @Mock
    private CurrentFundRuleRepository currentFundRuleRepo;

    @Mock
    private FundRepository fundRepo;

    @Mock
    private OAuth2RestTemplate restTemplate;

    @Value("${task.allocation.service.address}")
    private String taskAllocationRestUrl;

    @BeforeEach
    void setUp() {
        // Set up or inject values if needed
    }

    @Test
    void testUpdateAllocationTable_withNewTask() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = mock(TaskConfigurationDTO.class);
        FundTasksDTO newTask = mock(FundTasksDTO.class);

        when(taskConfiguration.getFundTasks()).thenReturn(Arrays.asList(newTask));
        when(newTask.isNewTasks()).thenReturn(true);

        // Act
        taskSetUpUtilService.updateAllocationTable(taskConfiguration);

        // Assert
        verify(taskSetUpUtilService, times(1)).addTaskAllocation(eq(taskConfiguration), eq(newTask.getTaskId()), eq(newTask.getUserType()));
        verify(taskSetUpUtilService, never()).modifyTaskAllocation(any(), any(), anyInt(), anyString());
    }

    @Test
    void testUpdateAllocationTable_withExistingTask() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = mock(TaskConfigurationDTO.class);
        FundTasksDTO existingTask = mock(FundTasksDTO.class);

        when(taskConfiguration.getFundTasks()).thenReturn(Arrays.asList(existingTask));
        when(existingTask.isNewTasks()).thenReturn(false);
        when(existingTask.getTaskId()).thenReturn(123);
        when(existingTask.getUserType()).thenReturn("UserType");

        // Act
        taskSetUpUtilService.updateAllocationTable(taskConfiguration);

        // Assert
        verify(taskSetUpUtilService, never()).addTaskAllocation(any(), anyInt(), anyString());
        verify(taskSetUpUtilService, times(1)).modifyTaskAllocation(eq(taskConfiguration), eq(existingTask), eq(123), eq("UserType"));
    }
}