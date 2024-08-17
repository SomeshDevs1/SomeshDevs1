import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.test.annotation.Rollback;

import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.List;

@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class AssocFundFundGroupRepositoryTest {

    @Autowired
    private AssocFundFundGroupRepository assocFundFundGroupRepository;

    @Test
    @Rollback(false)
    public void testFindByFundGroup() {
        // Assume you have a fund group object already created in your database
        FundGroup fundGroup = new FundGroup();  // Replace with actual FundGroup setup
        fundGroup.setId(1L);  // Set an ID that exists in your test database

        List<AssocFundFundGroup> result = assocFundFundGroupRepository.findByFundGroup(fundGroup);
        
        assertNotNull(result);  // Check that the result is not null
        assertEquals(1, result.size());  // Check if the expected number of results is returned
    }
}
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.when;

import java.util.ArrayList;
import java.util.List;

@SpringBootTest
public class AssocFundFundGroupRepositoryTest {

    @Mock
    private AssocFundFundGroupRepository assocFundFundGroupRepository;

    @InjectMocks
    private YourServiceClass service;  // Replace with your actual service class that uses the repository

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testFindByFundGroup() {
        // Mock the FundGroup and AssocFundFundGroup objects
        FundGroup fundGroup = new FundGroup();
        fundGroup.setId(1L);  // Set the ID or other properties as needed

        AssocFundFundGroup assocFundFundGroup = new AssocFundFundGroup();
        assocFundFundGroup.setFundGroup(fundGroup);

        List<AssocFundFundGroup> expectedList = new ArrayList<>();
        expectedList.add(assocFundFundGroup);

        // Mock the behavior of the repository
        when(assocFundFundGroupRepository.findByFundGroup(fundGroup)).thenReturn(expectedList);

        // Call the service or repository method
        List<AssocFundFundGroup> actualList = assocFundFundGroupRepository.findByFundGroup(fundGroup);

        // Verify the result
        assertEquals(1, actualList.size());
        assertEquals(fundGroup, actualList.get(0).getFundGroup());
    }
}
