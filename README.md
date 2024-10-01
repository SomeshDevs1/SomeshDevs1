import static org.mockito.Mockito.*;
import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;
import java.util.ArrayList;
import java.util.List;

public class ChecklistBatchProcessorTest {

    @Mock
    private CurrentTaskRunRepository currentTaskRunRepository;

    @Mock
    private RestTemplate restTemplate;

    @InjectMocks
    private ChecklistBatchProcessor checklistBatchProcessor;

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testProcess_Success() throws Exception {
        // Given
        ChecklistBatch inputBatch = new ChecklistBatch();
        inputBatch.setId(1L); // Assuming an ID field exists
        List<TaskList> taskList = new ArrayList<>();
        taskList.add(new TaskList()); // Add mock tasks
        when(currentTaskRunRepository.findBySysId(anyLong())).thenReturn(taskList);

        // Mock the REST call
        UriComponentsBuilder builder = UriComponentsBuilder.fromUriString("http://test-service-url");
        doNothing().when(restTemplate).postForEntity(builder.toUriString(), taskList, null);

        // When
        ChecklistBatch result = checklistBatchProcessor.process(inputBatch);

        // Then
        verify(currentTaskRunRepository, times(1)).findBySysId(inputBatch.getSysId());
        verify(restTemplate, times(1)).postForEntity(anyString(), eq(taskList), isNull());
        assertEquals(inputBatch, result); // Ensure the returned object is the same as the input
    }

    @Test(expected = Exception.class)
    public void testProcess_ExceptionOnRestCall() throws Exception {
        // Given
        ChecklistBatch inputBatch = new ChecklistBatch();
        inputBatch.setId(1L); 
        List<TaskList> taskList = new ArrayList<>();
        taskList.add(new TaskList());
        when(currentTaskRunRepository.findBySysId(anyLong())).thenReturn(taskList);

        // Simulate an exception when the REST call is made
        UriComponentsBuilder builder = UriComponentsBuilder.fromUriString("http://test-service-url");
        doThrow(new RuntimeException("REST call failed")).when(restTemplate).postForEntity(builder.toUriString(), taskList, null);

        // When
        checklistBatchProcessor.process(inputBatch);

        // Then (exception is expected, no assertions needed)
    }

    @Test(expected = Exception.class)
    public void testProcess_ExceptionOnRepositoryCall() throws Exception {
        // Given
        ChecklistBatch inputBatch = new ChecklistBatch();
        inputBatch.setId(1L); 
        when(currentTaskRunRepository.findBySysId(anyLong())).thenThrow(new RuntimeException("Database error"));

        // When
        checklistBatchProcessor.process(inputBatch);

        // Then (exception is expected, no assertions needed)
    }
}