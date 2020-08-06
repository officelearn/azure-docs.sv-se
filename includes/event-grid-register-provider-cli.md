---
title: inkludera fil
description: inkludera fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830144"
---
## <a name="enable-the-event-grid-resource-provider"></a>Aktivera Event Grid Resource Provider

Om du inte tidigare har använt Event Grid i din Azure-prenumeration kan du behöva registrera Event Grid Resource Provider. Registrera providern genom att köra följande kommando:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Det kan ta en stund innan registreringen har slutförts. Du kan kontrollera status genom att köra:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

När `registrationState` är `Registered` kan du fortsätta.