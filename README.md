we import org.junit.jupiter.api.BeforeEach;
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

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.boot.test.context.SpringBootTest;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.Mockito.mock;

@SpringBootTest
public class RestTemplateConfigurationTest {

    private Object api2ConfigurationInstance;

    @Mock
    private Object mockClientCredentialsProperties;

    @BeforeEach
    void setUp() throws Exception {
        MockitoAnnotations.openMocks(this);

        // Use reflection to instantiate Api2Configuration
        Class<?> outerClass = Class.forName("com.sgss.ast.fvs.vap.link.fund.api.config.RestTemplateConfiguration");
        Class<?> innerClass = Class.forName("com.sgss.ast.fvs.vap.link.fund.api.config.RestTemplateConfiguration$Api2Configuration");

        api2ConfigurationInstance = innerClass.getDeclaredConstructor(outerClass).newInstance(mock(outerClass));
    }

    @Test
    void testClientCredentialsPropertiesBean() throws Exception {
        // Access the method using reflection
        Method api2PropsMethod = api2ConfigurationInstance.getClass().getDeclaredMethod("api2Props");
        api2PropsMethod.setAccessible(true);

        Object clientCredentialsProperties = api2PropsMethod.invoke(api2ConfigurationInstance);
        assertNotNull(clientCredentialsProperties);

        // If you need to interact with the ClientCredentialsProperties class
        Field clientIdField = clientCredentialsProperties.getClass().getDeclaredField("clientId");
        clientIdField.setAccessible(true);
        clientIdField.set(clientCredentialsProperties, "test-client-id");

        assertNotNull(clientIdField.get(clientCredentialsProperties));
    }

    @Test
    void testVapGp3RestTemplateBean() throws Exception {
        // Access the method using reflection
        Method vapGp3RestTemplateMethod = api2ConfigurationInstance.getClass().getDeclaredMethod("vapGp3RestTemplate");
        vapGp3RestTemplateMethod.setAccessible(true);

        Object restTemplate = vapGp3RestTemplateMethod.invoke(api2ConfigurationInstance);
        assertNotNull(restTemplate);
    }
}
