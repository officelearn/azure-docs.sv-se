---
title: Hur du använder SendGrid e-posttjänst (Java) | Microsoft Docs
description: Lär dig hur skicka e-post med SendGrid-e-posttjänsten på Azure. Kodexempel som skrivits i Java.
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
ms.openlocfilehash: c9f7d4841846e41eacb66125bcdeeb29466bb0b8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Hur du skickar e-post med SendGrid från Java
Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Exemplen är skrivna i Java. Scenarier som tas upp inkluderar **konstruera e-post**, **skicka e-post**, **lägga till bilagor**, **med hjälp av filter**, och **uppdatera egenskaperna för**. Mer information om SendGrid och skicka e-post finns i [nästa steg](#next-steps) avsnitt.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrid e-posttjänst?
SendGrid är en [molnbaserade e-posttjänst] som ger tillförlitliga [transaktionella e-postleverans], skalbarhet och analys i realtid tillsammans med flexibel API: er som gör det enkelt anpassad integrering. Vanliga Användningsscenarier för SendGrid är:

* Skicka automatiskt kvitton till kunder
* Administrera distribution visas för att skicka kunder månatliga e-reklamblad och specialerbjudanden
* Samla in realtid mätvärden för sådant som blockerade e-post och kunden svarstider
* Generera rapporter för att identifiera trender
* Vidarebefordran av kundfrågor
* E-postaviseringar från ditt program

Mer information finns i <http://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Så här: använda javax.mail-bibliotek
Hämta javax.mail bibliotek, till exempel från <http://www.oracle.com/technetwork/java/javamail> och importera dem till din kod. Vid en hög nivå är processen för att använda biblioteket javax.mail för att skicka e-post via SMTP att göra följande:

1. Ange SMTP-värden, inklusive SMTP-servern som för SendGrid smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.activation.*;
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

1. Utöka den *javax.mail.Authenticator* klass, och i din implementering av den *getPasswordAuthentication* -metoden returnerar din SendGrid användarnamn och lösenord.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Skapa en session autentiserade e-post via en *javax.mail.Session* objekt.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Skapa ditt meddelande och tilldela **till**, **från**, **ämne** och innehåll värden. Detta framgår av [hur man: skapa ett e-postmeddelande](#how-to-create-an-email) avsnitt.
4. Skicka meddelandet via en *javax.mail.Transport* objekt. Detta framgår av [hur man: skicka ett e-postmeddelande] [#how-till-send-en-e]-avsnitt.

## <a name="how-to-create-an-email"></a>Så här: skapa ett e-postmeddelande
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

## <a name="how-to-send-an-email"></a>Så här: skicka ett e-postmeddelande
Nedan visas hur du skickar ett e-postmeddelande.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Så här: Lägg till en bifogad fil
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

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Så här: använda filter för att aktivera sidfötter, spårning och analys
SendGrid ger ytterligare e-postfunktioner med *filter*. Dessa finns inställningar som du kan lägga till ett e-postmeddelande för att aktivera vissa funktioner, till exempel aktivera Klicka spårning, Google analytics, prenumeration spårning och så vidare. En fullständig lista över filter finns [filterinställningar][Filter Settings].

* Nedan visas hur du infogar en sidfot filter som resulterar i HTML-text som visas längst ned i e-postmeddelandet som skickas.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Ett annat exempel på ett filter är klickar du på spårning. Anta att e-postmeddelandet innehåller en hyperlänk, till exempel följande, och du vill spåra Klicka hastighet:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Aktivera klicka spårning genom att använda följande kod:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Så här: uppdatera e-egenskaper
Vissa egenskaper för e-post kan skrivas över med **ange * egenskapen*** eller läggas till med hjälp av **lägga till*egenskapen ***.

Till exempel för att ange **ReplyTo** adresser, använder du följande:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Att lägga till en **kopia** mottagaren, använder du följande:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Så här: använda ytterligare SendGrid-tjänster
SendGrid erbjuder webbaserade API: er som du kan använda för att utnyttja ytterligare funktioner för SendGrid från Azure-program. Fullständig information finns i [SendGrid API-dokumentationen][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om tjänsten SendGrid e-post, kan du följa dessa länkar om du vill veta mer.

* Exempel som visar med SendGrid i Azure-distribution: [hur du skickar e-post med SendGrid från Java i Azure-distribution](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-dokumentationen: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid specialerbjudande för Azure-kunder: <https://sendgrid.com/windowsazure.html>

[http://sendgrid.com]: https://sendgrid.com
[http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[http://sendgrid.com/features]: https://sendgrid.com/features
[http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[molnbaserade e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionella e-postleverans]: https://sendgrid.com/transactional-email
