---
title: Använda Twilio för röst- och SMS (Python) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i Python.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 63eb894c64919826922fa60f4e12894542a97c69
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994173"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Använda Twilio för röst- och SMS-funktioner i Python
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med Twilio-API-tjänsten på Azure. Scenarier som omfattas är ringa ett samtal och skicka ett meddelande om tjänsten SMS (Short Message). Mer information om Twilio och använda röst och SMS i dina program finns i den [nästa steg](#NextSteps) avsnittet.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio driva framtidens business-kommunikation, så att utvecklare kan bädda in röst, VoIP och meddelanden i program. De virtualisera all infrastruktur som behövs i en molnbaserad och global miljö kan exponera den via Twilio kommunikation API-plattformen. Program är enkel att bygga och skalbar. Få flexibilitet med betala per användning-som-du gå priser och dra nytta av molnet tillförlitlighet.

**Twilio-röst** kan dina program kan ringa och ta emot samtal.
**Twilio-SMS** gör att programmet kan skicka och ta emot textmeddelanden.
**Twilio-klienten** kan du se VoIP-anrop från valfri telefon, surfplatta eller webbläsare och har stöd för WebRTC.

## <a id="Pricing"></a>Twilio priser och specialerbjudanden
Azure-kunder får en [specialerbjudande] [ special_offer] $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-kredit kan tillämpas på all Twilio-användning (10 USD kredit motsvarar skicka upp till 1 000 SMS-meddelanden eller ta emot upp till 1 000 inkommande röst minuter beroende på platsen för din telefon och meddelandet eller anrop mål). Lös in detta [Twilio kredit] [ special_offer] och komma igång.

Twilio är en betala per tjänst. Det finns inga avgifter för konfiguration och du kan avsluta ditt konto när som helst. Du hittar mer information finns på [Twilio priser][twilio_pricing].

## <a id="Concepts"></a>Begrepp
Twilio-API är ett RESTful-API med röst och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista i [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et gör använda Twilio verb; till exempel den **&lt;Say&gt;** verb instruerar Twilio hörbart uppfyller ett meddelande på ett anrop.

Här följer en lista över Twilio-verb. Läs mer om andra verb och funktioner i form av [Twilio Markup Language dokumentation][twiml].

* **&lt;Uppringning&gt;**: Ansluter anroparen till en annan telefon.
* **&lt;Samla in&gt;**: Samlar in siffror som anges på telefon-tangentbordet.
* **&lt;Koppla ned&gt;**: Slutar ett anrop.
* **&lt;Pausa&gt;**: Väntar tyst under ett angivet antal sekunder.
* **&lt;Spela upp&gt;**: Spelar upp en ljudfil.
* **&lt;Kön&gt;**: Lägg till den till en kö med anropare.
* **&lt;Post&gt;**: Registrerar anroparen röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överföringar kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: Avvisar inkommande samtal till din Twilio-nummer utan fakturering du.
* **&lt;Anta att&gt;**: Konverterar text till tal som görs på ett anrop.
* **&lt;SMS&gt;**: Skickar ett SMS-meddelande.

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktionerna baserat på de Twilio-verb som meddelar Twilio av behandla ett samtal eller SMS.

Till exempel följande TwiML konverteras texten **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML svaret. Du kan använda Twilio-angivna URL: er för utveckling, för att ge TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den `TwiMLResponse` objekt.

Läs mer om Twilio-verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns i [Twilio-API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att få ett Twilio-konto, registrera dig på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett SID-konto och en autentiseringstoken. Båda krävs för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto måste skydda din autentiseringstoken. Ditt konto SID och autentiseringstoken kan visas i den [Twilio-konsolen][twilio_console], i fälten med etiketten **konto-SID** och **AUTENTISERINGSTOKEN**respektive.

## <a id="create_app"></a>Skapa en Python-program
Ett Python-program som använder Twilio-tjänsten och körs i Azure är inte skiljer sig från andra Python-program som använder Twilio-tjänsten. Twilio-tjänster är REST-baserade och kan anropas från Python på flera olika sätt, i den här artikeln fokuserar på hur du använder Twilio-tjänster med [Twilio-bibliotek för Python från GitHub][twilio_python]. Mer information om hur du använder Twilio-bibliotek för Python finns i [ https://www.twilio.com/docs/libraries/python ] [ twilio_lib_docs].

Första, [ställa in en ny virtuell Linux-dator] [azure_vm_setup] att fungera som värd för din nya Python-webbprogram. När den virtuella datorn körs, måste du exponera dina program på en offentlig port enligt beskrivningen nedan.

### <a name="add-an-incoming-rule"></a>Lägg till en inkommande regel
  1. Gå till sidan [Nätverkssäkerhetsgrupp] [azure_nsg].
  2. Välj den Nätverkssäkerhetsgrupp som motsvarar med din virtuella dator.
  3. Lägg till och **utgående regel** för **port 80**. Var noga med att tillåta inkommande från alla adresser.

### <a name="set-the-dns-name-label"></a>Ange DNS-namnetikett
  1. Gå till sidan [den offentliga IP-adresser] [azure_ips].
  2. Välj den offentliga IP-Adressen som correspends med din virtuella dator.
  3. Ange den **DNS-Namnetiketten** i den **Configuration** avsnittet. Det här exemplet ska det se ut ungefär så här *your domänetiketten*. centralus.cloudapp.azure.com

När du har möjlighet att ansluta via SSH till den virtuella datorn kan du installera webbramverk valfri (de mest välkända i Python som två [Flask](http://flask.pocoo.org/) och [Django](https://www.djangoproject.com)). Du kan installera någon av dem genom att köra den `pip install` kommando.

Tänk på att vi har konfigurerat den virtuella datorn för att tillåta trafik på port 80. Så se till att konfigurera programmet till att använda den här porten.

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio-bibliotek
Du kan konfigurera ditt program använder Twilio-bibliotek för Python på två sätt:

* Installera Twilio-bibliotek för Python som ett Pip-paket. Den kan installeras med följande kommandon:
   
        $ pip install twilio

    ELLER

* Hämta Twilio-bibliotek för Python från GitHub ([https://github.com/twilio/twilio-python][twilio_python]) och installera den så här:

        $ python setup.py install

När du har installerat Twilio-bibliotek för Python, kan du sedan `import` den i Python-filer:

        import twilio

Mer information finns i [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.rst).

## <a id="howto_make_call"></a>Hur: Göra ett externt anrop
Nedan visas hur du gör en utgående samtal. Den här koden använder också en Twilio-angivna plats för att returnera Twilio Markup Language (TwiML)-svaret. Ersätt värdena för den **from_number** och **to_number** telefonnummer och se till att du har kontrollerat det **from_number** telefonnummer för ditt Twilio-konto innan du kör koden.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Som tidigare nämnts använder den här koden en Twilio-angivna plats för att returnera TwiML-svar. Du kan i stället använda en egen plats för att tillhandahålla TwiML svaret; Mer information finns i [hur du ger TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Hur: Skicka ett SMS-meddelande
Följande visar hur du skicka ett SMS-meddelande med den `TwilioRestClient` klass. Den **from_number** tal kommer från Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **to_number** tal måste verifieras för ditt Twilio-konto innan du kör koden.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Hur: Ange TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API, skickar Twilio din begäran till en URL som förväntas returnera ett TwiML svar. I exemplet ovan används URL: en med Twilio-angivna [ https://twimlets.com/message ] [ twimlet_message_url]. (Medan TwiML är avsedd för användning av Twilio, kan du visa den i webbläsaren. Klicka till exempel [ https://twimlets.com/message ] [ twimlet_message_url] att se en tom `<Response>` elementet; Klicka på ett annat exempel är [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ] [ twimlet_message_url_hello_world]att se en `<Response>` element som innehåller en `<Say>` element.)

I stället för en Twilio-angivna URL: en, kan du skapa en egen plats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar XML-svar; Det här avsnittet förutsätter att du kommer att använda Python för att skapa TwiML.

I följande exempel kommer utdata svar TwiML där det står **Hello World** på anropet.

Med Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Med Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Som du ser i exemplet ovan är TwiML svaret helt enkelt ett XML-dokument. Twilio-bibliotek för Python innehåller klasser som ska generera TwiML åt dig. Exemplet nedan skapar motsvarande svaret enligt ovan, men använder den `twiml` modul i Twilio-bibliotek för Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Läs mer om TwiML [ https://www.twilio.com/docs/api/twiml ] [ twiml_reference].

När du har ditt Python-program som ställts in för att ge TwiML svar använder URL: en för programmet som URL: en som skickades till den `client.calls.create` metoden. Exempel: Om du har en webbapp med namnet **MyTwiML** distribueras till en Azure-värdtjänsten tjänsten, du kan använda URL: en som webhook, enligt följande exempel:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Hur: Använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio-API-dokumentation][twilio_api].

## <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna för Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio HowTo guider och exempelkod][twilio_howtos]
* [Twilio Snabbstartsguider][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Tala med Twilio-stöd][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
