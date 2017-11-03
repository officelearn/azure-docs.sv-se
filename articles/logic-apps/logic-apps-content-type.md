---
title: "Hantera innehållstyper - Azure Logic Apps | Microsoft Docs"
description: "Hur Azure Logikappar behandlar innehållstyper vid design och körning"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="handle-content-types-in-logic-apps"></a>Hantera innehållstyper i logikappar

Många olika typer av innehåll kan flöda via en logikapp, inklusive JSON, XML, flat-filer och binära data. Medan Logic Apps motorn stöder alla typer av innehåll, Förstå vissa internt av motorn för Logic Apps. Andra kanske behöver omvandling eller konverteringar vid behov. Den här artikeln beskriver hur motorn hanteras olika typer av innehåll och hur du hanterar dessa typer vid behov.

## <a name="content-type-header"></a>Content-Type-huvud

Om du vill starta i princip ska vi titta på två `Content-Types` som inte kräver konvertering eller omvandling som du kan använda i en logikapp: `application/json` och `text/plain`.

## <a name="applicationjson"></a>Application/JSON

Arbetsflödesmotorn förlitar sig på den `Content-Type` huvudet från HTTP anropar för att avgöra lämpliga hantering. Alla förfrågningar med innehållstyp `application/json` lagras och hanteras som ett JSON-objekt. Innehållet i JSON kan dessutom tolkas som standard utan någon omvandling. 

Till exempel en begäran med content-type-huvudet gick att parsa `application/json ` i ett arbetsflöde med hjälp av ett uttryck som `@body('myAction')['foo'][0]` att hämta värdet `bar` i det här fallet:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Inga ytterligare omvandling krävs. Om du arbetar med data som är JSON men inte har ett huvud angivet, du kan manuellt omvandlas den till JSON med hjälp av den `@json()` fungerar, till exempel: `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Schema- och schema-generator

Begäran-utlösare kan du ange en JSON-schema för nyttolasten du förväntar dig att få. Det här schemat kan designer generera token så att du kan använda innehållet i begäran. Om du inte har ett schema som är klar, Välj **Använd exemplet nyttolast för att skapa schema**, så du kan generera ett JSON-schema från en exempel-nyttolast.

![Schemat](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>'Parsa JSON-åtgärd

Den `Parse JSON` kan parsa JSON-innehåll till eget token för förbrukning av logik app. Liknar begäran utlösaren, du kan ange eller skapa en JSON-schema för det innehåll som du vill analysera. Det här verktyget gör datahantering från Service Bus, Azure Cosmos DB och så vidare, enklare.

![Parsa JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

Liknar `application/json`, HTTP-meddelanden som tagits emot med den `Content-Type` rubriken för `text/plain` lagras i obearbetat format. Även om dessa meddelanden ingår i efterföljande åtgärder utan omvandling dessa begäranden går med `Content-Type`: `text/plain` rubrik. Till exempel när du arbetar med en flat-fil kan du få den här HTTP innehåll som `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Om du i nästa åtgärd skicka begäran som en del av en annan begäran (`@body('flatfile')`), förfrågan skulle ha en `text/plain` Content-Type-huvud. Om du arbetar med data som har oformaterad text men inte har ett huvud angivet, kan du manuellt omvandla data till text med den `@string()` fungerar, till exempel: `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml och program/oktett-ström och konverterare funktioner

Logic Apps motorn bevaras alltid den `Content-Type` som togs emot på HTTP-begäran eller svar. Om motorn tar emot innehåll med den `Content-Type` av `application/octet-stream`, och inkluderar att innehåll i en efterföljande åtgärd utan omvandling, utgående begäran har `Content-Type`: `application/octet-stream`. På så sätt kan kan motorn garantera att data inte förloras när genom arbetsflödet. Dock lagras åtgärdstillstånd (indata och utdata) i en JSON-objekt när tillståndet flyttas genom arbetsflödet. Så om du vill behålla vissa datatyper motorn konverterar innehållet till en binary base64-kodad sträng med tillämpliga metadata som bevarar både `$content` och `$content-type`, som automatiskt ska konverteras. 

* `@json()`-kastar data till`application/json`
* `@xml()`-kastar data till`application/xml`
* `@binary()`-kastar data till`application/octet-stream`
* `@string()`-kastar data till`text/plain`
* `@base64()`-Konverterar innehållet till en base64-sträng
* `@base64toString()`– konverterar en base64-kodad sträng`text/plain`
* `@base64toBinary()`– konverterar en base64-kodad sträng`application/octet-stream`
* `@encodeDataUri()`-kodar en sträng som en bitmatris dataUri
* `@decodeDataUri()`-avkodar ett dataUri till en bytematris

Till exempel om du har fått en HTTP-begäran med `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Du kan konvertera och använda senare med något som liknar `@xml(triggerBody())`, eller i en funktion som `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Andra typer av innehåll

Andra typer av innehåll som stöds och arbeta med logic apps, men kan kräva manuellt hämtar meddelandets brödtext genom att avkoda den `$content`. Anta att du utlöser en `application/x-www-url-formencoded` begäran var `$content` är nyttolast kodade som en base64-sträng för att bevara alla data:

```
CustomerName=Frank&Address=123+Avenue
```

Eftersom begäran inte oformaterad text eller JSON, lagras begäran i åtgärd på följande sätt:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

För närvarande finns det inte finns en inbyggd funktion för formulärdata, så att du kan fortfarande använda informationen i ett arbetsflöde genom att öppna manuellt data med en funktion som `@string(body('formdataAction'))`. Om du vill lägga till utgående begäran om att också ha den `application/x-www-url-formencoded` content-type-huvud, kan du lägga till begäran till åtgärd brödtexten utan någon omvandling som `@body('formdataAction')`. Men den här metoden fungerar bara om brödtexten är den enda parametern i den `body` indata. Om du försöker använda `@body('formdataAction')` i en `application/json` begär kan du få ett körningsfel eftersom meddelandets kodade brödtext skickas.

