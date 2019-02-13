---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 3dc799ecc75589279c8d1c73062a8f2157761330
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213214"
---
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
 

Kör den [ **Connect-Az Account** ](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet och du kommer att visas en skärm att ange dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer använder den [ **Set-AzContext** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) cmdlet för att välja vilken prenumeration som din PowerShell-session ska använda. Om du vill se vilken prenumeration den aktuella PowerShell-sessionen använder kör [ **Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Om du vill se alla dina prenumerationer kör [ **Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

