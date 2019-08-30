---
title: 'Snabbstart: Textanalys klient bibliotek för python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja använda API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 0543bc639e60c65d9ab5a6cc810ddf6b7d10087a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142732"
---
# <a name="quickstart-text-analytics-client-library-for-python"></a>Snabbstart: Klient bibliotek för text analys för python
<a name="HOLTop"></a>

Kom igång med Textanalys klient biblioteket för python. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. 

Använd Textanalys klient bibliotek för python för att utföra:

* Sentimentanalys
* Språkidentifiering
* Enhets igenkänning
* Extrahering av nyckelfraser


[Referens dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Textanalys med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md) eller [Azure CLI](../../cognitive-services-apis-create-account-cli.md) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i 7 dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/)

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `TEXT_ANALYTICS_SUBSCRIPTION_KEY`med namnet.

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE. Importera sedan följande bibliotek.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

Skapa variabler för resursens Azure-slut punkt och prenumerations nyckel. Hämta de här värdena från miljövariablerna TEXT_ANALYTICS_SUBSCRIPTION_KEY och TEXT_ANALYTICS_ENDPOINT. Om du har skapat dessa miljövariabler när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till variablerna.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```python
import os

key_var_name = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TEXT_ANALYTICS_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]

credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch. 

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination `id`av `text`,, `language` och attribut beroende på vilken metod som används. Attributet lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara vilket värde som helst. `text` 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande med Textanalys klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett nytt [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) -objekt `credentials` med `text_analytics_url` och som en parameter. Använd rätt Azure-region för din Textanalys-prenumeration (till `westcentralus`exempel).

```python
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Anropa funktionen [sentiment ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämta resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "I had the best day of my life."
    }
]
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Sentiment Score: ",
          "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
```

## <a name="language-detection"></a>Språkidentifiering

Med hjälp av klienten som skapades tidigare anropar du [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och får resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och det första returnerade språket.

```python
documents = [
    {
        'id': '1',
        'text': 'This is a document written in English.'
    }
]
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id, ", Language: ",
          document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
```

## <a name="entity-recognition"></a>Enhets igenkänning

Med hjälp av klienten som skapades tidigare anropar du funktionen [entiteter ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och entiteterna i det.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    }
]
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ", entity.name, "\tType: ",
              entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```

### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Med hjälp av klienten som skapades tidigare anropar du funktionen [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) och hämtar resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och de viktigaste fraserna i det.

```python
documents = [
    {
        "id": "1",
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    }
]
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t", phrase)
```


### <a name="output"></a>Output

```console
Document Id:  3
    Phrases:
         cat
         veterinarian
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är associerade med resurs gruppen.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Översikt över Textanalys](../overview.md)
* [Sentiment-analys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).
