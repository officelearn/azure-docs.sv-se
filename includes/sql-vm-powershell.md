---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187301"
---
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
 

Kör cmdleten [**Connect-AZ-konto**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) så visas en inloggnings skärm där du kan ange dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure-portalen.

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer använder du cmdleten [**set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) för att välja vilken prenumeration som din PowerShell-session ska använda. Om du vill se vilken prenumeration den aktuella PowerShell-sessionen använder kör du [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Om du vill se alla dina prenumerationer kör du [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

