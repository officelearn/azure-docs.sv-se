---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133612"
---
Öppna PowerShell-konsolen med utökade privilegier.

Om du kör Azure PowerShell lokalt ansluter du till ditt Azure-konto. Med cmdleten *Connect-AzAccount* uppmanas du att ange autentiseringsuppgifter. Efter autentiseringen hämtas dina konto inställningar så att de är tillgängliga för Azure PowerShell. Om du använder Azure Cloud Shell i stället behöver du inte köra *Connect-AzAccount*. Azure Cloud Shell ansluter automatiskt till ditt Azure-konto.

```azurepowershell
Connect-AzAccount
```

Om du har mer än en prenumeration hämtar du en lista över dina Azure-prenumerationer.

```azurepowershell-interactive
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```