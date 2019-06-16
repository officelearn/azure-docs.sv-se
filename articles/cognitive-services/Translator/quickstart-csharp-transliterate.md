---
title: 'Snabbstart: Omvandla textskript, C# – Translator Text'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig hur du translittererar (konverterar) text från ett skript till ett annat med hjälp av .NET Core och Translator Text REST API. I det här exemplet translittereras japanska till det latinska alfabetet.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: erhopf
ms.openlocfilehash: 8e08372e591c9d600b42ae8e66baf7addf7806c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123376"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-c"></a>Snabbstart: Använda Translator Text API för att transkribera text med hjälp av C#

I den här snabbstarten du lär dig hur att transkribera (konvertera) text från ett skript till en annan med .NET Core (C#), C# 7.1 eller senare, och Translator Text REST API. I det angivna exemplet translittereras japanska till det latinska alfabetet.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

* C#7.1 eller senare
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-paket](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-net-core-project"></a>Skapa ett .NET Core-projekt

Öppna en ny kommandotolk (eller en terminalsession) och kör följande kommandon:

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

Det första kommandot gör två saker. Det skapar ett nytt .NET-konsolprogram och en katalog med namnet `transliterate-sample`. Det andra kommandot ändrar till katalogen för ditt projekt.

Därefter behöver du installera Json.Net. Från projektkatalogen kör du:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Välj den C# språkversion

Den här snabbstarten kräver C# 7.1 eller senare. Det finns ett antal sätt att ändra den C# version för ditt projekt. I den här guiden får du lära dig hur du ändrar den `transliterate-sample.csproj` filen. Alla tillgängliga alternativ, till exempel ändra språk i Visual Studio finns i [väljer den C# språkversion](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Öppna projektet och öppna sedan `transliterate-sample.csproj`. Se till att `LangVersion` är inställt på 7.1 eller senare. Om det inte finns en egenskapsgrupp med språkversion, lägger du till följande rader:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Lägg till nödvändiga namnrymder i projektet

Kommandot `dotnet new console` som du körde tidigare skapade ett nytt projekt, inklusive `Program.cs`. Den här filen är där du lägger programkoden. Öppna `Program.cs` och ersätt de befintliga using-instruktionerna. De här instruktionerna ser till att du har åtkomst till alla typer som krävs för att skapa och köra exempelappen.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Skapa klasser för JSON-svar

Nu ska vi skapa en klass som används när avserialisering av JSON-svaret som returnerades av Translator Text API.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}
```

## <a name="create-a-function-to-transliterate-text"></a>Skapa en funktion för att translitterera text

I den `Program` klass, skapa en asynkron funktion som kallas `TransliterateTextRequest()`. Den här funktionen använder fyra argument: `subscriptionKey`, `host`, `route`, och `inputText`.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serialisera translation begäran

Sedan behöver vi skapa och serialisera det JSON-objekt som innehåller den text du vill översätta. Ha i åtanke kan du skicka fler än ett objekt i den `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instansiera klienten och göra en begäran

Dessa rader instansierar `HttpClient` och `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Konstruera begäran och skriva ut svaret

I `HttpRequestMessage` gör du följande:

* Deklarera HTTP-metoden
* Konstruera begärande-URI
* Infoga begärandetexten (serialiserat JSON-objekt)
* Lägga huvuden som krävs
* Göra en asynkron begäran
* Skriva ut svaret

Lägg till den här koden i `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att anropa `TransliterateTextRequest()` i funktionen `Main`. I det här exemplet vi transliterating från japanska till latin-skript. Leta upp `static void Main(string[] args)` och Ersätt den med den här koden:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, host, route, textToTransliterate);
}
```

Lägg märke till att `Main`, du deklarerar `subscriptionKey`, `host`, `route`, och skript för att transkribera `textToTransliterate`.

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
dotnet run
```

## <a name="sample-response"></a>Exempelsvar

När du kör exemplet bör du se följande ut till terminal:

```bash
Transliterated to latn script: Kon\'nichiwa
```

Det här meddelandet är byggd från JSON-rådataformat, som ser ut så här:

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och språkidentifiering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Se även

* [Översätta text](quickstart-csharp-translate.md)
* [Identifiera språket efter indata](quickstart-csharp-detect.md)
* [Hämta alternativa översättningar](quickstart-csharp-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-csharp-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-csharp-sentences.md)
