---
title: Azure händelse rutnätet begrepp
description: Beskriver Azure Event Grid och dess begrepp. Definierar flera viktiga komponenter av händelse rutnät.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: tomfitz
ms.openlocfilehash: abc1302f0317c8d5ecdc7ddaf8ca6d3a9e82b582
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626043"
---
# <a name="concepts-in-azure-event-grid"></a>Koncept i Azure händelse rutnätet

Den här artikeln beskriver grundbegreppen i rutnätet för Azure-händelse.

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som beskriver fullständigt något som har inträffat i systemet. Alla händelser har gemensamma information, till exempel: källan för händelsen, tiden händelsen tog plats och unik identifierare. Alla händelser har också specifik information som endast är relevanta för typ av händelse. Till exempel en händelse om en ny fil skapas i Azure Storage har information om filen, som den `lastTimeModified` värde. Eller en händelse för Händelsehubbar har URL för filen. 

Varje händelse är begränsat till 64 KB med data.

De egenskaper som skickas i en händelse, se [Azure händelse rutnätet Händelseschema](event-schema.md).

## <a name="publishers"></a>Utgivare

En utgivare är användaren eller organisationen som bestämmer sig för att skicka händelser till händelse rutnätet. Microsoft publicerar händelser för flera Azure-tjänster. Du kan publicera händelser från ditt eget program. Organisationer som är värdar för tjänster utanför Azure kan publicera händelser via händelsen rutnätet.

## <a name="event-sources"></a>Händelsekällor

En händelsekälla är där händelsen inträffar. Varje källa är kopplad till en eller flera typer av händelser. Azure Storage är till exempel händelsekällan för blob som skapats av händelser. IoT-hubben är händelsekällan för enhet som skapas av händelser. Tillämpningsprogrammet är händelsekällan för anpassade händelser som du definierar. Händelsekällan ansvarar för att skicka händelser till händelse rutnätet.

Information om hur du implementerar någon av händelse rutnätet-källor som stöds finns i [händelsekällor i Azure händelse rutnät](event-sources.md).

## <a name="topics"></a>Ämnen

Händelsen rutnätet avsnittet innehåller en slutpunkt där källan skickar händelser. Utgivaren skapar händelsen rutnätet avsnittet och bestämmer om en händelsekälla måste ett eller fler än ett ämne. Ett avsnitt används för en samling relaterade händelser. För att svara på vissa typer av händelser Bestäm prenumeranter vilka avsnitt för att prenumerera på.

System avsnitten är inbyggt avsnitt som tillhandahålls av Azure-tjänster. Avsnitt om systemet visas inte i din Azure-prenumeration eftersom utgivaren äger i avsnitt, men du kan prenumerera på dem. Om du vill prenumerera på, anger du information om resursen som du vill ta emot händelser från. Du kan prenumerera på händelser som du har åtkomst till resursen.

Anpassade avsnitt är program- och tredjeparts-avsnitt. När du skapar eller tilldelas åtkomst till en anpassad avsnittet finns det anpassade avsnittet i din prenumeration.

När du skapar ditt program bör ha du flexibilitet när du bestämmer hur många avsnitt för att skapa. Skapa en anpassad avsnittet för varje kategori av relaterade händelser för stora lösningar. Tänk dig ett program som skickar händelser relaterade till ändring av användarkonton och bearbetning av order. Det är inte troligt alla händelsehanteraren vill båda typer av händelser. Skapa två anpassade avsnitt och kan prenumerera på det som intresserar dem händelsehanterare. För små lösningar, kanske du föredrar att skicka alla händelser till ett ämne. Händelse-prenumeranter kan filtrera händelsetyper som de vill.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration anger händelse rutnätet vilka händelser på ett ämne som du är intresserad av att ta emot. När du skapar prenumerationen kan ange du en slutpunkt för hantering av händelsen. Du kan filtrera de händelser som skickas till slutpunkten. Du kan filtrera efter händelsetyp eller ämne mönster. Mer information finns i [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).

Exempel för att skapa prenumerationer finns:

* [Azure CLI-exempel för händelsen rutnätet](cli-samples.md)
* [Azure PowerShell-exempel för händelsen rutnätet](powershell-samples.md)
* [Azure Resource Manager-mallar för händelsen rutnätet](template-samples.md)

Information om att få din aktuella händelse rutnätet prenumerationer finns i [händelse rutnätet prenumerationer](query-event-subscriptions.md).

## <a name="event-handlers"></a>Händelsehanterare

En ur händelse rutnätet är en händelsehanterare den plats där händelsen att skickas. Hanteraren tar vissa ytterligare åtgärder för att bearbeta händelsen. Händelsen rutnätet har stöd för flera typer för hanteraren. Du kan använda en stöds Azure-tjänst eller en egen webhook som hanterare. Beroende på vilken typ av hanterare följer händelse rutnätet olika metoder för att garantera att leveransen av händelsen. För händelsehanterare för HTTP-webhook händelsen försöks tills hanteraren returnerar statuskoden `200 – OK`. För Azure Storage Queue är händelser igen förrän kötjänsten kan behandla meddelande push-meddelandet i kön.

Information om hur du implementerar någon av stöds händelse rutnätet hanterare finns [händelsehanterare i Azure händelse rutnät](event-handlers.md).

## <a name="security"></a>Säkerhet

Händelsen rutnätet ger säkerhet för att prenumerera på ämnen och publicera avsnitt. När du prenumererar, måste du ha tillräckliga behörigheter på avsnittet resurs eller händelse rutnätet. När du publicerar, måste du ha en SAS-token eller nyckelautentisering för avsnittet. Mer information finns i [händelse rutnätet säkerhets- och autentiseringstjänster](security-authentication.md).

## <a name="event-delivery"></a>Händelsen leverans

Om händelsen rutnätet inte kan bekräfta att en händelse har tagits emot av prenumerantens slutpunkten, redelivers händelsen. Mer information finns i [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
