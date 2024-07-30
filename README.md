package com.sgss.ast.fvs.vap.link.fund.transformer;

import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import org.apache.commons.lang3.StringUtils;
import org.junit.Test;
import org.mockito.Mockito;

import com.sgss.ast.fvs.vap.link.fund.constants.LinkAPIConstants;
import com.sgss.ast.fvs.vap.link.fund.constants.LinkFundConstants.Area;
import com.sgss.ast.fvs.vap.link.fund.dto.LinkFollowUpTasksDTO;
import com.sgss.ast.fvs.vap.link.fund.entities.Followup;
import com.sgss.ast.fvs.vap.link.fund.util.DateUtils;

public class FollowupTransformerTest {

    @Test
    public void testTransform() {
        Followup followup = new Followup();
        followup.setTaskId("taskId");
        followup.setClient("client");
        followup.setFund("fund");
        followup.setIdFund("idFund");
        followup.setFundGroup("fundGroup");
        followup.setNavDate(new Date());
        followup.setNavDateUi("navDateUi");
        followup.setCutoff("cutoff");
        followup.setPocket("pocket");
        followup.setCreatedDate(new Date());
        followup.setWorkingDate(new Date());
        followup.setWorkingDateStr("workingDateStr");
        followup.setTaskOwner("taskOwner");
        followup.setTask("task");
        followup.setStatus(LinkAPIConstants.GO_STATUS);
        followup.setUserName("userName");
        followup.setTaskComment("taskComment");
        followup.setCommentMandatory(true);
        followup.setConditionalTask(true);
        followup.setOffshoreSignoffTask(true);
        followup.setWfsFinalSignOffTask(true);
        followup.setTaskOrder(1);
        followup.setUserType("userType");
        followup.setCountryCode("countryCode");
        followup.setRedoType("redoType");
        followup.setCutoffMoreThanOneDay(true);
        followup.setIdArea("idArea");
        followup.setPostponed(true);
        followup.setCommentMandatoryByTaskConfig(true);
        followup.setVsild("vsild");

        LinkFollowUpTasksDTO dto = FollowupTransformer.transform(followup);

        assertNotNull(dto);
        assertEquals("taskId", dto.getTaskId());
        assertEquals("client", dto.getClient());
        assertEquals("fund", dto.getFund());
        assertEquals("idFund", dto.getIdFund());
        assertEquals("fundGroup", dto.getFundGroup());
        assertEquals(followup.getNavDate(), dto.getNavDate());
        assertEquals("navDateUi", dto.getNavDateUi());
        assertEquals("cutoff", dto.getCutoff());
        assertEquals("pocket", dto.getPocket());
        assertEquals(followup.getCreatedDate(), dto.getCreatedDate());
        assertEquals(followup.getWorkingDate(), dto.getWorkingDate());
        assertEquals("workingDateStr", dto.getWorkingDateStr());
        assertEquals("taskOwner", dto.getTaskOwner());
        assertEquals("task", dto.getTask());
        assertEquals(LinkAPIConstants.GO_STATUS, dto.getStatus());
        assertEquals("userName", dto.getUserName());
        assertEquals("taskComment", dto.getTaskComment());
        assertTrue(dto.isCommentMandatory());
        assertTrue(dto.isConditionalTask());
        assertTrue(dto.isOffshoreSignoffTask());
        assertTrue(dto.isWfsFinalSignOffTask());
        assertEquals(1, dto.getTaskOrder());
        assertEquals("userType", dto.getUserType());
        assertEquals("countryCode", dto.getCountryCode());
        assertEquals("redoType", dto.getRedoType());
        assertTrue(dto.isCutoffMoreThanOneDay());
        assertEquals("idArea", dto.getIdArea());
        assertTrue(dto.isPostponed());
        assertTrue(dto.isCommentMandatoryByTaskConfig());
        assertEquals("vsild", dto.getVsiId());
    }

    @Test
    public void testTransformNullFollowup() {
        LinkFollowUpTasksDTO dto = FollowupTransformer.transform(null);
        assertNull(dto);
    }

    @Test
    public void testTransformWithStatusConditions() {
        Followup followup = new Followup();
        followup.setStatus(LinkAPIConstants.OK_STATUS);
        followup.setCreatedDate(new Date());
        followup.setWorkingDate(new Date());
        followup.setCutoff("cutoff");
        followup.setCountryCode("countryCode");
        followup.setIdArea("idArea");

        DateUtils dateUtils = Mockito.mock(DateUtils.class);
        when(dateUtils.isBeforeCutoff(any(Date.class), any(Date.class), anyString(), anyString(), anyBoolean(), anyString())).thenReturn(true);

        LinkFollowUpTasksDTO dto = FollowupTransformer.transform(followup);

        assertNotNull(dto);
        assertTrue(dto.isCrossedCutoff());

        followup.setStatus(LinkAPIConstants.NA_STATUS);
        dto = FollowupTransformer.transform(followup);
        assertTrue(dto.isCrossedCutoff());

        followup.setStatus(LinkAPIConstants.GO_STATUS);
        dto = FollowupTransformer.transform(followup);
        assertFalse(dto.isCrossedCutoff());
    }

