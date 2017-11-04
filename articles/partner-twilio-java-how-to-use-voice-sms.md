---
title: "Hur du använder Twilio för röst- och SMS (Java) | Microsoft Docs"
description: "Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i Java."
services: 
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
ms.openlocfilehash: 5a1b2ffa160a31b639605242b651dc8d14e7a01b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Hur du använder Twilio för röst- och SMS-funktioner i Java
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio-API-tjänsten på Azure. Scenarier som tas upp inkluderar att ringa och ett kort meddelande (SMS Service)-meddelande skickas. Mer information om Twilio och använder röst- och SMS i dina program finns i [nästa steg](#NextSteps) avsnitt.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio är en telefoni webbtjänst API som gör att du kan använda din befintliga web språk och kunskaper för att skapa röst- och SMS-program. Twilio är en från tredje part (inte en funktion i Azure och inte en Microsoft-produkt).

**Twilio röst** gör att dina program att göra och ta emot telefonsamtal. **Twilio SMS** gör att dina program att göra och ta emot SMS-meddelanden. **Twilio klienten** kan ditt program genom att aktivera röstkommunikation via befintliga Internet-anslutningar, inklusive mobila anslutningar.

## <a id="Pricing"></a>Priser för Twilio och specialerbjudanden
Information om priser Twilio finns på [Twilio priser][twilio_pricing]. Azure-kunder får en [specialerbjudande][special_offer]: en kredit på 1000 texter eller 1000 inkommande minuter. Om du vill registrera dig för det här erbjudandet eller få mer information, besök [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Begrepp
Twilio-API är en RESTful-API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista, se [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et tillämpar Twilio verb; till exempel den  **&lt;säg&gt;**  verb instruerar Twilio att hörbart leverera ett meddelande på ett samtal.

Följande är en lista över Twilio verb.

* **&lt;Ring&gt;**: anroparen ansluter till en annan telefon.
* **&lt;Samla in&gt;**: samlar in siffror som anges på telefon tangentbordet.
* **&lt;Koppla ned&gt;**: slutar ett anrop.
* **&lt;Spela upp&gt;**: spelar en ljudfil.
* **&lt;Kön&gt;**: lägga till den till en kö med anropare.
* **&lt;Pausa&gt;**: tyst väntar på ett angivet antal sekunder.
* **&lt;Posten&gt;**: registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt;Omdirigera&gt;**: Överför kontroll över ett samtal eller SMS till TwiML på en annan URL.
* **&lt;Avvisa&gt;**: avvisar ett inkommande samtal till Twilio-nummer utan fakturering du.
* **&lt;Säg&gt;**: konverterar text till tal, som görs på ett samtal.
* **&lt;SMS&gt;**: skickar ett SMS-meddelande.

### <a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner baserat på de Twilio-verb som informerar Twilio för att behandla ett samtal eller SMS.

Följande TwiML skulle exempelvis Omvandla text **Hello World!** till tal.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML-svar. Du kan använda Twilio-tillhandahållna URL: er för utveckling, ange TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den **TwiMLResponse** objekt.

Läs mer om Twilio verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan logga på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och ett token för autentisering. Både behövs för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, skydda din token för autentisering. Ditt konto-ID och autentisering token kan visas på den [Twilio konsolen][twilio_console], i fälten med etiketten **konto SID** och **AUTH TOKEN**respektive.

## <a id="create_app"></a>Skapa ett Java-program
1. Hämta Twilio JAR och lägga till den i din Java byggsökväg och WAR-distribution för sammansättningen. Vid [https://github.com/twilio/twilio-java][twilio_java], du kan hämta GitHub-datakällor och skapa egna JAR eller hämta en förskapad JAR (med eller utan beroenden).
2. Se till att din JDK **cacerts** keystore innehåller Equifax certifikatutfärdare för säkra-certifikat med MD5 fingeravtryck 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (serienumret är 35:DE:F4:CF och SHA1 fingeravtrycket D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Det här är certifikatet certifikatutfärdarens certifikat för den [https://api.twilio.com] [ twilio_api_service] -tjänsten som anropas när du använder Twilio APIs. Information om att säkerställa ditt JDK **cacerts** keystore innehåller rätt CA-certifikatet, se [att lägga till ett certifikat i certifikatarkivet för Java CA][add_ca_cert].

Detaljerade anvisningar för att använda Twilio-klientbibliotek för Java finns på [hur du gör ett telefonsamtal med Twilio i ett Java-program på Azure][howto_phonecall_java].

## <a id="configure_app"></a>Konfigurera programmet att använda Twilio-bibliotek
I din kod kan du lägga till **importera** instruktioner överst i källfilerna för Twilio paket eller klasser som du vill använda i ditt program.

För Java-källfiler:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java-sida (JSP) källfiler:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Beroende på vilka Twilio paket eller klasser som du vill använda din **importera** instruktioner kan vara olika.

## <a id="howto_make_call"></a>Så här: göra en utgående anrop
Följande visar hur du gör en utgående Ring upp med den **anropa** klass. Den här koden används även en webbplats med angivna Twilio för att returnera svaret Twilio Markup Language (TwiML). Ersätt värdena för den **från** och **till** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("http://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Mer information om de parametrar som skickas till den **Call.creator** -metoden finns [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Som tidigare nämnts kan används den här koden en angivna Twilio plats för att returnera TwiML svaret. Du kan använda din egen webbplats i stället för att tillhandahålla TwiML svaret; Mer information finns i [så ange TwiML svar i ett Java-program på Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Så här: skicka ett SMS-meddelande
Här visas hur du skickar ett SMS-meddelande med den **meddelandet** klass. Den **från** nummer, **4155992671**, tillhandahålls av Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **till** numret måste verifieras för ditt Twilio-konto innan du kör koden.

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

Mer information om de parametrar som skickas till den **Message.creator** -metoden finns [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Så här: Ange TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API, till exempel den **CallCreator.create** metoden Twilio skicka din begäran till en URL som förväntas returnera ett TwiML svar. I exemplet ovan används URL: en som tillhandahålls av Twilio [http://twimlets.com/message][twimlet_message_url]. (När TwiML är avsedd för användning av webbtjänster, du kan visa TwiML i webbläsaren. Klicka till exempel [http://twimlets.com/message] [ twimlet_message_url] att se en tom  **&lt;svar&gt;**  element; Klicka på ett annat exempel är [http://twimlets.com/message?Message%5B0%5D=Hello%20World%21] [ twimlet_message_url_hello_world] att se en  **&lt;svar&gt;**  element som innehåller en  **&lt;säg&gt;**  element.)

I stället för på den angivna Twilio URL, kan du skapa din egen URL: en webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på alla språk som returnerar HTTP-svar Det här avsnittet förutsätter att du ska vara värd för en URL i en JSP-sida.

Följande JSP-sidan resulterar i ett TwiML-svar som säger **Hello World!** i det här anropet.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Följande JSP-sidan resulterar i ett TwiML-svar som säger text har flera pausar och anger information om Twilio-API-versionen och Azure rollnamnet.

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

Den **ApiVersion** parametern är tillgängliga i Twilio röst begäranden (SMS begäranden inte). Tillgängliga parametrarna för Twilio röst- och SMS-begäranden finns <https://www.twilio.com/docs/api/twiml/twilio_request> och <https://www.twilio.com/docs/api/twiml/sms/twilio_request>respektive. Den **RoleName** miljövariabeln är tillgänglig som en del av Azure-distribution. (Om du vill lägga till anpassade miljövariabler så att de kan öppnas från **System.getenv**, se miljövariablerna avsnittet på [diverse konfigurationsinställningar för rollen][misc_role_config_settings].)

När du har din JSP-sida som ställts in för att ange TwiML svar använder URL till sidan JSP som URL som skickades till den **Call.creator** metod. Till exempel om du har ett webbprogram med namnet MyTwiML som distribueras till en Azure värdtjänsten och namnet på sidan JSP är mytwiml.jsp, URL: en kan skickas till **Call.creator** enligt följande:

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

Mer information om hur du använder Twilio i Azure med Java finns [hur du gör ett telefonsamtal med Twilio i ett Java-program på Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Så här: använda ytterligare Twilio-tjänster
Förutom de exempel som visas här, erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare funktioner för Twilio från Azure-program. Fullständig information finns i [Twilio-API-dokumentationen][twilio_api_documentation].

## <a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar om du vill veta mer:

* [Riktlinjer för Twilio-säkerhet][twilio_security_guidelines]
* [Twilio ta och exempelkod][twilio_howtos]
* [Twilio snabbstarten självstudier][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-Support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/docs
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
