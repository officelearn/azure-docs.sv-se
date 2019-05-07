---
title: Dela galleriavbildningar över klienter i Azure | Microsoft Docs
description: Lär dig mer om att dela VM-avbildningar i Azure-klienter med hjälp av delad Image Galleries.
services: virtual-machine-scale-sets
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: cbaaac629fd013602eed75cc7dc357f13a62e3b1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160132"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Dela galleriet VM-avbildningar i Azure-klienter

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Skapa en skalningsuppsättning med Azure CLI

Logga in tjänstens huvudnamn för klient 1 använda appID, appnyckeln och ID för klient 1. Du kan använda `az account show --query "tenantId"` att skaffa klientens ID: N om det behövs.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Logga in tjänstens huvudnamn för klient 2 med appID, appnyckeln och ID-klient 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Skapa skalningsuppsättningen. Ersätt informationen i det här exemplet med dina egna.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem, kan du [Felsöka delade bildgallerier](troubleshooting-shared-images.md).