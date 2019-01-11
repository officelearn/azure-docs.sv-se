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
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201490"
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