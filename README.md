import com.sgss.ast.fvs.vap.link.followup.services.impl.CallReportRestServiceImpl;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.web.util.UriComponentsBuilder;
import org.springframework.security.oauth2.client.OAuth2RestTemplate;

import java.util.Collections;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.when;

@SpringBootTest
public class CallReportRestServiceImplTest {

    @Mock
    private OAuth2RestTemplate restTemplate;

    @InjectMocks
    private CallReportRestServiceImpl callReportRestServiceImpl;

    @Value("${vap.report.service.adress}")
    private String baseUrlServices;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        callReportRestServiceImpl = new CallReportRestServiceImpl();
        callReportRestServiceImpl.restTemplate = restTemplate;
        callReportRestServiceImpl.baseUrlServices = baseUrlServices;
    }

    @Test
    public void testConsumeReportApi() {
        // Arrange
        List<ReportData> reportDatas = Collections.emptyList(); // Mock empty list for simplicity
        String expectedResponse = "Mocked Response";

        // Mock UriComponentsBuilder
        UriComponentsBuilder builder = UriComponentsBuilder.fromHttpUrl(baseUrlServices);

        // Mock the restTemplate response
        when(restTemplate.postForObject(anyString(), eq(reportDatas), eq(String.class))).thenReturn(expectedResponse);

        // Act
        String actualResponse = callReportRestServiceImpl.consumeReportApi(reportDatas);

        // Assert
        assertEquals(expectedResponse, actualResponse);
    }
}