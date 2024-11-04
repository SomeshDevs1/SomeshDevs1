public String generateBatchStatusEmail(List<BatchJobDetailsDTO> batchJob, List<BatchReportStatus> kpiReports, String formattedDate) {
    StringBuilder msgBody = new StringBuilder();
    msgBody.append("<html> <h4> Link Batch Status for Working Date: ").append(formattedDate).append(" </h4> <br>");
    msgBody.append("<style> table { border: 1px solid #dddddd; border-collapse: collapse; text-align: left; } </style>");

    // Primary Batch Job Table
    msgBody.append("<table cellpadding='10' style='background-color: #eeeeee;'>");
    msgBody.append("<tr><th>Job Name</th><th>Job Execution</th><th>Status</th><th>Start Time</th><th>End Time</th></tr>");

    // Booleans to track the job status conditions
    boolean allJobsCompleted = true;
    boolean specificJobsCompleted = true; // Specific 4 jobs + 4 KPI reports
    boolean otherJobsCompleted = true;    // Remaining jobs

    // Define the specific jobs to track, including the KPI report filenames
    Set<String> specificJobs = new HashSet<>(Arrays.asList(
        "clearMonitoringFund", "populateChecklistWithCwdTaskStep", 
        "populateChecklistWithTbcTaskStep", "GenerateChecklist",
        "nav", "lux", "india", "italy" // Added KPI report filenames
    ));

    // Process batch jobs and populate the table
    for (BatchJobDetailsDTO item : batchJob) {
        msgBody.append("<tr>");
        msgBody.append("<td>").append(item.getJobName()).append("</td>");
        msgBody.append("<td>").append(item.getJobExecutionId()).append("</td>");
        msgBody.append("<td>").append(item.getStatus()).append("</td>");
        msgBody.append("<td>").append(item.getStartTime()).append("</td>");
        msgBody.append("<td>").append(item.getEndTime()).append("</td>");
        msgBody.append("</tr>");

        // Check if all jobs are completed
        if (!"COMPLETED".equals(item.getStatus())) {
            allJobsCompleted = false;
        }

        // Check specific and other jobs
        if (specificJobs.contains(item.getJobName())) {
            if (!"COMPLETED".equals(item.getStatus())) {
                specificJobsCompleted = false;
            }
        } else {
            if (!"COMPLETED".equals(item.getStatus())) {
                otherJobsCompleted = false;
            }
        }
    }

    msgBody.append("</table><br>");

    // KPI Reports Table
    msgBody.append("<h4>KPI Report Status</h4>");
    msgBody.append("<table cellpadding='10' style='background-color: #eeeeee;'>");
    msgBody.append("<tr><th>File Name</th><th>Status</th><th>Time Created</th><th>Time Completed</th></tr>");

    // Process KPI reports and populate the KPI table
    for (BatchReportStatus report : kpiReports) {
        msgBody.append("<tr>");
        msgBody.append("<td>").append(report.getFileName()).append("</td>");
        msgBody.append("<td>").append(report.getStatus()).append("</td>");
        msgBody.append("<td>").append(report.getTimeCreated()).append("</td>");
        msgBody.append("<td>").append(report.getTimeCompleted()).append("</td>");
        msgBody.append("</tr>");

        // Check if each KPI report (considered a specific job) is completed
        if (specificJobs.contains(report.getFileName()) && !"COMPLETED".equals(report.getStatus())) {
            specificJobsCompleted = false;
        }
    }

    msgBody.append("</table>");
    msgBody.append("</html>");

    // Determine the final email subject based on the completion conditions
    String subject;
    if (allJobsCompleted) {
        subject = "[LINK] Batch Status for " + formattedDate + " OK";
    } else if (otherJobsCompleted && specificJobsCompleted) {
        subject = "[LINK] Batch Status for " + formattedDate + " AMBER";
    } else {
        subject = "[LINK] Batch Status for " + formattedDate + " KO";
    }

    emailService.sendBatchJobFailedMail(subject, msgBody.toString());
    return null;
}