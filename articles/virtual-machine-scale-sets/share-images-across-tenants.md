---
title: Dela galleriavbildningar över klienter i Azure
description: Lär dig hur du delar VM-avbildningar över Azure-klienter med delade avbildningsgallerier.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276344"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Dela vm-avbildningar i galleri över Azure-klienter

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Skapa en skalningsuppsättning med Azure CLI

Logga in tjänstens huvudnamn för klient 1 med appID, appnyckeln och ID:et för klient 1. Du kan `az account show --query "tenantId"` använda för att hämta klient-ID:t om det behövs.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Logga in tjänstens huvudnamn för klient 2 med appID, appnyckeln och ID:et för klient 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Skapa skalningsuppsättningen. Ersätt informationen i exemplet med din egen.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på några problem kan du [felsöka delade bildgallerier](troubleshooting-shared-images.md).
