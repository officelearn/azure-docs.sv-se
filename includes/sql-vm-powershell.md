---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165827"
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

