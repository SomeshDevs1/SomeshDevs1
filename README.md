import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Set;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

class TaskSetUpServiceImplTest {

    @InjectMocks
    private TaskSetUpServiceImpl taskSetUpServiceImpl;

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @Mock
    private TaskConfig taskConfig1;

    @Mock
    private TaskConfig taskConfig2;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testFetchTaskListbyFundAndArea_CommentMandatory() {
        List<TaskConfig> taskConfigs = Arrays.asList(taskConfig1, taskConfig2);
        List<Integer> fundIds = Arrays.asList(1, 2);
        String area = "someArea";
        Integer taskId = 123;

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds)).thenReturn(taskConfigs);
        when(taskConfig1.getIsCommentMandatory()).thenReturn(true);
        when(taskConfig2.getIsCommentMandatory()).thenReturn(false);
        when(taskConfig1.getFundId()).thenReturn(1);
        when(taskConfig2.getFundId()).thenReturn(2);
        when(taskSetUpServiceImpl.getFundCodeFromId(1)).thenReturn("Fund1");
        when(taskSetUpServiceImpl.getFundCodeFromId(2)).thenReturn("Fund2");

        Map<Boolean, Set<String>> result = taskSetUpServiceImpl.fetchTaskListbyFundAndArea(fundIds, taskId, area, TaskSetUpServiceImpl.COMMENT_MANDATORY);

        assertEquals(2, result.size());
        assertEquals(Set.of("Fund1"), result.get(true));
        assertEquals(Set.of("Fund2"), result.get(false));
    }

    @Test
    void testFetchTaskListbyFundAndArea_UserCutOff() {
        List<TaskConfig> taskConfigs = Arrays.asList(taskConfig1, taskConfig2);
        List<Integer> fundIds = Arrays.asList(1, 2);
        String area = "someArea";
        Integer taskId = 123;

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds)).thenReturn(taskConfigs);
        when(taskConfig1.getUserCuttoff()).thenReturn("cutoff1");
        when(taskConfig2.getUserCuttoff()).thenReturn("cutoff2");
        when(taskConfig1.getFundId()).thenReturn(1);
        when(taskConfig2.getFundId()).thenReturn(2);
        when(taskSetUpServiceImpl.getFundCodeFromId(1)).thenReturn("Fund1");
        when(taskSetUpServiceImpl.getFundCodeFromId(2)).thenReturn("Fund2");

        Map<String, Set<String>> result = taskSetUpServiceImpl.fetchTaskListbyFundAndArea(fundIds, taskId, area, TaskSetUpServiceImpl.USER_CUT_OFF);

        assertEquals(2, result.size());
        assertEquals(Set.of("Fund1"), result.get("cutoff1"));
        assertEquals(Set.of("Fund2"), result.get("cutoff2"));
    }

    @Test
    void testFetchTaskListbyFundAndArea_Automation() {
        List<TaskConfig> taskConfigs = Arrays.asList(taskConfig1, taskConfig2);
        List<Integer> fundIds = Arrays.asList(1, 2);
        String area = "someArea";
        Integer taskId = 123;

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds)).thenReturn(taskConfigs);
        when(taskConfig1.getAutomation()).thenReturn(Arrays.asList("auto1"));
        when(taskConfig2.getAutomation()).thenReturn(Arrays.asList("auto2"));
        when(taskConfig1.getFundId()).thenReturn(1);
        when(taskConfig2.getFundId()).thenReturn(2);
        when(taskSetUpServiceImpl.getFundCodeFromId(1)).thenReturn("Fund1");
        when(taskSetUpServiceImpl.getFundCodeFromId(2)).thenReturn("Fund2");
        when(taskSetUpServiceImpl.getAutomationCodesFromIds(Arrays.asList("auto1"))).thenReturn(Arrays.asList("auto1"));
        when(taskSetUpServiceImpl.getAutomationCodesFromIds(Arrays.asList("auto2"))).thenReturn(Arrays.asList("auto2"));

        Map<String, Set<String>> result = taskSetUpServiceImpl.fetchTaskListbyFundAndArea(fundIds, taskId, area, TaskSetUpServiceImpl.AUTOMATION);

        assertEquals(2, result.size());
        assertEquals(Set.of("Fund1"), result.get("auto1"));
        assertEquals(Set.of("Fund2"), result.get("auto2"));
    }

    @Test
    void testFetchTaskListbyFundAndArea_AfterCutOff() {
        List<TaskConfig> taskConfigs = Arrays.asList(taskConfig1, taskConfig2);
        List<Integer> fundIds = Arrays.asList(1, 2);
        String area = "someArea";
        Integer taskId = 123;

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds)).thenReturn(taskConfigs);
        when(taskConfig1.getIsAutomationAfterCutoff()).thenReturn(true);
        when(taskConfig2.getIsAutomationAfterCutoff()).thenReturn(false);
        when(taskConfig1.getFundId()).thenReturn(1);
        when(taskConfig2.getFundId()).thenReturn(2);
        when(taskSetUpServiceImpl.getFundCodeFromId(1)).thenReturn("Fund1");
        when(taskSetUpServiceImpl.getFundCodeFromId(2)).thenReturn("Fund2");

        Map<Boolean, Set<String>> result = taskSetUpServiceImpl.fetchTaskListbyFundAndArea(fundIds, taskId, area, TaskSetUpServiceImpl.AFTER_CUT_OFF);

        assertEquals(2, result.size());
        assertEquals(Set.of("Fund1"), result.get(true));
        assertEquals(Set.of("Fund2"), result.get(false));
    }

    @Test
    void testFetchTaskListbyFundAndArea_DefaultCase() {
        List<Integer> fundIds = Arrays.asList(1, 2);
        String area = "someArea";
        Integer taskId = 123;

        Map<?, ?> result = taskSetUpServiceImpl.fetchTaskListbyFundAndArea(fundIds, taskId, area, "unknownField");

        assertEquals(null, result);
    }
}



