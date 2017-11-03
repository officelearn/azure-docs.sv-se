---
title: "Använda åtgärder för förfrågan och svar | Microsoft Docs"
description: "Översikt över förfrågan och svar utlösare och åtgärden i en Azure logikapp"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: e45b07d709927af64cfba28dfb0d8ee9cb8893b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-request-and-response-components"></a>Kom igång med komponenter för förfrågan och svar
Med förfrågan och svar komponenterna i en logikapp, kan du svara i realtid på händelser.

Du kan till exempel:

* Svara på en HTTP-begäran med data från en lokal databas via en logikapp.
* Utlös en logikapp från en extern webhook-händelse.
* Anropa en logikapp med en åtgärd för förfrågan och svar från inom en annan logikapp.

Om du vill komma igång med åtgärder för förfrågan och svar i en logikapp, se [skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Använda HTTP-begäran-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definieras i en logikapp. [Mer information om utlösare](connectors-overview.md).

Här är en Exempelsekvens av hur du ställer in en HTTP-begäran i logik App Designer.

1. Lägg till utlösaren **begäran - när en HTTP-begäran tas emot** i din logikapp. Du kan också medföra en JSON-schema (med hjälp av ett verktyg som [JSONSchema.net](http://jsonschema.net)) för begärandetexten. Detta gör att generera token för egenskaper i HTTP-begäran.
2. Lägg till en annan åtgärd så att du kan spara logikappen.
3. Du kan hämta URL för HTTP-begäran från kortet begäran när du har sparat logikappen.
4. En HTTP POST (du kan använda ett verktyg som [Postman](https://www.getpostman.com/)) till URL som utlöser logikappen.

> [!NOTE]
> Om du inte definierar en åtgärd för svar, en `202 ACCEPTED` omedelbart svar returneras till anroparen. Du kan använda åtgärden svar för att anpassa ett svar.
> 
> 

![Svaret utlösare](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Använd åtgärden HTTP-svar
HTTP-svar-åtgärden är endast giltig när du använder den i ett arbetsflöde som utlöses av en HTTP-begäran. Om du inte definierar en åtgärd för svar, en `202 ACCEPTED` omedelbart svar returneras till anroparen.  Du kan lägga till en åtgärd som svar på något steg i arbetsflödet. Logikappen upprätthåller bara den inkommande begäranden öppna en minut för svar.  Efter en minut, om inget svar skickades från arbetsflödet (och det finns ett svar-åtgärd i definitionen), en `504 GATEWAY TIMEOUT` returneras till anroparen.

Här är hur du lägger till en åtgärd för HTTP-svar:

1. Välj den **nytt steg** knappen.
2. Välj **lägga till en åtgärd**.
3. Skriv i sökrutan åtgärd **svar** att lista åtgärden svar.
   
    ![Välj åtgärden som svar](./media/connectors-native-reqres/using-action-1.png)
4. Lägga till i alla parametrar som krävs för HTTP-svarsmeddelandet.
   
    ![Utför åtgärden svar](./media/connectors-native-reqres/using-action-2.png)
5. Klicka på det övre vänstra hörnet i verktygsfältet för att spara och logikappen både sparar och publicera (aktivera).

## <a name="request-trigger"></a>Begäran utlösare
Här följer information om utlösaren som har stöd för den här anslutningen. Det finns en enskild begäran-utlösare.

| Utlösare | Beskrivning |
| --- | --- |
| Förfrågan |Inträffar när en HTTP-begäran tas emot |

## <a name="response-action"></a>Svaret åtgärd
Här följer information om vad som har stöd för den här anslutningen. Det finns ett enda svar-åtgärd som kan endast användas när den åtföljs av en begäran-utlösare.

| Åtgärd | Beskrivning |
| --- | --- |
| Svar |Returnerar ett svar till korrelerade HTTP-begäran |

### <a name="trigger-and-action-details"></a>Trigger och action information
I följande tabeller beskrivs indatafält för trigger och action och motsvarande utdata information.

#### <a name="request-trigger"></a>Begäran utlösare
Följande är ett inmatningsfält för utlösaren från en inkommande HTTP-begäran.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| JSON-Schema |Schemat |JSON-schema av text för HTTP-begäran |

<br>

**Information för utdata**

Nedan visas information för utdata för begäran.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Rubriker |Objektet |Huvuden för begäran |
| Innehåll |Objektet |Request-objektet |

#### <a name="response-action"></a>Svaret åtgärd
Följande är inmatningsfält för åtgärden HTTP-svar. A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Status kod * |statusCode |HTTP-statuskod |
| Rubriker |Rubriker |Ett JSON-objekt för alla svarshuvuden att inkludera |
| Innehåll |Brödtext |Svarstexten |

## <a name="next-steps"></a>Nästa steg
Prova nu, plattform och [skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md). Du kan utforska andra tillgängliga kopplingar i logikappar genom att titta på vår [API: er listan](apis-list.md).

