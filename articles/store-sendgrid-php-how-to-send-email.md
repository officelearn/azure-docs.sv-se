---
title: Hur du använder SendGrid e-posttjänst (PHP) | Microsoft Docs
description: Lär dig hur skicka e-post med SendGrid-e-posttjänsten på Azure. Kodexempel som skrivits i PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: 523b986f66a2e48685e9707903194856f0dcf4a2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "23874033"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Hur du använder tjänsten SendGrid e-post från PHP
Den här guiden visar hur du utför vanliga programmeringsuppgifter med SendGrid-e-posttjänsten på Azure. Exemplen är skrivna i PHP.
Scenarier som tas upp inkluderar **konstruera e-post**, **skicka e-post**, och **lägga till bilagor**. Mer information om SendGrid och skicka e-post finns i [nästa steg](#next-steps) avsnitt.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är SendGrid e-posttjänst?
SendGrid är en [molnbaserade e-posttjänst] som ger tillförlitliga [transaktionella e-postleverans], skalbarhet och analys i realtid tillsammans med flexibel API: er som gör det enkelt anpassad integrering. Vanliga Användningsscenarier för SendGrid är:

* Skicka automatiskt kvitton till kunder
* Administrera distribution visas för att skicka kunder månatliga e-reklamblad och specialerbjudanden
* Samla in realtid mätvärden för sådant som blockerade e-post och kunden svarstider
* Generera rapporter för att identifiera trender
* Vidarebefordran av kundfrågor
* E-postaviseringar från ditt program

Mer information finns i [ https://sendgrid.com ] [ https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Med SendGrid från PHP-program
Med SendGrid i ett Azure PHP-program kräver ingen särskild konfiguration eller kodning. Eftersom SendGrid är en tjänst, kan den användas på exakt samma sätt från ett moln-program som går från ett lokalt program.

## <a name="how-to-send-an-email"></a>Så här: skicka ett e-postmeddelande
Du kan skicka e-post med SMTP- eller webb-API som tillhandahålls av SendGrid.

### <a name="smtp-api"></a>SMTP-API
Om du vill skicka e-post med SendGrid SMTP-API, Använd *Swift adresshuvud*, ett komponentbaserade bibliotek för att skicka e-post från PHP-program. Du kan hämta den *Swift adresshuvud* bibliotek från [ http://swiftmailer.org/download ] [ http://swiftmailer.org/download] v5.3.0 (använda [Composer] installera Swift adresshuvud). Skicka e-post med biblioteket innebär att du skapar instanser av den <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_adresshuvud</span>, och <span class="auto-style2">Swift\_meddelande </span> klasser, ange lämpliga egenskaper och anropa den <span class="auto-style2">Swift\_Mailer::send</span> metod.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the receiver is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';

     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');

     // send message
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>Webb-API
Använd PHP'S [curl funktionen] [ curl function] att skicka e-post med SendGrid Web API.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );

     $request = $url.'api/mail.send.json';

     // Generate curl request
     $session = curl_init($request);

     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);

     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

     // obtain response
     $response = curl_exec($session);
     curl_close($session);

     // print everything out
     print_r($response);

Sendgrid's Web API är mycket likt ett REST-API, om den inte verkligen en RESTful-API eftersom i de flesta anrop, både få och POST verb är utbytbara.

## <a name="how-to-add-an-attachment"></a>Så här: Lägg till en bifogad fil
### <a name="smtp-api"></a>SMTP-API
En ytterligare kodrad till exempelskript för att skicka ett e-postmeddelande med Swift adresshuvud innebär att skicka bifogade filer med hjälp av SMTP-API.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');

     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';

     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

     // send message
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

Ytterligare kodrad är följande:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Kod anropar metoden bifoga på den <span class="auto-style2">Swift\_meddelandet</span> objekt och använder statisk metod <span class="auto-style2">fromPath</span> på den <span class="auto-style2">Swift\_bilaga</span> klass för att få och bifoga en fil i ett meddelande.

### <a name="web-api"></a>Webb-API
Skicka bifogade filer med hjälp av Web API liknar e-post med hjälp av Web-API. Observera dock att parametermatris i följande exempel måste innehålla det här elementet:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exempel:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';

     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );

     print_r($params);

     $request = $url.'api/mail.send.json';

     // Generate curl request
     $session = curl_init($request);

     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);

     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

     // obtain response
     $response = curl_exec($session);
     curl_close($session);

     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Så här: använda filter för att aktivera sidfötter, spårning och analys
SendGrid ger ytterligare e-postfunktioner genom att använda ”filter”. Dessa finns inställningar som du kan lägga till ett e-postmeddelande för att aktivera vissa funktioner, till exempel aktivera Klicka spårning, Google analytics, prenumeration spårning och så vidare.

Filter kan tillämpas på ett meddelande med hjälp av egenskapen filter. Varje filter som anges av ett hash-värde som innehåller filter-specifika inställningar. I följande exempel aktiverar sidfot filter och anger ett textmeddelande som läggs till längst ned i e-postmeddelandet.
Det här exemplet ska vi använda [sendgrid php biblioteket].
Använd [Composer] installera biblioteket:

    php composer.phar require sendgrid/sendgrid 2.1.1

Exempel:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version).
     # text is your plain-text email
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html>
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om tjänsten SendGrid e-post, kan du följa dessa länkar om du vill veta mer.

* SendGrid-dokumentation: <https://sendgrid.com/docs>
* SendGrid PHP-bibliotek: <https://github.com/sendgrid/sendgrid-php>
* SendGrid specialerbjudande för Azure-kunder: <https://sendgrid.com/windowsazure.html>

Mer information finns också i [PHP Developer Center](/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: http://php.net/curl
[molnbaserade e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionella e-postleverans]: https://sendgrid.com/transactional-email
[sendgrid php biblioteket]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
