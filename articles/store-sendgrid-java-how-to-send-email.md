---
title: Hur du använder e-posttjänsten SendGrid (Java) | Microsoft Docs
description: Lär dig hur skicka e-postmeddelande med e-posttjänsten SendGrid på Azure. Kodexempel som skrivits i Java.
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
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 0cb75c1acb731432ed524560698e3355699b2500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931219"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Hur du skickar e-post med SendGrid från Java
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med e-posttjänsten SendGrid på Azure. Exemplen är skrivna i Java. Scenarier som omfattas är **konstruera e-post**, **skicka e-postmeddelande**, **att lägga till bilagor**, **med hjälp av filter**, och **uppdatera egenskaperna för**. Mer information om SendGrid och skicka e-post finns i den [nästa steg](#next-steps) avsnittet.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten SendGrid?
SendGrid är en [molnbaserade e-posttjänsten] som ger tillförlitliga [transaktionsbaserad e-postleverans], skalbarhet och realtidsanalys tillsammans med flexibla API: er som gör anpassad integration enkel. Vanliga Användningsscenarier för SendGrid är:

* Skicka automatiskt kvitton till kunder
* Administrera distribution visas för att skicka kunder månatliga e-flygblad och specialerbjudanden
* Samla in i realtid mått för saker som blockerade e-post och kundsvarstid
* Generera rapporter för att identifiera trender
* Vidarebefordran av kundfrågor
* E-postmeddelanden från ditt program

Mer information finns i <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Anvisningar: Använda javax.mail-bibliotek
Hämta javax.mail-bibliotek, till exempel från <https://www.oracle.com/technetwork/java/javamail> och importera dem till din kod. Vid en hög nivå är processen för att använda biblioteket javax.mail skicka e-post via SMTP att göra följande:

1. Ange SMTP-värden, inklusive SMTP-servern, vilket för SendGrid är smtp.sendgrid.net.

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

1. Utöka den *javax.mail.Authenticator* klass, och i din implementering av den *getPasswordAuthentication* metoden returnera ditt SendGrid-användarnamn och lösenord.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Skapa en session för autentiserade e-post via en *javax.mail.Session* objekt.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Skapa ditt meddelande och tilldela **till**, **från**, **ämne** och innehåll värden. Detta visas i den [How To: Skapa ett e-postmeddelande](#how-to-create-an-email) avsnittet.
4. Skicka meddelandet via en *javax.mail.Transport* objekt. Detta visas i den [How To: Skicka ett e-postmeddelande] [# anvisningar – skicka e-en-post]-avsnitt.

## <a name="how-to-create-an-email"></a>Anvisningar: Skapa ett e-postmeddelande
Nedan visas hur du anger värden för ett e-postmeddelande.

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

## <a name="how-to-send-an-email"></a>Anvisningar: Skicka ett e-postmeddelande
Nedan visas hur du skickar ett e-postmeddelande.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Anvisningar: Lägg till en bifogad fil
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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Anvisningar: Använd filter för att aktivera sidfötter, spårning och analys
SendGrid ger ytterligare e-funktioner med *filter*. Det här är inställningar som kan läggas till i ett e-postmeddelande för att aktivera vissa funktioner, till exempel aktivera klickspårning, Google analytics, prenumeration, spårnings- och så vidare. En fullständig lista över filter finns i [filterinställningar][Filter Settings].

* Nedan visas hur du infogar ett sidfot filter som resulterar i HTML-text som visas längst ned i e-postmeddelandet som skickas.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Ett annat exempel på ett filter är klickspårning. Anta att dina e-postmeddelandet innehåller en hyperlänk, till exempel följande, och du vill spåra frekvensen klickar du på:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Om du vill aktivera den klickspårning, Använd följande kod:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Anvisningar: Uppdatera postegenskaper för e
Vissa postegenskaper för e-kan skrivas över med hjälp av **egenskapen** eller läggs till med **Lägg till egenskap**.

Till exempel vill ange **ReplyTo** adresser, använder du följande:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Att lägga till en **Cc** mottagaren använder du följande:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Anvisningar: Använda ytterligare SendGrid-tjänster
SendGrid erbjuder webbaserad API: er som du kan använda för att utnyttja ytterligare funktioner för SendGrid från ditt Azure-program. Fullständig information finns i [SendGrid API-dokumentation][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna för den e-posttjänsten SendGrid kan du följa dessa länkar om du vill veta mer.

* Exempel som visar hur du använder SendGrid i en Azure-distribution: [Hur du skickar e-post med SendGrid från Java i en Azure-distribution](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-dokumentation: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid specialerbjudande för Azure-kunder: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[molnbaserade e-posttjänsten]: https://sendgrid.com/email-solutions
[transaktionsbaserad e-postleverans]: https://sendgrid.com/transactional-email
