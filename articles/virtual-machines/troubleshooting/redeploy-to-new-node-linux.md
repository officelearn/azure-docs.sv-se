---
title: Distribuera om Linux-datorer i Azure | Microsoft Docs
description: Så här att distribuera om Linux-datorer i Azure för att minimera SSH-anslutningsproblem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 1c1ac1a20c1c06db10c1462c95f4d924c5ec09d1
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756198"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Distribuera om virtuell Linux-dator till nya Azure-nod
Om du stöter på problem med felsökning av SSH eller programmet åtkomst till en Linux-dator (VM) i Azure, distribuera om den virtuella datorn kan hjälpa. När du distribuerar om en virtuell dator, flyttas den virtuella datorn till en ny nod i Azure-infrastrukturen och aktiverar den igen. Alla konfigurationsalternativ och associerade resurser finns kvar. Den här artikeln visar hur du distribuera om en virtuell dator med Azure CLI eller Azure-portalen.

> [!NOTE]
> När du distribuerar om en virtuell dator, den temporära disken går förlorad och den dynamiska IP-adresser som är associerade med virtuella nätverksgränssnittet har uppdaterats. 


## <a name="use-the-azure-cli"></a>Använda Azure CLI
Installera senast [Azure CLI](/cli/azure/install-az-cli2) och logga in på Azure med hjälp av [az-inloggning](/cli/azure/reference-index).

Distribuera om den virtuella datorn med [az vm omdistribution](/cli/azure/vm). I följande exempel distribuerar om den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Använda den klassiska Azure CLI
Installera den [senaste klassiska Azure-CLI](../../cli-install-nodejs.md) och logga in på ditt Azure-konto. Se till att du är i Resource Manager-läge (`azure config mode arm`).

I följande exempel distribuerar om den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du hittar detaljerad hjälp på [felsökning av SSH-anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [detaljerad SSH felsökningssteg](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Om du inte kommer åt ett program som körs på den virtuella datorn, du kan också läsa [felsökning av problem med programmet](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


