---
title: Azure Service Bus – pausa meddelande enheter
description: Den här artikeln förklarar hur du tillfälligt inaktiverar och återaktiverar Azure Service Bus meddelande enheter (köer, ämnen och prenumerationer).
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575266"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Pausa och återaktivera meddelande enheter (inaktivera)

Köer, ämnen och prenumerationer kan tillfälligt avbrytas. SUS Pension placerar entiteten i ett inaktiverat tillstånd där alla meddelanden bevaras i lagrings utrymmet. Meddelanden kan dock inte tas bort eller läggas till, och motsvarande protokoll åtgärder ger fel.

Att pausa en entitet görs vanligt vis av brådskande administrativa skäl. Ett scenario har distribuerat en felaktig mottagare som tar emot meddelanden från kön, Miss lyckas bearbetningen och sedan tar bort alla meddelanden på ett felaktigt sätt. Om detta beteende är diagnostiserat kan kön inaktive ras för mottagning tills korrigerad kod har distribuerats och ytterligare data förlust som orsakas av fel koden kan förhindras.

En SUS pension eller reaktivering kan utföras antingen av användaren eller av systemet. Systemet pausar endast enheter på grund av grava administrativa skäl, till exempel genom att trycka på prenumerations utgifts gränsen. System-inaktiverade entiteter kan inte återaktiveras av användaren, men återställs när orsaken till uppskjutningen har åtgärd ATS.

## <a name="queue-status"></a>Status för kö 
De tillstånd som kan ställas in för en kö är:

-   **Aktiv**: kön är aktiv.
-   **Inaktive rad**: kön har pausats. Det motsvarar att ställa in både **SendDisabled** och **ReceiveDisabled**. 
-   **SendDisabled**: kön är delvis inaktive rad, med mottagning tillåten.
-   **ReceiveDisabled**: kön är delvis inaktive rad, med skicka som tillåten.

### <a name="change-the-queue-status-in-the-azure-portal"></a>Ändra köns status i Azure Portal: 

1. I Azure Portal navigerar du till Service Bus namn området. 
1. Välj den kö som du vill ändra status för. Du ser köer i det nedre fönstret i mitten. 
1. På sidan **Service Bus kö** , se köns aktuella status som en hyperlänk. Om **översikten** inte är markerad på den vänstra menyn väljer du den för att se köns status. Välj aktuell status för kön för att ändra den. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Välj status för kön":::
4. Välj ny status för kön och välj **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Välj status för kön":::
    
Portalen tillåter bara fullständigt inaktive ring av köer. Du kan också inaktivera åtgärderna skicka och ta emot separat med Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -API: er i .NET Framework SDK eller med en Azure Resource Manager-mall via Azure CLI eller Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>Ändra köns status med hjälp av Azure PowerShell
PowerShell-kommandot för att inaktivera en kö visas i följande exempel. Kommandot reaktive ras är detsamma, `Status` inställningen **aktiv**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Ämnes status
Att ändra ämnes status i Azure Portal liknar att ändra status för en kö. När du väljer aktuell status för ämnet visas följande sida där du kan ändra status. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Välj status för kön":::

De tillstånd som kan ställas in för ett ämne är:
- **Aktiv**: ämnet är aktivt.
- **Inaktive rad**: avsnittet har pausats.
- **SendDisabled**: samma resultat som **inaktive rad**.

## <a name="subscription-status"></a>Prenumerations status
Att ändra prenumerations statusen i Azure Portal liknar att ändra status för ett ämne eller en kö. När du väljer aktuell status för prenumerationen visas följande sida där du kan ändra status. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Välj status för kön":::

De tillstånd som kan ställas in för ett ämne är:
- **Aktiv**: ämnet är aktivt.
- **Inaktive rad**: avsnittet har pausats.
- **ReceiveDisabled**: samma resultat som **inaktive rad**.

## <a name="other-statuses"></a>Andra statusar
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) -uppräkningen definierar också en uppsättning över gångs tillstånd som bara kan ställas in av systemet. 


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

