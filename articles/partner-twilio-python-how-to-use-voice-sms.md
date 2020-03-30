---
title: Hur man använder Twilio för röst och SMS (Python) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242190"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Så här använder du Twilio för röst- och SMS-funktioner i Python
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio API-tjänsten på Azure. Scenarierna som omfattas är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst och SMS i dina program finns i avsnittet [Nästa steg.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio driver framtiden för affärskommunikation, vilket gör det möjligt för utvecklare att bädda in röst, VoIP och meddelanden i program. De virtualiserar all infrastruktur som behövs i en molnbaserad, global miljö och exponerar den via Twilio-plattformen för kommunikation API. Program är enkla att bygga och skalbara. Njut av flexibilitet med priser för användningsbaserad betalning och dra nytta av molntillförlitlighet.

**Twilio Voice** gör det möjligt för dina program att ringa och ta emot telefonsamtal.
**Twilio SMS** gör det möjligt för din ansökan att skicka och ta emot textmeddelanden.
**Twilio Client** kan du ringa VoIP-samtal från vilken telefon, surfplatta eller webbläsare som helst och stöder WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prissättning och specialerbjudanden
Azure-kunder får ett [specialerbjudande][special_offer] $10 av Twilio Credit när du uppgraderar ditt Twilio-konto. Denna Twilio Credit kan tillämpas på alla Twilio användning ($ 10 kredit motsvarar att skicka så många som 1.000 SMS-meddelanden eller ta emot upp till 1000 inkommande röst minuter, beroende på var ditt telefonnummer och meddelande eller samtal destination). Lös in den här [Twilio-krediten][special_offer] och kom igång.

Twilio är en pay-as-you-go tjänst. Det finns inga inställningsavgifter och du kan avsluta ditt konto när som helst. Du kan hitta mer information på [Twilio Prissättning][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio API är ett RESTful API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. för en lista, se [Twilio API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio API är Twilio verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Verb
API använder Twilio verb; Till exempel instruerar ** &lt;&gt; Say-verbet** Twilio att hörbart leverera ett meddelande i ett samtal.

Följande är en lista över Twilio-verb. Lär dig mer om andra verb och funktioner via [dokumentationen för Twilio Markup Language][twiml].

* **Slå :&gt;Ansluter den som ringer till en annan telefon. &lt;**
* **Samla:&gt;Samlar numeriska siffror som anges på telefonens knappsats. &lt;**
* **Hangup&gt;: Avslutar ett &lt;** samtal.
* **Paus:&gt;Väntar tyst på ett visst antal &lt;** sekunder.
* **Spela upp&gt;: Spelar upp en ljudfil. &lt;**
* Kö : Lägg till i en kö med uppringare. ** &lt;&gt;**
* **Post:&gt;Registrerar den som ringer och returnerar en URL för en fil som innehåller &lt;** inspelningen.
* **Omdirigering&gt;: Överför kontroll över ett samtal eller SMS till TwiML på en annan webbadress. &lt;**
* **Avvisa:&gt;Avvisar ett inkommande samtal till ditt Twilio-nummer utan att fakturera &lt;** dig.
* **Säg:&gt;Konverterar text till tal som görs på ett &lt;** samtal.
* Sms : Skickar ett SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML (TwiML)
TwiML är en uppsättning XML-baserade instruktioner baserade på Twilio-verben som informerar Twilio om hur du bearbetar ett samtal eller SMS.

Som ett exempel skulle följande TwiML konvertera texten **Hello World** till tal.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

När ditt program anropar Twilio API är en av API-parametrarna url:en som returnerar TwiML-svaret. I utvecklingssyfte kan du använda url:er som tillhandahålls av Twilio för att ange de TwiML-svar som används av dina program. Du kan också vara värd för dina egna webbadresser för att `TwiMLResponse` skapa TwiML-svaren, och ett annat alternativ är att använda objektet.

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API:et finns i [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto registrerar du dig på [Prova Twilio.][try_twilio] Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett SID-konto och en autentiseringstoken. Båda kommer att behövas för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, hålla din autentiseringstoken säker. Ditt konto SID och autentiseringstoken kan visas i [Twilio-konsolen][twilio_console]i fälten **KONTO SID** respektive **AUTH TOKEN.**

## <a name="create-a-python-application"></a><a id="create_app"></a>Skapa ett Python-program
Ett Python-program som använder Tjänsten Twilio och körs i Azure skiljer sig inte från något annat Python-program som använder Tjänsten Twilio. Medan Twilio-tjänster är REST-baserade och kan anropas från Python på flera sätt, fokuserar den här artikeln på hur du använder Twilio-tjänster med [Twilio-bibliotek för Python från GitHub][twilio_python]. Mer information om hur du använder Twilio-biblioteket för Python finns i [https://www.twilio.com/docs/libraries/python][twilio_lib_docs].

Först [konfigurera en ny Azure Linux VM][azure_vm_setup] för att fungera som värd för ditt nya Python-webbprogram. När den virtuella datorn körs måste du exponera ditt program på en offentlig port enligt beskrivningen nedan.

### <a name="add-an-incoming-rule"></a>Lägga till en inkommande regel
  1. Gå till sidan [Network Security Group][azure_nsg].
  2. Välj den nätverkssäkerhetsgrupp som motsvarar din virtuella dator.
  3. Lägg till och **utgående regel** för **port 80**. Var noga med att tillåta inkommande från valfri adress.

### <a name="set-the-dns-name-label"></a>Ange DNS-namnetikett
  1. Gå till sidan [De offentliga IP-adresserna][azure_ips].
  2. Välj den offentliga IP som motsvarar din virtuella dator.
  3. Ange **DNS-namnetiketten** i avsnittet **Konfiguration.** I det här exemplet kommer det att se ut ungefär så här *din domän-etikett*.centralus.cloudapp.azure.com

När du har möjlighet att ansluta via SSH till den virtuella maskinen kan du installera Web Framework som du väljer (de två mest kända i Python är [Flask](http://flask.pocoo.org/) och [Django).](https://www.djangoproject.com) Du kan installera någon av `pip install` dem bara genom att köra kommandot.

Tänk på att vi har konfigurerat den virtuella datorn så att trafik endast tillåts på port 80. Så se till att konfigurera programmet för att använda den här porten.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program för att använda Twilio-bibliotek
Du kan konfigurera programmet så att det använder Twilio-biblioteket för Python på två sätt:

* Installera Twilio-biblioteket för Python som pippaket. Den kan installeras med följande kommandon:
   
        $ pip install twilio

    ELLER

* Ladda ner Twilio-biblioteket för Python[https://github.com/twilio/twilio-python][twilio_python]från GitHub ( ) och installera det så här:

        $ python setup.py install

När du har installerat Twilio-biblioteket för `import` Python kan du sedan det i dina Python-filer:

        import twilio

Mer information finns [i twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Så här: Ringa ett utgående samtal
Följande visar hur du ringer ett utgående samtal. Den här koden använder också en Twilio-plats för att returnera Twilio Markup Language (TwiML) svar. Ersätt dina värden med **from_number** och **to_number** telefonnummer och se till att du har verifierat **from_number** telefonnummer för ditt Twilio-konto innan du kör koden.

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

Som nämnts använder den här koden en Twilio-tillsedd plats för att returnera TwiML-svaret. Du kan istället använda din egen webbplats för att ge TwiML-svaret. Mer information finns i [Så här ger du TwiML-svar från din egen webbplats](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Så här skickar du ett SMS
Följande visar hur du skickar ett `TwilioRestClient` SMS-meddelande med hjälp av klassen. Det **from_number** numret tillhandahålls av Twilio för utvärderingskonton för att skicka SMS-meddelanden. Det **to_number** numret måste verifieras för ditt Twilio-konto innan koden körs.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Så här: Ge TwiML-svar från din egen webbplats
När ditt program startar ett anrop till Twilio API skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. I exemplet ovan används url:en [https://twimlets.com/message][twimlet_message_url]som tillhandahålls av Twilio . (Medan TwiML är utformad för användning av Twilio kan du visa den i din webbläsare. Klicka till [https://twimlets.com/message][twimlet_message_url] exempel för `<Response>` att se ett tomt element. Som ett annat [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] exempel `<Response>` klickar du på `<Say>` om du vill visa ett element som innehåller ett element.)

I stället för att förlita sig på den Twilio-tillsentade webbadressen kan du skapa en egen webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar XML-svar. Det här avsnittet förutsätter att du kommer att använda Python för att skapa TwiML.

Följande exempel kommer att mata ut en TwiML svar som säger **Hello World** på samtalet.

Med kolv:

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

Som du kan se i exemplet ovan är TwiML-svaret helt enkelt ett XML-dokument. Twilio-biblioteket för Python innehåller klasser som genererar TwiML åt dig. Exemplet nedan ger motsvarande svar som visas `twiml` ovan, men använder modulen i Twilio-biblioteket för Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Mer information om TwiML [https://www.twilio.com/docs/api/twiml][twiml_reference]finns i .

När du har konfigurerat pythonprogrammet för att ge TwiML-svar använder du `client.calls.create` URL:en för programmet när URL:en skickas till metoden. Om du till exempel har ett webbprogram med namnet **MyTwiML** distribuerat till en Azure-värdtjänst kan du använda url:en som webhook som visas i följande exempel:

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

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Så här använder du ytterligare Twilio-tjänster
Förutom de exempel som visas här erbjuder Twilio webbaserade API:er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio API-dokumentationen][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten följer du dessa länkar för att lära dig mer:

* [Twilio säkerhetsriktlinjer][twilio_security_guidelines]
* [Twilio HowTo Guider och exempelkod][twilio_howtos]
* [Twilio Snabbstart Tutorials][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio Support][twilio_support]

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
