---
title: Azure Service Bus – pausa meddelandeentiteter
description: I den här artikeln beskrivs hur du tillfälligt pausar och återaktiverar Azure Service Bus-meddelandeentiteter (köer, ämnen och prenumerationer).
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760393"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pausa och återaktivera meddelandeentiteter (inaktivera)

Köer, ämnen och prenumerationer kan tillfälligt stängas av. Suspension försätter entiteten i ett inaktiverat tillstånd där alla meddelanden lagras. Meddelanden kan dock inte tas bort eller läggas till, och respektive protokollåtgärder ger fel.

Att avbryta en entitet görs vanligtvis av brådskande administrativa skäl. Ett scenario är att ha distribuerat en felaktig mottagare som tar meddelanden från kön, misslyckas bearbetning, och ändå felaktigt slutför meddelanden och tar bort dem. Om det beteendet diagnostiseras kan kön inaktiveras för mottagning tills korrigerad kod har distribuerats och ytterligare dataförlust orsakad av den felaktiga koden kan förhindras.

En suspension eller reaktivering kan utföras antingen av användaren eller av systemet. Systemet avbryter endast enheter på grund av allvarliga administrativa skäl som att träffa gränsen för prenumerationsutgifter. Systemaktiverade entiteter kan inte återaktiveras av användaren, men återställs när orsaken till avstängningen har åtgärdats.

I portalen gör avsnittet **Egenskaper** för respektive entitet det möjligt att ändra tillståndet. följande skärmbild visar växlingsknappen för en kö:

![][1]

Portalen tillåter endast att köerna inaktiveras helt. Du kan också inaktivera skicka och ta emot åtgärder separat med hjälp av Api:erna för Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) i .NET Framework SDK eller med en Azure Resource Manager-mall via Azure CLI eller Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>Avstängningstillstånd

De tillstånd som kan ställas in för en kö är:

-   **Aktiv**: Kön är aktivt.
-   **Inaktiverad**: Kön är avbrutet.
-   **SendDisabled**: Kön är delvis avbruten, och mottagning tillåts.
-   **ReceiveDisabled**: Kön är delvis avbruten, med skicka tillåten.

För prenumerationer och ämnen kan endast **Active** och **Disabled** ställas in.

[Uppräkningen för entitystatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definierar också en uppsättning övergångstillstånd som bara kan ställas in av systemet. PowerShell-kommandot för att inaktivera en kö visas i följande exempel. Kommandot reaktivering är likvärdigt `Status` och inställningen till **Aktiv**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

