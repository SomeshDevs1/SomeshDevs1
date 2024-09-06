package com.sgss.ast.fvs.vap.link.fund.api.util;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.util.Arrays;
import java.util.List;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;

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

    @BeforeEach
    void setUp() {
        // Mocking OAuth2RestTemplate and taskAllocationRestUrl if required
    }

    @Test
    void testUpdateAllocationTable_withNewTask() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = mock(TaskConfigurationDTO.class);
        TaskDTO newTask = mock(TaskDTO.class);

        when(taskConfiguration.getFundTasks()).thenReturn(Arrays.asList(newTask));
        when(newTask.isNewTasks()).thenReturn(true);

        // Act
        taskSetUpUtilService.updateAllocationTable(taskConfiguration);

        // Assert
        verify(taskSetUpUtilService, times(1)).addTaskAllocation(eq(taskConfiguration), eq(newTask));
        verify(taskSetUpUtilService, never()).modifyTaskAllocation(any(), any(), anyLong(), any());
    }

    @Test
    void testUpdateAllocationTable_withExistingTask() {
        // Arrange
        TaskConfigurationDTO taskConfiguration = mock(TaskConfigurationDTO.class);
        TaskDTO existingTask = mock(TaskDTO.class);

        when(taskConfiguration.getFundTasks()).thenReturn(Arrays.asList(existingTask));
        when(existingTask.isNewTasks()).thenReturn(false);
        when(existingTask.getTaskId()).thenReturn(123L);
        when(existingTask.getUserType()).thenReturn("Admin");

        // Act
        taskSetUpUtilService.updateAllocationTable(taskConfiguration);

        // Assert
        verify(taskSetUpUtilService, never()).addTaskAllocation(any(), any());
        verify(taskSetUpUtilService, times(1)).modifyTaskAllocation(eq(taskConfiguration), eq(existingTask), eq(123L), eq("Admin"));
    }
}