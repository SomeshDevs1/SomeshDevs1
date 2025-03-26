import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.client.registration.ClientRegistrationRepository;
import org.springframework.security.oauth2.client.web.reactive.function.client.ServletOAuth2AuthorizedClientExchangeFilterFunction;
import org.springframework.security.oauth2.client.AuthorizedClientServiceOAuth2AuthorizedClientManager;
import org.springframework.security.oauth2.client.AuthorizedClientServiceOAuth2AuthorizedClientProvider;
import org.springframework.security.oauth2.client.registration.ClientRegistration;
import org.springframework.security.oauth2.client.AuthorizedClientServiceOAuth2AuthorizedClientManager;
import org.springframework.web.reactive.function.client.WebClient;
import org.springframework.beans.factory.annotation.Value;

@Configuration
public class WebClientConfig {

    private final ClientRegistrationRepository clientRegistrationRepository;

    public WebClientConfig(ClientRegistrationRepository clientRegistrationRepository) {
        this.clientRegistrationRepository = clientRegistrationRepository;
    }

    @Value("${vap.incident.api.url}")
    private String incidentUrl;

    @Value("${vap.report.service.base}")
    private String baseUrlServices;

    @Bean(name = "vapReportingWebClient")
    public WebClient vapReportingWebClient(ClientRegistrationRepository clientRegistrations) {
        ServletOAuth2AuthorizedClientExchangeFilterFunction oauth =
            new ServletOAuth2AuthorizedClientExchangeFilterFunction(
                new AuthorizedClientServiceOAuth2AuthorizedClientManager(
                    clientRegistrations, 
                    new AuthorizedClientServiceOAuth2AuthorizedClientProvider()
                )
            );
        oauth.setDefaultClientRegistrationId("vap");

        return WebClient.builder()
            .baseUrl(baseUrlServices)
            .filter(oauth)
            .build();
    }

    @Bean(name = "incidentReportingWebClient")
    public WebClient incidentReportingWebClient(ClientRegistrationRepository clientRegistrations) {
        ServletOAuth2AuthorizedClientExchangeFilterFunction oauth =
            new ServletOAuth2AuthorizedClientExchangeFilterFunction(
                new AuthorizedClientServiceOAuth2AuthorizedClientManager(
                    clientRegistrations, 
                    new AuthorizedClientServiceOAuth2AuthorizedClientProvider()
                )
            );
        oauth.setDefaultClientRegistrationId("incident");

        return WebClient.builder()
            .baseUrl(incidentUrl)
            .filter(oauth)
            .build();
    }
}