    @Test
    public void testTransformList() {
        List<Followup> followups = new ArrayList<>();
        Followup followup1 = new Followup();
        followup1.setCountryCode(LinkAPIConstants.LUX);
        followup1.setWfsFinalSignOffTask(true);

        Followup followup2 = new Followup();
        followup2.setCountryCode("other");
        followup2.setWfsFinalSignOffTask(false);

        followups.add(followup1);
        followups.add(followup2);

        List<LinkFollowUpTasksDTO> dtos = FollowupTransformer.transform(followups);

        assertNotNull(dtos);
        assertEquals(1, dtos.size());
    }

    @Test
    public void testTransformToEntity() {
        LinkFollowUpTasksDTO dto = new LinkFollowUpTasksDTO();
        dto.setTaskId("taskId");
        dto.setClient("client");
        dto.setFund("fund");
        dto.setIdFund("idFund");
        dto.setFundGroup("fundGroup");
        dto.setNavDate(new Date());
        dto.setNavDateUi("navDateUi");
        dto.setCutoff("cutoff");
        dto.setPocket("pocket");
        dto.setCreatedDate(new Date());
        dto.setWorkingDate(new Date());
        dto.setWorkingDateStr("workingDateStr");
        dto.setTaskOwner("taskOwner");
        dto.setTask("task");
        dto.setStatus(LinkAPIConstants.GO_STATUS);
        dto.setUserName("userName");
        dto.setTaskComment("taskComment");
        dto.setCommentMandatory(true);
        dto.setConditionalTask(true);
        dto.setOffshoreSignoffTask(true);
        dto.setWfsFinalSignOffTask(true);
        dto.setTaskOrder(1);
        dto.setUserType("userType");
        dto.setCountryCode("countryCode");
        dto.setRedoType("redoType");
        dto.setCutoffMoreThanOneDay(true);
        dto.setIdArea("idArea");
        dto.setIsPostponed(true);
        dto.setCommentMandatoryByTaskConfig(true);
        dto.setVsiId("vsild");

        Followup followup = FollowupTransformer.transformToEntity(dto);

        assertNotNull(followup);
        assertEquals("taskId", followup.getTaskId());
        assertEquals("client", followup.getClient());
        assertEquals("fund", followup.getFund());
        assertEquals("idFund", followup.getIdFund());
        assertEquals("fundGroup", followup.getFundGroup());
        assertEquals(dto.getNavDate(), followup.getNavDate());
        assertEquals("navDateUi", followup.getNavDateUi());
        assertEquals("cutoff", followup.getCutoff());
        assertEquals("pocket", followup.getPocket());
        assertEquals(dto.getCreatedDate(), followup.getCreatedDate());
        assertEquals(dto.getWorkingDate(), followup.getWorkingDate());
        assertEquals("workingDateStr", followup.getWorkingDateStr());
        assertEquals("taskOwner", followup.getTaskOwner());
        assertEquals("task", followup.getTask());
        assertEquals(LinkAPIConstants.GO_STATUS, followup.getStatus());
        assertEquals("userName", followup.getUserName());
        assertEquals("taskComment", followup.getTaskComment());
        assertTrue(followup.getCommentMandatory());
        assertTrue(followup.getConditionalTask());
        assertTrue(followup.getOffshoreSignoffTask());
        assertTrue(followup.getWfsFinalSignOffTask());
    assertEquals(1, followup.getTaskOrder());
    assertEquals("userType", followup.getUserType());
    assertEquals("countryCode", followup.getCountryCode());
    assertEquals("redoType", followup.getRedoType());
    assertTrue(followup.getCutoffMoreThanOneDay());
    assertEquals("idArea", followup.getIdArea());
    assertTrue(followup.getPostponed());
    assertTrue(followup.getCommentMandatoryByTaskConfig());
    assertEquals("vsild", followup.getVsild());
}

@Test
public void testTransformEntities() {
    List<LinkFollowUpTasksDTO> dtos = new ArrayList<>();
    LinkFollowUpTasksDTO dto1 = new LinkFollowUpTasksDTO();
    dto1.setTaskId("taskId1");
    LinkFollowUpTasksDTO dto2 = new LinkFollowUpTasksDTO();
    dto2.setTaskId("taskId2");

    dtos.add(dto1);
    dtos.add(dto2);

    List<Followup> followups = FollowupTransformer.transformEntities(dtos);

    assertNotNull(followups);
    assertEquals(2, followups.size());
    assertEquals("taskId1", followups.get(0).getTaskId());
    assertEquals("taskId2", followups.get(1).getTaskId());
}