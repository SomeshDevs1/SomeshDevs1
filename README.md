StringBuilder msgBody = new StringBuilder();
msgBody.append("<html> <h4> Link Batch Status for Working Date: ").append(formattedDate).append(" </h4> <br>");
msgBody.append("<style> table { border: 1px solid #dddddd; border-collapse: collapse; text-align: left; border: 1px solid #dddddd; border-collapse: collapse;} </style>");
msgBody.append("<table cellpadding='10' style='background-color: #eeeeee;'>");
msgBody.append("<tr><th> Job Name </th><th> Job Execution </th><th> Status </th><th> Start Time </th><th> End Time </th></tr>");

// Booleans to track the conditions
boolean allJobsCompleted = true;
boolean specificJobsCompleted = true;  // To check the 4 specific jobs
boolean otherJobsCompleted = true;     // To check the remaining 11 jobs

// Define the four specific jobs
Set<String> specificJobs = new HashSet<>(Arrays.asList(
    "clearMonitoringFund", 
    "populateChecklistWithCwdTaskStep", 
    "populateChecklistWithTbcTaskStep", 
    "GenerateChecklist"
));

// Loop through all the batch jobs to list them in the table and track their completion status
for (BatchJobDetailsDTO item : batchJob) {
    // Append each job to the table
    msgBody.append("<tr>");
    msgBody.append("<td>").append(item.getJobName()).append("</td>");
    msgBody.append("<td>").append(item.getJobExecutionId()).append("</td>");
    msgBody.append("<td>").append(item.getStatus()).append("</td>");
    msgBody.append("<td>").append(item.getStartTime()).append("</td>");
    msgBody.append("<td>").append(item.getEndTime()).append("</td>");
    msgBody.append("</tr>");

    // Track whether all jobs are completed
    if (!"COMPLETED".equals(item.getStatus())) {
        allJobsCompleted = false;  // If any job is not completed
    }

    // Track the 4 specific jobs
    if (specificJobs.contains(item.getJobName())) {
        if (!"COMPLETED".equals(item.getStatus())) {
            specificJobsCompleted = false;  // If any of the 4 specific jobs are not completed
        }
    } else {
        // Track the other 11 jobs
        if (!"COMPLETED".equals(item.getStatus())) {
            otherJobsCompleted = false;  // If any of the other 11 jobs are not completed
        }
    }
}

msgBody.append("</table>");

// Determine the overall batch status based on the conditions
String overallStatus;
if (allJobsCompleted) {
    overallStatus = "OK";  // All jobs are completed
} else if (otherJobsCompleted && !specificJobsCompleted) {
    overallStatus = "AMBER";  // Other 11 jobs completed, but the 4 specific jobs are not completed
} else {
    overallStatus = "KO";  // Any of the other 11 jobs are not completed
}

// Append the overall status to the message body below the table
msgBody.append("<br><br><strong>Batch Overall Status: ").append(overallStatus).append("</strong>");
msgBody.append("</html>");

// Construct the email subject
String subject = "[LINK] Batch Status for " + formattedDate + " " + overallStatus;

// Send the email with the subject and message body
emailService.sendBatchJobFailedMail(subject, msgBody.toString());

return null;

