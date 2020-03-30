---
title: Hur man använder Twilio för röst och SMS (.NET) | Microsoft-dokument
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69876809"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Så här använder du Twilio för röst- och SMS-funktioner från Azure
Den här guiden visar hur du utför vanliga programmeringsuppgifter med Twilio API-tjänsten på Azure. Scenarierna som omfattas är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst och SMS i dina program finns i avsnittet [Nästa steg.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio driver framtiden för affärskommunikation, vilket gör det möjligt för utvecklare att bädda in röst, VoIP och meddelanden i program. De virtualiserar all infrastruktur som behövs i en molnbaserad, global miljö och exponerar den via Twilio-plattformen för kommunikation API. Program är enkla att bygga och skalbara. Njut av flexibilitet med priser för användningsbaserad betalning och dra nytta av molntillförlitlighet.

**Twilio Voice** gör det möjligt för dina program att ringa och ta emot telefonsamtal. **Twilio SMS** gör det möjligt för dina program att skicka och ta emot SMS-meddelanden. **Twilio Client** kan du ringa VoIP-samtal från vilken telefon, surfplatta eller webbläsare som helst och stöder WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prissättning och specialerbjudanden
Azure-kunder får ett [specialerbjudande:](https://www.twilio.com/azure)gratis $10 av Twilio Credit när du uppgraderar ditt Twilio-konto. Denna Twilio Credit kan tillämpas på alla Twilio användning ($ 10 kredit motsvarar att skicka så många som 1.000 SMS-meddelanden eller ta emot upp till 1000 inkommande röst minuter, beroende på var ditt telefonnummer och meddelande eller samtal destination). Lös in denna Twilio kredit och komma igång på [twilio.com/azure](https://twilio.com/azure).

Twilio är en pay-as-you-go tjänst. Det finns inga inställningsavgifter och du kan avsluta ditt konto när som helst. Du kan hitta mer information på [Twilio Prissättning](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio API är ett RESTful API som tillhandahåller röst- och SMS-funktioner för program. Klientbibliotek är tillgängliga på flera språk. för en lista, se [Twilio API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio API är Twilio verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio verb
API använder Twilio verb; Till exempel instruerar ** &lt;&gt; Say-verbet** Twilio att hörbart leverera ett meddelande i ett samtal.

Följande är en lista över Twilio-verb.  Lär dig mer om andra verb och funktioner via [dokumentationen för Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Ansluter den som ringer till en annan telefon.
* `<Gather>`: Samlar in numeriska siffror som anges på telefonens knappsats.
* `<Hangup>`: Avslutar ett samtal.
* `<Play>`: Spelar upp en ljudfil.
* `<Pause>`: Väntar tyst på ett visst antal sekunder.
* `<Record>`: Registrerar den som ringer röst och returnerar en URL för en fil som innehåller inspelningen.
* `<Redirect>`: Överför kontroll över ett samtal eller SMS till TwiML på en annan webbadress.
* `<Reject>`: Avvisar ett inkommande samtal till ditt Twilio-nummer utan att fakturera dig
* `<Say>`: Konverterar text till tal som görs i ett samtal.
* `<Sms>`: Skickar ett SMS-meddelande.

### <a name="twiml"></a>TwiML (TwiML)
TwiML är en uppsättning XML-baserade instruktioner baserade på Twilio-verben som informerar Twilio om hur du bearbetar ett samtal eller SMS.

Som ett exempel skulle följande TwiML konvertera texten **Hello World** till tal.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

När ditt program anropar Twilio API är en av API-parametrarna url:en som returnerar TwiML-svaret. I utvecklingssyfte kan du använda url:er som tillhandahålls av Twilio för att ange de TwiML-svar som används av dina program. Du kan också vara värd för dina egna webbadresser för att skapa TwiML-svaren, och ett annat alternativ är att använda **TwiMLResponse-objektet.**

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API:et finns i [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto registrerar du dig på [Try Twilio][try_twilio]. Du kan börja med ett kostnadsfritt konto och uppgradera ditt konto senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och en autentiseringstoken. Båda kommer att behövas för att göra Twilio API-anrop. För att förhindra obehörig åtkomst till ditt konto, hålla din autentiseringstoken säker. Ditt konto-ID och autentiseringstoken kan visas på [Twilio-kontosidan][twilio_account]i fälten **KONTO SID** respektive **AUTH TOKEN.**

## <a name="create-an-azure-application"></a><a id="create_app"></a>Skapa ett Azure-program
Ett Azure-program som är värd för ett Twilio-aktiverat program skiljer sig inte från något annat Azure-program. Du lägger till Twilio .NET-biblioteket och konfigurerar rollen så att den använder Twilio .NET-biblioteken.
Information om hur du skapar ett första Azure-projekt finns i [Skapa ett Azure-projekt med Visual Studio][vs_project].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program så att det använder Twilio-bibliotek
Twilio tillhandahåller en uppsättning .NET-hjälpbibliotek som sveper in olika aspekter av Twilio för att ge enkla och enkla sätt att interagera med Twilio REST API och Twilio Client för att generera TwiML-svar.

Twilio tillhandahåller fem bibliotek för .NET-utvecklare:

| Bibliotek | Beskrivning |
| --- | --- |
| Twilio.API | Det centrala Twilio-biblioteket som sveper in Twilio REST API i ett användarvänligt .NET-bibliotek. Det här biblioteket är tillgängligt för .NET, Silverlight och Windows Phone 7. |
| Twilio.TwiML (Twilio.TwiML) | Ger ett .NET-vänligt sätt att generera TwiML-markering. |
| Twilio.MVC (Twilio.MVC) | För utvecklare som använder ASP.NET MVC innehåller det här biblioteket ett TwilioController-, TwiML ActionResult- och begärandeverifieringsattribut. |
| Twilio.WebMatrix | För utvecklare som använder Microsofts kostnadsfria webmatrix-utvecklingsverktyg innehåller det här biblioteket rakbladssyntaxhjälpare för olika Twilio-åtgärder. |
| Twilio.Client.Capability | Innehåller funktionstokengeneratorn för användning med Twilio Client JavaScript SDK. |

> [!Important]
> Alla bibliotek kräver .NET 3.5, Silverlight 4 eller Windows Phone 7 eller senare.

De exempel som finns i den här guiden använder Twilio.API-biblioteket.

Biblioteken kan [installeras med tillägget NuGet-pakethanteraren](https://www.twilio.com/docs/csharp/install) som är tillgängligt för Visual Studio 2010 fram till 2015.  Källkoden finns på [GitHub][twilio_github_repo], som innehåller en Wiki som innehåller fullständig dokumentation för att använda biblioteken.

Som standard installerar Microsoft Visual Studio 2010 version 1.2 av NuGet. Installera Twilio bibliotek kräver version 1.6 av NuGet eller högre. Information om hur du installerar [https://nuget.org/][nuget]eller uppdaterar NuGet finns i .

> [!NOTE]
> Om du vill installera den senaste versionen av NuGet måste du först avinstallera den inlästa versionen med Visual Studio Extension Manager. För att kunna göra det måste du köra Visual Studio som administratör. Annars är knappen Avinstallera inaktiverad.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Så här lägger du till Twilio-biblioteken i Visual Studio-projektet:
1. Öppna din lösning i Visual Studio.
2. Högerklicka på **Referenser**.
3. Klicka på **Hantera NuGet-paket...**
4. Klicka på **Online**.
5. Skriv *twilio*i sökrutan online .
6. Klicka på **Installera** på Twilio-paketet.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Så här: Ringa ett utgående samtal
Följande visar hur du ringer ett utgående samtal med klassen **CallResource.** Den här koden använder också en Twilio-plats för att returnera Twilio Markup Language (TwiML) svar. Ersätt dina värden med **till-** och **från** telefonnummer och se till att du verifierar **från-telefonnumret** för ditt Twilio-konto innan du kör koden.

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

Mer information om parametrarna som skickas in i metoden [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] **CallResource.Create** finns i .

Som nämnts använder den här koden en Twilio-tillsedd plats för att returnera TwiML-svaret. Du kan istället använda din egen webbplats för att ge TwiML-svaret. Mer information finns i [Så här ger du TwiML-svar från din egen webbplats](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Så här skickar du ett SMS
Följande skärmbild visar hur du skickar ett SMS-meddelande med klassen **MessageResource.** **Från-numret** tillhandahålls av Twilio för utvärderingskonton för att skicka SMS-meddelanden. **Till-numret** måste verifieras för ditt Twilio-konto innan du kör koden.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Så här: Ge TwiML-svar från din egen webbplats
När ditt program initierar ett anrop till Twilio API - till exempel via **CallResource.Create-metoden** - skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. Exemplet i [Så här gör du: Ringa ett utgående samtal](#howto_make_call) använder den Twilio-tillförda URL:en [https://twimlets.com/message][twimlet_message_url] för att returnera svaret.

> [!NOTE]
> Medan TwiML är utformat för användning av webbtjänster kan du visa TwiML i din webbläsare. Klicka till [https://twimlets.com/message][twimlet_message_url] exempel för `<Response>` att se ett tomt element. Som ett annat [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) exempel `<Response>` klickar du på &lt;&gt; om du vill visa ett element som innehåller ett Say-element.
>

I stället för att förlita sig på den Twilio-tillsentade webbadressen kan du skapa en egen URL-webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar HTTP-svar. Det här avsnittet förutsätter att du är värd för webbadressen från en ASP.NET allmän hanterare.

Följande ASP.NET Handler hantverk en TwiML svar som säger **Hello World** på samtalet.

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
    
Som du kan se i exemplet ovan är TwiML-svaret helt enkelt ett XML-dokument. Twilio.TwiML-biblioteket innehåller klasser som genererar TwiML åt dig. Exemplet nedan ger motsvarande svar som visas ovan, men använder klassen **VoiceResponse.**

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

Mer information om TwiML [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)finns i .

När du har konfigurerat ett sätt att ge TwiML-svar kan du skicka webbadressen till **callresource.create-metoden.** Om du till exempel har ett webbprogram med namnet MyTwiML distribuerat till en Azure-molntjänst och namnet på din ASP.NET Handler är mytwiml.ashx, kan URL:en skickas till **CallResource.Create** enligt följande kodexempel:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Mer information om hur du använder Twilio på Azure med ASP.NET finns i [Så här ringer du ett telefonsamtal med Twilio i en webbroll på Azure][howto_phonecall_dotnet].

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
