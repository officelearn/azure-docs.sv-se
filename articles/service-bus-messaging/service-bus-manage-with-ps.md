---
title: Använda PowerShell för att hantera Azure Service Bus-resurser | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Azure PowerShell-modulen för att skapa och hantera Service Bus-entiteter (namnområden, köer, ämnen, prenumerationer).
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e333dfb109840538fd5dec8110e1c32adedce989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759269"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Använda PowerShell för att hantera servicebussresurser

Microsoft Azure PowerShell är en skriptmiljö som du kan använda för att styra och automatisera distributionen och hanteringen av Azure-tjänster. I den hÃ¤r artikeln beskrivs hur du ansÃ¤nder [PowerShell-modulen Service Bus Resource Manager](/powershell/module/az.servicebus) fÃ¤r etablera och hantera Service Bus-entiteter (namnområden, köer, ämnen och prenumerationer) med hjälp av en lokal Azure PowerShell-konsol eller skript.

Du kan också hantera Service Bus-entiteter med Hjälp av Azure Resource Manager-mallar. Mer information finns i artikeln [Skapa servicebussresurser med Azure Resource Manager-mallar](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar behöver du följande förutsättningar:

* En Azure-prenumeration. Mer information om hur du skaffar en prenumeration finns i [köpalternativ,][purchase options] [medlemserbjudanden][member offers]eller [gratiskonto][free account].
* En dator med Azure PowerShell. Instruktioner finns i [Komma igång med Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps).
* En allmän förståelse av PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="get-started"></a>Komma igång

Det första steget är att använda PowerShell för att logga in på ditt Azure-konto och Azure-prenumeration. Följ instruktionerna i [Komma igång med Azure PowerShell-cmdletar](/powershell/azure/get-started-azureps) för att logga in på ditt Azure-konto och hämta och komma åt resurserna i din Azure-prenumeration.

## <a name="provision-a-service-bus-namespace"></a>Etablera ett tjänstbussnamnområde

När du arbetar med servicebussnamnområden kan du använda cmdletsna [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)och [Set-AzServiceBusNamespace.](/powershell/module/az.servicebus/set-azservicebusnamespace)

I det här exemplet skapas några lokala variabler i skriptet. `$Namespace` och `$Location`.

* `$Namespace`är namnet på servicebussens namnområde som vi vill arbeta med.
* `$Location`identifierar det datacenter där vi etablerar namnområdet.
* `$CurrentNamespace`lagrar referensnamnområdet som vi hämtar (eller skapar).

I ett verkligt `$Namespace` `$Location` skript och kan skickas som parametrar.

Den här delen av skriptet gör följande:

1. Försöker hämta ett servicebussnamnområde med det angivna namnet.
2. Om namnområdet hittas rapporterar det vad som hittades.
3. Om namnområdet inte hittas skapas namnområdet och det nyskapade namnområdet hämtas.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Skapa en namnområdesauktoriseringsregel

I följande exempel visas hur du hanterar namnområdesauktoriseringsregler med hjälp av cmdletsen [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)och [Remove-AzServiceBusAuthorizationRule.](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule)

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Skapa en kö

Om du vill skapa en kö eller ett ämne utför du en namnområdeskontroll med skriptet i föregående avsnitt. Skapa sedan kön:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Ändra köegenskaper

När du har kört skriptet i föregående avsnitt kan du använda cmdlet [set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) för att uppdatera egenskaperna för en kö, som i följande exempel:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Etablera andra servicebussenheter

Du kan använda [servicebuss powershell-modulen](/powershell/module/az.servicebus) för att etablera andra entiteter, till exempel ämnen och prenumerationer. Dessa cmdlets är syntaktiskt liknar den köskapande cmdlets som visas i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

- Se den fullständiga PowerShell-modulen PowerShell-modulen Service Bus Resource Manager [här](/powershell/module/az.servicebus). På den här sidan visas alla tillgängliga cmdlets.
- Information om hur du använder Azure Resource Manager-mallar finns i artikeln [Skapa servicebussresurser med Azure Resource Manager-mallar](service-bus-resource-manager-overview.md).
- Information om [Service Bus .NET-hanteringsbibliotek](service-bus-management-libraries.md).

Det finns några alternativa sätt att hantera Service Bus-entiteter, som beskrivs i dessa blogginlägg:

* [Skapa servicebussköer, ämnen och prenumerationer med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Skapa ett servicebussnamnområde och en händelsehubb med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
