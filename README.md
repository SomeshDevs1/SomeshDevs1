import static org.mockito.Mockito.*;
import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.JobParameters;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.batch.core.launch.JobExecutionAlreadyRunningException;
import org.springframework.batch.core.launch.JobInstanceAlreadyCompleteException;
import org.springframework.batch.core.launch.JobRestartException;
import org.springframework.batch.core.repository.JobParametersInvalidException;

public class YourServiceTest {

    @Mock
    private JobLauncher jobLauncher;
    
    @Mock
    private JobExecution jobExecution;

    @Mock
    private Job yourJob; // Replace with your specific job if needed

    @InjectMocks
    private YourService yourService; // The service containing the method

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testProcessCleanMonitoringFund_success() throws Exception {
        // Given
        when(jobLauncher.run(any(Job.class), any(JobParameters.class))).thenReturn(jobExecution);
        when(jobExecution.getExitStatus()).thenReturn(ExitStatus.COMPLETED);

        // When
        String status = yourService.processCleanMonitoringFund();

        // Then
        assertEquals(ExitStatus.COMPLETED.getExitCode(), status);
        verify(jobLauncher, times(1)).run(any(Job.class), any(JobParameters.class));
    }

    @Test
    public void testProcessCleanMonitoringFund_jobAlreadyRunning() throws Exception {
        // Given
        when(jobLauncher.run(any(Job.class), any(JobParameters.class)))
            .thenThrow(new JobExecutionAlreadyRunningException("Job already running"));

        // When
        String status = yourService.processCleanMonitoringFund();

        // Then
        assertEquals("ERROR", status); // Replace with actual error handling
        verify(jobLauncher, times(1)).run(any(Job.class), any(JobParameters.class));
    }

    @Test
    public void testProcessCleanMonitoringFund_jobParametersInvalid() throws Exception {
        // Given
        when(jobLauncher.run(any(Job.class), any(JobParameters.class)))
            .thenThrow(new JobParametersInvalidException("Invalid parameters"));

        // When
        String status = yourService.processCleanMonitoringFund();

        // Then
        assertEquals("ERROR", status); // Replace with actual error handling
        verify(jobLauncher, times(1)).run(any(Job.class), any(JobParameters.class));
    }

    // Add similar tests for other exceptions
}