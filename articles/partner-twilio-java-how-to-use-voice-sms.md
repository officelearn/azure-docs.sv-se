---
title: Använda Twilio för röst- och SMS (Java) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i Java.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 386b4b8440c74f6599e7147996b5843ea0f67e68
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423372"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Använda Twilio för röst- och SMS-funktioner i Java
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med Twilio-API-tjänsten på Azure. Scenarier som omfattas är ringa ett samtal och skicka ett meddelande om tjänsten SMS (Short Message). Mer information om Twilio och använda röst och SMS i dina program finns i den [nästa steg](#NextSteps) avsnittet.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio är en telefoni webbtjänst-API som kan du använda dina befintliga webb-språk och färdigheter för att skapa röst och SMS-program. Twilio är en tjänst från tredje part (inte en funktion i Azure och inte en Microsoft-produkt).

**Twilio-röst** kan dina program kan ringa och ta emot samtal. **Twilio-SMS** kan dina program kan ringa och ta emot SMS. **Twilio-klienten** gör att dina program för att möjliggöra röstkommunikation via befintliga Internetanslutningar, även mobila anslutningar.

## <a id="Pricing"></a>Twilio priser och specialerbjudanden
Information om Twilio priser finns på [Twilio priser][twilio_pricing]. Azure-kunder får en [specialerbjudande][special_offer]: en kostnadsfri kredit på 1000 texter eller 1000 inkommande minuter. Om du vill registrera dig för erbjudandet eller få mer information, besök [ https://ahoy.twilio.com/azure ] [ special_offer].

## <a id="Concepts"></a>Begrepp
Twilio-API är ett RESTful-API med röst och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista i [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et gör använda Twilio verb; till exempel den **&lt;Say&gt;** verb instruerar Twilio hörbart uppfyller ett meddelande på ett anrop.

Här följer en lista över Twilio-verb.

* **&lt;Uppringning&gt;**: anroparen ansluter till en annan telefon.
* **&lt;Samla in&gt;**: samlar in siffror som anges på telefon-tangentbordet.
* **&lt;Koppla ned&gt;**: slutar ett anrop.
* **&lt;Spela upp&gt;**: spelar upp en ljudfil.
* **&lt;Kön&gt;**: lägga till den till en kö med anropare.
* **&lt;Pausa&gt;**: tyst väntar på ett angivet antal sekunder.
* **&lt;Post&gt;**: registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överför kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: avvisar ett inkommande samtal till din Twilio-nummer utan fakturering du.
* **&lt;Anta att&gt;**: konverterar text till tal som görs på ett anrop.
* **&lt;SMS&gt;**: skickar ett SMS-meddelande.

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktionerna baserat på de Twilio-verb som meddelar Twilio av behandla ett samtal eller SMS.

Till exempel följande TwiML konverteras texten **Hello World!** till tal.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML svaret. Du kan använda Twilio-angivna URL: er för utveckling, för att ge TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den **TwiMLResponse** objekt.

Läs mer om Twilio-verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns i [Twilio-API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att få ett Twilio-konto kan du registrera dig på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du en konto-ID och en autentiseringstoken. Båda krävs för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto måste skydda din autentiseringstoken. Ditt konto-ID och autentiseringsnyckel token kan visas på den [Twilio-konsolen][twilio_console], i fälten med etiketten **konto-SID** och **AUTENTISERINGSTOKEN**respektive.

## <a id="create_app"></a>Skapa en Java-program
1. Hämta Twilio JAR och lägga till den i din Java byggsökväg och WAR distribution sammansättningen. Vid [ https://github.com/twilio/twilio-java ] [ twilio_java], du kan hämta GitHub-källor och skapa dina egna JAR eller ladda ned en färdiga JAR-filen (med eller utan beroenden).
2. Se till att din JDK **cacerts** keystore innehåller Equifax certifikatutfärdare för säkra-certifikat med MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienumret är 35:DE:F4:CF och SHA1 fingeravtryck är D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Det här är certificate authority (CA)-certifikat för den [ https://api.twilio.com ] [ twilio_api_service] -tjänsten, som anropas när du använder Twilio APIs. Information om att se till att din JDK **cacerts** keystore innehåller rätt CA-certifikat, se [att lägga till ett certifikat i Java CA Certificate Store][add_ca_cert].

Detaljerade instruktioner för att använda Twilio-klientbibliotek för Java finns på [hur du får ett telefonsamtal med Twilio i Java-program på Azure][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio-bibliotek
I din kod kan du lägga till **importera** uttryck överst i källfilerna för Twilio-paket eller klasser som du vill använda i ditt program.

För källfiler för Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

För källfiler för sidan för Java-Server (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Beroende på vilken Twilio-paket eller klasser som du vill använda din **importera** uttryck kan vara olika.

## <a id="howto_make_call"></a>Så här: göra ett externt anrop
Följande visar hur du gör en utgående anropa med hjälp av den **anropa** klass. Den här koden använder också en Twilio-angivna plats för att returnera Twilio Markup Language (TwiML)-svaret. Ersätt värdena för den **från** och **till** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

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

Mer information om de parametrar som skickas till den **Call.creator** metod, se [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Som tidigare nämnts använder den här koden en Twilio-angivna plats för att returnera TwiML-svar. Du kan i stället använda en egen plats för att tillhandahålla TwiML svaret; Mer information finns i [hur du ger TwiML svar i ett Java-program på Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Så här: skicka ett SMS-meddelande
Följande visar hur du skicka ett SMS-meddelande med den **meddelande** klass. Den **från** tal, **4155992671**, kommer från Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **till** tal måste verifieras för ditt Twilio-konto innan du kör koden.

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

Mer information om de parametrar som skickas till den **Message.creator** metod, se [ https://www.twilio.com/docs/api/rest/sending-sms ] [ twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Så här: Ange TwiML svar från din egen webbplats
När programmet startar ett anrop till Twilio-API, till exempel den **CallCreator.create** metoden Twilio skicka din begäran till en URL som kan förväntas returnera ett TwiML svar. I exemplet ovan används URL: en med Twilio-angivna [ https://twimlets.com/message ] [ twimlet_message_url]. (Medan TwiML är avsedd för användning av webbtjänster, du kan visa TwiML i din webbläsare. Klicka till exempel [ https://twimlets.com/message ] [ twimlet_message_url] att se en tom **&lt;svar&gt;** elementet; Klicka på ett annat exempel är [ https://twimlets.com/message?Message%5B0%5D=Hello%20World%21 ] [ twimlet_message_url_hello_world] att se en **&lt;svar&gt;** element som innehåller en **&lt;Say&gt;** element.)

I stället för en Twilio-angivna URL: en, kan du skapa en egen URL-plats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar HTTP-svar; Det här avsnittet förutsätter att du ska värd för URL: en i en JSP-sida.

Följande JSP-sida som resulterar i ett TwiML-svar som säger **Hello World!** på anropet.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Följande JSP-sida resulterar i ett TwiML-svar som säger text har flera pauser och säger information om Twilio-API-versionen och namnet på Azure.

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

Den **ApiVersion** parametern är tillgängliga i Twilio röstförfrågningar (inte SMS begäranden). Om du vill se tillgängliga parametrarna för Twilio-röst och SMS-begäranden, se <https://www.twilio.com/docs/api/twiml/twilio_request> och <https://www.twilio.com/docs/api/twiml/sms/twilio_request>respektive. Den **RoleName** miljövariabeln är tillgänglig som en del av en Azure-distribution. (Om du vill lägga till anpassade miljövariabler, så att de kan hämtas från **System.getenv**, finns i miljövariablerna som avsnittet på [övriga konfigurationsinställningar för rollen] [ misc_role_config_settings].)

När du har din JSP-sida som ställts in för att ge TwiML svar använder URL: en för JSP-sida som URL som skickades till den **Call.creator** metod. Till exempel om du har en webbapp med namnet MyTwiML som distribuerats till en Azure den värdbaserade tjänsten, och namnet på JSP-sida är mytwiml.jsp, URL: en kan skickas till **Call.creator** enligt följande:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Ett annat alternativ för att svara med TwiML är den **VoiceResponse** klassen, som är tillgängliga i den **com.twilio.twiml** paketet.

Mer information om hur du använder Twilio i Azure med Java finns i [hur du får ett telefonsamtal med Twilio i Java-program på Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Så här: använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [Twilio-API-dokumentation][twilio_api_documentation].

## <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna för Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio HowTo och exempelkod][twilio_howtos]
* [Twilio Snabbstartsguider][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Tala med Twilio-stöd][twilio_support]

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
