---
title: Azure Event Grid-begrepp
description: Beskriver Azure Event Grid och dess begrepp. Definierar flera viktiga komponenter i Event Grid.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 2a288cdb96a1e1ff7e261d4782f7e02aee12868f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621209"
---
# <a name="concepts-in-azure-event-grid"></a>Koncept i Azure Event Grid

Den här artikeln beskriver de viktigaste begreppen i Azure Event Grid.

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som helt beskriver något som har inträffat i systemet. Varje händelse har common information, till exempel: källan för händelsen, tid händelsen tog plats och unik identifierare. Varje händelse har också specifik information som endast är relevanta för den specifika typen av händelse. Exempelvis kan en händelse om en ny fil skapas i Azure Storage innehåller information om filen, till exempel den `lastTimeModified` värde. Eller en händelse i Event Hubs har URL: en för filen avbildning. 

Varje händelse är begränsat till 64 KB data.

De egenskaper som skickas i en händelse, se [Azure Event Grid Händelseschema](event-schema.md).

## <a name="publishers"></a>Utgivare

En utgivare är användare eller organisation som bestämmer sig för att skicka händelser till Event Grid. Microsoft publicerar händelser för flera Azure-tjänster. Du kan publicera händelser från ditt eget program. Organisationer som är värdar för tjänster utanför Azure kan publicera händelser via Event Grid.

## <a name="event-sources"></a>Händelsekällor

En händelsekälla är där händelsen inträffar. Varje händelsekälla är relaterat till en eller flera händelsetyper. Till exempel är Azure Storage händelsekällan för blob skapas händelser. IoT Hub är händelsekällan för enheten som skapade händelser. Programmet är händelsekällan för anpassade händelser som du definierar. Händelsekällor ansvarar för att skicka händelser till Event Grid.

Information om hur du implementerar någon av Event Grid-källor som stöds finns i [händelsekällor i Azure Event Grid](event-sources.md).

## <a name="topics"></a>Ämnen

Event grid-ämne tillhandahåller en slutpunkt där källan skickar händelser. Utgivaren skapar event grid-ämne och avgör om en händelsekälla måste ett eller fler än ett ämne. Ett ämne används för en uppsättning relaterade händelser. För att svara på vissa typer av händelser, bestämma prenumeranter vilka avsnitt om att prenumerera på.

System-avsnitten är inbyggda avsnitt som tillhandahålls av Azure-tjänster. Du ser inte system ämnen i Azure-prenumerationen eftersom utgivaren äger i avsnitt, men du kan prenumerera på dem. Om du vill prenumerera på, anger du information om resursen som du vill ta emot händelser från. Så länge som du har åtkomst till resursen, kan du prenumerera på händelser.

Anpassade ämnen är program och från tredje part ämnen. När du skapar eller tilldelas åtkomst till ett anpassat ämne, visas detta anpassade ämne i din prenumeration.

När ditt program har flexibilitet när du bestämmer hur många avsnitt för att skapa. Skapa ett anpassat ämne för varje kategori av relaterade händelser för stora lösningar. Tänk dig ett program som skickar händelser relaterade till ändra användarkonton och behandla beställningar. Det är osannolikt alla händelsehanteraren vill båda typer av händelser. Skapa två anpassade ämnen och låt händelsehanterare prenumerera på det som intresserar dem. För små lösningar kanske du föredrar att skicka alla händelser till ett ämne. Händelseprenumeranter filtrera händelsetyper som de vill.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration anger Event Grid vilka händelser på ett ämne som du är intresserad av som tar emot. När du skapar prenumerationen kan ange du en slutpunkt för hantering av händelsen. Du kan filtrera de händelser som skickas till slutpunkten. Du kan filtrera efter händelsetyp eller ämne mönster. Mer information finns i [Event Grid prenumerationsschema](subscription-creation-schema.md).

Exempel för att skapa prenumerationer finns:

* [Azure CLI-exempel för Event Grid](cli-samples.md)
* [Azure PowerShell-exempel för Event Grid](powershell-samples.md)
* [Azure Resource Manager-mallar för Event Grid](template-samples.md)

Information om hur du får din aktuella event grid-prenumerationer finns i [Query Event Grid-prenumerationer](query-event-subscriptions.md).

## <a name="event-handlers"></a>Händelsehanterare

En händelsehanterare är den plats där händelsen skickas från en Event Grid-perspektiv. Hanteraren tar några ytterligare åtgärder för att bearbeta händelsen. Event Grid har stöd för flera typer av hanteraren. Du kan använda en tjänst som stöds Azure eller dina egna webhook som hanteraren. Beroende på vilken typ av hanteraren kan följer Event Grid olika metoder för att garantera leverans av händelsen. För HTTP-webhook händelsehanterare händelsen görs tills hanteraren returnerar statuskoden `200 – OK`. Händelserna görs tills kötjänsten kan bearbeta meddelandet push-meddelandet i kön i Azure Storage-kö.

Information om hur du implementerar någon av de Event Grid-hanterarna som stöds finns i [händelsehanterare i Azure Event Grid](event-handlers.md).

## <a name="security"></a>Säkerhet

Event Grid ger säkerhet för att prenumerera på ämnen och publicera ämnen. När du prenumererar måste du ha tillräcklig behörighet på resurs eller event grid-ämne. När du publicerar, måste du ha en SAS-token eller nyckelautentisering för ämnet. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

## <a name="event-delivery"></a>Händelseleverans

Om Event Grid inte kan bekräfta att en händelse har tagits emot av prenumerantens slutpunkten, redelivers händelsen. Mer information finns i [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).

## <a name="batching"></a>Batchbearbetning

När du använder ett anpassat ämne, måste alltid händelser publiceras i en matris. Detta kan vara en batch med ett för lågt dataflöde scenarier, men för hög volym usecases rekommenderar vi att du batch flera händelser tillsammans per publicera för att uppnå högre effektivitet. Batchar kan vara upp till 1 MB. Varje händelse fortfarande överstiga inte 64 KB.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
