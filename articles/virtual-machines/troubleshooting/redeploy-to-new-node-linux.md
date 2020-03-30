---
title: Distribuera om virtuella Linux-datorer i Azure | Microsoft-dokument
description: Så här distribuerar du om virtuella Linux-datorer i Azure för att minska SSH-anslutningsproblem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 18e96f9463176b0fce04252492eea6dbede416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531115"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Distribuera om en virtuell Linux-dator till en ny Azure-nod
Om du har problem med att felsöka SSH- eller programåtkomst till en virtuell Linux-dator (VM) i Azure kan det hjälpa att distribuera om den virtuella datorn. När du distribuerar om en virtuell dator flyttas den virtuella datorn till en ny nod i Azure-infrastrukturen och vidarebefordrar den sedan igen. Alla konfigurationsalternativ och associerade resurser behålls. Den här artikeln visar hur du distribuerar om en virtuell dator med Azure CLI eller Azure-portalen.

> [!NOTE]
> När du har distribuerat om en virtuell dator går den tillfälliga disken förlorad och dynamiska IP-adresser som är associerade med virtuellt nätverksgränssnitt uppdateras. 


## <a name="use-the-azure-cli"></a>Använda Azure CLI
Installera den senaste [Azure CLI](/cli/azure/install-az-cli2) och logga in på ditt Azure-konto med [az login](/cli/azure/reference-index).

Distribuera om den virtuella datorn med [az vm-omdistribution](/cli/azure/vm). I följande exempel distribueras den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup:*

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Använd Den klassiska Azure-CLI-et

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Installera den [senaste Azure-klassikern CLI](../../cli-install-nodejs.md) och logga in på ditt Azure-konto. Kontrollera att du är i`azure config mode arm`resurshanterarens läge ( ).

I följande exempel distribueras den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup:*

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du hitta specifik hjälp med [felsökning av SSH-anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [detaljerade SSH-felsökningssteg](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Om du inte kan komma åt ett program som körs på den virtuella datorn kan du även läsa [problem med programfelsökning](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


