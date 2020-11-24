---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558586"
---
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
 

Kör cmdleten [**Connect-AZ-konto**](/powershell/module/Az.Accounts/Connect-AzAccount) så visas en inloggnings skärm där du kan ange dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure-portalen.

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer använder du cmdleten [**set-AzContext**](/powershell/module/az.accounts/set-azcontext) för att välja vilken prenumeration som din PowerShell-session ska använda. Om du vill se vilken prenumeration den aktuella PowerShell-sessionen använder kör du [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Om du vill se alla dina prenumerationer kör du [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```