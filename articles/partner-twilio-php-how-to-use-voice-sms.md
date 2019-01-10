---
title: Använda Twilio för röst- och SMS (PHP) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som är skrivet i PHP.
documentationcenter: php
services: ''
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: a51cd589702320ecb55e6a2e3c5f0a6139e281fe
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158082"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Använda Twilio för röst- och SMS-funktioner i PHP
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med Twilio-API-tjänsten på Azure. Scenarier som omfattas är ringa ett samtal och skicka ett meddelande om tjänsten SMS (Short Message). Mer information om Twilio och använda röst och SMS i dina program finns i den [nästa steg](#NextSteps) avsnittet.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio driva framtidens business-kommunikation, så att utvecklare kan bädda in röst, VoIP och meddelanden i program. De virtualisera all infrastruktur som behövs i en molnbaserad och global miljö kan exponera den via Twilio kommunikation API-plattformen. Program är enkel att bygga och skalbar. Få flexibilitet med betala per användning-som-du gå priser och dra nytta av molnet tillförlitlighet.

**Twilio-röst** kan dina program kan ringa och ta emot samtal. **Twilio-SMS** gör att programmet kan skicka och ta emot textmeddelanden. **Twilio-klienten** kan du se VoIP-anrop från valfri telefon, surfplatta eller webbläsare och har stöd för WebRTC.

## <a id="Pricing"></a>Twilio priser och specialerbjudanden
Azure-kunder får en [specialerbjudande](https://www.twilio.com/azure): kostnadsfri $10 av Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-kredit kan tillämpas på all Twilio-användning (10 USD kredit motsvarar skicka upp till 1 000 SMS-meddelanden eller ta emot upp till 1 000 inkommande röst minuter beroende på platsen för din telefon och meddelandet eller anrop mål). Lösa in den här Twilio-kredit och kom igång med: [ https://ahoy.twilio.com/azure ](https://ahoy.twilio.com/azure).

Twilio är en betala per tjänst. Det finns inga avgifter för konfiguration och du kan avsluta ditt konto när som helst. Du hittar mer information finns på [Twilio priser][twilio_pricing].

## <a id="Concepts"></a>Begrepp
Twilio-API är ett RESTful-API med röst och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista i [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et gör använda Twilio verb; till exempel den **&lt;Say&gt;** verb instruerar Twilio hörbart uppfyller ett meddelande på ett anrop.

Här följer en lista över Twilio-verb. Läs mer om andra verb och funktioner i form av [Twilio Markup Language dokumentation](https://www.twilio.com/docs/api/twiml).

* **&lt;Uppringning&gt;**: Ansluter anroparen till en annan telefon.
* **&lt;Samla in&gt;**: Samlar in siffror som anges på telefon-tangentbordet.
* **&lt;Koppla ned&gt;**: Slutar ett anrop.
* **&lt;Spela upp&gt;**: Spelar upp en ljudfil.
* **&lt;Pausa&gt;**: Väntar tyst under ett angivet antal sekunder.
* **&lt;Post&gt;**: Registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överföringar kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: Avvisar inkommande samtal till din Twilio-nummer utan fakturering du
* **&lt;Anta att&gt;**: Konverterar text till tal som görs på ett anrop.
* **&lt;SMS&gt;**: Skickar ett SMS-meddelande.

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktionerna baserat på de Twilio-verb som meddelar Twilio av behandla ett samtal eller SMS.

Till exempel följande TwiML konverteras texten **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML svaret. Du kan använda Twilio-angivna URL: er för utveckling, för att ge TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den **TwiMLResponse** objekt.

Läs mer om Twilio-verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns i [Twilio-API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att få ett Twilio-konto kan du registrera dig på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du en konto-ID och en autentiseringstoken. Båda krävs för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto måste skydda din autentiseringstoken. Ditt konto-ID och autentiseringsnyckel token kan visas på den [Twilio kontosidan][twilio_account], i fälten med etiketten **konto-SID** och **AUTENTISERINGSTOKEN**respektive.

## <a id="create_app"></a>Skapa en PHP-App
Ett PHP-program som använder Twilio-tjänsten och körs i Azure är inte skiljer sig från andra PHP-program som använder Twilio-tjänsten. Twilio-tjänster är REST-baserade och kan anropas från PHP på flera olika sätt, i den här artikeln fokuserar på hur du använder Twilio-tjänster med [Twilio-biblioteket för PHP från GitHub][twilio_php]. Mer information om hur du använder Twilio-biblioteket för PHP finns i [ https://www.twilio.com/docs/libraries/php ] [ twilio_lib_docs].

Detaljerade anvisningar för att skapa och distribuera ett Twilio/PHP-program till Azure finns på [hur du får ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio-bibliotek
Du kan konfigurera ditt program använder Twilio-biblioteket för PHP på två sätt:

1. Hämta Twilio-biblioteket för PHP från GitHub ([https://github.com/twilio/twilio-php][twilio_php]) och Lägg till den **Services** katalogen för ditt program.
   
    ELLER
2. Installera Twilio-biblioteket för PHP som PÄRONTRÄD paket. Den kan installeras med följande kommandon:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

När du har installerat Twilio-biblioteket för PHP, du kan sedan lägga till en **require_once** instruktion överst i din PHP-filer för att referera till biblioteket:

        require_once 'Services/Twilio.php';

Mer information finns i [ https://github.com/twilio/twilio-php/blob/master/README.md ] [ twilio_github_readme].

## <a id="howto_make_call"></a>Hur: Göra ett externt anrop
Följande visar hur du gör en utgående anropa med hjälp av den **Services_Twilio** klass. Den här koden använder också en Twilio-angivna plats för att returnera Twilio Markup Language (TwiML)-svaret. Ersätt värdena för den **från** och **till** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

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

Som tidigare nämnts använder den här koden en Twilio-angivna plats för att returnera TwiML-svar. Du kan i stället använda en egen plats för att tillhandahålla TwiML svaret; Mer information finns i [hur du ger TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

* **Obs!** Om du vill felsöka verifieringsfel för SSL-certifikat, se [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Hur: Skicka ett SMS-meddelande
Följande visar hur du skicka ett SMS-meddelande med den **Services_Twilio** klass. Den **från** tal kommer från Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **till** tal måste verifieras för ditt Twilio-konto innan du kör koden.

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

## <a id="howto_provide_twiml_responses"></a>Hur: Ange TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API, skickar Twilio din begäran till en URL som förväntas returnera ett TwiML svar. I exemplet ovan används URL: en med Twilio-angivna [ https://twimlets.com/message ] [ twimlet_message_url]. (Medan TwiML är avsedd för användning av Twilio, du kan visa it i din webbläsare. Klicka till exempel [ https://twimlets.com/message ] [ twimlet_message_url] att se en tom `<Response>` elementet; Klicka på ett annat exempel är [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]att se en `<Response>` element som innehåller en `<Say>` element.)

I stället för en Twilio-angivna URL: en, kan du skapa en egen plats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar XML-svar; Det här avsnittet förutsätter att du använder PHP för att skapa TwiML.

Sidan PHP resulterar i ett TwiML-svar som säger **Hello World** på anropet.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Som du ser i exemplet ovan är TwiML svaret helt enkelt ett XML-dokument. Twilio-bibliotek för PHP innehåller klasser som ska generera TwiML åt dig. Exemplet nedan skapar motsvarande svaret enligt ovan, men använder den **Services\_Twilio\_Twiml** klassen i Twilio-biblioteket för PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Läs mer om TwiML [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference]. 

När du har din PHP-sida som ställts in för att ge TwiML svar använder URL: en för PHP-sida som URL som skickades till den `Services_Twilio->account->calls->create` metoden. Exempel: Om du har en webbapp med namnet **MyTwiML** distribueras till en Azure-värdtjänsten och namnet på PHP-sida är **mytwiml.php**, URL: en kan skickas till **Services_Twilio -> konto -> anrop -> Skapa** som visas i följande exempel:

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

Mer information om hur du använder Twilio i Azure med PHP finns i [hur du får ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Hur: Använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio-API-dokumentation][twilio_api_documentation].

## <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna för Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio HowTo och exempelkod][twilio_howtos]
* [Twilio Snabbstartsguider][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Tala med Twilio-stöd][twilio_support]

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
