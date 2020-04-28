---
title: Dela Galleri bilder över klienter i Azure
description: Lär dig hur du delar VM-avbildningar i Azure-klienter med hjälp av delade avbildnings gallerier.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276344"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Dela Galleri VM-avbildningar i Azure-klienter

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Skapa en skalningsuppsättning med Azure CLI

Logga in tjänstens huvud namn för klient organisation 1 med hjälp av appID, appens nyckel och ID för klient 1. Du kan använda `az account show --query "tenantId"` för att hämta klient-ID: n vid behov.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Logga in tjänstens huvud namn för klient 2 med hjälp av appID, appens nyckel och ID: t för klient organisationen 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Skapa skalnings uppsättningen. Ersätt informationen i exemplet med din egen.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem kan du [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).
