import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.cache.CacheManager;

import java.util.Optional;

class MonitoringCacheDaoTest {

    @Mock
    private MonitoringFundRepository monitoringFundRepository;

    @InjectMocks
    private MonitoringCacheDao monitoringCacheDao;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    void testGetDataFromMonitoringDetailsCache_Success() throws Exception {
        Long monitoringID = 1L;
        MonitoringFund monitoringFund = new MonitoringFund();
        monitoringFund.setMonitoringId(monitoringID);
        
        // Mock the repository response
        when(monitoringFundRepository.findById(monitoringID)).thenReturn(Optional.of(monitoringFund));
        
        // Test the method
        MonitoringFundDto result = monitoringCacheDao.getDataFromMonitoringDetailsCache(monitoringID);
        
        // Validate the result
        assertNotNull(result);
        verify(monitoringFundRepository, times(1)).findById(monitoringID);
    }

    @Test
    void testGetDataFromMonitoringDetailsCache_Exception() {
        Long monitoringID = 1L;
        
        // Simulate exception in repository call
        when(monitoringFundRepository.findById(monitoringID)).thenThrow(new RuntimeException("Database error"));
        
        // Test and assert the exception
        assertThrows(LinkServiceException.class, () -> {
            monitoringCacheDao.getDataFromMonitoringDetailsCache(monitoringID);
        });
        
        verify(monitoringFundRepository, times(1)).findById(monitoringID);
    }

    @Test
    void testUpdateCacheMonitoringDetails_Success() throws Exception {
        Long monitoringID = 1L;
        MonitoringFund monitoringFund = new MonitoringFund();
        monitoringFund.setMonitoringId(monitoringID);

        // Mock the repository response
        when(monitoringFundRepository.findById(monitoringID)).thenReturn(Optional.of(monitoringFund));

        // Test the method
        MonitoringFundDto result = monitoringCacheDao.updateCacheMonitoringDetails(monitoringID);

        // Validate the result
        assertNotNull(result);
        verify(monitoringFundRepository, times(1)).findById(monitoringID);
    }

    @Test
    void testUpdateCacheMonitoringDetails_Exception() {
        Long monitoringID = 1L;

        // Simulate exception in repository call
        when(monitoringFundRepository.findById(monitoringID)).thenThrow(new RuntimeException("Database error"));

        // Test and assert the exception
        assertThrows(LinkServiceException.class, () -> {
            monitoringCacheDao.updateCacheMonitoringDetails(monitoringID);
        });

        verify(monitoringFundRepository, times(1)).findById(monitoringID);
    }

    @Test
    void testDeleteMonitoringCacheValue_Success() throws Exception {
        Long monitoringID = 1L;

        // Test the method
        ResponseDto result = monitoringCacheDao.deleteMonitoringCacheValue(monitoringID);

        // Validate the result
        assertNotNull(result);
        assertEquals("Data has Deleted Successfully in MonitoringCache", result.getMessage());
    }

    @Test
    void testDeleteMonitoringCacheValue_Exception() {
        Long monitoringID = 1L;

        // Simulate exception during cache deletion
        doThrow(new RuntimeException("Cache error")).when(monitoringFundRepository).deleteById(monitoringID);

        // Test and assert the exception
        assertThrows(LinkServiceException.class, () -> {
            monitoringCacheDao.deleteMonitoringCacheValue(monitoringID);
        });
    }
}