---
title: store-sendgrid-Java-How-to-Send-email-example
description: "Hur du skickar e-post med SendGrid från Java i en Azure-distribution"
services: 
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
ms.openlocfilehash: d80d7d9c54bad12a4d26d8623eeccdf9bc2a743a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Hur du skickar e-post med SendGrid från Java i Azure-distribution
I följande exempel visas hur du kan använda SendGrid för att skicka e-post från en webbsida som finns på Azure. Exempelprogrammet uppmanas användaren för e-värden som visas i följande skärmbild visar.

![E-formulär][emailform]

Resulterande e-postmeddelandet ser ut ungefär som följande skärmbild visar.

![E-postmeddelande][emailsent]

Du behöver göra följande för att använda koden i det här avsnittet:

1. Hämta javax.mail burkar, till exempel från <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Lägg till burkar din Java byggsökväg.
3. Om du använder Eclipse för att skapa Java-program kan du inkludera SendGrid-bibliotek i din distribution programfilen (WAR) funktionen Eclipses distribution sammansättningen. Om du inte använder Eclipse för att skapa Java-program, se till att biblioteken ingår i samma Azure rollen som Java-programmet och lägga till klassen sökvägen till programmet.

Du måste också ha en egen SendGrid användarnamn och lösenord för att kunna skicka e-postmeddelandet. Kom igång med SendGrid, se [hur du skickar e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

Dessutom förtrogenhet med informationen på [skapar en Hello World program för Azure i Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944), eller med andra tekniker som värd för Java-program i Azure om du inte använder Eclipse rekommenderas starkt.

## <a name="create-a-web-form-for-sending-email"></a>Skapa ett webbformulär för att skicka e-post
Följande kod visar hur du skapar ett webbformulär för att hämta användardata för att skicka e-post. För det här innehållet är JSP-fil med namnet **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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

## <a name="create-the-code-to-send-the-email"></a>Skapa koden för att skicka e-postmeddelandet
Följande kod som anropas när du har slutfört formuläret i emailform.jsp, skapar e-postmeddelande och skickar den. För det här innehållet är JSP-fil med namnet **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
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

Förutom att skicka e-postmeddelandet tillhandahåller emailform.jsp ett resultat för användaren. Följande skärmdump är ett exempel:

![Skicka e-post-resultat][emailresult]

## <a name="next-steps"></a>Nästa steg
Distribuera appen till beräkningsemulatorn och kör emailform.jsp i en webbläsare, ange värden i formuläret, klicka på **skicka e-postmeddelandet**, och sedan visa resultatet i sendemail.jsp.

Den här koden har angetts för att visa dig hur du använder SendGrid i Java i Azure. Innan du distribuerar till Azure i produktion, kanske du vill lägga till flera felhantering eller andra funktioner. Exempel: 

* Du kan använda Azure storage-blobbar eller SQL-databas för att lagra e-postadresser och e-postmeddelanden, istället för att använda ett webbformulär. Information om hur du använder Azure storage-blobbar i Java finns [hur du använder tjänsten Blob Storage från Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Information om hur du använder SQL-databas i Java finns [med hjälp av SQL-databas i Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Du kan använda `RoleEnvironment.getConfigurationSettings` att hämta SendGrid användarnamn och lösenord från din distribution konfigurationsinställningar, i stället för webbformuläret för att hämta värdena. Information om den `RoleEnvironment` klassen, se [med hjälp av Azure Service Runtime Library i JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) och dokumentation för körtid för Azure-paketet på <http://dl.windowsazure.com/javadoc>.
* Mer information om hur du använder SendGrid i Java finns [hur du skickar e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
