---
title: Hur man använder Twilio för röst och SMS (PHP) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: f9fb250109a1c9000eae8da0d6337c96f19f0f89
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410546"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Hur man använder Twilio för röst-och SMS-funktioner i PHP
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio API-tjänsten på Azure. Scenarierna som omfattas är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst och SMS i dina program finns i avsnittet [Nästa steg.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio driver framtiden för affärskommunikation, vilket gör det möjligt för utvecklare att bädda in röst, VoIP och meddelanden i program. De virtualiserar all infrastruktur som behövs i en molnbaserad, global miljö och exponerar den via Twilio-plattformen för kommunikation API. Program är enkla att bygga och skalbara. Njut av flexibilitet med priser för användningsbaserad betalning och dra nytta av molntillförlitlighet.

**Twilio Voice** gör det möjligt för dina program att ringa och ta emot telefonsamtal. **Twilio SMS** gör det möjligt för din ansökan att skicka och ta emot textmeddelanden. **Twilio Client** kan du ringa VoIP-samtal från vilken telefon, surfplatta eller webbläsare som helst och stöder WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prissättning och specialerbjudanden
Azure-kunder får ett [specialerbjudande:](https://www.twilio.com/azure)gratis $10 av Twilio Credit när du uppgraderar ditt Twilio-konto. Denna Twilio Credit kan tillämpas på alla Twilio användning ($ 10 kredit motsvarar att skicka så många som 1.000 SMS-meddelanden eller ta emot upp till 1000 inkommande röst minuter, beroende på var ditt telefonnummer och meddelande eller samtal destination). Lös in denna Twilio kredit [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)och komma igång på: .

Twilio är en pay-as-you-go tjänst. Det finns inga inställningsavgifter och du kan avsluta ditt konto när som helst. Du kan hitta mer information på [Twilio Prissättning][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio API är ett RESTful API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. för en lista, se [Twilio API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio API är Twilio verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Verb
API använder Twilio verb; Till exempel instruerar ** &lt;&gt; Say-verbet** Twilio att hörbart leverera ett meddelande i ett samtal.

Följande är en lista över Twilio-verb. Lär dig mer om andra verb och funktioner via [dokumentationen för Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* **Slå :&gt;Ansluter den som ringer till en annan telefon. &lt;**
* **Samla:&gt;Samlar numeriska siffror som anges på telefonens knappsats. &lt;**
* **Hangup&gt;: Avslutar ett &lt;** samtal.
* **Spela upp&gt;: Spelar upp en ljudfil. &lt;**
* **Paus:&gt;Väntar tyst på ett visst antal &lt;** sekunder.
* **Post:&gt;Spelar in den som ringer röst och returnerar en URL för en fil som innehåller &lt;** inspelningen.
* **Omdirigering&gt;: Överför kontroll över ett samtal eller SMS till TwiML på en annan webbadress. &lt;**
* **Avvisa:&gt;Avvisar ett inkommande samtal till ditt Twilio-nummer utan att fakturera &lt;** dig
* **Säg:&gt;Konverterar text till tal som görs på ett &lt;** samtal.
* Sms : Skickar ett SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML (TwiML)
TwiML är en uppsättning XML-baserade instruktioner baserade på Twilio-verben som informerar Twilio om hur du bearbetar ett samtal eller SMS.

Som ett exempel skulle följande TwiML konvertera texten **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

När ditt program anropar Twilio API är en av API-parametrarna url:en som returnerar TwiML-svaret. I utvecklingssyfte kan du använda url:er som tillhandahålls av Twilio för att ange de TwiML-svar som används av dina program. Du kan också vara värd för dina egna webbadresser för att skapa TwiML-svaren, och ett annat alternativ är att använda **TwiMLResponse-objektet.**

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API:et finns i [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto registrerar du dig på [Try Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och en autentiseringstoken. Båda kommer att behövas för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, hålla din autentiseringstoken säker. Ditt konto-ID och autentiseringstoken kan visas på [Twilio-kontosidan][twilio_account]i fälten **KONTO SID** respektive **AUTH TOKEN.**

## <a name="create-a-php-application"></a><a id="create_app"></a>Skapa en PHP-applikation
Ett PHP-program som använder Tjänsten Twilio och som körs i Azure skiljer sig inte från något annat PHP-program som använder Tjänsten Twilio. Medan Twilio tjänster är REST-baserade och kan kallas från PHP på flera sätt, kommer den här artikeln att fokusera på hur man använder Twilio tjänster med [Twilio bibliotek för PHP från GitHub][twilio_php]. Mer information om hur du använder Twilio-biblioteket för PHP finns i [https://www.twilio.com/docs/libraries/php][twilio_lib_docs].

Detaljerade instruktioner för att skapa och distribuera ett Twilio/PHP-program till Azure finns på Så här ringer du [ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program för att använda Twilio-bibliotek
Du kan konfigurera ditt program för att använda Twilio-biblioteket för PHP på två sätt:

1. Ladda ner Twilio-biblioteket för PHP[https://github.com/twilio/twilio-php][twilio_php]från GitHub ( ) och lägg till **tjänsten** katalogen till ditt program.
   
    ELLER
2. Installera Twilio-biblioteket för PHP som ett PEAR-paket. Den kan installeras med följande kommandon:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

När du har installerat Twilio biblioteket för PHP, kan du sedan lägga till en **require_once** uttalande högst upp i dina PHP-filer för att referera till biblioteket:

        require_once 'Services/Twilio.php';

Mer information finns [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]i .

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Så här: Ringa ett utgående samtal
Följande visar hur du ringer ett utgående samtal med **Services_Twilio** klassen. Den här koden använder också en Twilio-plats för att returnera Twilio Markup Language (TwiML) svar. Ersätt dina värden med **telefonnummer från** och till och till och se **till** att du verifierar **Från-numret** för ditt Twilio-konto innan du kör koden.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Som nämnts använder den här koden en Twilio-tillsedd plats för att returnera TwiML-svaret. Du kan istället använda din egen webbplats för att ge TwiML-svaret. Mer information finns i [Så här ger du TwiML-svar från din egen webbplats](#howto_provide_twiml_responses).

* **Om**du vill felsöka valideringsfel för TLS/SSL-certifikat läser du[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Så här skickar du ett SMS
Följande visar hur du skickar ett SMS-meddelande med **Services_Twilio** klassen. **Från-numret** tillhandahålls av Twilio för utvärderingskonton för att skicka SMS-meddelanden. **Till-numret** måste verifieras för ditt Twilio-konto innan koden körs.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Så här: Ge TwiML-svar från din egen webbplats
När ditt program startar ett anrop till Twilio API skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. I exemplet ovan används url:en [https://twimlets.com/message][twimlet_message_url]som tillhandahålls av Twilio . (Medan TwiML är utformad för användning av Twilio kan du visa den i din webbläsare. Klicka till [https://twimlets.com/message][twimlet_message_url] exempel för `<Response>` att se ett tomt element. Som ett annat [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] exempel `<Response>` klickar du på `<Say>` om du vill visa ett element som innehåller ett element.)

I stället för att förlita sig på den Twilio-tillsentade webbadressen kan du skapa en egen webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar XML-svar. detta ämne förutsätter att du kommer att använda PHP för att skapa TwiML.

Följande PHP-sida resulterar i en TwiML svar som säger **Hello World** på samtalet.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Som du kan se i exemplet ovan är TwiML-svaret helt enkelt ett XML-dokument. Den Twilio biblioteket för PHP innehåller klasser som kommer att generera TwiML för dig. Exemplet nedan ger motsvarande svar som visas ovan, men använder klassen **Services\_Twilio\_Twiml** i Twilio-biblioteket för PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Mer information om TwiML [https://www.twilio.com/docs/api/twiml][twiml_reference]finns i . 

När du har din PHP-sida inställd för att ge TwiML svar, använd `Services_Twilio->account->calls->create` webbadressen till PHP-sidan som webbadressen skickas in i metoden. Om du till exempel har ett webbprogram med namnet **MyTwiML** distribuerat till en Azure-värdtjänst och namnet på PHP-sidan är **mytwiml.php,** kan webbadressen skickas till **Services_Twilio->konto->samtal >skapa** som visas i följande exempel:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Mer information om hur du använder Twilio i Azure med PHP finns i Så här ringer du [ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Så här använder du ytterligare Twilio-tjänster
Förutom de exempel som visas här erbjuder Twilio webbaserade API:er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio API-dokumentationen][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Tjänsten Twilio följer du de här länkarna för att lära dig mer:

* [Twilio säkerhetsriktlinjer][twilio_security_guidelines]
* [Twilio HowTo's och exempelkod][twilio_howtos]
* [Twilio Snabbstart Tutorials][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio Support][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
