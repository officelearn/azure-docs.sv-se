---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187025"
---
## <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider

Om du inte har använt Event Grid förut i din Azure-prenumeration kan du behöva registrera Event Grid-resursprovidern. Registrera providern genom att köra följande kommando:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Det kan ta en stund att slutföra registreringen. Du kan kontrollera status genom att köra:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

När `registrationState` är `Registered` kan du fortsätta.