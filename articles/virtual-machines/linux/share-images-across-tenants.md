---
title: Dela galleriavbildningar över klienter i Azure
description: Lär dig hur du delar VM-avbildningar över Azure-klienter med delade avbildningsgallerier.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034977"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Dela vm-avbildningar i galleri över Azure-klienter

Med delade bildgallerier kan du dela bilder med RBAC. Du kan använda RBAC för att dela avbildningar i din klientorganisation och till och med till personer utanför din klientorganisation. Mer information om det här enkla delningsalternativet finns i [Dela galleriet](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Du kan inte använda portalen för att distribuera en virtuell dator från en avbildning i en annan azure-klientorganisation. Om du vill skapa en virtuell dator från en avbildning som delas mellan klienter måste du använda Azure CLI eller [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Skapa en virtuell dator med Azure CLI

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

Skapa den virtuella datorn. Ersätt informationen i exemplet med din egen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på några problem kan du [felsöka delade bildgallerier](troubleshooting-shared-images.md).