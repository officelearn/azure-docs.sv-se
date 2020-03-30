---
title: Hur man använder Twilio för röst och SMS (Ruby) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637201"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Hur man använder Twilio för röst- och SMS-funktioner i Ruby
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio API-tjänsten på Azure. Scenarierna som omfattas är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst och SMS i dina program finns i avsnittet [Nästa steg.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio är en telefoni webbtjänst API som låter dig använda dina befintliga webbspråk och färdigheter för att bygga röst-och SMS-program. Twilio är en tjänst från tredje part (inte en Azure-funktion och inte en Microsoft-produkt).

**Twilio Voice** gör det möjligt för dina program att ringa och ta emot telefonsamtal. **Twilio SMS** gör att dina program kan göra och ta emot SMS.Twilio SMS allows your applications to make and receive SMS messages. **Twilio Client** gör att dina program kan aktivera röstkommunikation med befintliga Internet-anslutningar, inklusive mobila anslutningar.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prissättning och specialerbjudanden
Information om Twilio prissättning finns på [Twilio Prissättning][twilio_pricing]. Azure-kunder får ett [specialerbjudande:][special_offer]en gratis kredit på 1000 texter eller 1000 inkommande minuter. För att registrera dig för detta erbjudande [https://ahoy.twilio.com/azure][special_offer]eller få mer information, besök .  

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio API är ett RESTful API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. för en lista, se [Twilio API-bibliotek][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML (TwiML)
TwiML är en uppsättning XML-baserade instruktioner som informerar Twilio om hur du bearbetar ett samtal eller SMS.

Som ett exempel skulle följande TwiML konvertera texten **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Alla TwiML-dokument har `<Response>` som rotelement. Därifrån använder du Twilio Verbs för att definiera beteendet för ditt program.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML-verb
Twilio Verbs är XML-taggar som talar om för Twilio vad de ska **göra**. Säg-verbet ** &lt;&gt; ** instruerar twilio att hörbart leverera ett meddelande i ett samtal. 

Följande är en lista över Twilio-verb.

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

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API:et finns i [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto registrerar du dig på [Try Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett gratis telefonnummer för din ansökan. Du får också ett SID-konto och en auth-token. Båda kommer att behövas för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, hålla din autentiseringstoken säker. Ditt konto SID och auth token kan visas på [Twilio-kontosidan][twilio_account], i fälten som heter **ACCOUNT SID** och **AUTH TOKEN**, respektive.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Verifiera telefonnummer
Utöver det nummer du får av Twilio kan du också verifiera nummer som du styr (dvs. din mobiltelefon eller ditt hemtelefonnummer) för användning i dina program. 

Information om hur du verifierar ett telefonnummer finns i [Hantera nummer][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Skapa ett Ruby-program
Ett Ruby-program som använder Tjänsten Twilio och körs i Azure skiljer sig inte från något annat Ruby-program som använder Tjänsten Twilio. Medan Twilio tjänster är RESTful och kan kallas från Ruby på flera sätt, kommer den här artikeln att fokusera på hur man använder Twilio tjänster med [Twilio helper bibliotek för Ruby][twilio_ruby].

Konfigurera först [en ny Virtuell Azure Linux-dator][azure_vm_setup] för att fungera som värd för ditt nya Ruby-webbprogram. Ignorera stegen som innebär att skapa en Rails-app, bara konfigurera den virtuella datorn. Se till att du skapar en slutpunkt med en extern port på 80 och en intern port på 5000.

I exemplen nedan kommer vi att använda [Sinatra][sinatra], en mycket enkel webb ram för Ruby. Men du kan säkert använda Twilio hjälpare bibliotek för Ruby med någon annan webb ram, inklusive Ruby on Rails.

SSH i din nya virtuella dator och skapa en katalog för din nya app. I katalogen skapar du en fil som heter Gemfile och kopierar följande kod till den:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Kör på `bundle install`kommandoraden . Detta kommer att installera beroenden ovan. Skapa sedan en `web.rb`fil som heter . Det är här koden för din webbapp finns. Klistra in följande kod i den:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Nu bör du kunna köra kommandot `ruby web.rb -p 5000`. Detta kommer att snurra upp en liten webbserver på port 5000. Du bör kunna bläddra till den här appen i din webbläsare genom att besöka webbadressen som du konfigurerar för din Virtuella Azure-dator. När du kan nå din webbapp i webbläsaren är du redo att börja bygga en Twilio-app.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Konfigurera ditt program för att använda Twilio
Du kan konfigurera webbappen så att den `Gemfile` använder Twilio-biblioteket genom att uppdatera din för att inkludera den här raden:

    gem 'twilio-ruby'

Kör `bundle install`. Nu `web.rb` öppna och inklusive denna linje högst upp:

    require 'twilio-ruby'

Nu är du redo att använda Twilio-hjälpbiblioteket för Ruby i webbappen.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Så här: Ringa ett utgående samtal
Följande visar hur du ringer ett utgående samtal. Viktiga begrepp är att använda Twilio helper-biblioteket för Ruby att göra REST API-anrop och göra TwiML. Ersätt dina värden med **telefonnummer från** och till och till och se **till** att du verifierar **Från-numret** för ditt Twilio-konto innan du kör koden.

Lägg till `web.md`den här funktionen i:

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

Om du öppnar `http://yourdomain.cloudapp.net/make_call` upp i en webbläsare, som kommer att utlösa samtalet till Twilio API för att ringa telefonsamtalet. De två första `client.account.calls.create` parametrarna i är ganska självförklarande: numret samtalet är `from` och numret samtalet är `to`. 

Den tredje`url`parametern ( ) är den URL som Twilio begär för att få instruktioner om vad du ska göra när samtalet är anslutet. I det här fallet ställer`http://yourdomain.cloudapp.net`vi in en URL ( ) `<Say>` som returnerar ett enkelt TwiML-dokument och använder verbet för att göra lite text-till-tal och säga "Hello Monkey" till den person som tar emot samtalet.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Så här: Får du ett SMS-meddelande
I föregående exempel initierade vi ett **utgående** telefonsamtal. Den här gången, låt oss använda telefonnumret som Twilio gav oss under registreringen för att bearbeta ett **inkommande** SMS-meddelande.

Logga först in på [instrumentpanelen i Twilio][twilio_account]. Klicka på "Nummer" i den övre navigeringen och klicka sedan på Twilio nummer du angavs. Du ser två webbadresser som du kan konfigurera. En url för röstbegäran och en URL för SMS-begäran. Dessa är webbadresserna som Twilio ringer när ett telefonsamtal görs eller ett SMS skickas till ditt nummer. Webbadresserna kallas även "webbkrokar".

Vi vill behandla inkommande SMS-meddelanden, så låt oss `http://yourdomain.cloudapp.net/sms_url`uppdatera webbadressen till . Gå vidare och klicka på Spara ändringar längst ned på sidan. Nu, tillbaka `web.rb` i låt oss programmera vår ansökan för att hantera detta:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

När du har gjort ändringen måste du se till att starta webbappen igen. Nu, ta ut telefonen och skicka ett SMS till din Twilio nummer. Du bör snabbt få ett SMS svar som säger "Hej, tack för ping! Twilio och Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Så här använder du ytterligare Twilio-tjänster
Förutom de exempel som visas här erbjuder Twilio webbaserade API:er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio API-dokumentationen][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Tjänsten Twilio följer du de här länkarna för att lära dig mer:

* [Twilio säkerhetsriktlinjer][twilio_security_guidelines]
* [Twilio HowTos och exempelkod][twilio_howtos]
* [Twilio Snabbstart Tutorials][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio Support][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
