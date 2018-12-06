---
title: Uppdatera ägaren av Azure Stack-användarprenumeration | Microsoft Docs
description: Ändra fakturering ägaren för prenumerationer för Azure Stack-användare.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: 46dd9c786fddb2f6ce9fb8a761a1b9f135a82d74
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959204"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Ändra ägare för en prenumeration för Azure Stack-användare

Azure Stack-operatörer kan använda PowerShell för att ändra fakturering ägaren till en användarprenumeration. Ett skäl till att ändra ägare, till exempel är att ersätta en användare som lämnar din organisation.   

Det finns två typer av *ägare* som är tilldelade till en prenumeration:

- **Fakturering ägare**: som standard fakturering ägaren är det användarkonto som hämtar prenumerationen från ett erbjudande och sedan äger faktureringsrelation för den aktuella prenumerationen. Det här kontot är också en administratör för prenumerationen. Endast ett användarkonto kan ha den här beteckning på en prenumeration. En fakturering ägare är ofta en organisation eller ditt team lead. 

  Du använder PowerShell-cmdleten i [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) att ändra fakturering ägare.  

- **Ägare har lagts till via RBAC-roller** – ytterligare användare kan beviljas de **ägare** rollen med hjälp av den [rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md) (RBAC)-system. Valfritt antal ytterligare användarkonton kan läggas till som ägare att komplettera fakturering ägare. Ytterligare ägare är också administratörer för prenumerationen och har alla behörigheter för prenumerationen, förutom behörighet att ta bort fakturering ägare. 

  Du kan använda PowerShell för att hantera ytterligare ägare, se [Azure PowerShell för att hantera rollbaserad åtkomstkontroll](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Ändra fakturering ägaren

Kör följande skript för att ändra fakturering ägaren till en användarprenumeration. Datorn som du använder för att köra skriptet måste ansluta till Azure Stack och köra Azure Stack PowerShell-modulen 1.3.0 eller senare. Mer information finns i [installera Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]
>  Den nya ägaren måste finnas i samma katalog som den befintliga ägaren i flera innehavare Azure Stack. Innan du kan ge en användare som är i en annan katalog ägarskapet för prenumerationen måste du först [bjuda in användaren som gäst i din katalog](../active-directory/b2b/add-users-administrator.md). 

Ersätt följande värden i skriptet innan den körs: 
 
- **$ArmEndpoint**: Ange Resource Manager-slutpunkten för din miljö.  
- **$TenantId**: Ange ditt Klientorganisations-ID. 
- **$SubscriptionId**: Ange ditt prenumerations-ID.
- **$OwnerUpn**: Ange ett konto som **user@example.com** ska läggas till som den nya ägaren för fakturering.  

```PowerShell   
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Nästa steg

[Hantera rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md)
