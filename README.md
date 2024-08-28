import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.boot.context.properties.ConfigurationProperties;

import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.Mockito.*;

public class RestTemplateConfigurationTest {

    @InjectMocks
    private RestTemplateConfiguration.Api2Configuration api2Configuration;

    @Mock
    private RestTemplateConfiguration.ClientCredentialsProperties mockClientCredentialsProperties;

    @Mock
    private OAuth2RestTemplate mockOAuth2RestTemplate;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testClientCredentialsPropertiesBean() {
        when(mockClientCredentialsProperties.getClientId()).thenReturn("client-id");
        when(mockClientCredentialsProperties.getClientSecret()).thenReturn("client-secret");

        RestTemplateConfiguration.ClientCredentialsProperties props = api2Configuration.api2Props();
        
        assertNotNull(props);
        verify(mockClientCredentialsProperties, times(1)).getClientId();
        verify(mockClientCredentialsProperties, times(1)).getClientSecret();
    }

    @Test
    void testVapGp3RestTemplateBean() {
        when(mockOAuth2RestTemplate.getAccessToken()).thenReturn(mock(OAuth2AccessToken.class));
        
        OAuth2RestTemplate restTemplate = api2Configuration.vapGp3RestTemplate();
        
        assertNotNull(restTemplate);
        verify(mockOAuth2RestTemplate, times(1)).getAccessToken();
    }
}