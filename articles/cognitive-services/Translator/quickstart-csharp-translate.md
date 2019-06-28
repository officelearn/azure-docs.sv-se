---
title: 'Snabbstart: Översätta text, C# – Translator Text'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du översätta text från ett språk till ett annat med Translator Text-API med C#.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 242c16fd0ec1d32c889d511aee78924a296e191d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449432"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-c"></a>Snabbstart: Använda Translator Text API för att översätta en sträng med hjälp av C#

I den här snabbstarten får du lära dig hur att översätta en textsträng från engelska till tyska, italienska, japanska och thailändska med .NET Core C# 7.1 eller senare, och Translator Text REST API.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

* C#7.1 eller senare
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-paket](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-net-core-project"></a>Skapa ett .NET Core-projekt

Öppna en ny kommandotolk (eller en terminalsession) och kör följande kommandon:

```console
dotnet new console -o translate-sample
cd translate-sample
```

Det första kommandot gör två saker. Det skapar ett nytt .NET-konsolprogram och en katalog med namnet `translate-sample`. Det andra kommandot ändrar till katalogen för ditt projekt.

Därefter behöver du installera Json.Net. Från projektkatalogen kör du:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Välj den C# språkversion

Den här snabbstarten kräver C# 7.1 eller senare. Det finns ett antal sätt att ändra den C# version för ditt projekt. I den här guiden får du lära dig hur du ändrar den `translate-sample.csproj` filen. Alla tillgängliga alternativ, till exempel ändra språk i Visual Studio finns i [väljer den C# språkversion](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Öppna projektet och öppna sedan `translate-sample.csproj`. Se till att `LangVersion` är inställt på 7.1 eller senare. Om det inte finns en egenskapsgrupp med språkversion, lägger du till följande rader:

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

Nu ska vi skapa en uppsättning klasser som används när avserialisering av JSON-svaret som returnerades av Translator Text API.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
}
```

## <a name="create-a-function-to-translate-text"></a>Skapa en funktion för att översätta text

I den `Program` klass, skapa en asynkron funktion som kallas `TranslateTextRequest()`. Den här funktionen använder fyra argument: `subscriptionKey`, `host`, `route`, och `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string host, string route, string inputText)
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
* Skriva ut svaret med hjälp av de klasser som du skapade tidigare

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
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Om du använder en flera tjänster Cognitive Services-prenumeration, måste du också inkludera den `Ocp-Apim-Subscription-Region` i dina begäranparametrar. [Mer information om autentisering med flera tjänster prenumerationen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att anropa `TranslateTextRequest()` i funktionen `Main`. I det här exemplet vi översattes till tyska (`de`), italienska (`it`), japanska (`ja`), och thailändska (`th`). Leta upp `static void Main(string[] args)` och Ersätt den med den här koden:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, host, route, textToTranslate);
}
```

Lägg märke till att `Main`, du deklarerar `subscriptionKey`, `host`, och `route`. Du dessutom fråga användaren om indata med `Console.Readline()` och tilldela värdet till `textToTranslate`.

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
dotnet run
```

## <a name="sample-response"></a>Exempelsvar

När du kör exemplet bör du se följande ut till terminal:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Det här meddelandet är byggd från JSON-rådataformat, som ser ut så här:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive transkribering och språkidentifiering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Se även

* [Translitterera text](quickstart-csharp-transliterate.md)
* [Identifiera språket efter indata](quickstart-csharp-detect.md)
* [Hämta alternativa översättningar](quickstart-csharp-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-csharp-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-csharp-sentences.md)
