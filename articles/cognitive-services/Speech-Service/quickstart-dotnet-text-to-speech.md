---
title: 'Snabbstart: Omvandla text till tal, .NET Core - Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig att omvandla text till tal med REST-API för text till tal. Exempeltext som ingår i den här guiden är strukturerad som tal syntes Markup Language (SSML). På så sätt kan du välja rösten och språket för tal-svaret.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 9e0b71acc4faa97bf1aa82e56a3784c349663dc0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207113"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Snabbstart: Omvandla text till tal med .NET Core

I den här snabbstarten du lär dig hur du konverterar text till tal med hjälp av .NET Core- och REST-API för text till tal. Exempeltext som ingår i den här guiden är strukturerad som [tal syntes Markup Language (SSML)](speech-synthesis-markup.md), vilket gör att du kan välja rösten och språket i svaret.

Den här snabbstarten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech Service-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för Speech Service

## <a name="create-a-net-core-project"></a>Skapa ett .NET Core-projekt

Öppna en ny kommandotolk (eller en terminalsession) och kör följande kommandon:

```console
dotnet new console -o tts-sample
cd tts-sample
```

Det första kommandot gör två saker. Det skapar ett nytt .NET-konsolprogram och en katalog med namnet `tts-sample`. Det andra kommandot ändrar till katalogen för ditt projekt.

## <a name="select-the-c-language-version"></a>Välj den C# språkversion

Den här snabbstarten kräver C# 7.1 eller senare. Det finns ett antal sätt att ändra den C# version för ditt projekt. I den här guiden får du lära dig hur du ändrar den `tts-sample.csproj` filen. Alla tillgängliga alternativ, till exempel ändra språk i Visual Studio finns i [väljer den C# språkversion](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Öppna projektet och öppna sedan `tts-sample.csproj`. Se till att `LangVersion` är inställt på 7.1 eller senare. Om det inte finns en egenskapsgrupp med språkversion, lägger du till följande rader:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Se till att spara dina ändringar.

## <a name="add-required-namespaces-to-your-project"></a>Lägg till nödvändiga namnrymder i projektet

Kommandot `dotnet new console` som du körde tidigare skapade ett nytt projekt, inklusive `Program.cs`. Den här filen är där du lägger programkoden. Öppna `Program.cs` och ersätt de befintliga using-instruktionerna. De här instruktionerna ser till att du har åtkomst till alla typer som krävs för att skapa och köra exempelappen.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Skapa en klass för token exchange

Text till tal REST-API kräver en åtkomsttoken för autentisering. Om du vill få en åtkomsttoken, krävs ett utbyte. Det här exemplet utbyter prenumerationsnyckeln Speech Service för en token med den `issueToken` slutpunkt.

Det här exemplet förutsätter att prenumerationen Speech Service i regionen USA, västra. Om du använder en annan region måste du uppdatera värdet för `FetchTokenUri`. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Mer information om autentisering finns i [autentisera med åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Hämta en åtkomsttoken och ange URL: Värdadressen

Leta upp `static void Main(string[] args)` och Ersätt den med `static async Task Main(string[] args)`.

Kopiera den här koden i main-metoden. Den gör några saker, men viktigast av allt, det tar text som indata och anropar den `Authentication` funktionen att byta ut din prenumerationsnyckel för en åtkomsttoken. Om något går fel skrivs felet ut till konsolen.

Se till att lägga till din prenumerationsnyckel innan du kör appen.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Ange sedan värden och väg för text till tal:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Skapa SSML-begäran

Meddelandet skickas som en del av en `POST` begäran. Med SSML, kan du ange röst- och språk. I den här snabbstarten använder vi SSML med språket inställt `en-US` och röst ange som `ZiraRUS`. Nu ska vi skapa SSML för din förfrågan:

```csharp
string body = @"<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> Det här exemplet används den `ZiraRUS` rösttyp. En fullständig lista över Microsoft tillhandahålls röster/språk, finns i [språkstöd](language-support.md). Om du är intresserad av att skapa en unik, identifierbara ton för ditt varumärke, se [skapar anpassade rösttyper](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Skapa en instans av klienten, gör en begäran och spara syntetiserade ljud till en fil

Det är mycket som händer i det här kodexemplet. Nu ska vi snabbt se vad som händer:

* Klienten och begäran instansieras.
* HTTP-metoden har angetts som `POST`.
* Nödvändiga rubriker läggs till i begäran.
* Begäran skickades och statuskoden är markerad.
* Svaret är läsa asynkront och skrivs till en fil med namnet sample.wav.

Kopiera den här koden i projektet. Ersätt värdet för den `User-Agent` rubrik med namnet på din resurs från Azure-portalen.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt, är du redo att köra din app för text till tal. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
dotnet run
```

Om detta lyckas, sparas filen tal i projektmappen. Spela upp den med din favorit media player.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska .NET-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Se också

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
* [Post voice-exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
