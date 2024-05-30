- 👋 Hi, I’m @SomeshDevs1
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
SomeshDevs1/SomeshDevs1 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import java.util.Date;

public class AuditListenerTest {

    private AuditListener auditListener;
    private PersistentObject persistentObject;

    @BeforeEach
    public void setUp() {
        auditListener = new AuditListener();
        persistentObject = Mockito.mock(PersistentObject.class);
    }

    @Test
    public void testBeforeCreateOperation() {
        auditListener.beforeCreateOperation(persistentObject);
        
        // Verify that setDateCreated and setDateModified are called
        Mockito.verify(persistentObject).setDateCreated(Mockito.any(Date.class));
        Mockito.verify(persistentObject).setDateModified(Mockito.any(Date.class));
    }

    @Test
    public void testBeforeAnyOperation() {
        auditListener.beforeAnyOperation(persistentObject);
        
        // Verify that setDateModified is called
        Mockito.verify(persistentObject).setDateModified(Mockito.any(Date.class));
    }
}
