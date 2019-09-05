---
title: 'Snabbstart: Textanalys klient bibliotek för Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API för textanalys.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: b5eb327daa74d8e6f384d6f8e1054fb265ce4a7d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375876"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Snabbstart: Klient bibliotek för text analys för Node. js
<a name="HOLTop"></a>

Kom igång med Textanalys klient biblioteket för. Node. js. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. 

Använd Textanalys klient bibliotek för Node. js för att utföra:

* Sentimentanalys
* Språkidentifiering
* Enhets igenkänning
* Extrahering av nyckelfraser

[Referens dokumentation](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [paket (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node. js](https://nodejs.org/).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-text-analytics-azure-resource"></a>Skapa en Textanalys Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för text analys med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md) eller [Azure CLI](../../cognitive-services-apis-create-account-cli.md) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i 7 dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/)

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `TEXTANALYTICS_SUBSCRIPTION_KEY`med namnet.

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot för att skapa ett Node-program med `package.json` en fil. `npm init` 

```console
npm init
```

Skapa en fil med `index.js` namnet och importera följande bibliotek:

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

Skapa variabler för resursens Azure-slut punkt och prenumerations nyckel. Hämta de här värdena från miljövariablerna TEXT_ANALYTICS_SUBSCRIPTION_KEY och TEXT_ANALYTICS_ENDPOINT. Om du har skapat dessa miljövariabler när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till variablerna.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

`@azure/ms-rest-js` Installera och `@azure/cognitiveservices-textanalytics` NPM-paketen:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Appens `package.json` fil kommer att uppdateras med beroenden.

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch.

Text skickas till API: et som en lista över `documents`, som är `dictionary` objekt som innehåller en kombination `id`av `text`,, `language` och attribut beroende på vilken metod som används. Attributet lagrar texten som ska analyseras i ursprunget `language`och `id` kan vara vilket värde som helst. `text` 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kod exempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis)
* [Språk identifiering](#language-detection)
* [Enhets igenkänning](#entity-recognition)
* [Extrahering av nyckel fraser](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett nytt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) -objekt `credentials` med `endpoint` och som en parameter.

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en lista med objekt som innehåller dokumenten som du vill analysera.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
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
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en lista med objekt som innehåller dina dokument.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
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
```

## <a name="entity-recognition"></a>Enhets igenkänning

Skapa en lista med objekt som innehåller dina dokument.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
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
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en lista med objekt som innehåller dina dokument.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
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
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>Köra programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

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
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).
