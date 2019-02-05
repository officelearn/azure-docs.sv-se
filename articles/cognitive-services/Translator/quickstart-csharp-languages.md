---
title: 'Snabbstart: Hämta en lista över språk som stöds, C# – Translator Text-API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten hämtar du en lista över språk som stöds för översättning, translitterering och ordlistesökningar med hjälp av Translator Text API.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: erhopf
ms.openlocfilehash: e638375c9c8316c00ee8da000ca5223330072985
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215579"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>Snabbstart: Använda Translator Text API för att hämta en lista över språk som stöds med C#

I den här snabbstarten hämtar du en lista över språk som stöds för översättning, translitterering och ordlistesökningar med hjälp av Translator Text API.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-paket](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-net-core-project"></a>Skapa ett .NET Core-projekt

Öppna en ny kommandotolk (eller en terminalsession) och kör följande kommandon:

```console
dotnet new console -o languages-sample
cd languages-sample
```

Det första kommandot gör två saker. Det skapar ett nytt .NET-konsolprogram och en katalog med namnet `languages-sample`. Det andra kommandot ändrar till katalogen för ditt projekt.

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

## <a name="create-a-function-to-get-a-list-of-languages"></a>Skapa en funktion för att hämta en lista över språk

I klassen `Program` skapar du en funktion med namnet `GetLanguages`. Den här klassen kapslar in den kod som används för att anropa Languages-resursen och skriver ut resultatet till konsolen.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Ange prenumerationsnyckeln, värddatornamnet och sökvägen

Lägg till följande rader i funktionen `GetLanguages`.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
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
* Lägga huvuden som krävs
* Göra en asynkron begäran
* Skriva ut svaret

Lägg till den här koden i `HttpRequestMessage`:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att anropa `GetLanguages()` i funktionen `Main`. Leta upp `static void Main(string[] args)` och lägg till följande rader:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
dotnet run
```

## <a name="sample-response"></a>Exempelsvar

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive transkribering och språkidentifiering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Se även

* [Översätta text](quickstart-csharp-translate.md)
* [Translitterera text](quickstart-csharp-transliterate.md)
* [Identifiera språket efter indata](quickstart-csharp-detect.md)
* [Hämta alternativa översättningar](quickstart-csharp-dictionary.md)
* [Fastställa meningslängd utifrån indata](quickstart-csharp-sentences.md)
