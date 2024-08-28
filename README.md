import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.web.client.RestTemplate;
import org.springframework.security.oauth2.client.OAuth2RestTemplate;

import static org.junit.jupiter.api.Assertions.assertNotNull;

public class RestTemplateConfigurationTest {

    private RestTemplateConfiguration.Api2Configuration api2Configuration;

    @BeforeEach
    void setUp() {
        // Initialize the Api2Configuration object
        api2Configuration = new RestTemplateConfiguration().new Api2Configuration();
    }

    @Test
    void testApi2Props() {
        // Call api2Props to ensure it returns a non-null object
        RestTemplateConfiguration.ClientCredentialsProperties clientCredentialsProperties = api2Configuration.api2Props();
        assertNotNull(clientCredentialsProperties, "ClientCredentialsProperties bean should not be null");
    }

    @Test
    void testVapGp3RestTemplate() {
        // Call vapGp3RestTemplate to ensure it returns a non-null RestTemplate object
        OAuth2RestTemplate restTemplate = api2Configuration.vapGp3RestTemplate();
        assertNotNull(restTemplate, "OAuth2RestTemplate bean should not be null");
    }
}