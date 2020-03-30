---
title: Azure Event Grid-begrepp
description: Beskriver Azure Event Grid och dess begrepp. Definierar flera nyckelkomponenter i Händelserutnätet.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265069"
---
# <a name="concepts-in-azure-event-grid"></a>Begrepp i Azure Event Grid

I den här artikeln beskrivs de viktigaste begreppen i Azure Event Grid.

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som helt beskriver något som hände i systemet. Varje händelse har gemensam information som: källan till händelsen, tid händelsen ägde rum och unik identifierare. Varje händelse har också specifik information som bara är relevant för den specifika typen av händelse. Till exempel, en händelse som handlar om en fil som har skapats i Azure Storage innehåller information om filen, såsom värdet `lastTimeModified`. Eller en händelse i Event Hubs har URL:en för Capture-filen. 

En händelse av storlek upp till 64 KB omfattas av Service Level Agreement (General Availability) Service Level Agreement (SLA). Stödet för en händelse med storlek upp till 1 MB är för närvarande i förhandsversion. Händelser över 64 KB debiteras i steg om 64 KB. 


De egenskaper som skickas i en händelse finns i [Azure Event Grid-händelseschema](event-schema.md).

## <a name="publishers"></a>Utgivare

En utgivare är den användare eller organisation som bestämmer sig för att skicka händelser till Event Grid. Microsoft publicerar händelser för flera Azure-tjänster. Du kan publicera händelser från ditt eget program. Organisationer som är värdar för tjänster utanför Azure kan publicera händelser via Event Grid.

## <a name="event-sources"></a>Händelsekällor

En händelsekälla är där händelsen inträffar. Varje händelsekälla är relaterat till en eller flera händelsetyper. Till exempel är Azure Storage händelsekällan för händelser som skapas i blobbar. IoT Hub är händelsekällan för händelser som har skapats på enheter. Ditt program är händelsekällan för anpassade händelser som du definierar. Händelsekällor ansvarar för att skicka händelser till Event Grid.

Information om hur du implementerar någon av de händelserutnätskällor som stöds finns [i Händelsekällor i Azure Event Grid](event-sources.md).

## <a name="topics"></a>Ämnen

Händelserutnätets ämne innehåller en slutpunkt där källan skickar händelser. Utgivaren skapar ämnet för händelserutnätet och avgör om en händelsekälla behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser bestämmer prenumeranter vilka ämnen som ska prenumerera på.

Systemämnen är inbyggda ämnen som tillhandahålls av Azure-tjänster. Du ser inte systemämnen i Azure-prenumerationen eftersom utgivaren äger ämnena, men du kan prenumerera på dem. Om du vill prenumerera ska du tillhandahålla information om resursen som du vill ta emot händelser från. Så länge som du har åtkomst till resursen, kan du prenumerera på händelser.

Anpassade ämnen kommer från program och tredje part. När du skapar eller tilldelas åtkomst till ett anpassat ämne visas detta anpassade ämne i din prenumeration.

När du utformar programmet har du flexibilitet när du bestämmer hur många ämnen som ska skapas. Skapa ett anpassat ämne för varje kategori av relaterade händelser för stora lösningar. Tänk dig ett program som skickar händelser som handlar om att ändra användarkonton och bearbeta beställningar. Det är osannolikt alla händelsehanteraren vill ha båda händelsekategorier. Skapa två anpassade ämnen och låt händelsehanteraren prenumerera på det mest relevanta. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelseprenumeranter kan filtrera efter de händelsetyper de vill ha.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration talar om för Event Grid vilka händelser i ett ämne som du är intresserad av att ta emot. När du skapar prenumerationen anger du en slutpunkt för hantering av händelsen. Du kan filtrera de händelser som skickas till slutpunkten. Du kan filtrera efter händelsetyp eller ämnesmönster. Mer information finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).

Exempel på hur du skapar prenumerationer finns i:

* [Azure CLI-exempel för Event Grid](cli-samples.md)
* [Azure PowerShell-exempel för Event Grid](powershell-samples.md)
* [Azure Resource Manager-mallar för Event Grid](template-samples.md)

Information om hur du hämtar dina aktuella prenumerationer på händelserutnät finns i [Frågehändelserutnätsprenumerationer](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Förfallodatum för händelseprenumeration
Händelseprenumerationen upphör att gälla automatiskt efter det datumet. Ange en förfallodatum för händelseprenumerationer som bara behövs under en begränsad tid och du vill inte oroa dig för att rensa upp dessa prenumerationer. När du till exempel skapar en händelseprenumeration för att testa ett scenario kanske du vill ange en förfallodatum. 

Ett exempel på att ange ett förfallodatum finns [i Prenumerera med avancerade filter](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Händelsehanterare

Ur ett eventrutnätsperspektiv är en händelsehanterare den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Event Grid stöder flera typer av hanterare. Du kan använda en Azure-tjänst som stöds eller din egen webhook som hanterare. Beroende på vilken typ av hanterare, event grid följer olika mekanismer för att garantera leveransen av händelsen. För HTTP webhook-händelsehanterare görs händelsen igen tills hanteraren returnerar `200 – OK`en statuskod för . För Azure Storage Queue görs händelserna på nytt tills kötjänsten bearbetar meddelandet som skickas in i kön.

Information om hur du implementerar någon av de händelserutnätshanterare som stöds finns [i Händelsehanterare i Azure Event Grid](event-handlers.md).

## <a name="security"></a>Säkerhet

Event Grid ger säkerhet för att prenumerera på ämnen och publicera ämnen. När du prenumererar måste du ha tillräcklig behörighet för resurs- eller händelserutnätets ämne. När du publicerar måste du ha en SAS-token eller nyckelautentisering för ämnet. Mer information finns i [Säkerhet och autentisering av Händelserutnät](security-authentication.md).

## <a name="event-delivery"></a>Leverans av evenemang

Om Event Grid inte kan bekräfta att en händelse har tagits emot av prenumerantens slutpunkt, redigerar den händelsen igen. Mer information finns i [Leverans av meddelande från Event Grid och försök igen](delivery-and-retry.md).

## <a name="batching"></a>Batchbearbetning

När du använder ett anpassat ämne måste händelser alltid publiceras i en matris. Detta kan vara en batch med en för scenarier med låg dataflöde, men för användningsfall med hög volym rekommenderar vi att du batchar flera händelser tillsammans per publicera för att uppnå högre effektivitet. Batchar kan vara upp till 1 MB. Varje händelse bör fortfarande inte vara större än 64 KB (allmän tillgänglighet) eller 1 MB (förhandsversion).

> [!NOTE]
> En händelse av storlek upp till 64 KB omfattas av Service Level Agreement (General Availability) Service Level Agreement (SLA). Stödet för en händelse med storlek upp till 1 MB är för närvarande i förhandsversion. Händelser över 64 KB debiteras i steg om 64 kB. 

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
