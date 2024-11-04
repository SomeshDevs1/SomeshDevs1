import java.util.Calendar;
import java.util.Date;

@Autowired
private BatchReportStatusRepository batchReportStatusRepository;

public List<BatchReportStatusDto> findKpiReportsForToday() throws Exception {
    String reportName = "KpiReport";
    
    Calendar calendar = Calendar.getInstance();
    calendar.set(Calendar.HOUR_OF_DAY, 0);
    calendar.set(Calendar.MINUTE, 0);
    calendar.set(Calendar.SECOND, 0);
    calendar.set(Calendar.MILLISECOND, 0);
    Date startOfDay = calendar.getTime();

    Date endOfDay = new Date(); // current time

    List<BatchReportStatus> reportStatusList = batchReportStatusRepository.getRecords(startOfDay, endOfDay, reportName);
    
    List<BatchReportStatusDto> reportStatusDtoList = new ArrayList<>();
    for (BatchReportStatus reportStatus : reportStatusList) {
        BatchReportStatusDto dto = new BatchReportStatusDto();
        dto.setReportId(reportStatus.getReportId());
        dto.setReportName(reportStatus.getReportName());
        dto.setStatus(reportStatus.getStatus());
        dto.setTimeCreated(reportStatus.getTimeCreated());
        dto.setTimeCompleted(reportStatus.getTimeCompleted());
        reportStatusDtoList.add(dto);
    }
    
    return reportStatusDtoList;
}