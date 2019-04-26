---
title: 'Snabbstart: Översätta text, Ruby – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du översätta text från ett språk till ett annat med Translator Text-API:et med Ruby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
origin.date: 02/08/2019
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: f54421460e3af46570b67bb541df3afb755a1347
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509416"
---
# <a name="quickstart-translate-text-with-the-translator-text-rest-api-ruby"></a>Snabbstart: Översätta text med Translator Text REST API (Ruby)

I den här snabbstarten ska du översätta text från ett språk till ett annat med Translator Text-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) eller senare för att köra den här koden.

För att använda Translator Text-API:et behöver du också en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="translate-request"></a>Översättningsbegäran

Följande kod översätter källtext från ett språk till ett annat med hjälp av metoden [Översätt](./reference/v3-0-translate.md).

1. Skapa ett nytt Ruby-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `key` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'
region = 'your region'
host = 'https://api.translator.azure.cn'
path = '/translate?api-version=3.0'

# Translate to German and Italian.
params = '&to=de&to=it'

uri = URI (host + path + params)

text = 'Hello, world!'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['Ocp-Apim-Subscription-Region'] = region
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="translate-response"></a>Översättningssvar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

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
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska exempelkoden för den här snabbstarten och andra, inklusive transkribering och språkidentifiering, samt andra Translator Text-exempelprojekt på GitHub.

> [!div class="nextstepaction"]
> [Utforska Ruby-exempel på GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)

