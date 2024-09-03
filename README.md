import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Arrays;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class TaskConfigurationServiceTest {

    @Mock
    private TaskConfigUtil taskConfigUtil;

    @Mock
    private FundRulesUtil fundRulesUtil;

    @InjectMocks
    private TaskConfigurationService taskConfigurationService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetTaskConfiguration_withSingleFundCode() throws LinkServiceException {
        // Arrange
        List<Integer> fundCodes = Arrays.asList(1);
        String perimeter = LinkFundConstants.WFS;

        List<FundTasksDTO> mockTasks = Arrays.asList(new FundTasksDTO());
        FundRulesDTO mockRules = new FundRulesDTO();

        when(taskConfigUtil.getUniqueTasksForOneFund(1, any(TaskConfigurationDTO.class), eq(perimeter)))
            .thenReturn(mockTasks);
        when(fundRulesUtil.getFundRulesForOneFund(1, perimeter))
            .thenReturn(mockRules);

        // Act
        TaskConfigurationDTO result = taskConfigurationService.getTaskConfiguration(fundCodes, perimeter);

        // Assert
        assertNotNull(result);
        assertEquals(perimeter, result.getPerimeter());
        assertEquals(fundCodes, result.getFundCodes());
        assertEquals(mockTasks, result.getFundTasks());
        assertEquals(mockRules, result.getFundRules());

        verify(taskConfigUtil).getUniqueTasksForOneFund(1, any(TaskConfigurationDTO.class), eq(perimeter));
        verify(fundRulesUtil).getFundRulesForOneFund(1, perimeter);
    }

    @Test
    void testGetTaskConfiguration_withMultipleFundCodes() throws LinkServiceException {
        // Arrange
        List<Integer> fundCodes = Arrays.asList(1, 2);
        String perimeter = LinkFundConstants.WFS;

        List<FundTasksDTO> mockTasks = Arrays.asList(new FundTasksDTO());
        FundRulesDTO mockRules = new FundRulesDTO();

        when(taskConfigUtil.getUniqueTasks(fundCodes, any(TaskConfigurationDTO.class), eq(perimeter)))
            .thenReturn(mockTasks);
        when(fundRulesUtil.getFundRules(fundCodes, perimeter))
            .thenReturn(mockRules);

        // Act
        TaskConfigurationDTO result = taskConfigurationService.getTaskConfiguration(fundCodes, perimeter);

        // Assert
        assertNotNull(result);
        assertEquals(perimeter, result.getPerimeter());
        assertEquals(fundCodes, result.getFundCodes());
        assertEquals(mockTasks, result.getFundTasks());
        assertEquals(mockRules, result.getFundRules());

        verify(taskConfigUtil).getUniqueTasks(fundCodes, any(TaskConfigurationDTO.class), eq(perimeter));
        verify(fundRulesUtil).getFundRules(fundCodes, perimeter);
    }

    @Test
    void testGetTaskConfiguration_withNonWfsPerimeter() throws LinkServiceException {
        // Arrange
        List<Integer> fundCodes = Arrays.asList(1, 2);
        String perimeter = "NON_WFS";

        List<FundTasksDTO> mockTasks = Arrays.asList(new FundTasksDTO());
        FundRulesDTO mockRules = new FundRulesDTO();

        when(taskConfigUtil.getUniqueTasks(fundCodes, any(TaskConfigurationDTO.class), eq(perimeter)))
            .thenReturn(mockTasks);
        when(fundRulesUtil.getFundRules(fundCodes, perimeter))
            .thenReturn(mockRules);

        // Act
        TaskConfigurationDTO result = taskConfigurationService.getTaskConfiguration(fundCodes, perimeter);

        // Assert
        assertNotNull(result);
        assertEquals(perimeter, result.getPerimeter());
        assertEquals(fundCodes, result.getFundCodes());
        assertEquals(mockTasks, result.getFundTasks());
        assertEquals(mockRules, result.getFundRules());

        verify(taskConfigUtil).getUniqueTasks(fundCodes, any(TaskConfigurationDTO.class), eq(perimeter));
        verify(fundRulesUtil).getFundRules(fundCodes, perimeter);
    }
}