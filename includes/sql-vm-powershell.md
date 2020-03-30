---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187301"
---
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
 

Kör cmdlet för [**Connect-Az-konto**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) och du kommer att få en inloggningsskärm för att ange dina inloggningsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure-portalen.

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer använder [**cmdlet set-azcontext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) för att välja vilken prenumeration din PowerShell-session ska använda. Om du vill se vilken prenumeration den aktuella PowerShell-sessionen använder kör du [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Om du vill se alla dina prenumerationer kör du [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

