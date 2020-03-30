---
title: Hantera innehållstyper
description: Lär dig hur du hanterar olika innehållstyper i arbetsflöden under designtid och körning i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666881"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Hantera innehållstyper i Azure Logic Apps

Olika innehållstyper kan flöda genom en logikapp, till exempel JSON, XML, platta filer och binära data. Logic Apps stöder alla innehållstyper, men vissa har inbyggt stöd och kräver inte cast eller konvertering i logikapparna. Andra typer kan kräva gjutning eller konvertering vid behov. I den här artikeln beskrivs hur Logic Apps hanterar innehållstyper och hur du kan casta eller konvertera dessa typer på rätt sätt när det behövs.

För att avgöra rätt sätt för hantering av `Content-Type` innehållstyper förlitar sig Logic Apps på huvudvärdet i HTTP-anrop, till exempel:

* [program/json](#application-json) (inbyggt slag)
* [text/oformaterad](#text-plain) (ursprunglig text)
* [application/xml och application/octet-stream](#application-xml-octet-stream)
* [Andra innehållstyper](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps lagrar och hanterar alla begäranden med innehållstypen *Program/json* som ett JSON-objekt (JavaScript Notation). Som standard kan du tolka JSON-innehåll utan någon castning. Om du vill tolka en begäran som har en rubrik med innehållstypen "application/json" kan du använda ett uttryck. I det här `dog` exemplet `animal-type` returneras värdet från matrisen utan att casta: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Om du arbetar med JSON-data som inte anger ett huvud kan du manuellt casta dessa data till JSON med hjälp av [funktionen json(),](../logic-apps/workflow-definition-language-functions-reference.md#json)till exempel: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Skapa token för JSON-egenskaper

Logic Apps ger dig möjlighet att generera användarvänliga token som representerar egenskaperna i JSON-innehåll så att du lättare kan referera till och använda dessa egenskaper i logikappens arbetsflöde.

* **Utlösare för begäran**

  När du använder den här utlösaren i Logic App Designer kan du ange ett JSON-schema som beskriver den nyttolast du förväntar dig att ta emot. 
  Designern tolkar JSON-innehåll med hjälp av det här schemat och genererar användarvänliga token som representerar egenskaperna i ditt JSON-innehåll. 
  Du kan sedan enkelt referera till och använda dessa egenskaper i logikappens arbetsflöde. 
  
  Om du inte har ett schema kan du skapa schemat. 
  
  1. I utlösaren Begäran väljer du **Använd exempelnyttolast för att generera schema**.  
  
  2. Under **Ange eller klistra in ett prov på JSON-nyttolast**anger du en nyttolast i exempel och väljer sedan **Klar**. Ett exempel: 

     ![Ge prov JSON nyttolast](./media/logic-apps-content-type/request-trigger.png)

     Det genererade schemat visas nu i utlösaren.

     ![Ge prov JSON nyttolast](./media/logic-apps-content-type/generated-schema.png)

     Här är den underliggande definitionen för utlösaren begäran i kodvyredigeraren:

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

  3. I din begäran måste du `Content-Type` inkludera ett huvud och `application/json`ange rubrikens värde på .

* **Tolka JSON-åtgärd**

  När du använder den här åtgärden i Logic App Designer kan du tolka JSON-utdata och generera användarvänliga token som representerar egenskaperna i ditt JSON-innehåll. 
  Du kan sedan enkelt referera till och använda dessa egenskaper i logikappens arbetsflöde. I likhet med utlösaren Begäran kan du tillhandahålla eller generera ett JSON-schema som beskriver det JSON-innehåll som du vill tolka. 
  På så sätt kan du lättare använda data från Azure Service Bus, Azure Cosmos DB och så vidare.

  ![Parsa JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/oformaterad

När logikappen `Content-Type` tar emot HTTP-meddelanden `text/plain`som har rubriken inställd på lagrar logikappen dessa meddelanden i rå form. Om du inkluderar dessa meddelanden i efterföljande åtgärder `Content-Type` utan att `text/plain`casta, går begäranden ut med huvudet inställt på . 

När du till exempel arbetar med en platt fil kan du `Content-Type` få `text/plain` en HTTP-begäran med rubriken inställd på innehållstyp:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Om du sedan skickar den här begäran i en senare åtgärd `@body('flatfile')`som brödtext för `Content-Type` en annan begäran, till exempel, har den andra begäran också ett huvud som är inställt på `text/plain`. Om du arbetar med data som är oformaterad text men inte har angett något huvud kan du casta data manuellt till text med hjälp av [funktionen string()](../logic-apps/workflow-definition-language-functions-reference.md#string) som det här uttrycket: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>application/xml och application/octet-stream

Logic Apps bevarar `Content-Type` alltid i en mottagen HTTP-begäran eller svar. Så om logikappen `Content-Type` tar `application/octet-stream`emot innehåll med inställt på , och du inkluderar `Content-Type` innehållet `application/octet-stream`i en senare åtgärd utan att casta, har den utgående begäran också ställt in på . På så sätt kan Logic Apps garantera att data inte försvinner när de går igenom arbetsflödet. Åtgärdstillståndet, eller indata och utdata, lagras dock i ett JSON-objekt medan tillståndet flyttas genom arbetsflödet. 

## <a name="converter-functions"></a>Omformarfunktioner

Om du vill bevara vissa datatyper konverterar Logic Apps innehåll till en binär bas64-kodad sträng med lämpliga metadata som bevarar både `$content` nyttolasten och `$content-type`den som konverteras automatiskt. 

I den här listan beskrivs hur Logic Apps konverterar innehåll när du använder dessa [funktioner:](../logic-apps/workflow-definition-language-functions-reference.md)

* `json()`: Kastar data till`application/json`
* `xml()`: Kastar data till`application/xml`
* `binary()`: Kastar data till`application/octet-stream`
* `string()`: Kastar data till`text/plain`
* `base64()`: Konverterar innehåll till en base64-kodad sträng
* `base64toString()`: Konverterar en base64-kodad sträng till`text/plain`
* `base64toBinary()`: Konverterar en base64-kodad sträng till`application/octet-stream`
* `dataUri()`: Konverterar en sträng till en data-URI
* `dataUriToBinary()`: Konverterar en data-URI till en binär sträng
* `dataUriToString()`: Konverterar en data-URI till en sträng

Om du till exempel får `Content-Type` en `application/xml`HTTP-begäran där den är inställd på , till exempel det här innehållet:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Du kan casta det `@xml(triggerBody())` här `xml()` innehållet `triggerBody()` genom att använda uttrycket med funktionerna och sedan använda det här innehållet senare. Du kan också `@xpath(xml(triggerBody()), '/CustomerName')` använda uttrycket med funktionerna `xpath()` och. `xml()` 

## <a name="other-content-types"></a>Andra innehållstyper

Logic Apps fungerar med och stöder andra innehållstyper, men kan kräva att `$content` du manuellt hämtar meddelandetexten genom att avkoda variabeln.

Anta till exempel att logikappen utlöses `application/x-www-url-formencoded` av en begäran med innehållstypen. Om du vill bevara `$content` alla data har variabeln i begäranden en nyttolast som är kodad som en base64-sträng:

`CustomerName=Frank&Address=123+Avenue`

Eftersom begäran inte är oformaterad text eller JSON lagras begäran i åtgärden på följande sätt:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps tillhandahåller inbyggda funktioner för hantering av formulärdata, till exempel: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formulärDataVärde()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formulärDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Du kan också komma åt data manuellt med hjälp av ett uttryck som det här exemplet:

`@string(body('formdataAction'))` 

Om du vill att den `application/x-www-url-formencoded` utgående begäran ska ha samma innehållstypsrubrik kan du lägga till `@body('formdataAction')`begäran i åtgärdens brödtext utan att casta med hjälp av ett uttryck som . Den här metoden fungerar dock bara när brödtexten är den enda parametern i `body` indata. Om du försöker `@body('formdataAction')` använda uttrycket i en `application/json` begäran får du ett körningsfel eftersom brödtexten har kodats.
