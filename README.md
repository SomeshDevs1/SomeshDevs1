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