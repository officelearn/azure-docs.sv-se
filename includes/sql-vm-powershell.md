---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: f9a45da2703518000aa464da067c5cf71a198fd4
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984978"
---
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
 

Kör den [ **Connect-Az Account** ](https://docs.microsoft.com/powershell/module/az.accounts/connect-azmaccount) cmdlet och du kommer att visas en skärm att ange dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer använder den [ **Set-AzContext** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) cmdlet för att välja vilken prenumeration som din PowerShell-session ska använda. Om du vill se vilken prenumeration den aktuella PowerShell-sessionen använder kör [ **Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Om du vill se alla dina prenumerationer kör [ **Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

