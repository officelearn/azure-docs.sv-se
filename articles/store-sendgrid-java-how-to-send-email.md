---
title: Så här använder du e-posttjänsten SendGrid (Java) | Microsoft-dokument
description: Läs om hur du skickar e-post med SendGrid-e-posttjänsten på Azure. Kodexempel skrivna i Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876513"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Så här skickar du e-post med SendGrid från Java
Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Proverna är skrivna i Java. Scenarierna omfattar **att skapa e-post,** **skicka e-post,** **lägga till bifogade filer,** **använda filter**och **uppdatera egenskaper**. Mer information om SendGrid och skicka e-post finns i avsnittet [Nästa steg.](#next-steps)

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrids e-posttjänst?
SendGrid är en [molnbaserad e-posttjänst] som ger tillförlitlig [transaktionsbaserad e-postleverans,]skalbarhet och realtidsanalys tillsammans med flexibla API:er som gör anpassad integrering enkel. Vanliga SendGrid-användningsscenarier är:

* Skicka kvitton till kunder automatiskt
* Administrera distributionslistor för att skicka kunder månatliga e-flygblad och specialerbjudanden
* Samla in mätvärden i realtid för saker som blockerad e-post och kundens svarstider
* Generera rapporter för att identifiera trender
* Vidarebefordra kundfrågor
* E-postmeddelanden från ditt program

Mer information finns i <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Så här: Använd javax.mail-biblioteken
Skaffa javax.mail-biblioteken, till <https://www.oracle.com/technetwork/java/javamail> exempel från och importera dem till din kod. På en hög nivå är processen för att använda biblioteket javax.mail för att skicka e-post med SMTP att göra följande:

1. Ange SMTP-värden, inklusive SMTP-servern, som för SendGrid är smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Utöka klassen *javax.mail.Authenticator* och returnera ditt SendGrid-användarnamn och lösenord i implementeringen av metoden *getPasswordAuthentication.*  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Skapa en autentiserat e-postsession via ett *javax.mail.Session-objekt.*  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Skapa meddelandet och tilldela **värden för från**, **ämne** och innehåll. **From** Detta visas i avsnittet [Så här skapar du ett e-postmeddelande.](#how-to-create-an-email)
4. Skicka meddelandet via ett *javax.mail.Transport-objekt.* Detta visas i avsnittet [Så här skickar du ett e-postmeddelande][#how-till-skicka-ett-e-post].

## <a name="how-to-create-an-email"></a>Så här skapar du ett e-postmeddelande
Följande visar hur du anger värden för ett e-postmeddelande.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Så här skickar du ett e-postmeddelande
Följande visar hur du skickar ett e-postmeddelande.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Så här lägger du till en bifogad fil
Följande kod visar hur du lägger till en bifogad fil.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Så här: Använd filter för att aktivera sidföns, spårning och analys
SendGrid tillhandahåller ytterligare e-postfunktioner med hjälp av *filter*. Det här är inställningar som kan läggas till i ett e-postmeddelande för att aktivera specifika funktioner som att aktivera klickspårning, Google Analytics, prenumerationsspårning och så vidare. En fullständig lista över filter finns i [Filterinställningar][Filter Settings].

* Följande visar hur du infogar ett sidfotsfilter som resulterar i HTML-text som visas längst ned i e-postmeddelandet som skickas.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Ett annat exempel på ett filter är klickspårning. Anta att din e-posttext innehåller en hyperlänk, till exempel följande, och du vill spåra klickfrekvensen:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Om du vill aktivera klickspårningen använder du följande kod:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Så här uppdaterar du e-postegenskaper
Vissa e-postegenskaper kan skrivas över med **ange egenskap** eller läggas till med lägg **till egenskap**.

Om du till exempel vill ange **Svara** på-adresser använder du följande:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Om du vill lägga till en **Cc-mottagare** använder du följande:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Så här: Använda ytterligare SendGrid-tjänster
SendGrid erbjuder webbaserade API:er som du kan använda för att utnyttja ytterligare SendGrid-funktioner från ditt Azure-program. Fullständig information finns i [SendGrid API-dokumentationen][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i SendGrid e-posttjänsten, följ dessa länkar för att lära dig mer.

* Exempel som visas med SendGrid i en Azure-distribution: [Så här skickar du e-post med SendGrid från Java i en Azure-distribution](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK:<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-dokumentation:<https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid specialerbjudande för Azure-kunder:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[molnbaserad e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionell e-postleverans]: https://sendgrid.com/transactional-email
