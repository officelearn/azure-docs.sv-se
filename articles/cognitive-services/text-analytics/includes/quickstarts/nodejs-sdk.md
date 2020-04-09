---
title: 'Snabbstart: Text Analytics v3-klientbibliotek för Node.js | Microsoft-dokument'
description: Kom igång med v3 Text Analytics-klientbiblioteket för Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 0cfe651a91cc16e7d4b58af67dac29fe5106a48c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986623"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

[v3 Referensdokumentation](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 Biblioteks källkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 Paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-prover](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[v2 Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 Biblioteks källkod](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2-prover](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org/).
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt. När den har distribuerats klickar du på **Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den`F0`kostnadsfria prisnivån ( ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den. 

```console
mkdir myapp 

cd myapp
```

Kör `npm init` kommandot för att skapa ett `package.json` nodprogram med en fil. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installera klientbiblioteket

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Installera `@azure/ai-text-analytics` NPM-paketen:

```console
npm install --save @azure/ai-text-analytics@1.0.0-preview.3
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), som innehåller kodexemplen i den här snabbstarten. 

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Installera `@azure/cognitiveservices-textanalytics` NPM-paketen:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), som innehåller kodexemplen i den här snabbstarten. 

---

Appens `package.json` fil uppdateras med beroenden.
Skapa en `index.js` fil med namnet och lägg till följande:

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

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

Skapa variabler för resursens Azure-slutpunkt och -nyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektmodell

Text Analytics-klienten `TextAnalyticsClient` är ett objekt som autentiserar till Azure med hjälp av din nyckel. Klienten innehåller flera metoder för att analysera text, som en enda sträng eller en batch.

Text skickas till API:et `documents`som `dictionary` en lista över `id` `text`, `language` som är objekt som innehåller en kombination av , och attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara valfritt värde. 

Svarsobjektet är en lista som innehåller analysinformationen för varje dokument. 

## <a name="code-examples"></a>Kodexempel

* [Klientautentisering](#client-authentication)
* [Attitydanalys](#sentiment-analysis) 
* [Språkidentifiering](#language-detection)
* [Namngiven entitetsigenkänning](#named-entity-recognition-ner)
* [Entitetslänkning](#entity-linking)
* [Extraktion av nyckelfraser](#key-phrase-extraction)

## <a name="client-authentication"></a>Klientautentisering

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa ett `TextAnalyticsClient` nytt objekt med nyckeln och slutpunkten som parametrar.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa ett nytt [TextAnalyticsClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) med `credentials` och `endpoint` som parameter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalys

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `analyzeSentiment()` metod och hämta `SentimentBatchResult` det returnerade objektet. Iterera genom resultatlistan och skriv ut varje dokuments ID, sentiment på dokumentnivå med konfidenspoäng. För varje dokument innehåller resultatet sentiment på mening på mening på mening på mening på mening på mening på mening på mening på mening på meningsnivå på meningsnivå tillsammans med förskjutningar, längd och konfidenspoäng.

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

Kör koden `node index.js` med i konsolfönstret.

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

Skapa en lista med ordlisteobjekt som innehåller de dokument som du vill analysera. Anropa klientens [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) metod och få den returnerade [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iterera genom resultatlistan och skriv ut varje dokuments ID- och sentimentpoäng. En poäng närmare 0 indikerar en negativ känsla, medan en poäng närmare 1 indikerar en positiv känsla.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Kör koden `node index.js` med i konsolfönstret.

### <a name="output"></a>Resultat

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Språkidentifiering

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `detectLanguage()` metod och få `DetectLanguageResultCollection`den returnerade . Sedan iterera genom resultaten och skriva ut varje dokuments ID med respektive primärt språk.

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

Kör koden `node index.js` med i konsolfönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en lista med ordlisteobjekt som innehåller dina dokument. Anropa klientens [metoden detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) och hämta den returnerade [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Sedan iterera genom resultaten och skriva ut varje dokuments ID och språk.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Kör koden `node index.js` med i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Namngiven entitetsigenkänning (NER)

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

> [!NOTE]
> I `3.0-preview`version :
> * NER innehåller separata metoder för att upptäcka personlig information. 
> * Entitetslänkning är en separat begäran än NER.

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizeEntities()` metod och `RecognizeEntitiesResult` hämta objektet. Iterera genom resultatlistan och skriv ut entitetsnamn, typ, undertyp, förskjutning, längd och poäng.

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

Kör koden `node index.js` med i konsolfönstret.

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

## <a name="using-ner-to-detect-personal-information"></a>Använda NER för att upptäcka personlig information

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizePiiEntities()` metod och `EntitiesBatchResult` hämta objektet. Iterera genom resultatlistan och skriv ut entitetsnamn, typ, undertyp, förskjutning, längd och poäng.


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

Kör koden `node index.js` med i konsolfönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>Entity Linking

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `recognizeLinkedEntities()` metod och `RecognizeLinkedEntitiesResult` hämta objektet. Iterera genom resultatlistan och skriv ut entitetsnamn, ID, datakälla, url och matchningar. Varje objekt `matches` i matrisen innehåller förskjutning, längd och poäng för den matchen.

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

Kör koden `node index.js` med i konsolfönstret.

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
> I version 2.1 inkluderas entitetslänkning i NER-svaret.

Skapa en lista med objekt som innehåller dina dokument. Anropa klientens [entitets-metoden()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) och hämta [entitiesbatchResult-objektet.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) Iterera genom resultatlistan och skriv ut varje dokuments ID. För varje identifierad entitet skriver du ut dess wikipedia-namn, typen och undertyperna (om sådana finns) samt platserna i den ursprungliga texten.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Kör koden `node index.js` med i konsolfönstret.

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

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

Skapa en matris med strängar som innehåller det dokument som du vill analysera. Anropa klientens `extractKeyPhrases()` metod och hämta `ExtractKeyPhrasesResult` det returnerade objektet. Iterera genom resultaten och skriv ut varje dokuments ID och eventuella identifierade nyckelfraser.

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

Kör koden `node index.js` med i konsolfönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

Skapa en lista med objekt som innehåller dina dokument. Anropa klientens [keyPhrases()-metod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) och hämta det returnerade [KeyPhraseBatchResult-objektet.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) Iterera genom resultaten och skriv ut varje dokuments ID och eventuella identifierade nyckelfraser.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Kör koden `node index.js` med i konsolfönstret.

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

## <a name="run-the-application"></a>Köra appen

Kör programmet med `node` kommandot på snabbstartsfilen.

```console
node index.js
```
