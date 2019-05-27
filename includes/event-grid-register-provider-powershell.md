---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 68a208af1a9aa9e73f2af99021d195f264fb21f1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155974"
---
## <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider

Om du inte har använt Event Grid förut i din Azure-prenumeration kan du behöva registrera Event Grid-resursprovidern. Kör följande kommando:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

Det kan ta en stund att slutföra registreringen. Du kan kontrollera status genom att köra:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

När `RegistrationStatus` är `Registered` kan du fortsätta.
