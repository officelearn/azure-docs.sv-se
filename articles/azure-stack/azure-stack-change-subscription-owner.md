---
title: Uppdatera ägaren av Azure Stack-användarprenumeration | Microsoft Docs
description: Ändra ägaren fakturering för Azure STack-prenumerationer som användaren.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: brenduns
ms.reviewer: shnatara
ms.openlocfilehash: de8610944e11d8cf106ac484a0c6634c8661b5a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009494"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Ändra ägare för en prenumeration för Azure Stack-användare

Azure Stack-operatörer kan använda PowerShell för att ändra fakturering ägaren till en användarprenumeration. En anledning att ändra ägaren är att ersätta en användare som lämnar din organisation.   

Det finns två typer av *ägare* som är tilldelade till en prenumeration:

- **Fakturering ägare** – som standard ägare fakturering är det användarkonto som hämtar prenumerationen från ett erbjudande och sedan äger faktureringsrelation för den aktuella prenumerationen. Det här kontot är också en administratör för prenumerationen.  Endast ett användarkonto kan ha den här beteckning på en prenumeration. En fakturering ägare är ofta en organisation eller ditt team lead. 

  Du använder PowerShell-cmdleten i **Set-AzsUserSubscription** att ändra ägare för fakturering.  

- **Ägare har lagts till via RBAC-roller** – ytterligare användare kan tilldelas rollen ägare med den [rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md) (RBAC)-system.  Valfritt antal ytterligare användarkonton kan läggas till som ägare att komplettera fakturering-ägare. Ytterligare ägare är också administratörer för prenumerationen och ha alla behörigheter för prenumerationen förutom behörighet att ta bort ägaren fakturering. 

  Du kan använda PowerShell för att hantera ytterligare ägare, se [Azure PowerShell för att hantera rollbaserad åtkomstkontroll]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="change-the-billing-owner"></a>Ändra ägare för fakturering
Kör följande skript för att ändra fakturering ägaren till en användarprenumeration.  Datorn som du använder för att köra skriptet måste ansluta till Azure Stack och köra Azure Stack PowerShell-modulen 1.3.0 eller senare. Mer information finns i [installera Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]  
>  I en med flera innehavare Azure Stack, måste den nya ägaren finnas i samma katalog som den befintliga ägaren. Innan du kan ge en användare som är i en annan katalog ägarskapet för prenumerationen måste du först [bjuda in användaren som gäst i din katalog](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Ersätt följande värden i skriptet innan den körs: 
 
- **$ArmEndpoint** – ange Resource Manager-slutpunkten för din miljö.  
- **$TenantId** -ange dina klient-ID. 
- **$SubscriptionId** – ange ditt prenumerations-ID.
- **$OwnerUpn** -ange ett konto som *user@example.com* ska läggas till som den nya ägaren för fakturering.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>Nästa steg
[Hantera rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md)
