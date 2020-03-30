---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133612"
---
Öppna PowerShell-konsolen med förhöjda privilegier.

Om du kör Azure PowerShell lokalt ansluter du till ditt Azure-konto. Cmdlet *Connect-AzAccount* uppmanar dig att ange autentiseringsuppgifter. När du har autentiserat hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Om du använder Azure Cloud Shell i stället behöver du inte köra *Connect-AzAccount*. Azure Cloud Shell ansluter automatiskt till ditt Azure-konto.

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