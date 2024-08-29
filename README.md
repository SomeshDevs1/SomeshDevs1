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
        verify(taskConfigUtil).getUniqueTasks(singleFundCode.get(0), expectedDto, perimeter);
        verify(taskConfigUtil).getFundRulesForOneFund(singleFundCode.get(0), perimeter);
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
        TaskConfigurationDTO result = taskSetUpService.getTaskConfiguration(multipleFundCodes,​⬤