---
title: 'Snabb start: Textanalys v3-klient bibliotek för Node. js | Microsoft Docs'
description: Kom igång med v3-Textanalys klient biblioteket för Node. js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: c50326cf308d7d68f08fa5282f2baaa6b490d543
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987937"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

[v3 referens dokumentation](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

[v2 referens dokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 bibliotek käll kod](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2-paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node. js](https://nodejs.org/).

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

> [!NOTE]
> Du kan också köra den här versionen av klient biblioteket för Textanalys [i webbläsaren](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot `npm init` för att skapa ett Node-program med en `package.json`-fil. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `@azure/cognitiveservices-textanalytics` NPM-paket:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Appens `package.json`-fil kommer att uppdateras med beroenden.

Skapa en fil med namnet `index.js` och Lägg till följande bibliotek:

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Skapa variabler för resursens Azure-slutpunkt och nyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch.

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination av `id`, `text`och `language` attribut beroende på vilken metod som används. Attributet `text` lagrar texten som ska analyseras i ursprungs `language`och `id` kan vara vilket värde som helst. 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kod exempel

* [Klientautentisering](#client-authentication)
* [Attitydanalys](#sentiment-analysis) 
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner)
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="client-authentication"></a>Klientautentisering

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa ett nytt `TextAnalyticsClient`-objekt med din nyckel och slut punkt som parametrar.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa ett nytt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) -objekt med `credentials` och `endpoint` som en parameter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Känsloanalys

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `analyzeSentiment()` metod och hämta det returnerade `SentimentBatchResult`-objektet. Gå igenom listan över resultat och skriv ut varje dokuments ID, sentiment på dokument nivå med förtroende poäng. För varje dokument innehåller resultatet menings nivå sentiment tillsammans med förskjutningar, längd och förtroende poäng.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en lista över lexikon objekt som innehåller dokumenten som du vill analysera. Anropa klientens [sentiment ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) Metod och hämta den returnerade [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Upprepa listan över resultat och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Språkspårning

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `detectLanguages()` metod och hämta den returnerade `DetectLanguageResult`. Iterera sedan igenom resultaten och skriv ut varje dokuments ID, med respektive primära och identifierade språk.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Detected Language French
        Primary Language French
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en lista över lexikon objekt som innehåller dina dokument. Anropa klientens [detectLanguage ()-](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) Metod och hämta den returnerade [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Iterera sedan igenom resultaten och skriv ut dokumentets ID och språk.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Enhets länkning är en separat begäran än NER.

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizeEntities()` metod och hämta `RecognizeEntitiesResult`-objektet. Iterera igenom listan över resultat och skriv ut entitetsnamnet, typ, undertyp, förskjutning, längd och poäng.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="using-ner-to-detect-personal-information"></a>Använda NER för att identifiera personlig information

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizePiiEntities()` metod och hämta `EntitiesBatchResult`-objektet. Iterera igenom listan över resultat och skriv ut entitetsnamnet, typ, undertyp, förskjutning, längd och poäng.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entitetslänkning

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizeLinkedEntities()` metod och hämta `RecognizeLinkedEntitiesResult`-objektet. Iterera igenom listan över resultat och skriv ut enhets namn, ID, data källa, URL och matchningar. Varje objekt i `matches` matris innehåller förskjutning, längd och Poäng för matchningen.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

> [!NOTE]
> I version 2,1 ingår enhets länkning i NER-svaret.

Skapa en lista med objekt som innehåller dina dokument. Anropa klientens [entiteter ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) -Metod och hämta [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) -objektet. Upprepa i listan över resultat och skriv ut varje dokuments ID. För varje identifierad entitet skriver du ut dess Wikipedia-namn, typ och under typer (om de finns) samt platserna i den ursprungliga texten.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Kör koden med `node index.js` i konsol fönstret.

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

## <a name="key-phrase-extraction"></a>Extraktion av nyckelfraser

#### <a name="version-30-previewtabversion-3"></a>[Version 3,0 – för hands version](#tab/version-3)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `extractKeyPhrases()` metod och hämta det returnerade `ExtractKeyPhrasesResult`-objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och alla identifierade nyckel fraser.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kör koden med `node index.js` i konsol fönstret.

### <a name="output"></a>Resultat

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21tabversion-2"></a>[Version 2,1](#tab/version-2)

Skapa en lista med objekt som innehåller dina dokument. Anropa klientens [()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) -Metod och hämta det returnerade [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) -objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och alla identifierade nyckel fraser.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Kör koden med `node index.js` i konsol fönstret.

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

Kör programmet med kommandot `node` på snabb starts filen.

```console
node index.js
```
