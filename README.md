<import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.JobParameters;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.batch.core.launch.JobInstanceAlreadyCompleteException;
import org.springframework.batch.core.launch.JobRestartException;
import org.springframework.batch.core.repository.JobExecutionAlreadyRunningException;

import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.model.ObjectListing;

public class S3BucketServiceImplTest {

    @InjectMocks
    private S3BucketServiceImpl s3BucketServiceImpl;

    @Mock
    private AmazonS3 amazonS3Client;

    @Mock
    private JobLauncher jobLauncher;

    @Mock
    private Job germanyDataProcessJob;

    @Mock
    private JobExecution jobExecution;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testProcessGermanyFileData_WithFiles() throws Exception {
        // Arrange
        ObjectListing objectListing = mock(ObjectListing.class);
        when(amazonS3Client.listObjectsV2(anyString(), anyString()).getObjectSummaries().size()).thenReturn(2);
        when(jobLauncher.run(eq(germanyDataProcessJob), any(JobParameters.class))).thenReturn(jobExecution);
        when(jobExecution.getExitStatus().getExitCode()).thenReturn("COMPLETED");

        // Act
        String result = s3BucketServiceImpl.processGermanyFileData();

        // Assert
        assertEquals("COMPLETED", result);
    }

    @Test
    public void testProcessGermanyFileData_NoFiles() throws Exception {
        // Arrange
        ObjectListing objectListing = mock(ObjectListing.class);
        when(amazonS3Client.listObjectsV2(anyString(), anyString()).getObjectSummaries().size()).thenReturn(0);

        // Act
        String result = s3BucketServiceImpl.processGermanyFileData();

        // Assert
        assertEquals("No file present in the bucket", result);
    }

    @Test
    public void testProcessGermanyFileData_JobExecutionException() throws Exception {
        // Arrange
        when(amazonS3Client.listObjectsV2(anyString(), anyString()).getObjectSummaries().size()).thenReturn(2);
        when(jobLauncher.run(eq(germanyDataProcessJob), any(JobParameters.class))).thenThrow(JobExecutionAlreadyRunningException.class);

        // Act & Assert
        assertThrows(LinkBatchException.class, () -> s3BucketServiceImpl.processGermanyFileData());
    }
}




import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.JobParameters;
import org.springframework.batch.core.launch.JobLauncher;
import org.springframework.batch.core.launch.JobInstanceAlreadyCompleteException;
import org.springframework.batch.core.launch.JobRestartException;
import org.springframework.batch.core.repository.JobExecutionAlreadyRunningException;
import org.springframework.batch.core.repository.JobParametersInvalidException;

import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.model.ListObjectsV2Result;
import com.amazonaws.services.s3.model.S3ObjectSummary;

import java.util.Collections;

public class S3BucketServiceImplTest {

    @InjectMocks
    private S3BucketServiceImpl s3BucketServiceImpl;

    @Mock
    private AmazonS3 amazonS3Client;

    @Mock
    private JobLauncher jobLauncher;

    @Mock
    private Job germanyDataProcessJob;

    @Mock
    private JobExecution jobExecution;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testProcessGermanyFileData_WithFiles() throws Exception {
        // Arrange
        ListObjectsV2Result result = mock(ListObjectsV2Result.class);
        when(result.getObjectSummaries()).thenReturn(Collections.singletonList(new S3ObjectSummary()));
        when(amazonS3Client.listObjectsV2(anyString(), anyString())).thenReturn(result);
        when(jobLauncher.run(eq(germanyDataProcessJob), any(JobParameters.class))).thenReturn(jobExecution);
        when(jobExecution.getExitStatus().getExitCode()).thenReturn("COMPLETED");

        // Act
        String actualStatus = s3BucketServiceImpl.processGermanyFileData();

        // Assert
        assertEquals("COMPLETED", actualStatus);
    }

    @Test
    public void testProcessGermanyFileData_NoFiles() throws Exception {
        // Arrange
        ListObjectsV2Result result = mock(ListObjectsV2Result.class);
        when(result.getObjectSummaries()).thenReturn(Collections.emptyList());
        when(amazonS3Client.listObjectsV2(anyString(), anyString())).thenReturn(result);

        // Act
        String actualStatus = s3BucketServiceImpl.processGermanyFileData();

        // Assert
        assertEquals("No file present in the bucket", actualStatus);
    }

    @Test
    public void testProcessGermanyFileData_JobExecutionAlreadyRunningException() throws Exception {
        // Arrange
        ListObjectsV2Result result = mock(ListObjectsV2Result.class);
        when(result.getObjectSummaries()).thenReturn(Collections.singletonList(new S3ObjectSummary()));
        when(amazonS3Client.listObjectsV2(anyString(), anyString())).thenReturn(result);
        when(jobLauncher.run(eq(germanyDataProcessJob), any(JobParameters.class)))
            .thenThrow(JobExecutionAlreadyRunningException.class);

        // Act & Assert
        assertThrows(LinkBatchException.class, () ->​⬤