-------------------------------------------
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
    
    // Booleans to track the conditions
    boolean allJobsCompleted = true;
    boolean specificJobsCompleted = true;  // To check the 4 specific jobs
    boolean otherJobsCompleted = true;     // To check the remaining 11 jobs
    
    // Define the four specific jobs
    Set<String> specificJobs = new HashSet<>(Arrays.asList(
        "clearMonitoringFund", 
        "populateChecklistWithCwdTaskStep", 
        "populateChecklistWithTbcTaskStep", 
        "GenerateChecklist"
    ));
    
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
            allJobsCompleted = false;  // If any job is not completed
        }

        // Check for the 4 specific jobs
        if (specificJobs.contains(item.getJobName())) {
            if (!"COMPLETED".equals(item.getStatus())) {
                specificJobsCompleted = false;  // If any of the 4 specific jobs are not completed
            }
        } else {
            // Check for the other 11 jobs
            if (!"COMPLETED".equals(item.getStatus())) {
                otherJobsCompleted = false;  // If any of the other 11 jobs are not completed
            }
        }
    }
    
    msgBody.append("</table>");
    msgBody.append("</html>");
    
    String subject;
    
    // Determine the final status based on the conditions
    if (allJobsCompleted) {
        subject = "[LINK] Batch Status for " + formattedDate + " OK";  // All jobs are completed
    } else if (otherJobsCompleted && !specificJobsCompleted) {
        subject = "[LINK] Batch Status for " + formattedDate + " AMBER";  // Other 11 jobs completed, but the 4 specific jobs are not completed
    } else {
        subject = "[LINK] Batch Status for " + formattedDate + " KO";  // Any of the other 11 jobs are not completed
    }
    
    emailService.sendBatchJobFailedMail(subject, msgBody.toString());
    
    return null;
}



---------------------


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


----------------

public String generateBatchStatus() throws LinkReportingException, LinkBatchException {
    Date currentDate = DateUtils.getCurrentDate();
    String formattedDate = DateUtils.dateToStringWithMarker(currentDate);
    List<BatchJobDetailsDTO> batchJob = linkBatchServiceImpl.findBatchJobDetails();
    
    StringBuilder msgBody = new StringBuilder();
    msgBody.append("<html> <h4> Link Batch Status for Working Date: ").append(formattedDate).append(" </h4> <br>");
    msgBody.append("<style> table { border: 1px solid #dddddd; border-collapse: collapse; text-align: left; border: 1px solid #dddddd; border-collapse: collapse;} </style>");
    msgBody.append("<table cellpadding='10' style='background-color: #eeeeee;'>");
    msgBody.append("<tr><th> Job Name </th><th> Job Execution </th><th> Status </th><th> Start Time </th><th> End Time </th></tr>");
    
    // Booleans to track the conditions
    boolean allJobsCompleted = true;
    boolean specificJobsCompleted = true;  // To check the 4 specific jobs
    boolean otherJobsCompleted = true;     // To check the remaining 11 jobs
    
    // Define the four specific jobs
    Set<String> specificJobs = new HashSet<>(Arrays.asList(
        "clearMonitoringFund", 
        "populateChecklistWithCwdTaskStep", 
        "populateChecklistWithTbcTaskStep", 
        "GenerateChecklist"
    ));
    
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
            allJobsCompleted = false;  // If any job is not completed
        }

        // Check for the 4 specific jobs
        if (specificJobs.contains(item.getJobName())) {
            if (!"COMPLETED".equals(item.getStatus())) {
                specificJobsCompleted = false;  // If any of the 4 specific jobs are not completed
            }
        } else {
            // Check for the other 11 jobs
            if (!"COMPLETED".equals(item.getStatus())) {
                otherJobsCompleted = false;  // If any of the other 11 jobs are not completed
            }
        }
    }
    
    msgBody.append("</table>");
    
    String overallStatus;
    
    // Determine the final status based on the conditions
    if (allJobsCompleted) {
        overallStatus = "OK";  // All jobs are completed
    } else if (otherJobsCompleted && !specificJobsCompleted) {
        overallStatus = "AMBER";  // Other 11 jobs completed, but the 4 specific jobs are not completed
    } else {
        overallStatus = "KO";  // Any of the other 11 jobs are not completed
    }
    
    // Append the overall status to the message body below the table
    msgBody.append("<br><br><strong>Batch Overall Status: ").append(overallStatus).append("</strong>");
    msgBody.append("</html>");
    
    String subject = "[LINK] Batch Status for " + formattedDate + " " + overallStatus;
    
    emailService.sendBatchJobFailedMail(subject, msgBody.toString());
    
    return null;
}

