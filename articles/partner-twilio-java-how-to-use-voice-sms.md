---
title: Hur man använder Twilio för röst och SMS (Java) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637215"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Hur man använder Twilio för röst- och SMS-funktioner i Java
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio API-tjänsten på Azure. Scenarierna som omfattas är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst och SMS i dina program finns i avsnittet [Nästa steg.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio är en telefoni webbtjänst API som låter dig använda dina befintliga webbspråk och färdigheter för att bygga röst-och SMS-program. Twilio är en tjänst från tredje part (inte en Azure-funktion och inte en Microsoft-produkt).

**Twilio Voice** gör det möjligt för dina program att ringa och ta emot telefonsamtal. **Twilio SMS** gör att dina program kan göra och ta emot SMS.Twilio SMS allows your applications to make and receive SMS messages. **Twilio Client** gör att dina program kan aktivera röstkommunikation med befintliga Internet-anslutningar, inklusive mobila anslutningar.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prissättning och specialerbjudanden
Information om Twilio prissättning finns på [Twilio Prissättning][twilio_pricing]. Azure-kunder får ett [specialerbjudande:][special_offer]en gratis kredit på 1000 texter eller 1000 inkommande minuter. För att registrera dig för detta erbjudande [https://ahoy.twilio.com/azure][special_offer]eller få mer information, besök .

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio API är ett RESTful API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. för en lista, se [Twilio API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio API är Twilio verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Verb
API använder Twilio verb; Till exempel instruerar ** &lt;&gt; Say-verbet** Twilio att hörbart leverera ett meddelande i ett samtal.

Följande är en lista över Twilio-verb.

* **Slå :&gt;Ansluter den som ringer till en annan telefon. &lt;**
* **Samla:&gt;Samlar numeriska siffror som anges på telefonens knappsats. &lt;**
* **Hangup&gt;: Avslutar ett &lt;** samtal.
* **Spela upp&gt;: Spelar upp en ljudfil. &lt;**
* Kö : Lägg till i en kö med uppringare. ** &lt;&gt;**
* **Paus:&gt;Väntar tyst på ett visst antal &lt;** sekunder.
* **Post:&gt;Spelar in den som ringer röst och returnerar en URL för en fil som innehåller &lt;** inspelningen.
* **Omdirigering&gt;: Överför kontroll över ett samtal eller SMS till TwiML på en annan webbadress. &lt;**
* **Avvisa:&gt;Avvisar ett inkommande samtal till ditt Twilio-nummer utan att fakturera &lt;** dig.
* **Säg:&gt;Konverterar text till tal som görs på ett &lt;** samtal.
* Sms : Skickar ett SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML (TwiML)
TwiML är en uppsättning XML-baserade instruktioner baserade på Twilio-verben som informerar Twilio om hur du bearbetar ett samtal eller SMS.

Som ett exempel skulle följande TwiML konvertera texten **Hello World!** till tal.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

När ditt program anropar Twilio API är en av API-parametrarna url:en som returnerar TwiML-svaret. I utvecklingssyfte kan du använda url:er som tillhandahålls av Twilio för att ange de TwiML-svar som används av dina program. Du kan också vara värd för dina egna webbadresser för att skapa TwiML-svaren, och ett annat alternativ är att använda **TwiMLResponse-objektet.**

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API:et finns i [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto registrerar du dig på [Try Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och en autentiseringstoken. Båda kommer att behövas för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, hålla din autentiseringstoken säker. Ditt konto-ID och autentiseringstoken kan visas på [Twilio-konsolen][twilio_console]i fälten **KONTO SID** respektive **AUTH TOKEN.**

## <a name="create-a-java-application"></a><a id="create_app"></a>Skapa ett Java-program
1. Skaffa Twilio-JAR:en och lägg till den i java-byggsökvägen och din WAR-distributionsuppsättningsuppsättning. På [https://github.com/twilio/twilio-java][twilio_java]kan du hämta GitHub-källorna och skapa din egen JAR, eller ladda ner en färdig JAR (med eller utan beroenden).
2. Kontrollera att ditt JDK:s **cacerts-keystore** innehåller Equifax Secure Certificate Authority-certifikat med MD5-fingeravtryck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienumret är 35:DE:F4:CF och SHA1-fingeravtrycket är D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Det här är certifikatutfärdarcertifikatet [https://api.twilio.com][twilio_api_service] för tjänsten, som anropas när du använder Twilio API:er. Information om hur du säkerställer att din JDK:s **cacerts-nyckelbutik** innehåller rätt certifikatutfärdare finns i [Lägga till ett certifikat i Java CA Certificate Store][add_ca_cert].

Detaljerade instruktioner för hur du använder Twilio-klientbiblioteket för Java finns på Så här ringer du [ett telefonsamtal med Twilio i ett Java-program på Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program för att använda Twilio-bibliotek
I koden kan du lägga till **importsatser** högst upp i källfilerna för de Twilio-paket eller klasser som du vill använda i programmet.

För Java-källfiler:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

För JSP-källfiler (Java Server Page):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Beroende på vilka Twilio-paket eller klasser du vill använda kan **importsatserna** vara olika.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Så här: Ringa ett utgående samtal
Följande visar hur du ringer ett utgående samtal med klassen **Samtal.** Den här koden använder också en Twilio-plats för att returnera Twilio Markup Language (TwiML) svar. Ersätt dina värden med **från-** och **till-telefonnummer** och se till att du verifierar **från-telefonnumret** för ditt Twilio-konto innan du kör koden.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Mer information om parametrarna som skickas in till [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]metoden **Call.creator** finns i .

Som nämnts använder den här koden en Twilio-tillsedd plats för att returnera TwiML-svaret. Du kan istället använda din egen webbplats för att ge TwiML-svaret. Mer information finns i [Så här ger du TwiML-svar i ett Java-program på Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Så här skickar du ett SMS
Följande visar hur du skickar ett SMS-meddelande med klassen **Meddelande.** **Från** nummer, **4155992671**, tillhandahålls av Twilio för rättegång konton för att skicka SMS-meddelanden. **Till-numret** måste verifieras för ditt Twilio-konto innan koden körs.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Mer information om parametrarna som skickas in till [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]metoden **Message.creator** finns i .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Så här: Ge TwiML-svar från din egen webbplats
När ditt program initierar ett anrop till Twilio API, till exempel via **metoden CallCreator.create,** skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. I exemplet ovan används url:en [https://twimlets.com/message][twimlet_message_url]som tillhandahålls av Twilio . (TwiML är utformat för att användas av webbtjänster, men du kan visa TwiML i webbläsaren. Klicka till [https://twimlets.com/message][twimlet_message_url] exempel för att se ett tomt ** &lt;&gt; svarselement.** Som ett annat [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] exempel klickar du på om du vill visa ett ** &lt;&gt; svarselement** som innehåller ett ** &lt;Say-element.)&gt; **

I stället för att förlita sig på den Twilio-tillsentade webbadressen kan du skapa en egen URL-webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar HTTP-svar. Detta ämne förutsätter att du kommer att vara värd för webbadressen i en JSP sida.

Följande JSP sida resulterar i en TwiML svar som säger **Hello World!** på samtalet.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Följande JSP-sida resulterar i ett TwiML-svar som säger viss text, har flera pauser och säger information om Twilio API-versionen och Azure-rollnamnet.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

**Parametern ApiVersion** är tillgänglig i Twilio-röstbegäranden (inte SMS-begäranden). Om du vill se de tillgängliga parametrarna för <https://www.twilio.com/docs/api/twiml/twilio_request> <https://www.twilio.com/docs/api/twiml/sms/twilio_request>begäranden för Twilio-röst- och SMS-begäranden finns i respektive . **RoleName-miljövariabeln** är tillgänglig som en del av en Azure-distribution. (Om du vill lägga till anpassade miljövariabler så att de kan hämtas från **System.getenv**läser du avsnittet miljövariabler på Inställningar för diverse [rollkonfiguration .)][misc_role_config_settings]

När du har konfigurerat din JSP-sida för att ge TwiML-svar använder du webbadressen till JSP-sidan när webbadressen skickades till **metoden Call.creator.** Om du till exempel har ett webbprogram med namnet MyTwiML distribuerat till en Azure-värdtjänst och namnet på JSP-sidan är mytwiml.jsp, kan url:en skickas till **Call.creator** enligt följande:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Ett annat alternativ för att svara med TwiML är via **VoiceResponse-klassen,** som finns i **paketet com.twilio.twiml.**

Mer information om hur du använder Twilio i Azure med Java finns i Så här ringer du [ett telefonsamtal med Twilio i ett Java-program på Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Så här använder du ytterligare Twilio-tjänster
Förutom de exempel som visas här erbjuder Twilio webbaserade API:er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio API-dokumentationen][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Tjänsten Twilio följer du de här länkarna för att lära dig mer:

* [Twilio säkerhetsriktlinjer][twilio_security_guidelines]
* [Twilio HowTo's och exempelkod][twilio_howtos]
* [Twilio Snabbstart Tutorials][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio Support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
