@Override
public String generateBatchStatus() throws LinkReportingException, LinkBatchException {
    Date currentDate = DateUtils.getCurrentDate();
    String formattedDate = DateUtils.dateToStringWithMarker(currentDate);
    List<BatchJobDetailsDTO> batchJob = linkBatchServiceImpl.findBatchJobDetails();
    
    StringBuilder msgBody = new StringBuilder();
    msgBody.append("<html> <h4> Link Batch Status for Working Date: ").append(formattedDate).append(" </h4> <br>");
    msgBody.append("<style> table { border: 1px solid #dddddd; border-collapse: collapse; text-align: left; border: 1px solid #dddddd; border-collapse: collapse;} </style>");
    msgBody.append("<table cellpadding='10' style='background-color: #eeeeee;'>");
    msgBody.append(LinkConstant.TABLE_DATA_NO_WRAP.append("<th> Job Name </th><th> Job Execution </th><th> Status </th><th> Start Time </th><th> End Time </th>"));
    msgBody.append("</tr>");
    
    boolean allCompleted = true;  // This will track if all jobs are completed
    
    batchJob.stream().forEach(item -> {
        msgBody.append("<tr>");
        msgBody.append("<td>").append(item.getJobName()).append("</td>");
        msgBody.append("<td>").append(item.getJobExecutionId()).append("</td>");
        msgBody.append("<td>").append(item.getStatus()).append("</td>");
        msgBody.append("<td>").append(item.getStartTime()).append("</td>");
        msgBody.append("<td>").append(item.getEndTime()).append("</td>");
        msgBody.append("</tr>");
        
        if (!"COMPLETED".equals(item.getStatus())) {
            allCompleted = false;  // If any job is not completed, set this flag to false
        }
    });
    
    msgBody.append("</table>");
    msgBody.append("</html>");
    
    String subject = allCompleted ? "[LINK] Batch Status for " + formattedDate + " OK" : "[LINK] Batch Status for " + formattedDate + " KO";
    
    emailService.sendBatchJobFailedMail(subject, msgBody.toString());
    
    return null;
}