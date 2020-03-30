---
title: Hur man gör ett telefonsamtal från Twilio (PHP) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Prover är för PHP-program.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637313"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Hur man ringer ett telefonsamtal med Twilio i ett PHP-program på Azure
I följande exempel visas hur du kan använda Twilio för att ringa ett samtal från en PHP-webbsida som finns i Azure. Det resulterande programmet kommer att uppmana användaren för telefonsamtal värden, som visas i följande skärmdump.

![Azure-samtalsformulär med Twilio och PHP][twilio_php]

Du måste göra följande för att kunna använda koden i det här avsnittet:

1. Skaffa ett Twilio-konto och en autentiseringstoken från [Twilio-konsolen][twilio_console]. För att komma igång med Twilio, utvärdera prissättning på [https://www.twilio.com/pricing][twilio_pricing]. Du kan registrera dig för [https://www.twilio.com/try-twilio][try_twilio]ett utvärderingskonto på .
2. Skaffa [Twilio-biblioteket för PHP](https://github.com/twilio/twilio-php) eller installera det som ett PEAR-paket. Mer information finns i [readme-filen](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installera Azure SDK för PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Skapa ett webbformulär för att ringa ett samtal
Följande HTML-kod visar hur du skapar en webbsida (**callform.html**) som hämtar användardata för att ringa ett samtal:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Skapa koden för att ringa samtalet
Följande kod visar hur man bygger **makecall.php**, som anropas när användaren skickar formuläret som visas av **callform.html**. Koden som visas nedan skapar samtalsmeddelandet och genererar samtalet. Se också till att använda ditt Twilio-konto och autentiseringstoken från [Twilio-konsolen][twilio_console] i stället för de platshållarvärden som tilldelats **$sid** och **$token** i koden nedan.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Förutom att ringa samtalet, **visar makecall.php** några samtal metadata, som visas i bilden nedan. Mer information om anropsmetadata finns i [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure-samtalssvar med Twilio och PHP][twilio_php_response]

## <a name="run-the-application"></a>Köra appen
Nästa steg är att [distribuera ditt program till Azure Web Apps med Git](app-service/app-service-web-get-started-php.md) (men inte all information som finns relevant). 

## <a name="next-steps"></a>Nästa steg
Den här koden tillhandahölls för att visa dig grundläggande funktioner med Twilio i PHP på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till fler felhantering eller andra funktioner. Ett exempel:

* I stället för att använda ett webbformulär kan du använda Azure storage blobbar eller SQL Database för att lagra telefonnummer och samtalstext. Information om hur du använder Azure storage blobbar i PHP finns i [Använda Azure Storage med PHP Applications][howto_blob_storage_php]. Information om hur du använder SQL Database i PHP finns i [Använda SQL Database med PHP Applications][howto_sql_azure_php].
* Den **makecall.php** kod använder Twilio-förutsatt URL ([https://twimlets.com/message][twimlet_message_url]) för att ge en Twilio Markup Language (TwiML) svar som informerar Twilio hur man går vidare med samtalet. TwiML som returneras kan till exempel `<Say>` innehålla ett verb som resulterar i att texten läsas upp till samtalsmottagaren. I stället för att använda webbadressen som tillhandahålls av Twilio kan du skapa din egen tjänst för att svara på Twilios begäran. Mer information finns i [Hur du använder Twilio för röst- och SMS-funktioner i PHP][howto_twilio_voice_sms_php]. Mer information om TwiML [https://www.twilio.com/docs/api/twiml][twiml]finns på , `<Say>` och mer information om och [https://www.twilio.com/docs/api/twiml/say][twilio_say]andra Twilio verb finns på .
* Läs Twilio säkerhetsriktlinjer på [https://www.twilio.com/docs/security][twilio_docs_security].

Mer information om Twilio [https://www.twilio.com/docs][twilio_docs]finns i .

## <a name="see-also"></a>Se även
* [Hur man använder Twilio för röst-och SMS-funktioner i PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
