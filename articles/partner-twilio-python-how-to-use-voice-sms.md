---
title: "Hur du använder Twilio för röst- och SMS (Python) | Microsoft Docs"
description: "Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i Python."
services: 
documentationcenter: python
author: devinrader
manager: twilio
editor: 
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Hur du använder Twilio för röst- och SMS-funktioner i Python
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio-API-tjänsten på Azure. Scenarier som tas upp inkluderar att ringa och ett kort meddelande (SMS Service)-meddelande skickas. Mer information om Twilio och använder röst- och SMS i dina program finns i [nästa steg](#NextSteps) avsnitt.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio startar framtiden för kommunikation med, så att utvecklare kan bädda in röst, VoIP och meddelanden i program. De virtualisera alla infrastruktur som behövs i en molnbaserad, globala miljö, exponera den via Twilio kommunikation API-plattformen. Programmen är enkla att skapa och skalbara. Få flexibilitet med lön-som-dig gå priser och dra nytta av molnet tillförlitlighet.

**Twilio röst** gör att dina program att göra och ta emot telefonsamtal.
**Twilio SMS** gör att programmet kan skicka och ta emot textmeddelanden.
**Twilio klienten** kan du se VoIP-anrop från telefon, surfplatta eller webbläsare och stöder WebRTC.

## <a id="Pricing"></a>Priser för Twilio och specialerbjudanden
Azure-kunder får en [specialerbjudande] [ special_offer] $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-kredit kan tillämpas på alla Twilio-användning ($10 kredit motsvarar upp till 1 000 SMS-meddelanden skickas eller tas emot upp till 1 000 inkommande röst minuter beroende på platsen för ditt mål för telefon och meddelandet eller samtal). Lösa detta [Twilio kredit] [ special_offer] och komma igång.

Twilio är en betalning per användning. Det finns inga avgifter för installation och du kan stänga ditt konto när som helst. Du hittar mer information i [Twilio priser][twilio_pricing].

## <a id="Concepts"></a>Begrepp
Twilio-API är en RESTful-API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista, se [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et tillämpar Twilio verb; till exempel den  **&lt;säg&gt;**  verb instruerar Twilio att hörbart leverera ett meddelande på ett samtal.

Följande är en lista över Twilio verb. Lär dig mer om vilka verb och funktioner via [Markup Language Twilio dokumentationen][twiml].

* **&lt;Ring&gt;**: anroparen ansluter till en annan telefon.
* **&lt;Samla in&gt;**: samlar in siffror som anges på telefon tangentbordet.
* **&lt;Koppla ned&gt;**: slutar ett anrop.
* **&lt;Pausa&gt;**: tyst väntar på ett angivet antal sekunder.
* **&lt;Spela upp&gt;**: spelar en ljudfil.
* **&lt;Kön&gt;**: lägga till den till en kö med anropare.
* **&lt;Posten&gt;**: registrerar röst anroparen och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överför kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: avvisar ett inkommande samtal till Twilio-nummer utan fakturering du.
* **&lt;Säg&gt;**: konverterar text till tal, som görs på ett samtal.
* **&lt;SMS&gt;**: skickar ett SMS-meddelande.

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner baserat på de Twilio-verb som informerar Twilio för att behandla ett samtal eller SMS.

Följande TwiML skulle exempelvis Omvandla text **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML-svar. Du kan använda Twilio-tillhandahållna URL: er för utveckling, ange TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den `TwiMLResponse` objekt.

Läs mer om Twilio verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan logga på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett SID-konto och en token för autentisering. Både behövs för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, skydda din token för autentisering. Ditt konto SID och autentiseringstoken kan visas i den [Twilio konsolen][twilio_console], i fälten med etiketten **konto SID** och **AUTH TOKEN**respektive.

## <a id="create_app"></a>Skapa en Python-program
En Python-program som använder tjänsten Twilio och körs i Azure är inte annorlunda än andra Python-program som använder Twilio-tjänsten. När Twilio är REST-baserad och kan anropas från Python på flera sätt, den här artikeln fokuserar på hur du använder Twilio-tjänster med [Twilio-biblioteket för Python från GitHub][twilio_python]. Mer information om hur du använder Twilio-biblioteket för Python finns [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Första [ställa in en ny Azure Linux VM] [azure_vm_setup] för att fungera som värd för din nya Python-webbprogram. När den virtuella datorn körs, måste du exponera dina program på en offentlig port som beskrivs nedan.

### <a name="add-an-incoming-rule"></a>Lägg till en inkommande regel
  1. Gå till sidan [Nätverkssäkerhetsgruppen] [azure_nsg].
  2. Välj Nätverkssäkerhetsgruppen som överensstämmer med den virtuella datorn.
  3. Lägg till och **utgående regel** för **port 80**. Se till att tillåta inkommande från alla adresser.

### <a name="set-the-dns-name-label"></a>Ange DNS-namnetikett
  1. Gå till sidan [den offentliga IP-postadresser] [azure_ips].
  2. Välj offentlig IP-Adressen som correspends med den virtuella datorn.
  3. Ange den **DNS-namnetikett** i den **Configuration** avsnitt. I det här exemplet den ser ut ungefär så här *din domän etikett*. centralus.cloudapp.azure.com

När du ska kunna ansluta via SSH till den virtuella datorn kan du installera webbramverk önskat (de mest kända i Python som två [Flask](http://flask.pocoo.org/) och [Django](https://www.djangoproject.com)). Du kan installera någon av dem genom att köra den `pip install` kommando.

Tänk på att vi har konfigurerat den virtuella datorn för att tillåta trafik på port 80. Så se till att konfigurera programmet att använda den här porten.

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio-bibliotek
Du kan konfigurera tillämpningsprogrammet till att använda Twilio-biblioteket för Python på två sätt:

* Installera Twilio-biblioteket för Python som Pip-paket. Den kan installeras med följande kommandon:
   
        $ pip install twilio

    ELLER

* Hämta Twilio-biblioteket för Python från GitHub ([https://github.com/twilio/twilio-python][twilio_python]) och installera den så här:

        $ python setup.py install

När du har installerat Twilio-biblioteket för Python, kan du sedan `import` i Python-filer:

        import twilio

Mer information finns i [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Så här: göra en utgående anrop
Nedan visas hur du skapar ett utgående anrop. Den här koden används även en webbplats med angivna Twilio för att returnera svaret Twilio Markup Language (TwiML). Ersätt värdena för den **from_number** och **to_number** telefonnummer och se till att du har bekräftat att den **from_number** telefonnummer för ditt Twilio-konto innan du kör koden.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Som tidigare nämnts kan används den här koden en angivna Twilio plats för att returnera TwiML svaret. Du kan använda din egen webbplats i stället för att tillhandahålla TwiML svaret; Mer information finns i [hur du ger TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Så här: skicka ett SMS-meddelande
Här visas hur du skickar ett SMS-meddelande med den `TwilioRestClient` klass. Den **from_number** nummer tillhandahålls av Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **to_number** numret måste verifieras för ditt Twilio-konto innan du kör koden.

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

## <a id="howto_provide_twiml_responses"></a>Så här: Ange TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API, skickar Twilio din begäran till en URL som förväntas returnera ett TwiML svar. I exemplet ovan används URL: en som tillhandahålls av Twilio [http://twimlets.com/message][twimlet_message_url]. (När TwiML är avsedd för användning av Twilio, kan du visa den i webbläsaren. Klicka till exempel [http://twimlets.com/message] [ twimlet_message_url] att se en tom `<Response>` element; Klicka på ett annat exempel är [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] att se en `<Response>` element som innehåller en `<Say>` element.)

I stället för på den angivna Twilio URL, kan du skapa din egen webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på alla språk som returnerar XML-svar; Det här avsnittet förutsätter att du kommer att använda Python för att skapa TwiML.

I följande exempel kommer utdata TwiML svar som säger **Hello World** på anropet.

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

Du ser i exemplet ovan, är TwiML svaret bara ett XML-dokument. Twilio-bibliotek för Python innehåller klasser som ska generera TwiML för dig. Exemplet nedan skapar motsvarande svaret som ovan, men använder den `twiml` modul i Twilio-biblioteket för Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Läs mer om TwiML [https://www.twilio.com/docs/api/twiml][twiml_reference].

När du har Python programmet ställts in för att ange TwiML svar använder URL för programmet som URL som skickades till den `client.calls.create` metoden. Till exempel om du har ett webbprogram med namnet **MyTwiML** distribueras till en Azure-värdtjänsten tjänsten, kan du använda webbadressen som webhook som visas i följande exempel:

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

## <a id="AdditionalServices"></a>Så här: använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare funktioner för Twilio från Azure-program. Fullständig information finns i [Twilio-API-dokumentationen][twilio_api].

## <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio ta guider och exempelkod][twilio_howtos]
* [Twilio snabbstarten självstudier][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-Support][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
