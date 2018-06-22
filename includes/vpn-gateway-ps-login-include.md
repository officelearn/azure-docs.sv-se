---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313684"
---
Innan du påbörjar den här konfigurationen måste du logga in på ditt Azure-konto. Cmdlet efterfrågar autentiseringsuppgifter för inloggning för din Azure-konto. När du har registrerat hämtar den inställningarna för ditt konto, så att de är tillgängliga för Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](../articles/powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen med förhöjd behörighet för att logga in och ansluta till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzureRmAccount
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

Ange den prenumeration som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
