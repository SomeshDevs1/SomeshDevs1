import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.eq;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Collections;
import java.util.List;
import java.util.Optional;

@ExtendWith(MockitoExtension.class)
public class ChecklistServiceTest {

    @Mock
    private VSIRepo vsiRepo;

    @Mock
    private CheckListService checkListService;

    @InjectMocks
    private ChecklistService checklistService;

    private static final Logger LOGGER = LoggerFactory.getLogger(ChecklistServiceTest.class);

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGenerateChecklistFollowup_Success() throws LinkServiceException {
        List<Long> vsildList = List.of(1L, 2L);
        ValuationScheduleItem vsiMock = mock(ValuationScheduleItem.class);

        when(vsiRepo.findById(any(Long.class))).thenReturn(Optional.of(vsiMock));
        when(vsiMock.isChecklist()).thenReturn(true);
        when(checklistService.generateCheckList(anyLong())).thenReturn(List.of(1, 2));
        
        checklistService.generateChecklistFollowup(vsildList);

        verify(vsiRepo, times(2)).findById(any(Long.class));
        verify(checklistService, times(2)).generateCheckList(anyLong());
    }

    @Test
    public void testGenerateChecklistFollowup_NoChecklist() throws LinkServiceException {
        List<Long> vsildList = List.of(1L, 2L);
        ValuationScheduleItem vsiMock = mock(ValuationScheduleItem.class);

        when(vsiRepo.findById(any(Long.class))).thenReturn(Optional.of(vsiMock));
        when(vsiMock.isChecklist()).thenReturn(false);

        checklistService.generateChecklistFollowup(vsildList);

        verify(vsiRepo, times(2)).findById(any(Long.class));
        verify(checklistService, never()).generateCheckList(anyLong());
    }

    @Test
    public void testGenerateChecklistFollowup_Exception() {
        List<Long> vsildList = List.of(1L);

        when(vsiRepo.findById(any(Long.class))).thenThrow(new LinkServiceException("Database error"));

        assertThrows(LinkServiceException.class, () -> checklistService.generateChecklistFollowup(vsildList));
    }

    @Test
    public void testGenerateGermanyChecklistFollowup_Success() throws LinkServiceException {
        List<Long> vsildList = List.of(1L, 2L);
        String fundGroupName = "FundGroup";

        doNothing().when(checkListService).generateChecklistForGermany(vsildList, fundGroupName);
        doNothing().when(vsiRepo).updateChecklistAndClosedStatus(vsildList);

        checklistService.generateGermanyChecklistFollowup(vsildList, fundGroupName);

        verify(checkListService, times(1)).generateChecklistForGermany(vsildList, fundGroupName);
        verify(vsiRepo, times(1)).updateChecklistAndClosedStatus(vsildList);
    }

    @Test
    public void testGenerateGermanyChecklistFollowup_EmptyList() throws LinkServiceException {
        List<Long> vsildList = Collections.emptyList();
        String fundGroupName = "FundGroup";

        checklistService.generateGermanyChecklistFollowup(vsildList, fundGroupName);

        verify(checkListService, never()).generateChecklistForGermany(anyList(), anyString());
        verify(vsiRepo, never()).updateChecklistAndClosedStatus(anyList());
    }

    @Test
    public void testGenerateGermanyChecklistFollowup_Exception() {
        List<Long> vsildList = List.of(1L, 2L);
        String fundGroupName = "FundGroup";

        doThrow(new RuntimeException("Service error")).when(checkListService).generateChecklistForGermany(vsildList, fundGroupName);

        assertThrows(LinkServiceException.class, () -> checklistService.generateGermanyChecklistFollowup(vsildList, fundGroupName));
    }
}