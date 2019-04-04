---
title: store-sendgrid-java-how-to-send-email-example
description: Hur du skickar e-post med SendGrid från Java i en Azure-distribution
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: bc543218678b63d9c101f6cc3a1b65dd5c23a881
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895181"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Hur du skickar e-post med SendGrid från Java i en Azure-distribution
I följande exempel visas hur du kan använda SendGrid för att skicka e-post från en webbsida i Azure. Exempelprogrammet uppmanas användaren för e-värden, enligt följande skärmbild.

![E-formulär][emailform]

Resulterande e-postmeddelandet ser ut ungefär som följande skärmbild.

![E-postmeddelande][emailsent]

Du behöver göra följande för att använda koden i det här avsnittet:

1. Hämta de javax.mail JAR-filer, till exempel från <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. Lägg till för att din Java build path.
3. Om du använder Eclipse för att skapa den här Java-program, kan du inkludera SendGrid-bibliotek i din distribution programfilen (WAR) med hjälp av Eclipses distributionsfunktion sammansättningen. Om du inte använder Eclipse för att skapa den här Java-program, kontrollera biblioteken är inkluderade i samma Azure-roll som ditt Java-program och har lagts till i klassökvägen i ditt program.

Du måste också ha en egen SendGrid-användarnamn och lösenord, för att kunna skicka e-postmeddelandet. Kom igång med SendGrid, se [hur du skickar e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

Dessutom är bekant med informationen på [skapar ett Hello World-program för Azure i Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app), eller med andra tekniker som värd för Java-program i Azure om du inte använder Eclipse rekommenderas starkt.

## <a name="create-a-web-form-for-sending-email"></a>Skapa ett webbformulär för att skicka e-post
Följande kod visar hur du skapar ett webbformulär om du vill hämta användardata för att skicka e-post. För av det här innehållet, JSP-fil med namnet **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Skapa kod för att skicka e-postmeddelandet
Följande kod, som anropas när du har slutfört formuläret i emailform.jsp, skapar e-postmeddelande och skickar den. För av det här innehållet, JSP-fil med namnet **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Förutom att skicka e-postmeddelandet ger emailform.jsp ett resultat för användaren. ett exempel är följande skärmbild:

![Skicka e-post-resultat][emailresult]

## <a name="next-steps"></a>Nästa steg
Distribuera ditt program till compute-emulatorn och kör emailform.jsp i en webbläsare, ange värden i formuläret, klicka på **skicka e-postmeddelandet**, och sedan visar resultatet i sendemail.jsp.

Den här koden har angetts för att visa dig hur du använder SendGrid i Java på Azure. Innan du distribuerar till Azure i produktion ska du lägga till fler felhantering eller andra funktioner. Exempel: 

* Du kan använda Azure storage-blobbar eller SQL-databas för att lagra e-postadresser och e-postmeddelanden, istället för att använda ett webbformulär. Information om hur du använder Azure storage-blobbar i Java finns i [hur du använder Blob Storage-tjänsten från Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Information om hur du använder SQL Database i Java finns i [med hjälp av SQL Database i Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Du kan använda `RoleEnvironment.getConfigurationSettings` att hämta SendGrid-användarnamn och lösenord från din distribution konfigurationsinställningar, istället för att använda webbformuläret för att hämta dessa värden. Information om den `RoleEnvironment` klass, se [med hjälp av Azure Service Runtime-biblioteket i JSP](/previous-versions/azure/hh690948(v=azure.100)) och dokumentation för Azure Service Runtime-paketet på <http://dl.windowsazure.com/javadoc>.
* Mer information om hur du använder SendGrid i Java finns i [hur du skickar e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
