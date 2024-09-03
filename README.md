import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

@ExtendWith(MockitoExtension.class)
public class TaskSetUpServiceImplTest {

    @Mock
    private TaskConfigUtil taskConfigUtil;

    @InjectMocks
    private TaskSetUpServiceImpl taskSetUpService;

    private List<Integer> fundCodes;
    private String perimeter;

    @BeforeEach
    void setUp() {
        fundCodes = Arrays.asList(1, 2, 3);
        perimeter = "WFS";
    }

    @Test
    void testGetTaskConfiguration_withWFS_andOneFundCode() throws LinkServiceException {
        // Setup
        perimeter = "WFS";
        fundCodes = Collections.singletonList(1);

        // Mocking
        when(taskConfigUtil.getUniqueTasksForOneFund(1, any(), anyString())).thenReturn(any());
        when(taskConfigUtil.getFundRulesForOneFund(1, anyString())).thenReturn(any());

        // Execute
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(fundCodes, perimeter);

        // Assertions
        assertNotNull(result);
        verify(taskConfigUtil).getUniqueTasksForOneFund(1, result, perimeter);
        verify(taskConfigUtil).getFundRulesForOneFund(1, perimeter);
    }

    @Test
    void testGetTaskConfiguration_withWFS_andMultipleFundCodes() throws LinkServiceException {
        // Setup
        perimeter = "WFS";
        fundCodes = Arrays.asList(1, 2, 3);

        // Mocking
        when(taskConfigUtil.getUniqueTasksForOneFund(1, any(), anyString())).thenReturn(any());
        when(taskConfigUtil.getFundRulesForOneFund(1, anyString())).thenReturn(any());

        // Execute
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(fundCodes, perimeter);

        // Assertions
        assertNotNull(result);
        verify(taskConfigUtil, never()).getUniqueTasksForOneFund(anyInt(), any(), anyString());
        verify(taskConfigUtil, never()).getFundRulesForOneFund(anyInt(), anyString());
    }

    @Test
    void testGetTaskConfiguration_withOtherPerimeter() throws LinkServiceException {
        // Setup
        perimeter = "OTHER";

        // Mocking
        when(taskConfigUtil.getUniqueTasksForOneFund(anyInt(), any(), anyString())).thenReturn(any());
        when(taskConfigUtil.getFundRulesForOneFund(anyInt(), anyString())).thenReturn(any());

        // Execute
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(fundCodes, perimeter);

        // Assertions
        assertNotNull(result);
        verify(taskConfigUtil).getUniqueTasksForOneFund(anyInt(), any(), eq(perimeter));
        verify(taskConfigUtil).getFundRulesForOneFund(anyInt(), eq(perimeter));
    }
}