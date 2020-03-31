---
title: Koncept - Azure Event Grid IoT Edge | Microsoft-dokument
description: Begrepp i händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992553"
---
# <a name="event-grid-concepts"></a>Event Grid-begrepp

I den här artikeln beskrivs de viktigaste begreppen i Azure Event Grid.

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som helt beskriver något som hände i systemet. Varje händelse har gemensam information som: källan till händelsen, tid händelsen ägde rum och unik identifierare. Varje händelse har också specifik information som bara är relevant för den specifika typen av händelse. Stödet för en händelse med storlek upp till 1 MB är för närvarande i förhandsversion.

De egenskaper som ingår i en händelse finns i [Azure Event Grid-händelseschema](event-schemas.md).

## <a name="publishers"></a>Utgivare

En utgivare är den användare eller organisation som bestämmer sig för att skicka händelser till Event Grid. Du kan publicera händelser från ditt eget program.

## <a name="event-sources"></a>Händelsekällor

En händelsekälla är där händelsen inträffar. Varje händelsekälla är relaterat till en eller flera händelsetyper. Till exempel är Azure Storage händelsekällan för händelser som skapas i blobbar. Ditt program är händelsekällan för anpassade händelser som du definierar. Händelsekällor ansvarar för att skicka händelser till Event Grid.

## <a name="topics"></a>Ämnen

Händelserutnätets ämne innehåller en slutpunkt där källan skickar händelser. Utgivaren skapar ämnet för händelserutnätet och avgör om en händelsekälla behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser bestämmer prenumeranter vilka ämnen som ska prenumerera på.

När du utformar programmet har du flexibiliteten att bestämma hur många ämnen du ska skapa. Skapa ett anpassat ämne för varje kategori av relaterade händelser för stora lösningar. Tänk dig ett program som skickar händelser som handlar om att ändra användarkonton och bearbeta beställningar. Det är osannolikt alla händelsehanteraren vill ha båda händelsekategorier. Skapa två anpassade ämnen och låt händelsehanteraren prenumerera på det mest relevanta. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelseprenumeranter kan filtrera efter de händelsetyper de vill ha.

Se [REST API-dokumentation](api.md) om hur du hanterar ämnen i Event Grid.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration talar om för Event Grid vilka händelser i ett ämne som du är intresserad av att ta emot. När du skapar prenumerationen anger du en slutpunkt för hantering av händelsen. Du kan filtrera de händelser som skickas till slutpunkten. 

Se [REST API-dokumentation](api.md) om hur du hanterar prenumerationer i Event Grid.

## <a name="event-handlers"></a>Händelsehanterare

Ur ett eventrutnätsperspektiv är en händelsehanterare den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Event Grid stöder flera typer av hanterare. Du kan använda en Azure-tjänst som stöds eller din egen webbkrok som hanterare. Beroende på vilken typ av hanterare, event grid följer olika mekanismer för att garantera leveransen av händelsen. Om målhändelsehanteraren är en HTTP-webbkrok görs händelsen igen när hanteraren `200 – OK`returnerar en statuskod för . För edge Hub, om händelsen levereras utan undantag, anses det vara framgångsrikt.

## <a name="security"></a>Säkerhet

Event Grid ger säkerhet för att prenumerera på ämnen och publicera ämnen. Mer information finns i [Säkerhet och autentisering av Händelserutnät](security-authentication.md).

## <a name="event-delivery"></a>Leverans av evenemang

Om Event Grid inte kan bekräfta att en händelse har tagits emot av prenumerantens slutpunkt, redigerar den händelsen igen. Mer information finns i [Leverans av meddelande från Event Grid och försök igen](delivery-retry.md).

## <a name="batching"></a>Batchbearbetning

När du använder ett anpassat ämne måste händelser alltid publiceras i en matris. För scenarier med lågt dataflöde har matrisen bara ett värde. För användningsfall med hög volym rekommenderar vi att du batchar flera händelser tillsammans per publicera för att uppnå högre effektivitet. Batchar kan vara upp till 1 MB. Varje händelse bör fortfarande inte vara större än 1 MB (förhandsversion).