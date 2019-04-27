---
title: Hantera innehållstyper – Azure Logic Apps | Microsoft Docs
description: Lär dig hur Logikappar hanterar innehållstyper vid designtillfället och körtid
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2a9318317d5a01136a42b4fb6d580bafaf53ec4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685769"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Hantera innehållstyper i Azure Logic Apps

Olika typer av innehåll kan flöda via en logikapp, till exempel JSON, XML, flata filer och binära data. Även om Logic Apps har stöd för alla typer av innehåll, vissa har inbyggt stöd och kräver inte omvandling eller konvertering i logic apps. Andra typer kan kräva omvandling eller konvertering efter behov. Den här artikeln beskrivs hur Logikappar hanterar typer av innehåll och hur du kan korrekt konvertera eller konvertera dessa typer vid behov.

För att fastställa lämplig metod för att hantera innehållstyper Logic Apps är beroende av den `Content-Type` huvudets värde i HTTP-anrop, till exempel:

* [Application/json](#application-json) (interna typ)
* [text/plain](#text-plain) (interna typ)
* [Application/xml och application/octet-ström](#application-xml-octet-stream)
* [Andra typer av innehåll](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps lagrar och hanterar alla förfrågningar med den *application/json* innehållstyp som ett JavaScript-Notation (JSON)-objekt. Som standard kan du parsa JSON-innehåll utan att någon omvandling. Du kan använda ett uttryck för att analysera en begäran med ett huvud med ”application/json” innehållstyp. Det här exemplet returnerar värdet `dog` från den `animal-type` matris utan omvandling: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Om du arbetar med JSON-data som inte anger en rubrik, du kan skicka dessa data till JSON manuellt med hjälp av den [json() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#json), till exempel: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Skapa token för JSON-egenskaper

Logic Apps innehåller funktioner att generera användarvänliga token som representerar egenskaper i JSON-innehåll så att du kan referera till och använda dessa egenskaper enklare i logikappens arbetsflöde.

* **Begäran-utlösare**

  När du använder den här utlösaren i Logic App Designer kan du ange en JSON-schema som beskriver den nyttolast som du förväntar dig att ta emot. 
  Designern Parsar JSON-innehåll med hjälp av det här schemat och genererar användarvänliga token som representerar egenskaper i JSON-innehåll. 
  Du kan enkelt referera till och använda dessa egenskaper under hela logikappens arbetsflöde. 
  
  Om du inte har ett schema kan generera du schemat. 
  
  1. I begäran-utlösaren väljer **Använd exempel för att generera schemat**.  
  
  2. Under **Skriv eller klistra in en JSON-exempelnyttolast**, ange en exempelnyttolast och välj sedan **klar**. Exempel: 

     ![Ange JSON-exempelnyttolast](./media/logic-apps-content-type/request-trigger.png)

     Genererade schemat visas nu i utlösaren.

     ![Ange JSON-exempelnyttolast](./media/logic-apps-content-type/generated-schema.png)

     Här är den underliggande definitionen för begärandeutlösaren i vyn Kodredigeraren:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. I din begäran, se till att du inkluderar en `Content-Type` rubrik och ange sidhuvudets värde till `application/json`.

* **Parsa JSON-åtgärd**

  När du använder den här åtgärden i Logic App Designer kan du parsa JSON-utdata och generera användarvänliga token som representerar egenskaper i JSON-innehåll. 
  Du kan enkelt referera till och använda dessa egenskaper under hela logikappens arbetsflöde. Liknar begäranutlösaren, du kan ange eller skapa en JSON-schema som beskriver JSON-innehåll som du vill parsa. 
  På så sätt kan du enkelt använda data från Azure Service Bus, Azure Cosmos DB och så vidare.

  ![Parsa JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

När logikappen tar emot HTTP-meddelanden som har den `Content-Type` rubrik inställd `text/plain`, logikappen lagrar meddelandena i obearbetat format. Om du inkluderar dessa meddelanden i efterföljande åtgärder utan att använda databyte begäranden går med den `Content-Type` rubrik inställd `text/plain`. 

När du arbetar med en platt fil kan du till exempel få en HTTP-begäran med den `Content-Type` rubrik inställd `text/plain` innehållstyp:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Om du sedan skickar denna begäran i en senare åtgärd som brödtexten för en annan begäran, till exempel `@body('flatfile')`, den andra begäran har också en `Content-Type` -huvud som har angetts till `text/plain`. Om du arbetar med data som oformaterad text men inte har angett en rubrik, kan du manuellt omvandla dessa data till text med hjälp av den [string() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#string) , till exempel det här uttrycket: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Application/xml och application/octet-ström

Logic Apps bevarar alltid den `Content-Type` i en mottagna HTTP-begäran eller ett svar. Om logikappen tar emot innehåll med `Content-Type` inställd `application/octet-stream`, och inkluderar att innehåll i en senare åtgärd utan omvandling, utgående begäran har också `Content-Type` inställd `application/octet-stream`. På så sätt kan kan Logic Apps garantera att data inte får gå förlorade när du flyttar genom arbetsflödet. Dock lagras åtgärdstillstånd, eller indata och utdata, i ett JSON-objekt när tillståndet går genom arbetsflödet. 

## <a name="converter-functions"></a>Converter funktioner

För att bevara vissa datatyper, Logic Apps konverterar innehållet till en binary base64-kodad sträng med tillämpliga metadata som bevarar både den `$content` nyttolasten och `$content-type`, som automatiskt konverteras. 

Den här listan beskrivs hur Logic Apps konverterar innehåll när du använder dessa [functions](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Sändningar data till `application/json`
* `xml()`: Sändningar data till `application/xml`
* `binary()`: Sändningar data till `application/octet-stream`
* `string()`: Sändningar data till `text/plain`
* `base64()`: Konverterar innehållet till en base64-sträng
* `base64toString()`: Konverterar en base64-kodad sträng `text/plain`
* `base64toBinary()`: Konverterar en base64-kodad sträng `application/octet-stream`
* `encodeDataUri()`: Kodar en sträng som en bitmatris dataUri
* `decodeDataUri()`: Avkodar ett `dataUri` till en bytematris

Exempel: Om du får en HTTP-begäran där `Content-Type` inställd `application/xml`, till exempel det här innehållet:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Du kan omvandla det här innehållet med hjälp av den `@xml(triggerBody())` uttryck med den `xml()` och `triggerBody()` fungerar och sedan använda det här innehållet senare. Du kan också använda den `@xpath(xml(triggerBody()), '/CustomerName')` uttryck med den `xpath()` och `xml()` funktioner. 

## <a name="other-content-types"></a>Andra typer av innehåll

Logikappar fungerar med och har stöd för andra typer av innehåll, men kan kräva att du manuellt får meddelandets brödtext genom att avkoda de `$content` variabeln.

Anta exempelvis att logikappen hämtar utlöses av en begäran med den `application/x-www-url-formencoded` innehållstyp. Att bevara alla data, den `$content` variabel i begärandetexten har en nyttolast som är kodade som en base64-sträng:

`CustomerName=Frank&Address=123+Avenue`

Eftersom begäran inte är oformaterad text eller JSON, lagras begäran i åtgärden på följande sätt:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logikappar innehåller inbyggda funktioner för hantering av formulärdata, till exempel: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Alternativt kan du manuellt kan komma åt data med hjälp av ett uttryck, till exempel det här exemplet:

`@string(body('formdataAction'))` 

Om du vill ha utgående begäran om att ha samma `application/x-www-url-formencoded` innehållstypsrubrik, du kan lägga till begäran åtgärdsbody utan någon omvandling genom att använda ett uttryck som `@body('formdataAction')`. Men den här metoden fungerar bara om brödtexten är den enda parametern i den `body` indata. Om du försöker använda den `@body('formdataAction')` uttryck i en `application/json` begär kan du få ett körningsfel eftersom texten skickas kodade.
