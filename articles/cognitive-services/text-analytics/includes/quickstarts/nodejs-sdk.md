---
title: 'Snabbstart: Textanalys v3 – klientbiblioteket för Node.js | Microsoft Docs'
description: Kom igång med klientbiblioteket för Textanalys v3 för Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 8a045881a0533054633670349c22325851178b70
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371346"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[Referensdokumentation för v3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Bibliotekskällkod för v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Paket för v3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Referensdokumentation för v2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Bibliotekskällkod för v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Paket för v2 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [Skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org/).
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Skapa en resurs för Textanalys"  target="_blank">skapa en resurs för Textanalys <span class="docon docon-navigate-external x-hidden-focus"></span></a> i Microsoft Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från resursen som du skapade för att ansluta ditt program till API för textanalys. Du kommer att göra detta senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån för att testa tjänsten och uppgradera till en betald nivå för produktion senare.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för din app i ett konsolfönster (till exempel cmd, PowerShell eller bash) och navigera till den. 

```console
mkdir myapp 

cd myapp
```

Kör kommandot `npm init` för att skapa ett nodprogram med en `package.json`-fil. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installera klientbiblioteket

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Installera `@azure/ai-text-analytics` NPM-paket:

```console
npm install --save @azure/ai-text-analytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), som innehåller kodexemplen i den här snabbstarten. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Installera `@azure/cognitiveservices-textanalytics` NPM-paket:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), som innehåller kodexemplen i den här snabbstarten. 

---

Programmets `package.json`-fil kommer att uppdateras med beroenden.
Skapa en fil med namnet `index.js` och lägg till följande:

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Skapa variabler för din resurs Azure-slutpunkt och nyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektmodell

Klienten för Textanalys är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch.

Texten skickas till API:et som en lista med `documents`, som är `dictionary`-objekt som innehåller en kombination av attributen `id`, `text` och `language` beroende på vilken metod som används. `text`-attributet lagrar texten som ska analyseras i källa `language` och `id` kan vara vilket värde som helst. 

Svarsobjektet är en lista som innehåller analysinformationen om varje dokument. 

## <a name="code-examples"></a>Kodexempel

* [Klientautentisering](#client-authentication)
* [Attitydanalys](#sentiment-analysis) 
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven enhet](#named-entity-recognition-ner)
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="client-authentication"></a>Klientautentisering

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa ett nytt `TextAnalyticsClient`-objekt med din nyckel och slutpunkt som parametrar.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa ett nytt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient)-objekt med `credentials` och `endpoint` som en parameter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en matris av strängar som innehåller dokumentet som du vill analysera. Anropa klientens `analyzeSentiment()`-metod och hämta det returnerade `SentimentBatchResult`-objektet. Iterera genom listan över resultat och skriv ut varje dokuments ID och sentiment på dokumentnivå med förtroendepoäng. Resultaten innehåller sentiment på meningsnivå samt förskjutningar, längd och förtroendepoäng för varje dokument.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en lista över ordlisteobjekt som innehåller dokumenten som du vill analysera. Anropa klientens [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-)-metod och hämta det returnerade [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iterera genom listan över resultat och skriv ut varje dokuments ID och sentimentpoäng. Ett resultat nära 0 visar negativt sentiment medan ett resultat nära 1 indikerar ett positivt sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Språkidentifiering

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en matris av strängar som innehåller dokumentet som du vill analysera. Anropa klientens `detectLanguage()`-metod och hämta det returnerade `DetectLanguageResultCollection`. Iterera sedan genom resultaten och skriv ut varje dokuments ID och respektive primära språk.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en lista över ordlisteobjekt som innehåller dina dokument. Anropa klientens [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-)-metod och hämta det returnerade [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Iterera sedan genom resultaten och skriv ut varje dokuments ID och språk.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven enhet (NER)

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personliga uppgifter. 
> * Entitetslänkning är en begäran som skiljer sig från NER.

Skapa en matris av strängar som innehåller dokumentet som du vill analysera. Anropa klientens `recognizeEntities()`-metod och hämta `RecognizeEntitiesResult`-objektet. Iterera genom listan med resultat och skriv ut entitetsnamn, typ, undertyp, förskjutning, längd och poäng.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="using-ner-to-detect-personal-information"></a>Använd NER för att identifiera personliga uppgifter

Skapa en matris av strängar som innehåller dokumentet som du vill analysera. Anropa klientens `recognizePiiEntities()`-metod och hämta `EntitiesBatchResult`-objektet. Iterera genom listan med resultat och skriv ut entitetsnamn, typ, undertyp, förskjutning, längd och poäng.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>Entity Linking

Skapa en matris av strängar som innehåller dokumentet som du vill analysera. Anropa klientens `recognizeLinkedEntities()`-metod och hämta `RecognizeLinkedEntitiesResult`-objektet. Iterera genom listan med resultat och skriv ut enhets namn, ID, datakälla, URL och matchningar. Varje objekt i matrisen `matches` innehåller förskjutning, längd och poäng för matchningen.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

> [!NOTE]
> I version 2.1 ingår entitetslänkning i NER-svaret.

Skapa en lista över objekt som innehåller dina dokument. Anropa klientens [entitet()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-)-metod och hämta objektet [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Iterera genom listan över resultat och skriv ut varje dokuments ID. Skriv ut Wikipedia-namn, typ och undertyper (om de finns) samt platserna i den ursprungliga texten för varje identifierad entitet.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

Skapa en matris av strängar som innehåller dokumentet som du vill analysera. Anropa klientens `extractKeyPhrases()`-metod och hämta det returnerade `ExtractKeyPhrasesResult`-objektet. Iterera genom resultaten och skriv ut varje dokuments ID och alla identifierade nyckelfraser.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en lista över objekt som innehåller dina dokument. Anropa klientens [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-)-metod och hämta det returnerade objektet [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult). Iterera genom resultaten och skriv ut varje dokuments ID och alla identifierade nyckelfraser.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Kör din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Köra programmet

Kör programmet med kommandot `node` på din snabbstartsfil.

```console
node index.js
```
