---
title: Ringa ett telefonsamtal från Twilio (PHP) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Exempel är för PHP-program.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "69637313"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Ringa ett telefonsamtal med Twilio i ett PHP-program på Azure
I följande exempel visas hur du kan använda Twilio för att ringa ett samtal från en PHP-webbsida som finns i Azure. Det resulterande programmet kommer att uppmana användaren att ange Telefonsamtals värden, som du ser i följande skärm bild.

![Anrops formulär för Azure med Twilio och PHP][twilio_php]

Du måste göra följande för att använda koden i det här avsnittet:

1. Hämta ett Twilio-konto och autentiseringstoken från [Twilio-konsolen][twilio_console]. Kom igång med Twilio genom att utvärdera priserna på [https://www.twilio.com/pricing][twilio_pricing]. Du kan registrera dig för ett utvärderings konto [https://www.twilio.com/try-twilio][try_twilio]på.
2. Hämta [Twilio-biblioteket för php](https://github.com/twilio/twilio-php) eller installera det som ett päron paket. Mer information finns i Readme- [filen](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installera Azure SDK för PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Skapa ett webb formulär för att ringa ett samtal
Följande HTML-kod visar hur du skapar en webb sida (**callform. html**) som hämtar användar data för att ringa:

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

## <a name="create-the-code-to-make-the-call"></a>Skapa koden för att ringa
Följande kod visar hur du skapar **MakeCall. php**, som anropas när användaren skickar formuläret som visas av **callform. html**. Den kod som visas nedan skapar ett anrops meddelande och genererar anropet. Se också till att använda ditt Twilio-konto och autentiseringstoken från Twilio- [konsolen][twilio_console] i stället för plats hållarens värden som tilldelas **$sid** och **$token** i koden nedan.

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

Förutom att ringa upp visar **MakeCall. php** vissa anrops-metadata som visas i bilden nedan. Mer information om anrops-metadata finns [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]i.

![Azure-samtals svar med Twilio och PHP][twilio_php_response]

## <a name="run-the-application"></a>Köra appen
Nästa steg är att [distribuera ditt program till Azure Web Apps med git](app-service/app-service-web-get-started-php.md) (men inte all information som är relevant). 

## <a name="next-steps"></a>Nästa steg
Den här koden angavs för att visa grundläggande funktioner med Twilio i PHP på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till mer fel hantering eller andra funktioner. Ett exempel:

* I stället för att använda ett webb formulär kan du använda Azure Storage-blobbar eller SQL Database för att lagra telefonnummer och samtals text. Information om hur du använder Azure Storage-blobbar i PHP finns i [använda Azure Storage med php-program][howto_blob_storage_php]. Information om hur du använder SQL Database i PHP finns i [använda SQL Database med php-program][howto_sql_azure_php].
* **MakeCall. php** -koden använder Twilio URL ([https://twimlets.com/message][twimlet_message_url]) för att tillhandahålla ett TwiML-svar (Twilio Markup Language) som informerar Twilio hur anropet ska fortsätta. Den TwiML som returneras kan till exempel innehålla ett `<Say>` verb som resulterar i att text läses till mottagaren. I stället för att använda Twilio-URL: en kan du bygga din egen tjänst för att svara på Twilio: s begäran. Mer information finns i [så här använder du Twilio för röst-och SMS-funktioner i php][howto_twilio_voice_sms_php]. Mer information om TwiML finns i [https://www.twilio.com/docs/api/twiml][twiml]. du hittar mer information om `<Say>` och andra Twilio-verb på. [https://www.twilio.com/docs/api/twiml/say][twilio_say]
* Läs rikt linjerna för Twilio- [https://www.twilio.com/docs/security][twilio_docs_security]säkerhet på.

Mer information om Twilio finns i [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Se även
* [Använda Twilio för röst-och SMS-funktioner i PHP](partner-twilio-php-how-to-use-voice-sms.md)

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
