---
title: Azure Service Bus pausa meddelandeentiteter | Microsoft Docs
description: Inaktivera och återaktivera entiteter för Azure Service Bus-meddelande.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 5d1b2718b360a55d9b1510bcfcb2ddb6492e2830
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436756"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Inaktivera och återaktivera meddelandeentiteter (inaktivera)

Köer, ämnen och prenumerationer kan vara tillfälligt. Inaktiveringen placerar entiteten i ett inaktiverat tillstånd där alla meddelanden underhålls i lagring. Dock meddelanden kan inte tas bort eller läggs till, och åtgärderna för respektive protokollet ger fel.

Pausa en entitet är normalt görs för brådskande administrativa skäl. Ett scenario är har distribuerat en felaktig mottagare som tar meddelanden från kö, misslyckas bearbetning, och ännu felaktigt har slutförts meddelanden och tas bort. Om som standard är diagnostiseras kön kan inaktiveras för tar emot tills korrigerad kod distribueras och ytterligare dataförlust på grund av den felaktiga koden kan förhindras.

En inaktivering eller återaktivering kan utföras av användaren eller av systemet. Systemet pausar endast entiteter på grund av allvarligt administrativa orsaker, till exempel nått utgiftsgränsen. System-inaktiverad entiteter kan inte aktiveras av användaren, men återställs när orsaken till inaktiveringen har utförts.

I portalen på **egenskaper** avsnittet för respektive entiteten kan ändra tillståndet; i följande skärmbild visas växlingsknappen för en kö:

![][1]

Portalen tillåter endast inaktiveras fullständigt köer. Du kan också inaktivera skicka och ta emot åtgärder separat med hjälp av Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API: er i .NET Framework SDK eller med en Azure Resource Manager-mall via Azure CLI eller Azure PowerShell.

## <a name="suspension-states"></a>Inaktiveringen tillstånd

Tillstånd som kan ställas in för en kö är:

-   **Aktiva**: Kön är aktiv.
-   **Inaktiverad**: Kön har pausats.
-   **SendDisabled**: Kön har delvis pausats med ta emot de tillåts.
-   **ReceiveDisabled**: Kön har delvis pausats med skicka de tillåts.

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