import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.*;
import java.util.stream.Collectors;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class TaskSetUpServiceImplTest {

    @Mock
    private TaskConfigRepository taskConfigRepository;

    @InjectMocks
    private TaskSetUpServiceImpl taskSetUpService; // Replace with the actual service class name

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testFetchTaskListbyFundAndArea_CommentMandatory() {
        // Arrange
        List<Integer> fundIds = Arrays.asList(1, 2, 3);
        Integer taskId = 101;
        String area = "SampleArea";
        String field = "commentmandatory";

        TaskConfig taskConfig1 = new TaskConfig();
        taskConfig1.setFundId(1);
        taskConfig1.setIsCommentMandatory(true);

        TaskConfig taskConfig2 = new TaskConfig();
        taskConfig2.setFundId(2);
        taskConfig2.setIsCommentMandatory(false);

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds))
            .thenReturn(Arrays.asList(taskConfig1, taskConfig2));

        // Mock the getFundCodeFromId method if it's from the same service or class
        when(taskSetUpService.getFundCodeFromId(1)).thenReturn("FundCode1");
        when(taskSetUpService.getFundCodeFromId(2)).thenReturn("FundCode2");

        // Act
        Map<Boolean, Set<String>> result = taskSetUpService.fetchTaskListbyFundAndArea(fundIds, taskId, area, field);

        // Assert
        assertNotNull(result);
        assertEquals(2, result.size());
        assertTrue(result.containsKey(true));
        assertTrue(result.containsKey(false));
        assertEquals(Set.of("FundCode1"), result.get(true));
        assertEquals(Set.of("FundCode2"), result.get(false));

        verify(taskConfigRepository, times(1)).findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds);
    }

    @Test
    void testFetchTaskListbyFundAndArea_UserCutoff() {
        // Arrange
        List<Integer> fundIds = Arrays.asList(1, 2, 3);
        Integer taskId = 101;
        String area = "SampleArea";
        String field = "usercuttoff";

        TaskConfig taskConfig1 = new TaskConfig();
        taskConfig1.setFundId(1);
        taskConfig1.setUserCuttoff("2024-09-01T12:00:00");

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds))
            .thenReturn(Collections.singletonList(taskConfig1));

        when(taskSetUpService.getFundCodeFromId(1)).thenReturn("FundCode1");

        // Act
        Map<String, Set<String>> result = taskSetUpService.fetchTaskListbyFundAndArea(fundIds, taskId, area, field);

        // Assert
        assertNotNull(result);
        assertEquals(1, result.size());
        assertTrue(result.containsKey("2024-09-01T12:00:00"));
        assertEquals(Set.of("FundCode1"), result.get("2024-09-01T12:00:00"));

        verify(taskConfigRepository, times(1)).findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds);
    }

    @Test
    void testFetchTaskListbyFundAndArea_Automation() {
        // Arrange
        List<Integer> fundIds = Arrays.asList(1, 2, 3);
        Integer taskId = 101;
        String area = "SampleArea";
        String field = "automation";

        TaskConfig taskConfig1 = new TaskConfig();
        taskConfig1.setFundId(1);
        taskConfig1.setAutomation("AUTOMATION_CODE");

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds))
            .thenReturn(Collections.singletonList(taskConfig1));

        when(taskSetUpService.getAutomationCodesFromIds("AUTOMATION_CODE")).thenReturn(Arrays.asList("AUTO1", "AUTO2"));
        when(taskSetUpService.getFundCodeFromId(1)).thenReturn("FundCode1");

        // Act
        Map<String, Set<String>> result = taskSetUpService.fetchTaskListbyFundAndArea(fundIds, taskId, area, field);

        // Assert
        assertNotNull(result);
        assertEquals(1, result.size());
        assertTrue(result.containsKey("AUTO1,AUTO2"));
        assertEquals(Set.of("FundCode1"), result.get("AUTO1,AUTO2"));

        verify(taskConfigRepository, times(1)).findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds);
    }

    // You can add similar tests for AFTER_CUT_OFF and AFTER_DVLVLP cases

    @Test
    void testFetchTaskListbyFundAndArea_InvalidField() {
        // Arrange
        List<Integer> fundIds = Arrays.asList(1, 2, 3);
        Integer taskId = 101;
        String area = "SampleArea";
        String field = "invalidField";

        when(taskConfigRepository.findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds))
            .thenReturn(Collections.emptyList());

        // Act
        Map result = taskSetUpService.fetchTaskListbyFundAndArea(fundIds, taskId, area, field);

        // Assert
        assertNull(result);
        verify(taskConfigRepository, times(1)).findByPerimeterAndTaskIdAndFundIdIn(area, taskId, fundIds);
    }
}