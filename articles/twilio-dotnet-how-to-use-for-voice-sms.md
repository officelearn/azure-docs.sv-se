---
title: Använda Twilio för röst- och SMS (.NET) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: twilio
editor: ''
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 3b8b21de9664a969e8b1ce5699034aa9ab41d0f1
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762902"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Använda Twilio för röst- och SMS-funktioner från Azure
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med Twilio-API-tjänsten på Azure. Scenarier som omfattas är ringa ett samtal och skicka ett meddelande om tjänsten SMS (Short Message). Mer information om Twilio och använda röst och SMS i dina program finns i den [nästa steg](#NextSteps) avsnittet.

## <a id="WhatIs"></a>Vad är Twilio?
Twilio driva framtidens business-kommunikation, så att utvecklare kan bädda in röst, VoIP och meddelanden i program. De virtualisera all infrastruktur som behövs i en molnbaserad och global miljö kan exponera den via Twilio kommunikation API-plattformen. Program är enkel att bygga och skalbar. Få flexibilitet med när du betalar och dra nytta av molnet tillförlitlighet.

**Twilio-röst** kan dina program kan ringa och ta emot samtal. **Twilio-SMS** gör att dina program för att skicka och ta emot SMS-meddelanden. **Twilio-klienten** kan du se VoIP-anrop från valfri telefon, surfplatta eller webbläsare och har stöd för WebRTC.

## <a id="Pricing"></a>Twilio priser och specialerbjudanden
Azure-kunder får en [specialerbjudande](https://www.twilio.com/azure): kostnadsfri $10 av Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-kredit kan tillämpas på all Twilio-användning (10 USD kredit motsvarar skicka upp till 1 000 SMS-meddelanden eller ta emot upp till 1 000 inkommande röst minuter beroende på platsen för din telefon och meddelandet eller anrop mål). Lösa in den här Twilio-kredit och kom igång med [twilio.com/azure](https://twilio.com/azure).

Twilio är en betala per tjänst. Det finns inga avgifter för konfiguration och du kan avsluta ditt konto när som helst. Du hittar mer information finns på [Twilio priser](https://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Begrepp
Twilio-API är ett RESTful-API med röst och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. en lista i [Twilio-API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Twilio-verb
API: et gör använda Twilio verb; till exempel den **&lt;Say&gt;** verb instruerar Twilio hörbart uppfyller ett meddelande på ett anrop.

Här följer en lista över Twilio-verb.  Läs mer om andra verb och funktioner i form av [Twilio Markup Language dokumentation](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Ansluter anroparen till en annan telefon.
* `<Gather>`: Samlar in siffror som anges på telefon-tangentbordet.
* `<Hangup>`: Slutar ett anrop.
* `<Play>`: Spelar upp en ljudfil.
* `<Pause>`: Väntar tyst under ett angivet antal sekunder.
* `<Record>`: Registrerar anroparens röst och returnerar en URL till en fil som innehåller inspelningen.
* `<Redirect>`: Överföringar kontroll över ett samtal eller SMS till TwiML på en annan URL.
* `<Reject>`: Avvisar inkommande samtal till din Twilio-nummer utan fakturering du
* `<Say>`: Konverterar text till tal som görs på ett anrop.
* `<Sms>`: Skickar ett SMS-meddelande.

### <a name="twiml"></a>TwiML
TwiML är en uppsättning XML-baserade instruktionerna baserat på de Twilio-verb som meddelar Twilio av behandla ett samtal eller SMS.

Till exempel följande TwiML konverteras texten **Hello World** till tal.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

När programmet anropar Twilio-API, är en av parametrarna API den URL som returnerar TwiML svaret. Du kan använda Twilio-angivna URL: er för utveckling, för att ge TwiML-svar som används av dina program. Du kan också vara värd för din egen URL: er för att skapa TwiML-svar och ett annat alternativ är att använda den **TwiMLResponse** objekt.

Läs mer om Twilio-verb, deras attribut och TwiML [TwiML][twiml]. Mer information om Twilio-API finns i [Twilio-API][twilio_api].

## <a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att få ett Twilio-konto kan du registrera dig på [försök Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du en konto-ID och en autentiseringstoken. Båda krävs för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto måste skydda din autentiseringstoken. Ditt konto-ID och autentiseringsnyckel token kan visas på den [Twilio kontosidan][twilio_account], i fälten med etiketten **konto-SID** och **AUTENTISERINGSTOKEN**respektive.

## <a id="create_app"></a>Skapa ett Azure-program
Ett Azure-program som är värd för ett Twilio-aktiverat program är inte skiljer sig från andra Azure-program. Du lägger till Twilio-.NET-biblioteket och konfigurerar rollen för att använda Twilio-.NET-bibliotek.
Information om hur du skapar ett första Azure-projekt finns i [skapar ett Azure-projekt med Visual Studio][vs_project].

## <a id="configure_app"></a>Konfigurera ditt program att använda Twilio-bibliotek
Twilio innehåller en uppsättning av .NET helper-bibliotek som omsluter olika aspekter av Twilio för att tillhandahålla och enkelt sätt att interagera med Twilio REST API och Twilio-klienten för att generera TwiML svar.

Twilio innehåller fem bibliotek för .NET-utvecklare:

| Bibliotek | Beskrivning |
| --- | --- |
| Twilio.API | Kärnbibliotek som omsluter REST-API Twilio i ett eget .NET-bibliotek för Twilio. Det här biblioteket är tillgängliga för .NET, Silverlight och Windows Phone 7. |
| Twilio.TwiML | Ger ett .NET eget sätt att generera TwiML markup. |
| Twilio.MVC | För utvecklare som använder ASP.NET MVC, innehåller det här biblioteket en TwilioController och TwiML ActionResult begäran verifieringsattribut. |
| Twilio.WebMatrix | För utvecklare som använder Microsofts kostnadsfria WebMatrix-utvecklingsverktyg, innehåller det här biblioteket Razor-syntaxen inlärningsverktyg för olika Twilio-åtgärder. |
| Twilio.Client.Capability | Innehåller funktionen tokengenerator för användning med Twilio klient-SDK för JavaScript. |

> [!Important]
> Alla bibliotek kräver .NET 3.5, Silverlight 4 eller Windows Phone 7 eller senare.

Exemplen i den här guiden använder sig av Twilio.API library.

Biblioteken kan vara [installerad med hjälp av NuGet package manager-tillägget](https://www.twilio.com/docs/csharp/install) tillgängliga för Visual Studio 2010 upp till 2015.  Källkoden finns på [GitHub][twilio_github_repo], som innehåller en Wiki som innehåller komplett dokumentation för att använda biblioteken.

Som standard installerar Microsoft Visual Studio 2010 version 1.2 av NuGet. Twilio-biblioteken installeras kräver version 1.6 av NuGet eller senare. Mer information om hur du installerar eller uppdaterar NuGet, finns i [ https://nuget.org/ ] [ nuget].

> [!NOTE]
> Om du vill installera den senaste versionen av NuGet, måste du först avinstallera den inlästa versionen med hjälp av Visual Studio Extension Manager. Om du vill göra det måste du köra Visual Studio som administratör. I annat fall inaktiveras knappen Avinstallera.
>
>

### <a id="use_nuget"></a>Att lägga till Twilio-bibliotek i Visual Studio-projektet:
1. Öppna din lösning i Visual Studio.
2. Högerklicka på **referenser**.
3. Klicka på **hantera NuGet-paket...**
4. Klicka på **Online**.
5. Skriv i sökrutan online *twilio*.
6. Klicka på **installera** Twilio-paketet.

## <a id="howto_make_call"></a>Hur: Göra ett externt anrop
Följande visar hur du gör en utgående anropa med hjälp av den **CallResource** klass. Den här koden använder också en Twilio-angivna plats för att returnera Twilio Markup Language (TwiML)-svaret. Ersätt värdena för den **till** och **från** telefonnummer och se till att du kontrollerar den **från** telefonnummer för ditt Twilio-konto innan du kör koden.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Mer information om de parametrar som skickas till den **CallResource.Create** metod, se [ https://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Som tidigare nämnts använder den här koden en Twilio-angivna plats för att returnera TwiML-svar. Du kan i stället använda en egen plats för att tillhandahålla TwiML svaret. Mer information finns i [Gör så här: Ange TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Hur: Skicka ett SMS-meddelande
Skärmbilden nedan visar hur du skickar ett SMS-meddelande med den **MessageResource** klass. Den **från** tal kommer från Twilio för utvärderingskonton att skicka SMS-meddelanden. Den **till** tal måste verifieras för ditt Twilio-konto innan du kör koden.

```csharp
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
```

## <a id="howto_provide_twiml_responses"></a>Hur: Ange TwiML svar från din egen webbplats
När programmet startar ett anrop till Twilio-API – till exempel den **CallResource.Create** metoden - Twilio skickar din begäran till en URL som kan förväntas returnera ett TwiML svar. I exemplet i [så här: Göra ett externt anrop](#howto_make_call) använder URL: en med Twilio-angivna [ https://twimlets.com/message ] [ twimlet_message_url] att returnera svaret.

> [!NOTE]
> Medan TwiML är avsedd för användning av webbtjänster, kan du visa TwiML i webbläsaren. Klicka till exempel [ https://twimlets.com/message ] [ twimlet_message_url] att se en tom `<Response>` elementet; Klicka på ett annat exempel är [ https://twimlets.com/message?Message%5B0%5D=Hello%20World ](https://twimlets.com/message?Message%5B0%5D=Hello%20World) att se en `<Response>` element som innehåller en &lt;Say&gt; element.
>

I stället för en Twilio-angivna URL: en, kan du skapa en egen URL-plats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar HTTP-svar. Det här avsnittet förutsätter att du ska vara värd för URL: en från en allmän ASP.NET-hanteraren.

Följande ASP.NET-hanteraren utformar svar TwiML där det står **Hello World** på anropet.

```csharp
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
```
    
Som du ser i exemplet ovan är TwiML svaret helt enkelt ett XML-dokument. Twilio.TwiML-bibliotek innehåller klasser som ska generera TwiML åt dig. Exemplet nedan skapar motsvarande svaret enligt ovan, men använder den **VoiceResponse** klass.

```csharp
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
```

Läs mer om TwiML [ https://www.twilio.com/docs/api/twiml ](https://www.twilio.com/docs/api/twiml).

När du har konfigurerat ett sätt att ge TwiML svar, kan du skicka URL: en till den **CallResource.Create** metod. Till exempel om du har en webbapp med namnet MyTwiML som distribuerats till en Azure-molntjänst och namnet på din ASP.NET-hanteraren är mytwiml.ashx, URL: en kan skickas till **CallResource.Create** som visas i följande kodexempel:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Mer information om hur du använder Twilio på Azure med ASP.NET finns i [hur du ringa ett telefonsamtal med Twilio i en webbroll på Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
