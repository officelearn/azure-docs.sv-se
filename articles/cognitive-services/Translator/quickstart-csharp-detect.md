---
title: 'Snabbstart: Identifiera textspråk, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig att identifiera språket för angiven text med hjälp av .NET Core och Translator Text REST API.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: e2988260902b645237ad26420435441622127f8a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103425"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-c"></a>Snabbstart: Använda Translator Text API för att identifiera textspråk med hjälp av C#

I den här snabbstarten lär du dig att identifiera språket för angiven text med hjälp av .NET Core och Translator Text REST API.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-paket](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-net-core-project"></a>Skapa ett .NET Core-projekt

Öppna en ny kommandotolk (eller en terminalsession) och kör följande kommandon:

```console
dotnet new console -o detect-sample
cd detect-sample
```

Det första kommandot gör två saker. Det skapar ett nytt .NET-konsolprogram och en katalog med namnet `detect-sample`. Det andra kommandot ändrar till katalogen för ditt projekt.

Därefter behöver du installera Json.Net. Från projektkatalogen kör du:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Lägg till nödvändiga namnrymder i projektet

Kommandot `dotnet new console` som du körde tidigare skapade ett nytt projekt, inklusive `Program.cs`. Den här filen är där du lägger programkoden. Öppna `Program.cs` och ersätt de befintliga using-instruktionerna. De här instruktionerna ser till att du har åtkomst till alla typer som krävs för att skapa och köra exempelappen.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-detect-the-source-texts-language"></a>Skapa en funktion för att identifiera källtextens språk

I klassen `Program` skapar du en funktion med namnet `Detect`. Den här klassen kapslar in den kod som används för att anropa Detect-resursen och skriver ut resultatet till konsolen.

```csharp
static void Detect()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Ange prenumerationsnyckeln, värddatornamnet och sökvägen

Lägg till följande rader i funktionen `Detect`.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/detect?api-version=3.0";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Sedan behöver vi skapa och serialisera det JSON-objekt som innehåller den text som ska genomgå språkidentifiering.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Salve mondo!" } };
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
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Om du vill skriva ut svaret med ”ganska skriva ut” (formatering för svaret), lägger du till den här funktionen i klassen Program:
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att anropa `Detect()` i funktionen `Main`. Leta upp `static void Main(string[] args)` och lägg till följande rader:

```csharp
Detect();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
dotnet run
```

## <a name="sample-response"></a>Exempelsvar

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och transkribering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Se även

* [Översätta text](quickstart-csharp-translate.md)
* [Translitterera text](quickstart-csharp-transliterate.md)
* [Hämta alternativa översättningar](quickstart-csharp-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-csharp-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-csharp-sentences.md)
