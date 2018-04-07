---
title: Azure händelse rutnätet begrepp
description: Beskriver Azure händelse rutnätet och dess begrepp. Definierar flera viktiga komponenter av händelse rutnät.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/04/2018
ms.author: babanisa
ms.openlocfilehash: e55127e60470f8f95235893a14113b80e8d6565b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="concepts-in-azure-event-grid"></a>Koncept i Azure händelse rutnätet

Grundbegreppen i rutnätet för Azure-händelse är:

## <a name="events"></a>Händelser

En händelse är den minsta mängden information som beskriver fullständigt något som har inträffat i systemet.  Alla händelser har gemensamma information, till exempel: källan för händelsen, tiden händelsen tog plats och unik identifierare.  Alla händelser har också specifik information som endast är relevanta för typ av händelse. Till exempel en händelse om en ny fil skapas i Azure Storage innehåller information om filen som den `lastTimeModified` värde. Eller en händelse om en virtuell dator startas om innehåller namnet på den virtuella datorn och orsaken till omstart. Varje händelse är begränsat till 64 KB med data.

## <a name="event-sourcespublishers"></a>Källor/händelseutfärdare

En händelsekälla är där händelsen inträffar. Azure Storage är till exempel händelsekällan för blob som skapats av händelser. Azure VM-strukturen är händelsekällan för virtuell dator händelser. Händelsekällan ansvarar för att publicera händelser på händelsen rutnätet.

## <a name="topics"></a>Ämnen

Utgivare kategorisera händelser i avsnitt. Avsnittet innehåller en slutpunkt där utgivaren skickar händelser. För att svara på vissa typer av händelser Bestäm prenumeranter vilka avsnitt för att prenumerera på. Avsnitt innehåller också en Händelseschema så att prenumeranter kan identifiera använda händelser på lämpligt sätt.

System avsnitten är inbyggt avsnitt som tillhandahålls av Azure-tjänster. Anpassade avsnitt är program- och tredjeparts-avsnitt.

När du skapar ditt program, skapar du ett anpassat ämne för varje kategori av relaterade händelser. Tänk dig ett program som skickar händelser relaterade till ändring av användarkonton och bearbetning av order. Det är inte troligt alla händelsehanteraren vill båda typer av händelser. Skapa två anpassade avsnitt och kan prenumerera på det som intresserar dem händelsehanterare. När du prenumererar på avsnittet anpassade kan händelsehanteraren filtrera efter händelsetyp.

## <a name="event-subscriptions"></a>Prenumerationer på händelser

En prenumeration instruerar händelse rutnätet på vilka händelser på ett ämne en prenumerant är intresserad av att ta emot. En prenumeration innehåller också information om hur händelser som ska levereras till prenumeranten.

## <a name="event-handlers"></a>Händelsehanterare

En ur händelse rutnätet är en händelsehanterare den plats där händelsen att skickas. Hanteraren tar vissa ytterligare åtgärder för att bearbeta händelsen.  Händelsen rutnätet har stöd för flera typer av prenumeranten. Beroende på vilken typ av prenumeranten följer händelse rutnätet olika metoder för att garantera att leveransen av händelsen.  För händelsehanterare för HTTP-webhook händelsen försöks tills hanteraren returnerar statuskoden `200 – OK`. För Azure Storage Queue är händelser igen förrän kötjänsten kan behandla meddelande push-meddelandet i kön.

## <a name="filters"></a>Filter

När du prenumererar på ett ämne, kan du filtrera de händelser som skickas till slutpunkten. Du kan filtrera efter händelsetyp eller ämne mönster. Mer information finns i [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).

## <a name="security"></a>Säkerhet

Händelsen rutnätet ger säkerhet för att prenumerera på ämnen och publicera avsnitt. När du prenumererar, måste du ha tillräckliga behörigheter på resurs eller avsnittet. När du publicerar, måste du ha en SAS-token eller nyckelautentisering för avsnittet. Mer information finns i [händelse rutnätet säkerhets- och autentiseringstjänster](security-authentication.md).

## <a name="failed-delivery"></a>Misslyckade leverans

När händelsen rutnätet inte kan bekräfta att en händelse har tagits emot av prenumerantens slutpunkten, redelivers händelsen. Mer information finns i [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
