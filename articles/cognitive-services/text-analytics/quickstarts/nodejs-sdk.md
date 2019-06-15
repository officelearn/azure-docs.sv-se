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
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081521"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Anropa den kognitiva tjänsten för textanalys med hjälp av Node.js
<a name="HOLTop"></a>

Använd den här snabbstarten om du vill analysera språk med Text Analytics SDK för Node.js. Medan den [textanalys](//go.microsoft.com/fwlink/?LinkID=759711) REST API är kompatibelt med de flesta programmeringsspråk, SDK innehåller ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Se [API-definitionerna](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Node.js](https://nodejs.org/)
* Text Analytics [SDK för Node.js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) du kan installera SDK: N med:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Du måste även ha [slutpunkten och åtkomstnyckeln](../How-tos/text-analytics-how-to-access-key.md) som genererades åt dig vid registreringen.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Skapa en Node.js-program och installera SDK

När du har installerat Node.js, skapa ett nod-projekt. Skapa en ny katalog för din app och navigera till programmets katalog.

```mkdir myapp && cd myapp```

Kör ```npm init``` att skapa en node-App med en package.json-fil. Installera den `ms-rest-azure` och `azure-cognitiveservices-textanalytics` NPM-paket:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Appens package.json-fil kommer att uppdateras med beroenden.

## <a name="authenticate-your-credentials"></a>Autentisera dina autentiseringsuppgifter

Skapa en ny fil `index.js` i projektet rot och importera de installerade bibliotek

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Skapa en variabel för din prenumerationsnyckel för textanalys.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> För säker distribution av hemligheter i produktionssystem bör du använda [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Skapa en Text Analytics-klient

Skapa en ny `TextAnalyticsClient` objekt med `credentials` som en parameter. Använd rätt Azure-regionen för din Text Analytics-prenumeration.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en lista med objekt, som innehåller de dokument som du vill analysera. Nyttolasten i API: n består av en lista över `documents`, som innehåller en `id`, `language`, och `text` attribut. Den `text` attributet lagrar texten som ska analyseras, `language` är språket för dokumentet och `id` kan vara vilket värde. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Anropa `client.sentiment` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och attitydsresultatet. Ett värde närmare 0 anger negativ känsla, medan en poäng närmare 1 anger positiv känsla.

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

Köra din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Språkspårning

Skapa en lista med objekt som innehåller dina dokument. Nyttolasten i API: n består av en lista över `documents`, som innehåller en `id` och `text` attribut. Den `text` attributet lagrar texten som ska analyseras, och `id` kan vara vilket värde.

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

Anropa `client.detectLanguage()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och det första språket som returneras.

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

Köra din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Igenkänning av entiteter

Skapa en lista med objekt, som innehåller dina dokument. Nyttolasten i API: n består av en lista över `documents`, som innehåller en `id`, `language`, och `text` attribut. Den `text` attributet lagrar texten som ska analyseras, `language` är språket för dokumentet och `id` kan vara vilket värde.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Anropa `client.entities()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID. För varje identifierad entitet, skriva ut sitt wikipedia-namn, typ och undertyper (om det finns) samt platser i den ursprungliga texten.

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

Köra din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

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

Skapa en lista med objekt, som innehåller dina dokument. Nyttolasten i API: n består av en lista över `documents`, som innehåller en `id`, `language`, och `text` attribut. Den `text` attributet lagrar texten som ska analyseras, `language` är språket för dokumentet och `id` kan vara vilket värde.

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

Anropa `client.keyPhrases()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och alla identifierade viktiga fraser.

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

Köra din kod med `node index.js` i konsolfönstret.

### <a name="output"></a>Resultat

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
