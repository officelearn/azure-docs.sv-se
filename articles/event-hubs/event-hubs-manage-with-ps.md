---
title: "Hantera Azure Event Hubs resurser med hjälp av PowerShell | Microsoft Docs"
description: "Använd PowerShell-modulen för att skapa och hantera Händelsehubbar"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: de86b8241166d4e0bd03beb22550464457e3db5e
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Använda PowerShell för att hantera resurser för Händelsehubbar

Microsoft Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distribution och hantering av Azure-tjänster. Den här artikeln beskriver hur du använder den [Event Hubs Resource Manager PowerShell-modulen](/powershell/module/azurerm.eventhub) att etablera och hantera Händelsehubbar entiteter (namnområden, enskilda händelsehubbar och konsumentgrupper) med hjälp av en lokal Azure PowerShell-konsolen eller ett skript.

Du kan också hantera Händelsehubbar resurser med hjälp av Azure Resource Manager-mallar. Mer information finns i artikeln [skapa ett namnområde för Händelsehubbar med nav- och konsumenten händelsegruppen med en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du följande:

* En Azure-prenumeration. Mer information om hur du skaffar en prenumeration finns [köpalternativ][purchase options], [medlemserbjudanden][member offers], eller [kostnadsfritt konto][free account].
* En dator med Azure PowerShell. Instruktioner finns i [Kom igång med Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps).
* En förståelse av PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="get-started"></a>Kom igång

Det första steget är att använda PowerShell för att logga in på Azure-konto och Azure-prenumeration. Följ instruktionerna i [Kom igång med Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps) för att logga in på kontot sedan hämta och komma åt resurser i din Azure-prenumeration.

## <a name="provision-an-event-hubs-namespace"></a>Etablera ett namnområde för Händelsehubbar

När du arbetar med Händelsehubbar namnområden kan du använda den [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [ny AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [ta bort AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace), och [Set AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) cmdlets.

Det här exemplet skapar några lokala variabler i skriptet; `$Namespace` och `$Location`.

* `$Namespace`är namnet på namnområdet Händelsehubbar vi vill arbeta med.
* `$Location`identifierar datacenter där vi etablerar namnområdet.
* `$CurrentNamespace`lagrar referens-namnområde som vi hämta (eller skapa).

I en verklig skript `$Namespace` och `$Location` kan skickas som parametrar.

Den här delen av skriptet gör följande:

1. Försök att hämta ett Händelsehubbar namnområde med det angivna namnet.
2. Om namnområdet hittas rapporterar vad hittades.
3. Om det gick inte att hitta namnområdet, skapar namnområdet och hämtar sedan det nya namnområdet.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Om du vill skapa en händelsehubb, kontrollera namnområdet med hjälp av skriptet i föregående avsnitt. Använd sedan den [ny AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) för att skapa händelsehubben:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Om du vill skapa en konsumentgrupp i en händelsehubb utföra hubben namnområde och händelsen kontrollerar du med skripten i föregående avsnitt. Använd sedan den [ny AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) för att skapa konsumentgrupp i händelsehubben. Exempel:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Ange Användarmetadata

När du kör skripten i föregående avsnitt, kan du använda den [Set AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) att uppdatera egenskaperna för en konsumentgrupp, som i följande exempel:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Ta bort händelsehubb

Du kan använda för att ta bort händelsehubbar som du skapade den `Remove-*` cmdlets, som i följande exempel:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Nästa steg

- Finns i dokumentationen för fullständig Event Hubs Resource Manager PowerShell-modulen [här](/powershell/module/azurerm.eventhub). Den här sidan visas alla tillgängliga cmdlet: ar.
- Information om hur du använder Azure Resource Manager-mallar finns i artikeln [skapa ett namnområde för Händelsehubbar med nav- och konsumenten händelsegruppen med en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub.md).
- Information om [bibliotek för Event Hubs .NET](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
