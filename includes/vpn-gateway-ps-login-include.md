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
ms.openlocfilehash: 188f70d17531a3a7da12444ce5f1c97385ea33bc
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418049"
---
Innan du påbörjar den här konfigurationen måste du logga in på kontot. Cmdleten uppmanar dig logga in autentiseringsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](../articles/powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen med förhöjd behörighet för att logga in och Anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```powershell
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
