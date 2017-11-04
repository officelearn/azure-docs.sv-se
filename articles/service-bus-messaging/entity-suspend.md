---
title: Azure Service Bus pausa meddelandeentiteter | Microsoft Docs
description: "Inaktivera och återaktivera Azure Service Bus meddelandet entiteter."
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: 09aee1ba9ec3ce72732cb1f60c9a840ffc4beb2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Inaktivera och återaktivera meddelandeentiteter (inaktivera)

Köer, ämnen och prenumerationer kan vara tillfälligt. Upphävande placerar entiteten i ett inaktiverat tillstånd, där alla meddelanden underhålls i lagringen. Dock meddelanden kan inte tas bort eller lagts till och åtgärderna för respektive protokollet ger fel.

Pausa en entitet normalt görs för brådskande administrativa skäl. Ett scenario är har distribuerat en felaktig mottagare som tar meddelanden från kön, misslyckas bearbetning, och ännu felaktigt Slutför meddelanden och tas bort. Om att problemet har diagnostiserats kön kan inaktiveras för tar emot förrän korrigerade koden har distribuerats och ytterligare dataförlust på grund av felaktiga koden kan förhindras.

En upphävande eller återaktivering kan utföras av användaren eller av systemet. Systemet pausar endast entiteter på grund av allvarligt administrativa orsaker, till exempel prenumerationsgränsen utgiftsgräns. System-inaktiverad entiteter kan inte aktiveras igen av användaren, men återställs när orsaken till detta åtgärdats.

I portalen på **egenskaper** avsnittet för respektive enhet kan ändra tillståndet; följande skärmbild visar växla för en kö:

![][1]

Portalen tillåter endast inaktiveras fullständigt köer. Du kan också inaktivera skicka och ta emot åtgärder separat med hjälp av Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API i .NET Framework SDK eller med en Azure Resource Manager-mall med Azure CLI eller Azure PowerShell.

## <a name="suspension-states"></a>Tillfälligt tillstånd

Tillstånd som kan anges för en kö är:

-   **Aktiva**: kön är aktiv.
-   **Inaktiverade**: kön har pausats.
-   **SendDisabled**: kön delvis pausas, med ta emot de tillåts.
-   **ReceiveDisabled**: kön delvis pausas, skicka de tillåts.

För prenumerationer och ämnen, endast **Active** och **inaktiverad** kan anges.

Den [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) uppräkning definierar också en uppsättning övergående tillstånd som kan endast anges av systemet. PowerShell-kommando för att inaktivera en kö visas i följande exempel. Kommandot återaktivering är likvärdiga, `Status` till **Active**.

```powershell
$q = Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

