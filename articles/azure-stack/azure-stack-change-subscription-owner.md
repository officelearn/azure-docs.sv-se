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
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 1d9dd7d19c196679ead9b552bcf296b4acd4ca68
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842897"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Ändra ägare för en prenumeration för Azure Stack-användare

Azure Stack-operatörer kan använda PowerShell för att ändra fakturering ägaren till en användarprenumeration. Ett skäl till att ändra ägare, till exempel är att ersätta en användare som lämnar din organisation.

Det finns två typer av *ägare* som är tilldelade till en prenumeration:

- **Fakturering ägare**: Som standard är fakturering ägaren det användarkonto som hämtar prenumerationen från ett erbjudande och sedan äger faktureringsrelation för den aktuella prenumerationen. Det här kontot är också en administratör för prenumerationen. Endast ett användarkonto kan ha den här beteckning på en prenumeration. En fakturering ägare är ofta en organisation eller ditt team lead.

  Du kan använda PowerShell-cmdleten [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) att ändra fakturering ägare.  

- **Ägare har lagts till via RBAC-roller** – ytterligare användare kan beviljas de **ägare** rollen med hjälp av den [rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md) (RBAC)-system. Valfritt antal ytterligare användarkonton kan läggas till som ägare att komplettera fakturering ägare. Ytterligare ägare är också administratörer för prenumerationen och har alla behörigheter för prenumerationen, förutom behörighet att ta bort fakturering ägare.

  Du kan använda PowerShell för att hantera ytterligare ägare. Mer information finns i [den här artikeln](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Ändra fakturering ägaren

Kör följande skript för att ändra fakturering ägaren till en användarprenumeration. Datorn som du använder för att köra skriptet måste ansluta till Azure Stack och köra Azure Stack PowerShell-modulen 1.3.0 eller senare. Mer information finns i [installera Azure Stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Den nya ägaren måste finnas i samma katalog som den befintliga ägaren i flera innehavare Azure Stack. Innan du kan ge en användare som är i en annan katalog ägarskapet för prenumerationen måste du först [bjuda in användaren som gäst i din katalog](../active-directory/b2b/add-users-administrator.md).

Ersätt följande värden i skriptet innan den körs:

- **$ArmEndpoint**: Resource Manager-slutpunkten för din miljö.
- **$TenantId**: Din klient-ID.
- **$SubscriptionId**: Ditt prenumerations-ID.
- **$OwnerUpn**: Ett konto, till exempel **användaren\@example.com**, för att lägga till som den nya ägaren för fakturering.

```powershell
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

- [Hantera rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md)
