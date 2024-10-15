import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.MockitoJUnitRunner;
import org.springframework.web.util.UriComponentsBuilder;

import java.util.Arrays;
import java.util.List;

import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

@RunWith(MockitoJUnitRunner.class)
public class CallReportRestServiceImplTest {

    @Mock
    private OAuth2RestTemplate restTemplate;

    @InjectMocks
    private CallReportRestServiceImpl callReportRestService;

    @Before
    public void setUp() {
        // Setup base URL for the service
        callReportRestService.baseurlServices = "http://example.com/api";
    }

    @Test
    public void testConsumeReportApi_success() {
        // Arrange
        List<ReportData> reportDatas = Arrays.asList(new ReportData(), new ReportData()); // Example ReportData list
        String expectedResponse = "Success";

        UriComponentsBuilder builder = UriComponentsBuilder.fromHttpUrl("http://example.com/api");
        
        // Mock the restTemplate behavior
        when(restTemplate.postForObject(builder.toUriString(), reportDatas, String.class)).thenReturn(expectedResponse);

        // Act
        String actualResponse = callReportRestService.consumeReportApi(reportDatas);

        // Assert
        assertEquals(expectedResponse, actualResponse);
        verify(restTemplate, times(1)).postForObject(builder.toUriString(), reportDatas, String.class);
    }
}