---
title: 'Snabbstart: Hämta språk som stöds – Translator Text API, C#'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten hämtar du en lista över språk som stöds för översättning, transkribering och ordlistesökningar med hjälp av Translator Text-API:t med C#.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 82e051895bd5ba8542fa32aa147b050a25c43c84
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318409"
---
# <a name="quickstart-get-supported-languages-with-c35"></a>Snabbstart: Hämta språk som stöds med C&#35;

I den här snabbstarten hämtar du en lista över språk som stöds för översättning, transkribering och ordlistesökningar med hjälp av Translator Text-API:t.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Visual Studio 2017](https://www.visualstudio.com/downloads/) för att köra den här koden på Windows. (Den kostnadsfria Community Edition fungerar.)

För att använda Translator Text-API:et behöver du även en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="languages-request"></a>Språkbegäran

> [!TIP]
> Hämta den senaste koden från [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

Följande kod hämtar en lista över språk som stöds för översättning, transkribering och ordlistesökning och exempel, med hjälp av metoden [Languages](./reference/v3-0-languages.md) (Språk).

1. Skapa ett nytt C#-projekt i din favoritutvecklingsmiljö.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/languages?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static void GetLanguages()
        {
            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
            var uri = host + path;
            var response = await client.GetAsync(uri);
            var result = await response.Content.ReadAsStringAsync();
            var json = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(result), Formatting.Indented);
            // Note: If writing to the console, set this.
            // Console.OutputEncoding = UnicodeEncoding.UTF8;
            System.IO.File.WriteAllBytes("output.txt", Encoding.UTF8.GetBytes(json));
        }

        static void Main(string[] args)
        {
            GetLanguages();
            Console.ReadLine();
        }
    }
}
```

## <a name="languages-response"></a>Språksvar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

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

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive översättning och transkribering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)
