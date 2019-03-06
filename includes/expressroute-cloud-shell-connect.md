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
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410697"
---
 Om du använder Azure Cloud Shell kan logga du in på Azure-kontot automatiskt när du klickar på ”prova”. Öppna PowerShell-konsolen med förhöjd behörighet och kör cmdlet: en att ansluta för att logga in lokalt.

```azurepowershell
Connect-AzAccount
```

Om du har mer än en prenumeration kan du hämta en lista över dina Azure-prenumerationer.

```azurepowershell-interactive
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```