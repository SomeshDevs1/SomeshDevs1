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
    
    // AtomicBooleans to track the conditions
    AtomicBoolean allJobsCompleted = new AtomicBoolean(true);
    AtomicBoolean specificJobsCompleted = new AtomicBoolean(true);  // To check the 4 specific jobs
    AtomicBoolean otherJobsCompleted = new AtomicBoolean(true);     // To check the remaining 11 jobs
    
    // Define the four specific jobs
    Set<String> specificJobs = new HashSet<>(Arrays.asList(
        "clearMonitoringFund", 
        "populateChecklistWithCwdTaskStep", 
        "populateChecklistWithTbcTaskStep", 
        "GenerateChecklist"
    ));
    
    batchJob.stream().forEach(item -> {
        msgBody.append("<tr>");
        msgBody.append("<td>").append(item.getJobName()).append("</td>");
        msgBody.append("<td>").append(item.getJobExecutionId()).append("</td>");
        msgBody.append("<td>").append(item.getStatus()).append("</td>");
        msgBody.append("<td>").append(item.getStartTime()).append("</td>");
        msgBody.append("<td>").append(item.getEndTime()).append("</td>");
        msgBody.append("</tr>");
        
        // Check if all jobs are completed
        if (!"COMPLETED".equals(item.getStatus())) {
            allJobsCompleted.set(false);  // If any job is not completed
        }

        // Check for the 4 specific jobs
        if (specificJobs.contains(item.getJobName())) {
            if (!"COMPLETED".equals(item.getStatus())) {
                specificJobsCompleted.set(false);  // If any of the 4 specific jobs are not completed
            }
        } else {
            // Check for the other 11 jobs
            if (!"COMPLETED".equals(item.getStatus())) {
                otherJobsCompleted.set(false);  // If any of the other 11 jobs are not completed
            }
        }
    });
    
    msgBody.append("</table>");
    msgBody.append("</html>");
    
    String subject;
    
    // Determine the final status based on the conditions
    if (allJobsCompleted.get()) {
        subject = "[LINK] Batch Status for " + formattedDate + " OK";  // All jobs are completed
    } else if (otherJobsCompleted.get() && !specificJobsCompleted.get()) {
        subject = "[LINK] Batch Status for " + formattedDate + " AMBER";  // Other 11 jobs completed, but the 4 specific jobs are not completed
    } else {
        subject = "[LINK] Batch Status for " + formattedDate + " KO";  // Any of the other 11 jobs are not completed
    }
    
    emailService.sendBatchJobFailedMail(subject, msgBody.toString());
    
    return null;
}