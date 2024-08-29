import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.util.Arrays;
import java.util.List;

public class TaskSetUpServiceImplTest {

    @InjectMocks
    private TaskSetUpServiceImpl taskSetUpService;

    @Mock
    private TaskConfigUtil taskConfigUtil;

    private List<Integer> singleFundCode;
    private List<Integer> multipleFundCodes;
    private String perimeter;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        singleFundCode = Arrays.asList(1);
        multipleFundCodes = Arrays.asList(1, 2);
        perimeter = "MFS";  // Example perimeter value, change according to your requirements
    }

    @Test
    public void testGetTaskConfiguration_SingleFund_MFS() {
        // Arrange
        TaskConfigurationDTO expectedDto = new TaskConfigurationDTO();
        when(taskConfigUtil.getUniqueTasks(singleFundCode.get(0), expectedDto, perimeter))
                .thenReturn(expectedDto);
        when(taskConfigUtil.getFundRulesForOneFund(singleFundCode.get(0), perimeter))
                .thenReturn(expectedDto);

        // Act
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(singleFundCode, perimeter);

        // Assert
        assertNotNull(result);
        verify(taskConfigUtil).getFundRulesForOneFund(singleFundCode.get(0), perimeter);
        verify(taskConfigUtil).setFundTasks(singleFundCode.get(0), result, perimeter);
    }

    @Test
    public void testGetTaskConfiguration_MultipleFunds_MFS() {
        // Arrange
        TaskConfigurationDTO expectedDto = new TaskConfigurationDTO();
        when(taskConfigUtil.getUniqueTasks(multipleFundCodes, expectedDto, perimeter))
                .thenReturn(expectedDto);
        when(taskConfigUtil.getFundRules(multipleFundCodes, perimeter))
                .thenReturn(expectedDto);

        // Act
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(multipleFundCodes, perimeter);

        // Assert
        assertNotNull(result);
        verify(taskConfigUtil).getUniqueTasks(multipleFundCodes, expectedDto, perimeter);
        verify(taskConfigUtil).getFundRules(multipleFundCodes, perimeter);
    }

    @Test
    public void testGetTaskConfiguration_SingleFund_OtherPerimeter() {
        // Arrange
        String otherPerimeter = "OTHER";  // Non-MFS perimeter
        TaskConfigurationDTO expectedDto = new TaskConfigurationDTO();
        when(taskConfigUtil.getFundRulesForOneFund(singleFundCode.get(0), otherPerimeter))
                .thenReturn(expectedDto);
        when(taskConfigUtil.getUniqueTasks(singleFundCode.get(0), expectedDto, otherPerimeter))
                .thenReturn(expectedDto);

        // Act
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(singleFundCode, otherPerimeter);

        // Assert
        assertNotNull(result);
        verify(taskConfigUtil).getFundRulesForOneFund(singleFundCode.get(0), otherPerimeter);
        verify(taskConfigUtil).setFundTasks(singleFundCode.get(0), result, otherPerimeter);
    }

    @Test
    public void testGetTaskConfiguration_MultipleFunds_OtherPerimeter() {
        // Arrange
        String otherPerimeter = "OTHER";  // Non-MFS perimeter
        TaskConfigurationDTO expectedDto = new TaskConfigurationDTO();
        when(taskConfigUtil.getFundRules(multipleFundCodes, otherPerimeter))
                .thenReturn(expectedDto);
        when(taskConfigUtil.getUniqueTasks(multipleFundCodes, expectedDto, otherPerimeter))
                .thenReturn(expectedDto);

        // Act
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(multipleFundCodes, otherPerimeter);

        // Assert
        assertNotNull(result);
        verify(taskConfigUtil).getFundRules(multipleFundCodes, otherPerimeter);
        verify(taskConfigUtil).setFundTasks(multipleFundCodes, result, otherPerimeter);
    }
}