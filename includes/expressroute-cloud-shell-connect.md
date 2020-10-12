---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187076"
---
 Om du använder Azure Cloud Shell loggar du in på ditt Azure-konto automatiskt när du har klickat på "testa". Logga in lokalt genom att öppna PowerShell-konsolen med utökade privilegier och köra cmdleten för att ansluta.

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