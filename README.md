import com.sgss.ast.fvs.vap.link.followup.services.impl.CallReportRestServiceImpl;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.security.oauth2.client.OAuth2RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.lang.reflect.Field;
import java.util.Collections;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.when;

public class CallReportRestServiceImplTest {

    @Mock
    private OAuth2RestTemplate restTemplate;

    @InjectMocks
    private CallReportRestServiceImpl callReportRestServiceImpl;

    @BeforeEach
    public void setUp() throws Exception {
        MockitoAnnotations.openMocks(this);

        // Use reflection to set the private baseUrlServices field
        Field baseUrlField = CallReportRestServiceImpl.class.getDeclaredField("baseUrlServices");
        baseUrlField.setAccessible(true);
        baseUrlField.set(callReportRestServiceImpl, "http://mocked-base-url");

        // Use reflection to set the private restTemplate field
        Field restTemplateField = CallReportRestServiceImpl.class.getDeclaredField("restTemplate");
        restTemplateField.setAccessible(true);
        restTemplateField.set(callReportRestServiceImpl, restTemplate);
    }

    @Test
    public void testConsumeReportApi() {
        // Arrange
        List<ReportData> reportDatas = Collections.emptyList(); // Mock empty list for simplicity
        String expectedResponse = "Mocked Response";

        // Mock the restTemplate response
        when(restTemplate.postForObject(anyString(), eq(reportDatas), eq(String.class))).thenReturn(expectedResponse);

        // Act
        String actualResponse = callReportRestServiceImpl.consumeReportApi(reportDatas);

        // Assert
        assertEquals(expectedResponse, actualResponse);
    }
}