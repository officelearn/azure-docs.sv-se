---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852375"
---
Öppna PowerShell-konsolen med förhöjd behörighet.



Om du kör Azure PowerShell lokalt måste du ansluta till ditt Azure-konto. Den *Connect-AzureRmAccount* cmdlet uppmanar dig att autentiseringsuppgifter. När autentisering, hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Hoppa över detta steg om du inte kör PowerShell lokalt och i stället använder Azure Cloud Shell ”prova” i webbläsaren. Du ansluter till ditt Azure-konto automatiskt.

```azurepowershell
Connect-AzureRmAccount
```

Om du har mer än en prenumeration kan du hämta en lista över dina Azure-prenumerationer.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Ange den prenumeration som du vill använda.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```