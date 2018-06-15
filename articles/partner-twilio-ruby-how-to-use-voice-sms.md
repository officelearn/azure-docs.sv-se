---
title: Hur du använder Twilio för röst- och SMS (Ruby) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i Ruby.
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 69e50e7fe0e1f302e96c309878b8dea6869dff4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866081"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Hur du använder Twilio för röst- och SMS-funktioner i Ruby
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio-API-tjänsten på Azure. Scenarier som tas upp inkluderar att ringa och ett kort meddelande (SMS Service)-meddelande skickas. Mer information om Twilio och använder röst- och SMS i dina program finns i [nästa steg](#NextSteps) avsnitt.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio är en telefoni webbtjänst API som gör att du kan använda din befintliga web språk och kunskaper för att skapa röst- och SMS-program. Twilio är en från tredje part (inte en funktion i Azure och inte en Microsoft-produkt).

**Twilio röst** gör att dina program att göra och ta emot telefonsamtal. **Twilio SMS** gör att dina program att göra och ta emot SMS-meddelanden. **Twilio klienten** kan ditt program genom att aktivera röstkommunikation via befintliga Internet-anslutningar, inklusive mobila anslutningar.

## <a id="Pricing"></a>Priser för Twilio och specialerbjudanden
Information om priser Twilio finns på [Twilio priser][twilio_pricing]. Azure-kunder får en [specialerbjudande][special_offer]: en kredit på 1000 texter eller 1000 inkommande minuter. Om du vill registrera dig för det här erbjudandet eller få mer information, besök [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Begrepp
Twilio-API är en RESTful-API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista, se [Twilio-API-bibliotek][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner som talar om för Twilio för att behandla ett samtal eller SMS.

Följande TwiML skulle exempelvis Omvandla text **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Alla TwiML dokument har `<Response>` som deras rotelementet. Därifrån kan använda du Twilio verb för att definiera beteendet för ditt program.

### <a id="Verbs"></a>TwiML verb
Twilio-verb är XML-taggar som talar Twilio vad du **gör**. Till exempel den  **&lt;säg&gt;**  verb instruerar Twilio att hörbart leverera ett meddelande på ett samtal. 

Följande är en lista över Twilio verb.

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

Läs mer om Twilio verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan logga på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett kostnadsfritt telefonnummer för ditt program. Du får även ett SID-konto och en token för autentisering. Både behövs för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, skydda din token för autentisering. Ditt konto SID och auth token kan visas på den [Twilio kontosida][twilio_account], i fälten med etiketten **konto SID** och **AUTH TOKEN**respektive.

### <a id="VerifyPhoneNumbers"></a>Kontrollera telefonnummer
Förutom numret du får från Twilio, kan du också kontrollera siffror som du hanterar (d.v.s. din mobiltelefon eller home telefonnumret) för användning i dina program. 

Information om hur du verifierar ett telefonnummer finns [hantera siffror][verify_phone].

## <a id="create_app"></a>Skapa ett Ruby-program
Ett Ruby program som använder tjänsten Twilio och körs i Azure är inte annorlunda än andra Ruby program som använder Twilio-tjänsten. Medan Twilio tjänsterna RESTful och kan anropas från Ruby på flera sätt, den här artikeln fokuserar på hur du använder Twilio-tjänster med [Twilio hjälpbibliotek för Ruby][twilio_ruby].

Första, [ställa in en ny Azure Linux VM] [ azure_vm_setup] att fungera som värd för ditt nya Ruby webbprogram. Ignorera steg som inbegriper skapandet av en spår app bara konfigurera den virtuella datorn. Kontrollera att du skapar en slutpunkt med en extern port 80 och en intern port 5000.

I exemplen nedan, kommer vi att använda [Sinatra][sinatra], ett väldigt enkelt webbramverk för Ruby. Men du kan visserligen använda hjälpbibliotek Twilio för Ruby med alla andra webbramverk, inklusive Ruby spår.

SSH till den nya virtuella datorn och skapar en katalog för din nya app. Skapa en fil med namnet Gemfile och kopiera följande kod till den i katalogen:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

På kommandoraden som kör `bundle install`. Detta installerar ovanstående beroenden. Sedan skapar en fil med namnet `web.rb`. Det här är där koden för ditt webbprogram finns. Klistra in följande kod i den:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Nu ska du kör kommandot `ruby web.rb -p 5000`. Detta kommer rotationsrutan upp en liten webbserver på port 5000. Du ska kunna bläddra till den här appen i webbläsaren genom att gå till URL du ställa in för din virtuella Azure-datorn. När du kan komma åt ditt webbprogram i webbläsaren, är du redo att börja bygga en Twilio-app.

## <a id="configure_app"></a>Konfigurera programmet så att Twilio
Du kan konfigurera ditt webbprogram för användning av Twilio-biblioteket genom att uppdatera din `Gemfile` att inkludera den här raden:

    gem 'twilio-ruby'

Kör på kommandoraden `bundle install`. Öppna `web.rb` och raden längst upp, inklusive:

    require 'twilio-ruby'

Du har nu allt klart att använda hjälpbibliotek Twilio för Ruby i ditt webbprogram.

## <a id="howto_make_call"></a>Så här: göra en utgående anrop
Nedan visas hur du skapar ett utgående anrop. Viktiga begrepp är med hjälp av Twilio hjälpbibliotek för Ruby REST API-anrop och återges TwiML. Ersätt värdena för den **från** och **till** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

Lägg till den här funktionen till `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Om du öppna upp `http://yourdomain.cloudapp.net/make_call` i en webbläsare som utlöser anrop till Twilio-API för att göra telefonsamtal. De två första parametrarna i `client.account.calls.create` är ganska självförklarande: antalet anropet är `from` och antalet anropet är `to`. 

Den tredje parametern (`url`) är den URL som Twilio begär vill ha information om vad du ska göra när anropet är ansluten. I det här fallet vi ställa in en URL (`http://yourdomain.cloudapp.net`) som returnerar ett enkelt TwiML dokument och använder den `<Say>` verb för att göra vissa text till tal och säg ”Hello apa” till den person som tar emot samtalet.

## <a id="howto_recieve_sms"></a>Så här: ta emot ett SMS-meddelande
I föregående exempel vi initierade en **utgående** telefonsamtal. Den här gången, skriver du det telefonnummer som Twilio angav under registrering för att bearbeta en **inkommande** SMS-meddelande.

Första, logga in på ditt [Twilio instrumentpanelen][twilio_account]. Klicka på ”tal” i den översta nav och klicka sedan på Twilio-nummer som du har angett. Ser du två URL: er som du kan konfigurera. En röst begärd URL och ett SMS URL-begäran. Dessa är de URL: er som Twilio anropar när det görs ett telefonsamtal eller ett SMS skickas till ditt nummer. URL: er kallas även ”web hook”.

Vi vill bearbeta inkommande SMS-meddelanden, så vi uppdatera Webbadressen till `http://yourdomain.cloudapp.net/sms_url`. Gå vidare och klicka på Spara ändringar längst ned på sidan. Nu tillbaka `web.rb` programmet vi vårt program för att hantera detta:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

När du gjort ändringen, se till att starta ditt webbprogram. Nu ta ut din telefon och skicka ett SMS till Twilio-nummer. Du får omedelbart ett SMS-svar som säger ”Hey, Tack för ping! Twilio och Azure Berg ”!.

## <a id="additional_services"></a>Så här: använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare funktioner för Twilio från Azure-program. Fullständig information finns i [Twilio-API-dokumentationen][twilio_api_documentation].

### <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio HowTos och exempelkod][twilio_howtos]
* [Twilio snabbstarten självstudier][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-Support][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
