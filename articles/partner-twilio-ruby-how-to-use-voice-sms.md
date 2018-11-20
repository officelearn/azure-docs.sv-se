---
title: Använda Twilio för röst- och SMS (Ruby) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som är skrivna i Ruby.
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
ms.openlocfilehash: 10ac41e60829e1d9a088033f0e754a447cc46f0f
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975540"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Använda Twilio för röst- och SMS-funktioner i Ruby
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med Twilio-API-tjänsten på Azure. Scenarier som omfattas är ringa ett samtal och skicka ett meddelande om tjänsten SMS (Short Message). Mer information om Twilio och använda röst och SMS i dina program finns i den [nästa steg](#NextSteps) avsnittet.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio är en telefoni webbtjänst-API som kan du använda dina befintliga webb-språk och färdigheter för att skapa röst och SMS-program. Twilio är en tjänst från tredje part (inte en funktion i Azure och inte en Microsoft-produkt).

**Twilio-röst** kan dina program kan ringa och ta emot samtal. **Twilio-SMS** kan dina program kan ringa och ta emot SMS. **Twilio-klienten** gör att dina program för att möjliggöra röstkommunikation via befintliga Internetanslutningar, även mobila anslutningar.

## <a id="Pricing"></a>Twilio priser och specialerbjudanden
Information om Twilio priser finns på [Twilio priser][twilio_pricing]. Azure-kunder får en [specialerbjudande][special_offer]: en kostnadsfri kredit på 1000 texter eller 1000 inkommande minuter. Om du vill registrera dig för erbjudandet eller få mer information, besök [ http://ahoy.twilio.com/azure ] [ special_offer].  

## <a id="Concepts"></a>Begrepp
Twilio-API är ett RESTful-API med röst och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista i [Twilio-API-bibliotek][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner som meddelar Twilio av behandla ett samtal eller SMS.

Till exempel följande TwiML konverteras texten **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Alla TwiML dokument har `<Response>` som deras rotelement. Därifrån kan använda du Twilio-verb för att definiera beteendet för ditt program.

### <a id="Verbs"></a>TwiML verb
Twilio-verb är XML-taggar som talar om för Twilio vad du **gör**. Till exempel den **&lt;Say&gt;** verb instruerar Twilio hörbart uppfyller ett meddelande på ett anrop. 

Här följer en lista över Twilio-verb.

* **&lt;Uppringning&gt;**: anroparen ansluter till en annan telefon.
* **&lt;Samla in&gt;**: samlar in siffror som anges på telefon-tangentbordet.
* **&lt;Koppla ned&gt;**: slutar ett anrop.
* **&lt;Spela upp&gt;**: spelar upp en ljudfil.
* **&lt;Pausa&gt;**: tyst väntar på ett angivet antal sekunder.
* **&lt;Post&gt;**: registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överför kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: avvisar ett inkommande samtal till din Twilio-nummer utan fakturering du
* **&lt;Anta att&gt;**: konverterar text till tal som görs på ett anrop.
* **&lt;SMS&gt;**: skickar ett SMS-meddelande.

Läs mer om Twilio-verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns i [Twilio-API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att få ett Twilio-konto kan du registrera dig på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du en kostnadsfri telefonnumret för ditt program. Du får även ett SID-konto och en autentiseringstoken. Båda krävs för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto måste skydda din autentiseringstoken. Ditt konto SID och -token som kan visas på den [Twilio kontosidan][twilio_account], i fälten med etiketten **konto-SID** och **AUTENTISERINGSTOKEN**, respektive.

### <a id="VerifyPhoneNumbers"></a>Verifiera telefonnummer
Förutom numret du får från Twilio, du kan också kontrollera tal som du hanterar (d.v.s. hemma eller mobiltelefon numret till din telefon) för användning i dina program. 

Information om hur du verifierar ett telefonnummer, finns i [hantera siffror][verify_phone].

## <a id="create_app"></a>Skapa en Ruby-program
En Ruby-program som använder Twilio-tjänsten och körs i Azure är inte skiljer sig från alla Ruby program som använder Twilio-tjänsten. Twilio-tjänster är RESTful och kan anropas från Ruby på flera olika sätt, i den här artikeln fokuserar på hur du använder Twilio-tjänster med [Twilio hjälpbibliotek för Ruby][twilio_ruby].

Först [ställa in en ny virtuell Linux-dator] [ azure_vm_setup] så att den fungerar som värd för ditt nya Ruby webbprogram. Ignorera de steg som inbegriper att skapa en Rails-app, bara konfigurera den virtuella datorn. Se till att skapa en slutpunkt med en extern port 80 och en intern port 5000.

I exemplen nedan, kommer vi att använda [Sinatra][sinatra], ett mycket enkelt webbramverk för Ruby. Men du kan däremot använda Twilio hjälpbibliotek för Ruby med alla andra webbramverk, inklusive Ruby on Rails.

SSH till den nya virtuella datorn och skapa en katalog för din nya app. Skapa en fil med namnet Gemfile i katalogen och kopiera följande kod till den:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

På kommandoraden som kör `bundle install`. Detta installerar beroendena ovan. Därefter skapar en fil med namnet `web.rb`. Det här är var koden för din webbapp finns. Klistra in följande kod i den:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Nu ska du kunna köra kommandot `ruby web.rb -p 5000`. Detta kommer snurra upp en liten webbserver på port 5000. Du bör kunna bläddra till den här appen i webbläsaren genom att gå till URL: en du konfiguration för Azure-VM. När du kan nå din webbapp i webbläsaren, är du redo att börja bygga en Twilio-app.

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio
Du kan konfigurera webbappen för att använda Twilio-biblioteket genom att uppdatera din `Gemfile` att inkludera den här raden:

    gem 'twilio-ruby'

Kör på kommandoraden, `bundle install`. Öppna nu `web.rb` och med den här raden överst:

    require 'twilio-ruby'

Du är nu allt klart att använda Twilio-hjälpbibliotek för Ruby i web Apps.

## <a id="howto_make_call"></a>Så här: göra ett externt anrop
Nedan visas hur du gör en utgående samtal. Viktiga begrepp exempel med Twilio hjälpbibliotek för Ruby för att göra REST API-anrop och rendering TwiML. Ersätt värdena för den **från** och **till** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

Lägg till den här funktionen till `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
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

Om du in `http://yourdomain.cloudapp.net/make_call` i en webbläsare som ska utlösa anrop till Twilio-API för att göra telefonsamtal. De första två parametrarna i `client.account.calls.create` är ganska självförklarande: antalet anropet är `from` och antalet anropet är `to`. 

Den tredje parametern (`url`) är den URL som Twilio begär vill ha information om vad du gör när anropet är ansluten. I det här fallet vi ställa in en URL (`http://yourdomain.cloudapp.net`) som returnerar ett vanligt TwiML dokument och använder den `<Say>` verb som gör vissa text till tal och säger ”Hello apa” till den person som tar emot samtalet.

## <a id="howto_receive_sms"></a>Så här: får ett SMS-meddelande
I föregående exempel startade vi en **utgående** telefonsamtal. Den här gången, använder vi det telefonnummer som Twilio gav oss under registrering för att bearbeta en **inkommande** SMS: et.

Första, logga in på din [Twilio-instrumentpanel][twilio_account]. Klicka på ”tal” i det övre navigeringsfältet och klicka sedan på Twilio-talet som du har angett. Du ser två URL: er som du kan konfigurera. En röst begärd URL och ett SMS URL för begäran. Det här är URL: er som Twilio anropar när det görs ett telefonsamtal eller ett SMS skickas till ditt nummer. URL: er är också kända som ”webhooks”.

Vi vill bearbeta inkommande SMS-meddelanden, så vi uppdaterar URL: en till `http://yourdomain.cloudapp.net/sms_url`. Gå vidare och klicka på Spara ändringar längst ned på sidan. Nu igen `web.rb` vi programmet våra program kan hantera detta:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

När du gjort ändringen, se till att starta webbappen. Nu kan ta reda på din telefon och skicka ett SMS till din Twilio-nummer. Du får direkt ett SMS-svar som säger ”Hey, Tack för att ping! Twilio och Azure rock ”!.

## <a id="additional_services"></a>Så här: använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio-API-dokumentation][twilio_api_documentation].

### <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna för Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio HowTos och exempelkod][twilio_howtos]
* [Twilio Snabbstartsguider][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Tala med Twilio-stöd][twilio_support]

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
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
