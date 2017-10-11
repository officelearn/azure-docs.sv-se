---
title: "Hur du använder Twilio för röst- och SMS (.NET) | Microsoft Docs"
description: "Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i .NET."
services: 
documentationcenter: .net
author: devinrader
manager: twilio
editor: 
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Hur du använder Twilio för röst- och SMS-funktioner från Azure
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio-API-tjänsten på Azure. Scenarier som tas upp inkluderar att ringa och ett kort meddelande (SMS Service)-meddelande skickas. Mer information om Twilio och använder röst- och SMS i dina program finns i [nästa steg](#NextSteps) avsnitt.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio startar framtiden för kommunikation med, så att utvecklare kan bädda in röst, VoIP och meddelanden i program. De virtualisera alla infrastruktur som behövs i en molnbaserad, globala miljö, exponera den via Twilio kommunikation API-plattformen. Programmen är enkla att skapa och skalbara. Utnyttja flexibilitet med betala per användning priser och dra nytta av molnet tillförlitlighet.

**Twilio röst** gör att dina program att göra och ta emot telefonsamtal. **Twilio SMS** kan skicka och ta emot SMS-meddelanden-program. **Twilio klienten** kan du se VoIP-anrop från telefon, surfplatta eller webbläsare och stöder WebRTC.

## <a id="Pricing"></a>Priser för Twilio och specialerbjudanden
Azure-kunder får en [specialerbjudande](http://www.twilio.com/azure): kostnadsfri $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-kredit kan tillämpas på alla Twilio-användning ($10 kredit motsvarar upp till 1 000 SMS-meddelanden skickas eller tas emot upp till 1 000 inkommande röst minuter beroende på platsen för ditt mål för telefon och meddelandet eller samtal). Lösa in den här Twilio-kredit och kom igång med [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio är en betalning per användning. Det finns inga avgifter för installation och du kan stänga ditt konto när som helst. Du hittar mer information i [Twilio priser](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Begrepp
Twilio-API är en RESTful-API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista, se [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et tillämpar Twilio verb; till exempel den  **&lt;säg&gt;**  verb instruerar Twilio att hörbart leverera ett meddelande på ett samtal.

Följande är en lista över Twilio verb.  Lär dig mer om vilka verb och funktioner via [Markup Language Twilio dokumentationen](http://www.twilio.com/docs/api/twiml).

* **&lt;Ring&gt;**: anroparen ansluter till en annan telefon.
* **&lt;Samla in&gt;**: samlar in siffror som anges på telefon tangentbordet.
* **&lt;Koppla ned&gt;**: slutar ett anrop.
* **&lt;Spela upp&gt;**: spelar en ljudfil.
* **&lt;Pausa&gt;**: tyst väntar på ett angivet antal sekunder.
* **&lt;Posten&gt;**: registrerar anroparens röst och returnerar ett URL-Adressen till en fil som innehåller inspelningen.
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

## <a id="create_app"></a>Skapa ett Azure-program
Ett Azure-program som är värd för ett Twilio-aktiverat program skiljer sig inte från andra Azure-program. Du lägger till Twilio .NET-bibliotek och konfigurera roll om du vill använda Twilio .NET-bibliotek.
Information om hur du skapar ett första Azure-projekt finns [skapa ett Azure-projekt i Visual Studio][vs_project].

## <a id="configure_app"></a>Konfigurera ditt program att använda Twilio-bibliotek
Twilio innehåller en uppsättning .NET helper bibliotek som omsluter olika aspekter av Twilio att tillhandahålla enkel och enkelt sätt att interagera med Twilio REST-API och Twilio-klienten för att generera TwiML svar.

Twilio innehåller fem bibliotek för .NET-utvecklare:
Bibliotek|Beskrivning
---|---
Twilio.API|Kärnbibliotek som omsluter Twilio REST API i ett eget .NET-bibliotek för Twilio. Det här biblioteket är tillgängliga för .NET, Silverlight och Windows Phone 7.
Twilio.TwiML|Ger ett .NET egna sätt att generera TwiML markering.
Twilio.MVC|För utvecklare som använder ASP.NET MVC, innehåller det här biblioteket en TwilioController och TwiML ActionResult begäran verifieringsattribut.
Twilio.WebMatrix|För utvecklare som använder Microsofts ledigt WebMatrix utvecklingsverktyg för innehåller det här biblioteket Razor syntax hjälpprogram för olika Twilio-åtgärder.
Twilio.Client.Capability|Innehåller funktionen token generatorn för användning med Twilio klienten JavaScript SDK.

Observera att alla bibliotek kräver .NET 3.5, Silverlight 4 eller Windows Phone 7 eller senare.

Exemplen i den här guiden använder Twilio.API-bibliotek.

Biblioteken kan vara [installeras med filnamnstillägget NuGet package manager](http://www.twilio.com/docs/csharp/install) tillgängliga för Visual Studio 2010 upp till 2015.  Källkoden finns på [GitHub][twilio_github_repo], som innehåller en Wiki som innehåller komplett dokumentation för att använda biblioteken.

Som standard installerar Microsoft Visual Studio 2010 version 1.2 av NuGet. Installera Twilio-bibliotek kräver version 1.6 i NuGet eller högre. Mer information om installation eller uppdatering av NuGet finns [http://nuget.org/][nuget].

> [!NOTE]
> Om du vill installera den senaste versionen av NuGet måste du först avinstallera den inlästa versionen med hjälp av Tilläggshanteraren för Visual Studio. Om du vill göra det, måste du köra Visual Studio som administratör. I annat fall inaktiveras knappen Avinstallera.
>
>

### <a id="use_nuget"></a>Lägga till Twilio-bibliotek i Visual Studio-projekt:
1. Öppna din lösning i Visual Studio.
2. Högerklicka på **referenser**.
3. Klicka på **hantera NuGet-paket...**
4. Klicka på **Online**.
5. Skriv i sökrutan online *twilio*.
6. Klicka på **installera** på Twilio-paketet.

## <a id="howto_make_call"></a>Så här: göra en utgående anrop
Följande visar hur du gör en utgående Ring upp med den **CallResource** klass. Den här koden används även en webbplats med angivna Twilio för att returnera svaret Twilio Markup Language (TwiML). Ersätt värdena för den **till** och **från** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    var url = "http://twimlets.com/message";
    url = $"{url}?Message%5B0%5D=Hello%20World";

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri(url));
        }

Mer information om de parametrar som skickas till den **CallResource.Create** -metoden finns [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Som tidigare nämnts kan används den här koden en angivna Twilio plats för att returnera TwiML svaret. Du kan använda din egen webbplats i stället för att tillhandahålla TwiML svaret. Mer information finns i [så här: Ange TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Så här: skicka ett SMS-meddelande
Följande skärmbild visar hur du skickar ett SMS-meddelande med den **MessageResource** klass. Den **från** nummer tillhandahålls av Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **till** numret måste verifieras för ditt Twilio-konto innan du kör kod.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    const string accountSID = "your_twilio_account";
    const string authToken = "your_twilio_authentication_token";

    // Initialize the TwilioClient.
    TwilioClient.Init(accountSID, authToken);

    try
    {
        // Send an SMS message.
        var message = MessageResource.Create(
            to: new PhoneNumber("+12069419717"),
            from: new PhoneNumber("+14155992671"),
            body: "This is my SMS message.");
    }
    catch (TwilioException ex)
    {
        // An exception occurred making the REST call
        Console.WriteLine(ex.Message);
    }

## <a id="howto_provide_twiml_responses"></a>Så här: Ange TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API – exempelvis den **CallResource.Create** metod - Twilio skickar din begäran till en URL som förväntas returnera ett TwiML svar. Exemplet i [så här: utgående ringa](#howto_make_call) använder den angivna Twilio URL [http://twimlets.com/message] [ twimlet_message_url] att returnera svaret.

> [!NOTE]
> När TwiML är avsedd för användning av webbtjänster, kan du visa TwiML i webbläsaren. Klicka till exempel [http://twimlets.com/message] [ twimlet_message_url] att se en tom &lt;svar&gt; element, klicka på ett annat exempel är [http://twimlets.com/message? Meddelande % 5B0 %5 D = Hello % 20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) att se en &lt;svar&gt; element som innehåller en &lt;säg&gt; element.
>
>

I stället för på den angivna Twilio URL, kan du skapa din egen URL: en webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på alla språk som returnerar HTTP-svar. Det här avsnittet förutsätter att du ska vara värd för URL: en från en allmän ASP.NET-hanterare.

Följande ASP.NET-hanteraren utformar TwiML svar som säger **Hello World** på anropet.

    using System.Text;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {
            public void ProcessRequest(HttpContext context)
            {
                const string twiMLResponse =
                    "<Response><Say>Hello World.</Say></Response>";
                
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = Encoding.UTF8;
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }
    
Du ser i exemplet ovan, är TwiML svaret bara ett XML-dokument. Twilio.TwiML-bibliotek innehåller klasser som ska generera TwiML för dig. Exemplet nedan skapar motsvarande svaret som ovan, men använder den **VoiceResponse** klass.

    using System.Web;
    using Twilio.TwiML;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new VoiceResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Läs mer om TwiML [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

När du har konfigurerat ett sätt att ange TwiML svar du kan ange Webbadressen till den **CallResource.Create** metod. Till exempel om du har ett webbprogram med namnet MyTwiML som distribueras till en Azure-molntjänst och namnet på din ASP.NET-hanteraren är mytwiml.ashx, URL: en kan skickas till **CallResource.Create** som visas i följande kodexempel:

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Mer information om hur du använder Twilio på Azure med ASP.NET finns [så att ringa ett telefonsamtal med Twilio i en webbroll på Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
