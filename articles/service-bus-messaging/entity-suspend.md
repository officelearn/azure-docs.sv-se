---
title: Azure Service Bus pausa meddelandeentiteter | Microsoft Docs
description: Inaktivera och återaktivera entiteter för Azure Service Bus-meddelande.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: b7ea8c14bfc3f2ba3720ad919d6c36ec50c3823e
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854495"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Inaktivera och återaktivera meddelandeentiteter (inaktivera)

Köer, ämnen och prenumerationer kan vara tillfälligt. Inaktiveringen placerar entiteten i ett inaktiverat tillstånd där alla meddelanden underhålls i lagring. Dock meddelanden kan inte tas bort eller läggs till, och åtgärderna för respektive protokollet ger fel.

Pausa en entitet är normalt görs för brådskande administrativa skäl. Ett scenario är har distribuerat en felaktig mottagare som tar meddelanden från kö, misslyckas bearbetning, och ännu felaktigt har slutförts meddelanden och tas bort. Om som standard är diagnostiseras kön kan inaktiveras för tar emot tills korrigerad kod distribueras och ytterligare dataförlust på grund av den felaktiga koden kan förhindras.

En inaktivering eller återaktivering kan utföras av användaren eller av systemet. Systemet pausar endast entiteter på grund av allvarligt administrativa orsaker, till exempel nått utgiftsgränsen. System-inaktiverad entiteter kan inte aktiveras av användaren, men återställs när orsaken till inaktiveringen har utförts.

I portalen på **egenskaper** avsnittet för respektive entiteten kan ändra tillståndet; på skärmbilden nedan visar växlingsknappen för en kö:

![][1]

Portalen tillåter endast inaktiveras fullständigt köer. Du kan också inaktivera skicka och ta emot åtgärder separat med hjälp av Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API: er i .NET Framework SDK eller med en Azure Resource Manager-mall via Azure CLI eller Azure PowerShell.

## <a name="suspension-states"></a>Inaktiveringen tillstånd

Tillstånd som kan ställas in för en kö är:

-   **Aktiva**: kön är aktiv.
-   **Inaktiverad**: kön har pausats.
-   **SendDisabled**: kön delvis pausas, med ta emot de tillåts.
-   **ReceiveDisabled**: kön delvis pausas, med skicka de tillåts.

För prenumerationer och ämnen, endast **Active** och **inaktiverad** kan ställas in.

Den [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) uppräkning definierar också en uppsättning av övergående tillstånd som kan bara ställas in av systemet. PowerShell-kommando för att inaktivera en kö visas i följande exempel. Kommandot återaktivering är motsvarande inställningen `Status` till **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

