---
title: 'Snabbstart: Anropa API:et för textanalys med hjälp av Node.js'
titleSuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API för textanalys.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697485"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Anropa den kognitiva tjänsten för textanalys med hjälp av Node.js
<a name="HOLTop"></a>

Använd den här snabb starten för att börja analysera språk med Textanalys SDK för Node. js. Även om [Textanalys](//go.microsoft.com/fwlink/?LinkID=759711) REST API är kompatibel med de flesta programmeringsspråk, är SDK ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Se [API-definitionerna](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/)
* Textanalys [SDK för Node. js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) du kan installera SDK: n med:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Du måste även ha [slutpunkten och åtkomstnyckeln](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som genererades åt dig vid registreringen.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Skapa ett Node. js-program och installera SDK: n

När du har installerat Node. js skapar du ett Node-projekt. Skapa en ny katalog för din app och navigera till dess katalog.

```mkdir myapp && cd myapp```

Kör ```npm init``` för att skapa ett Node-program med en Package. JSON-fil. `ms-rest-azure` Installera och `azure-cognitiveservices-textanalytics` NPM-paketen:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Appens Package. JSON-fil kommer att uppdateras med beroenden.

## <a name="authenticate-your-credentials"></a>Autentisera dina autentiseringsuppgifter

Skapa en ny fil `index.js` i projekt roten och importera de installerade biblioteken

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Skapa en variabel för din Textanalys prenumerations nyckel.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Vi rekommenderar att du använder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)för säker distribution av hemligheter i produktions system.
>

## <a name="create-a-text-analytics-client"></a>Skapa en Textanalys-klient

Skapa ett nytt `TextAnalyticsClient` objekt med `credentials` som en parameter. Använd rätt Azure-region för din Textanalys prenumeration.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en lista med objekt som innehåller dokumenten som du vill analysera. Nytto lasten till API: et består av en `documents`lista över, som `id`innehåller `language`ett, `text` -och-attribut. Attributet lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. `text` 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Anropa `client.sentiment` och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en lista med objekt som innehåller dina dokument. Nytto lasten till API: et består av en `documents`lista över, som `id` innehåller `text` ett-och-attribut. Attributet lagrar texten som ska analyseras `id` och kan vara vilket värde som helst. `text`

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

Anropa `client.detectLanguage()` och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och det första returnerade språket.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Enhets igenkänning

Skapa en lista med objekt som innehåller dina dokument. Nytto lasten till API: et består av en `documents`lista över, som `id`innehåller `language`ett, `text` -och-attribut. Attributet lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. `text`

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Anropa `client.entities()` och få resultatet. Iterera sedan igenom resultaten och skriv ut dokumentets ID. För varje identifierad entitet skriver du ut dess Wikipedia-namn, typ och under typer (om de finns) samt platserna i den ursprungliga texten.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en lista med objekt som innehåller dina dokument. Nytto lasten till API: et består av en `documents`lista över, som `id`innehåller `language`ett, `text` -och-attribut. Attributet lagrar texten som ska analyseras, `language` är språket i dokumentet och `id` kan vara vilket värde som helst. `text`

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

Anropa `client.keyPhrases()` och få resultatet. Iterera sedan igenom resultaten och skriv ut varje dokuments ID och alla identifierade nyckel fraser.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Output

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också

 [Översikt över Textanalys](../overview.md) [Vanliga frågor och svar](../text-analytics-resource-faq.md)
