---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187076"
---
 Om du använder Azure Cloud Shell loggar du in på ditt Azure-konto automatiskt efter att ha klickat på "Prova". Om du vill logga in lokalt öppnar du PowerShell-konsolen med förhöjda privilegier och kör cmdleten för att ansluta.

```azurepowershell
Connect-AzAccount
```

Om du har mer än en prenumeration får du en lista över dina Azure-prenumerationer.

```azurepowershell-interactive
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```