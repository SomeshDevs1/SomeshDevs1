import static org.mockito.Mockito.*;

import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.slf4j.Logger;

public class FundServiceForOldLinkImplTest {

    @Mock
    private Logger logger;

    @InjectMocks
    private FundServiceForOldLinkImpl fundServiceForOldLinkImpl;

    @Test
    public void testSaveFundDetailsForOldLink_Logger() {
        // Arrange
        FundItemDTO fundItemDTO = new FundItemDTO();
        // Assuming some necessary setup of fundItemDTO
        fundItemDTO.setNonOfiFollowed(null);

        // Act
        fundServiceForOldLinkImpl.saveFundDetailsForOldLink(fundItemDTO);

        // Assert
        verify(logger).info("NonOfi Followed is null. It's updated with default value 'YES'");
    }
}