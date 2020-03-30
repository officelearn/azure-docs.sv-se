---
title: store-sendgrid-java-how-to-send-email-exempel
description: Så här skickar du e-post med SendGrid från Java i en Azure-distribution
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
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 35307848c09391ae4468afc00adafd8171aaaa7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876486"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Så här skickar du e-post med SendGrid från Java i en Azure-distribution
I följande exempel visas hur du kan använda SendGrid för att skicka e-postmeddelanden från en webbsida som finns i Azure. Det resulterande programmet kommer att uppmana användaren om e-postvärden, som visas i följande skärmdump.

![Formulär för e-post][emailform]

Den resulterande e-post kommer att se ut ungefär som följande skärmdump.

![E-postmeddelande][emailsent]

Du måste göra följande för att kunna använda koden i det här avsnittet:

1. Skaffa javax.mail JARs, till <https://www.oracle.com/technetwork/java/javamail/index.html>exempel från .
2. Lägg till JARs till din Java bygga sökväg.
3. Om du använder Eclipse för att skapa det här Java-programmet kan du inkludera SendGrid-biblioteken i programdistributionsfilen (WAR) med Eclipses distributionsmonteringsfunktion. Om du inte använder Eclipse för att skapa det här Java-programmet kontrollerar du att biblioteken ingår i samma Azure-roll som ditt Java-program och läggs till i klasssökvägen till ditt program.

Du måste också ha ditt eget SendGrid användarnamn och lösenord, för att kunna skicka e-post. Kom igång med SendGrid och läser [Så här skickar du e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

Dessutom rekommenderas starkt information om hur du [skapar ett Hello World-program för Azure i Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)eller med andra tekniker för att vara värd för Java-program i Azure om du inte använder Eclipse.

## <a name="create-a-web-form-for-sending-email"></a>Skapa ett webbformulär för att skicka e-post
Följande kod visar hur du skapar ett webbformulär för att hämta användardata för att skicka e-post. I detta innehåll heter JSP-filen **emailform.jsp**.

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

## <a name="create-the-code-to-send-the-email"></a>Skapa koden för att skicka e-postmeddelandet
Följande kod, som anropas när du fyller i formuläret i emailform.jsp, skapar e-postmeddelandet och skickar det. I detta innehåll heter JSP-filen **sendemail.jsp**.

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

Förutom att skicka e-postmeddelandet ger emailform.jsp ett resultat för användaren. ett exempel är följande skärmdump:

![Skicka e-postresultat][emailresult]

## <a name="next-steps"></a>Nästa steg
Distribuera ditt program till beräkningemulatorn och inom en webbläsare kör emailform.jsp, ange värden i formuläret, klicka på **Skicka det här e-postmeddelandet**och se sedan resultat i sendemail.jsp.

Den här koden har angetts för att visa hur du använder SendGrid i Java på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till fler felhantering eller andra funktioner. Ett exempel: 

* Du kan använda Azure storage blobbar eller SQL Database för att lagra e-postadresser och e-postmeddelanden, i stället för att använda ett webbformulär. Information om hur du använder Azure storage blobbar i Java finns i [Så här använder du Blob Storage Service från Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Information om hur du använder SQL Database i Java finns [i Använda SQL Database i Java](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java).
* Mer information om hur du använder SendGrid i Java finns i [Så här skickar du e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
