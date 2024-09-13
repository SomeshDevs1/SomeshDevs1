import static org.mockito.Mockito.*;
import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.web.util.UriComponentsBuilder;

public class TaskConfigUtilServiceTest {

    @Mock
    private TaskConfigUtilService taskConfigUtilService; // Assuming this is the class

    @Mock
    private Logger logger; // Mock logger if used

    // Inject the mocks into the class under test
    @InjectMocks
    private TaskConfigUtilService serviceUnderTest;

    private String taskAllocationRestUrl = "http://localhost/test";

    @Before
    public void setup() {
        MockitoAnnotations.openMocks(this);  // Initialize mocks
    }

    @Test
    public void testGetRestTemplate_Success() {
        // When: Call the getRestTemplate method
        UriComponentsBuilder uriBuilder = serviceUnderTest.getRestTemplate();

        // Then: Validate that the URL is properly built
        assertNotNull(uriBuilder);
        assertEquals(taskAllocationRestUrl, uriBuilder.toUriString());
    }

    @Test
    public void testAddTaskAllocation_WithValidURL() {
        // Given
        TaskAllocationItemDto taskAllocationDto = new TaskAllocationItemDto(); // Mock object
        when(taskConfigUtilService.getTaskAllocationItem(any(), anyInt(), anyString())).thenReturn(taskAllocationDto);

        // Mock getRestTemplate to return a valid URL
        when(serviceUnderTest.getRestTemplate()).thenReturn(UriComponentsBuilder.fromHttpUrl(taskAllocationRestUrl));

        when(restTemplate.postForObject(anyString(), any(), eq(Boolean.class))).thenReturn(true);

        // When
        boolean result = serviceUnderTest.addTaskAllocation(mock(TaskConfigurationDTO.class), 1, "USER");

        // Then
        assertTrue(result);
        verify(restTemplate).postForObject(anyString(), eq(taskAllocationDto), eq(Boolean.class));
    }

    @Test
    public void testAddTaskAllocation_NullUrl() {
        // Given
        TaskAllocationItemDto taskAllocationDto = new TaskAllocationItemDto(); // Mock object
        when(taskConfigUtilService.getTaskAllocationItem(any(), anyInt(), anyString())).thenReturn(taskAllocationDto);

        // Mock getRestTemplate to return a null URL
        when(serviceUnderTest.getRestTemplate()).thenReturn(UriComponentsBuilder.newInstance()); // No URL set

        // When
        boolean result = serviceUnderTest.addTaskAllocation(mock(TaskConfigurationDTO.class), 1, "USER");

        // Then
        assertFalse(result);  // Should return false because URL is null
        verify(logger).error("Generated URL is null or empty");
    }
}