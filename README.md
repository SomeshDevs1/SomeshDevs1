import java.util.List;
import java.util.stream.Collectors;
import javax.mail.*;
import javax.mail.internet.*;
import java.util.Properties;

public class YourServiceImpl {

    private LinkBatchServiceImpl linkBatchService;  // Assuming this is how you access the other service

    // Method to process the list and send email based on status
    public void checkStatusAndSendEmail() {
        // Fetch the list from linkBatchService
        List<YourObject> list = linkBatchService.getYourList(); // Assuming this method fetches your list

        // Check if all statuses are "completed"
        boolean allCompleted = list.stream()
            .allMatch(item -> "completed".equalsIgnoreCase(item.getStatus())); // Modify according to how you get the status

        // Set email content
        String subject = allCompleted ? "Status OK" : "Status KO";
        String htmlMessage = generateHtmlMessage(allCompleted);

        // Send email
        sendEmail("recipient@example.com", subject, htmlMessage);
    }

    // Method to generate HTML message
    private String generateHtmlMessage(boolean allCompleted) {
        if (allCompleted) {
            return "<html><body><h1>All tasks are completed successfully!</h1></body></html>";
        } else {
            return "<html><body><h1>Some tasks failed. Please check!</h1></body></html>";
        }
    }

    // Method to send email
    private void sendEmail(String to, String subject, String htmlMessage) {
        // Assuming you have mail properties set up
        String from = "sender@example.com";
        String host = "smtp.example.com"; // Replace with your SMTP server

        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", host);

        Session session = Session.getDefaultInstance(properties);

        try {
            MimeMessage message = new MimeMessage(session);
            message.setFrom(new InternetAddress(from));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
            message.setSubject(subject);
            message.setContent(htmlMessage, "text/html");

            Transport.send(message);
            System.out.println("Email Sent Successfully");
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}

// Sample class representing your object in the list
class YourObject {
    private String status;

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }
}
