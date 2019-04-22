---
title: Använda åtgärder för begäranden och svar | Microsoft Docs
description: Översikt över begäranden och svar-utlösare och åtgärder i Azure logic app
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 0f6ee8729cbed9cb8baf3668f7b1a332bc5eddc1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892837"
---
# <a name="get-started-with-the-request-and-response-components"></a>Kom igång med begäranden och svar-komponenter
Med begäranden och svar-komponenter i en logikapp kan du svara i realtid på händelser.

Du kan till exempel:

* Svara på en HTTP-begäran med data från en lokal databas via en logikapp.
* Utlös en logikapp från en extern webhook-händelse.
* Anropa en logikapp med en åtgärd för begäran och svar från en annan logic App.

Kom igång med åtgärderna som begäran och svar i en logikapp, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Använda HTTP-begäran-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definieras i en logikapp. 
[Mer information om utlösare](../connectors/apis-list.md).

Här är en Exempelsekvens av hur du ställer in en HTTP-begäran i Logic App Designer.

1. Lägg till utlösaren **begäran – när en HTTP-begäran tas emot** i din logikapp. Du kan också bifoga en JSON-schema (med hjälp av ett verktyg som [JSONSchema.net](https://jsonschema.net)) för det begärda innehållet. På så sätt kan designern för att generera token för egenskaper i HTTP-begäran.
2. Lägg till en annan åtgärd så att du kan spara logikappen.
3. Du kan få HTTP-begärans-URL från kortet begäran när du har sparat logikappen.
4. En HTTP POST (du kan använda ett verktyg som [Postman](https://www.getpostman.com/)) utlöser logikappen i URL: en.

> [!NOTE]
> Om du inte definierar en svarsåtgärd en `202 ACCEPTED` svar returneras omedelbart till anroparen. Du kan använda instruktionen svar för att anpassa ett svar.
> 
> 

![Svaret utlösare](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Använda instruktionen HTTP-svar
HTTP-svar-åtgärden är endast giltig när du använder den i ett arbetsflöde som utlöses av en HTTP-begäran. Om du inte definierar en svarsåtgärd en `202 ACCEPTED` svar returneras omedelbart till anroparen.  Du kan lägga till en svarsåtgärd i något steg i arbetsflödet. Logikappen endast från den inkommande begäranden öppen för en minut i ett svar.  Efter en minut, om inget svar har skickats från arbetsflödet (och en svarsåtgärd finns i definitionen), en `504 GATEWAY TIMEOUT` returneras till anroparen.

Här är hur du lägger till en HTTP-svar-åtgärd:

1. Välj den **nytt steg** knappen.
2. Välj **Lägg till en åtgärd**.
3. Skriv i sökrutan åtgärd **svar** att lista svarsåtgärden.
   
    ![Välj svarsåtgärden som](./media/connectors-native-reqres/using-action-1.png)
4. Lägg till i alla parametrar som krävs för HTTP-svarsmeddelande.
   
    ![Slutföra svarsåtgärden](./media/connectors-native-reqres/using-action-2.png)
5. Klicka på det övre vänstra hörnet i verktygsfältet för att spara och din logikapp kommer både spara och publicera (aktivera).

## <a name="request-trigger"></a>Begäran-utlösare
Här följer information om utlösaren som stöder den här anslutningen. Det finns en enskild begäran-utlösare.

| Utlösare | Beskrivning |
| --- | --- |
| Förfrågan |Inträffar när en HTTP-begäran tas emot |

## <a name="response-action"></a>Svarsåtgärd
Här följer information om vad som har stöd för den här anslutningen. Det finns ett enda svar-åtgärd som kan endast användas när den åtföljs av en begäransutlösare.

| Åtgärd | Beskrivning |
| --- | --- |
| Svar |Returnerar ett svar till korrelerade HTTP-begäran |

### <a name="trigger-and-action-details"></a>Information om utlösare och åtgärd
I följande tabeller beskrivs inmatningsfält för utlösare och åtgärd och motsvarande utdata information.

#### <a name="request-trigger"></a>Begäran-utlösare
Följande är ett inmatningsfält för utlösaren från en inkommande HTTP-begäran.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| JSON-Schema |schemat |JSON-schemat för HTTP-begärandetexten |

<br>

**Utdatainformation**

Här följer utdatainformation för begäran.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Rubriker |objekt |Begärandehuvud |
| Innehåll |objekt |Objekt |

#### <a name="response-action"></a>Svarsåtgärd
Här följer inmatningsfält för HTTP-svar-åtgärden. A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Status för kod * |statusCode |HTTP-statuskod |
| Rubriker |Rubriker |Ett JSON-objekt för alla svarshuvuden att inkludera |
| Innehåll |brödtext |Svarstexten |

## <a name="next-steps"></a>Nästa steg
Nu kan prova att använda plattformen och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska andra tillgängliga anslutningsappar i logic apps genom att titta på våra [API: er lista](apis-list.md).

