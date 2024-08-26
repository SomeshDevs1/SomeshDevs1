import static org.mockito.Mockito.*;
import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.MockitoJUnitRunner;
import org.springframework.dao.EmptyResultDataAccessException;

@RunWith(MockitoJUnitRunner.class)
public class FundGroupSetupServiceImplTest {

    @Mock
    private CountryRepository countryRepository;

    @Mock
    private FundRepository fundRepository;

    @Mock
    private PocketRepository pocketRepository;

    @InjectMocks
    private FundGroupSetupServiceImpl fundGroupSetupService;

    private Fund fund;
    private Pocket pocket;
    private FundDetailForLinkDto fundDetailForLinkDto;

    @Before
    public void setUp() {
        fund = new Fund();
        pocket = new Pocket();
        fundDetailForLinkDto = new FundDetailForLinkDto();
    }

    @Test
    public void testGetFundDetailByFundAndPocket_WithFundCodeAndPocketCode() throws Exception {
        String fundCode = "FUND123";
        String pocketCode = "POCKET123";

        when(fundRepository.findFund(fundCode)).thenReturn(fund);
        when(pocketRepository.getPocket(fundCode, pocketCode)).thenReturn(pocket);

        FundDetailForLinkDto result = fundGroupSetupService.getFundDetailByFundAndPocket(fundCode, pocketCode);

        assertNotNull(result);
        verify(pocketRepository, times(1)).getPocket(fundCode, pocketCode);
    }

    @Test
    public void testGetFundDetailByFundAndPocket_WithFundCodeOnly() throws Exception {
        String fundCode = "FUND123";
        String pocketCode = null;

        when(fundRepository.findFund(fundCode)).thenReturn(fund);
        when(fundRepository.getFundDetail(fund)).thenReturn(fundDetailForLinkDto);
        when(fundRepository.getNumberOfPocket(fund)).thenReturn(1);

        FundDetailForLinkDto result = fundGroupSetupService.getFundDetailByFundAndPocket(fundCode, pocketCode);

        assertNotNull(result);
        assertEquals(1, result.getNumberOfPockets());
        verify(fundRepository, times(1)).findFund(fundCode);
    }

    @Test(expected = LinkServiceException.class)
    public void testGetFundDetailByFundAndPocket_FundCodeNotFound() throws Exception {
        String fundCode = "INVALID_FUND_CODE";
        String pocketCode = null;

        when(fundRepository.findFund(fundCode)).thenThrow(new EmptyResultDataAccessException(1));

        fundGroupSetupService.getFundDetailByFundAndPocket(fundCode, pocketCode);
    }

    @Test(expected = LinkServiceException.class)
    public void testGetFundDetailByFundAndPocket_FundDetailIsNull() throws Exception {
        String fundCode = "FUND123";
        String pocketCode = null;

        when(fundRepository.findFund(fundCode)).thenReturn(fund);
        when(fundRepository.getFundDetail(fund)).thenReturn(null);

        fundGroupSetupService.getFundDetailByFundAndPocket(fundCode, pocketCode);
    }
}