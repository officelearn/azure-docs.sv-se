---
title: Använd PowerShell för att hantera Azure Service Bus-resurser | Microsoft Docs
description: Använda PowerShell-modulen för att skapa och hantera Service Bus-resurser
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 7095a10d38912a21707f52e170c2465d1f8713bc
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54857054"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Använd PowerShell för att hantera Service Bus-resurser

Microsoft Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av Azure-tjänster. Den här artikeln beskriver hur du använder den [Service Bus Resource Managers PowerShell-modulen](/powershell/module/azurerm.servicebus) att etablera och hantera Service Bus-entiteter (namnområden, köer, ämnen och prenumerationer) med hjälp av en lokal Azure PowerShell-konsolen eller ett skript.

Du kan också hantera Service Bus-entiteter med hjälp av Azure Resource Manager-mallar. Mer information finns i artikeln [skapa Service Bus-resurser med hjälp av Azure Resource Manager-mallar](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du följande krav:

* En Azure-prenumeration. Läs mer om hur du skaffar en prenumeration, [köpalternativ][purchase options], [medlemserbjudanden][member offers], eller [kostnadsfritt kontot][free account].
* En dator med Azure PowerShell. Anvisningar finns i [Kom igång med Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps).
* En allmän förståelse för PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="get-started"></a>Kom igång

Det första steget är att använda PowerShell för att logga in på Azure-konto och Azure-prenumeration. Följ instruktionerna i [Kom igång med Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps) att logga in på ditt Azure-konto och hämta och komma åt resurser i din Azure-prenumeration.

## <a name="provision-a-service-bus-namespace"></a>Etablera en Service Bus-namnområde

När du arbetar med Service Bus-namnområden, du kan använda den [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), och [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) cmdletar.

Det här exemplet skapar några lokala variabler i skriptet; `$Namespace` och `$Location`.

* `$Namespace` är namnet på Service Bus-namnområde som vi vill arbeta.
* `$Location` identifierar det datacenter där vi etablera namnområdet.
* `$CurrentNamespace` lagrar referens-namnområde som vi hämta (eller skapa).

I ett faktiskt skript `$Namespace` och `$Location` kan skickas som parametrar.

Den här delen av skriptet gör följande:

1. Om du försöker hämta ett Service Bus-namnområde med det angivna namnet.
2. Om namnområdet hittas, rapporterar vad hittades.
3. Om namnområdet inte finns, skapar namnområdet och sedan hämtar det nyligen skapade namnområdet.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Skapa en auktoriseringsregel för namnområde

I följande exempel visar hur du hanterar auktoriseringsregler för namnområdet med hjälp av den [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), och [Remove-AzureRmServiceBusNamespaceAuthorizationRule cmdletar](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Skapa en kö

Om du vill skapa en kö eller ämne, kontrollera namnområdet med hjälp av skriptet i föregående avsnitt. Skapa sedan kön:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Ändra egenskaper för kö

När du kör skriptet i föregående avsnitt, kan du använda den [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) cmdlet för att uppdatera egenskaperna för en kö, som i följande exempel:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Etablera andra Service Bus-entiteter

Du kan använda den [Service Bus PowerShell-modulen](/powershell/module/azurerm.servicebus) att etablera andra entiteter, till exempel ämnen och prenumerationer. Dessa cmdletar är syntaktiskt liknande i kön skapas-cmdletar som visas i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

- Se den fullständiga dokumentationen för Service Bus Resource Managers PowerShell-modulen [här](/powershell/module/azurerm.servicebus). Den här sidan visas alla tillgängliga cmdlet: ar.
- Information om hur du använder Azure Resource Manager-mallar finns i artikeln [skapa Service Bus-resurser med hjälp av Azure Resource Manager-mallar](service-bus-resource-manager-overview.md).
- Information om [Service Bus .NET-hanteringsbibliotek](service-bus-management-libraries.md).

Det finns några alternativa metoder för att hantera Service Bus-entiteter, enligt beskrivningen i följande blogginlägg:

* [Så här skapar du Service Bus-köer, ämnen och prenumerationer med hjälp av ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Så här skapar du en Service Bus Namespace och en Händelsehubb med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
