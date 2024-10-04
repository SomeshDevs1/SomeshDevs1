import static org.junit.Assert.*;
import org.junit.Test;
import org.mockito.Mockito;

import java.sql.Timestamp;
import java.text.SimpleDateFormat;
import java.util.Calendar;

public class FundPlanUtilsTest {

    @Test
    public void testGetCutoff_SuccessCase() throws Exception {
        // Mock inputs
        Timestamp deliveryPeriod = new Timestamp(System.currentTimeMillis());
        String inputTimeStr = "12:00:00";  // Example valid time
        String countryCode = "UK";

        // Create a mock FundRule object and set behavior
        FundRule fundRuleMock = Mockito.mock(FundRule.class);
        Mockito.when(fundRuleMock.getCutOffMoreThanOneDay()).thenReturn(1);

        // Call the method
        Timestamp result = FundPlanUtils.getCutoff(fundRuleMock, deliveryPeriod, inputTimeStr, countryCode);

        // Validate result - This depends on the expected output.
        assertNotNull(result);
        // You can use SimpleDateFormat to validate the output date format if necessary
    }

    @Test(expected = LinkServiceException.class)
    public void testGetCutoff_InvalidTimeString() throws Exception {
        // Mock inputs with invalid time string
        Timestamp deliveryPeriod = new Timestamp(System.currentTimeMillis());
        String invalidInputTimeStr = "invalidTimeString";
        String countryCode = "UK";

        FundRule fundRuleMock = Mockito.mock(FundRule.class);
        
        // This should throw an exception due to invalid time format
        FundPlanUtils.getCutoff(fundRuleMock, deliveryPeriod, invalidInputTimeStr, countryCode);
    }

    @Test
    public void testGetCutoff_ZeroDaysCutoff() throws Exception {
        // Test the scenario where fundRule.getCutOffMoreThanOneDay() returns 0
        Timestamp deliveryPeriod = new Timestamp(System.currentTimeMillis());
        String inputTimeStr = "12:00:00";
        String countryCode = "UK";

        FundRule fundRuleMock = Mockito.mock(FundRule.class);
        Mockito.when(fundRuleMock.getCutOffMoreThanOneDay()).thenReturn(0);

        // Call the method
        Timestamp result = FundPlanUtils.getCutoff(fundRuleMock, deliveryPeriod, inputTimeStr, countryCode);

        // Validate the result for this case
        assertNotNull(result);
    }

    @Test(expected = LinkServiceException.class)
    public void testGetCutoff_HoursExceededException() throws Exception {
        // Test for exception when hours exceed limit
        Timestamp deliveryPeriod = new Timestamp(System.currentTimeMillis());
        String inputTimeStr = "24:01:00";  // Invalid hours
        String countryCode = "FR";

        FundRule fundRuleMock = Mockito.mock(FundRule.class);
        
        // This should throw an exception due to exceeding hours
        FundPlanUtils.getCutoff(fundRuleMock, deliveryPeriod, inputTimeStr, countryCode);
    }
}