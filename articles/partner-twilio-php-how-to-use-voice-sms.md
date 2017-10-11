---
title: "Hur du använder Twilio för röst- och SMS (PHP) | Microsoft Docs"
description: "Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i PHP."
documentationcenter: php
services: 
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
ms.openlocfilehash: bd50eac7390e8639f77894689388e6926cdb619c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Hur du använder Twilio för röst- och SMS-funktionerna i PHP
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio-API-tjänsten på Azure. Scenarier som tas upp inkluderar att ringa och ett kort meddelande (SMS Service)-meddelande skickas. Mer information om Twilio och använder röst- och SMS i dina program finns i [nästa steg](#NextSteps) avsnitt.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio startar framtiden för kommunikation med, så att utvecklare kan bädda in röst, VoIP och meddelanden i program. De virtualisera alla infrastruktur som behövs i en molnbaserad, globala miljö, exponera den via Twilio kommunikation API-plattformen. Programmen är enkla att skapa och skalbara. Få flexibilitet med lön-som-dig gå priser och dra nytta av molnet tillförlitlighet.

**Twilio röst** gör att dina program att göra och ta emot telefonsamtal. **Twilio SMS** gör att programmet kan skicka och ta emot textmeddelanden. **Twilio klienten** kan du se VoIP-anrop från telefon, surfplatta eller webbläsare och stöder WebRTC.

## <a id="Pricing"></a>Priser för Twilio och specialerbjudanden
Azure-kunder får en [specialerbjudande](http://www.twilio.com/azure): kostnadsfri $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-kredit kan tillämpas på alla Twilio-användning ($10 kredit motsvarar upp till 1 000 SMS-meddelanden skickas eller tas emot upp till 1 000 inkommande röst minuter beroende på platsen för ditt mål för telefon och meddelandet eller samtal). Lösa in den här Twilio-kredit och kom igång på: [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio är en betalning per användning. Det finns inga avgifter för installation och du kan stänga ditt konto när som helst. Du hittar mer information i [Twilio priser][twilio_pricing].

## <a id="Concepts"></a>Begrepp
Twilio-API är en RESTful-API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista, se [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et tillämpar Twilio verb; till exempel den  **&lt;säg&gt;**  verb instruerar Twilio att hörbart leverera ett meddelande på ett samtal.

Följande är en lista över Twilio verb. Lär dig mer om vilka verb och funktioner via [Markup Language Twilio dokumentationen](http://www.twilio.com/docs/api/twiml).

* **&lt;Ring&gt;**: anroparen ansluter till en annan telefon.
* **&lt;Samla in&gt;**: samlar in siffror som anges på telefon tangentbordet.
* **&lt;Koppla ned&gt;**: slutar ett anrop.
* **&lt;Spela upp&gt;**: spelar en ljudfil.
* **&lt;Pausa&gt;**: tyst väntar på ett angivet antal sekunder.
* **&lt;Posten&gt;**: registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överför kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: avvisar ett inkommande samtal till Twilio-nummer utan fakturering du
* **&lt;Säg&gt;**: konverterar text till tal, som görs på ett samtal.
* **&lt;SMS&gt;**: skickar ett SMS-meddelande.

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner baserat på de Twilio-verb som informerar Twilio för att behandla ett samtal eller SMS.

Följande TwiML skulle exempelvis Omvandla text **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML-svar. Du kan använda Twilio-tillhandahållna URL: er för utveckling, ange TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den **TwiMLResponse** objekt.

Läs mer om Twilio verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan logga på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och ett token för autentisering. Både behövs för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, skydda din token för autentisering. Ditt konto-ID och autentisering token kan visas på den [Twilio kontosida][twilio_account], i fälten med etiketten **konto SID** och **AUTH TOKEN**respektive.

## <a id="create_app"></a>Skapa en PHP-program
Ett PHP-program som använder tjänsten Twilio och körs i Azure är inte annorlunda än andra PHP-program som använder tjänsten Twilio. När Twilio är REST-baserad och kan anropas från PHP på flera sätt, den här artikeln fokuserar på hur du använder Twilio-tjänster med [Twilio-biblioteket för PHP från GitHub][twilio_php]. Mer information om hur du använder Twilio-biblioteket för PHP finns [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Detaljerade anvisningar för att skapa och distribuera ett Twilio/PHP-program till Azure finns på [hur du gör ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio-bibliotek
Du kan konfigurera tillämpningsprogrammet till att använda Twilio-biblioteket för PHP på två sätt:

1. Hämta Twilio-biblioteket för PHP från GitHub ([https://github.com/twilio/twilio-php][twilio_php]) och Lägg till den **Services** katalogen i ditt program.
   
    ELLER
2. Installera Twilio-biblioteket för PHP som PÄRONTRÄD paket. Den kan installeras med följande kommandon:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

När du har installerat Twilio-biblioteket för PHP, du kan sedan lägga till en **require_once** uttryck överst i PHP-filer för att referera till biblioteket:

        require_once 'Services/Twilio.php';

Mer information finns i [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Så här: göra en utgående anrop
Följande visar hur du gör en utgående Ring upp med den **Services_Twilio** klass. Den här koden används även en webbplats med angivna Twilio för att returnera svaret Twilio Markup Language (TwiML). Ersätt värdena för den **från** och **till** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

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

Som tidigare nämnts kan används den här koden en angivna Twilio plats för att returnera TwiML svaret. Du kan använda din egen webbplats i stället för att tillhandahålla TwiML svaret; Mer information finns i [hur du ger TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

* **Obs**: Om du vill felsöka verifieringsfel för SSL-certifikat finns [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>Så här: skicka ett SMS-meddelande
Här visas hur du skickar ett SMS-meddelande med den **Services_Twilio** klass. Den **från** nummer tillhandahålls av Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **till** numret måste verifieras för ditt Twilio-konto innan du kör koden.

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

## <a id="howto_provide_twiml_responses"></a>Så här: Ange TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API, skickar Twilio din begäran till en URL som förväntas returnera ett TwiML svar. I exemplet ovan används URL: en som tillhandahålls av Twilio [http://twimlets.com/message][twimlet_message_url]. (När TwiML är avsedd för användning av Twilio, du kan visa it i din webbläsare. Klicka till exempel [http://twimlets.com/message] [ twimlet_message_url] att se en tom `<Response>` element; Klicka på ett annat exempel är [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] att se en `<Response>` element som innehåller en `<Say>` element.)

I stället för på den angivna Twilio URL, kan du skapa din egen webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på alla språk som returnerar XML-svar; Det här avsnittet förutsätter att du använder PHP för att skapa TwiML.

Sidan följande PHP resulterar i ett TwiML-svar som säger **Hello World** på anropet.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Du ser i exemplet ovan, är TwiML svaret bara ett XML-dokument. Twilio-bibliotek för PHP innehåller klasser som ska generera TwiML för dig. Exemplet nedan skapar motsvarande svaret som ovan, men använder den **Services\_Twilio\_Twiml** klass i Twilio-biblioteket för PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Läs mer om TwiML [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

När du har din PHP-sida som ställts in för att ange TwiML svar använder URL för PHP-sidan som URL som skickades till den `Services_Twilio->account->calls->create` metoden. Till exempel om du har ett webbprogram med namnet **MyTwiML** distribueras till en Azure-värdtjänsten och namnet på sidan PHP är **mytwiml.php**, URL: en kan skickas till **Services_Twilio -> konto -> anrop -> Skapa** som visas i följande exempel:

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

Mer information om hur du använder Twilio i Azure med PHP finns [hur du gör ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Så här: använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare funktioner för Twilio från Azure-program. Fullständig information finns i [Twilio-API-dokumentationen][twilio_api_documentation].

## <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio ta och exempelkod][twilio_howtos]
* [Twilio snabbstarten självstudier][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-Support][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
