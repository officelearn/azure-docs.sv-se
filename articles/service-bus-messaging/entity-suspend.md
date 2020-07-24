---
title: Azure Service Bus – pausa meddelande enheter
description: Den här artikeln förklarar hur du tillfälligt inaktiverar och återaktiverar Azure Service Bus meddelande enheter (köer, ämnen och prenumerationer).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: da7c25c8ef9f3daa32984ac26aa62710ab775951
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038145"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pausa och återaktivera meddelande enheter (inaktivera)

Köer, ämnen och prenumerationer kan tillfälligt avbrytas. SUS Pension placerar entiteten i ett inaktiverat tillstånd där alla meddelanden bevaras i lagrings utrymmet. Meddelanden kan dock inte tas bort eller läggas till, och motsvarande protokoll åtgärder ger fel.

Att pausa en entitet görs vanligt vis av brådskande administrativa skäl. Ett scenario har distribuerat en felaktig mottagare som tar emot meddelanden från kön, Miss lyckas bearbetningen och sedan tar bort alla meddelanden på ett felaktigt sätt. Om detta beteende är diagnostiserat kan kön inaktive ras för mottagning tills korrigerad kod har distribuerats och ytterligare data förlust som orsakas av fel koden kan förhindras.

En SUS pension eller reaktivering kan utföras antingen av användaren eller av systemet. Systemet pausar endast enheter på grund av grava administrativa skäl, till exempel genom att trycka på prenumerations utgifts gränsen. System-inaktiverade entiteter kan inte återaktiveras av användaren, men återställs när orsaken till uppskjutningen har åtgärd ATS.

I portalen gör avsnittet **Egenskaper** för respektive entitet att ändra tillstånd. följande skärm bild visar växlingen för en kö:

![Skärm bild av Service Bus funktionen med egenskaps inställningen markerad och alternativet köläge är inställt på aktiv och beskrivs i rött.][1]

Portalen tillåter bara fullständigt inaktive ring av köer. Du kan också inaktivera åtgärderna skicka och ta emot separat med Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -API: er i .NET Framework SDK eller med en Azure Resource Manager-mall via Azure CLI eller Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>SUS pensions tillstånd

De tillstånd som kan ställas in för en kö är:

-   **Aktiv**: kön är aktiv.
-   **Inaktive rad**: kön har pausats.
-   **SendDisabled**: kön är delvis inaktive rad, med mottagning tillåten.
-   **ReceiveDisabled**: kön är delvis inaktive rad, med skicka som tillåten.

Endast **aktiv** och **inaktive rad** kan anges för prenumerationer och ämnen.

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) -uppräkningen definierar också en uppsättning över gångs tillstånd som bara kan ställas in av systemet. PowerShell-kommandot för att inaktivera en kö visas i följande exempel. Kommandot reaktive ras är detsamma, `Status` inställningen **aktiv**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

