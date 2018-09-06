---
title: Ruby Snabbstart för Azure Cognitive Services API för textanalys | Microsoft Docs
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med API för textanalys i Microsoft Cognitive Services på Azure.
services: cognitive-services
documentationcenter: ''
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 75c6476e86ee4a742e32ae0e7ffd27842f591843
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841770"
---
# <a name="quickstart-for-text-analytics-api-with-ruby"></a>Snabbstart för textanalys-API med Ruby 
<a name="HOLTop"></a>

Den här artikeln visar hur du [identifiera språk](#Detect), [analysera sentiment](#SentimentAnalysis), [extrahera nyckelfraser](#KeyPhraseExtraction), och [identifiera länkade entiteter](#Entities) med hjälp av den [Text Analytics API: er](//go.microsoft.com/fwlink/?LinkID=759711) med Ruby.

Referera till den [API-definitioner](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API: erna.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API för textanalys**. Du kan använda den **kostnadsfri nivå för 5 000 transaktioner per månad** att slutföra den här snabbstarten.

Du måste också ha den [slutpunkt och åtkomstnyckel](../How-tos/text-analytics-how-to-access-key.md) som genererades för dig när du registrerade dig. 

<a name="Detect"></a>

## <a name="detect-language"></a>Identifiera språk

Language identifiering API identifierar språket för dokument, med hjälp av den [identifiera språk metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

1. Skapa ett nytt projekt för Ruby i din favorit-IDE.
2. Lägg till koden nedan.
3. Ersätt den `accessKey` värde med en giltig åtkomstnyckel för din prenumeration.
4. Ersätt plats i `uri` (för närvarande `westus`) till den region som du registrerat dig för.
5. Kör programmet.

```ruby
require 'net/https'
require 'uri'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the accessKey string value with your valid access key.
accessKey = 'ENTER KEY HERE'

# Replace or verify the region.
#
# You must use the same region in your REST API call as you used to obtain your access keys.
# For example, if you obtained your access keys from the westus region, replace 
# "westcentralus" in the URI below with "westus".
#
# NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
# a free trial access key, you should not need to change this region.
uri = 'https://westus.api.cognitive.microsoft.com'
path = '/text/analytics/v2.0/'

uri = URI(uri + path + 'languages')

documents = { 'documents': [
    { 'id' => '1', 'text' => 'This is a document written in English.' },
    { 'id' => '2', 'text' => 'Este es un document escrito en Español.' },
    { 'id' => '3', 'text' => '这是一个用中文写的文件' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Svaret för identifiering av språk**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analysera sentiment

API för Attitydstextanalys Analysis detexts känsla av en uppsättning textposter, med hjälp av den [Sentiment metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). I följande exempel poängsätter två dokument, en i engelska och en annan på spanska.

Lägg till följande kod i koden från den [föregående avsnitt](#Detect).

```ruby
uri = URI(uri + path + 'sentiment')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' }
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Sentiment analysis svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser

Key frasen extrahering API: et extraherar nyckelfraser från en text dokument, med hjälp av den [Nyckelfraser metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). I följande exempel extraherar viktiga fraser för både engelska och spanska dokument.

Lägg till följande kod i koden från den [föregående avsnitt](#SentimentAnalysis).

```ruby
uri = URI(uri + path + 'keyPhrases')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Svaret för extrahering av diskussionsämne**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```
<a name="Entities"></a>

## <a name="identify-linked-entities"></a>Identifiera länkade entiteter

API för Entity Linking identifierar välkända entiteter i en text dokument, med hjälp av den [Entitetslänkning metoden](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). I följande exempel identifierar entiteter för dokument på engelska.

Lägg till följande kod i koden från den [föregående avsnitt](#KeyPhraseExtraction).

```ruby
uri = URI(uri + path + 'entities')

documents = { 'documents': [
    { 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id' => '2', 'language' => 'en', 'text' => 'The Seattle Seahawks won the Super Bowl in 2014.' },
]}

puts 'Please wait a moment for the results to appear.'

request = Net::HTTP::Post.new(uri)
request['Content-Type'] = "application/json"
request['Ocp-Apim-Subscription-Key'] = accessKey
request.body = documents.to_json

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

puts JSON::pretty_generate (JSON (response.body))
```

**Entiteten länkande svar**

Ett lyckat svar returneras i JSON, som visas i följande exempel: 

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "Ultra-high-definition television",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Ultra-high-definition television",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                    "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "2013 Seattle Seahawks season",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "2013 Seattle Seahawks season",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                    "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Powerbi](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
