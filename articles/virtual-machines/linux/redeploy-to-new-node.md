---
title: Distribuera om Linux-datorer i Azure | Microsoft Docs
description: Så här att distribuera om Linux-datorer i Azure för att minimera SSH-anslutningsproblem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 1bd13c35ed49aeaab1a4f4aa94c984dc28f6c111
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931556"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Distribuera om virtuell Linux-dator till nya Azure-nod
Om du stöter på problem med felsökning av SSH eller programmet åtkomst till en Linux-dator (VM) i Azure, distribuera om den virtuella datorn kan hjälpa. När du distribuerar om en virtuell dator, flyttas den virtuella datorn till en ny nod i Azure-infrastrukturen och aktiverar den igen. Alla konfigurationsalternativ och associerade resurser finns kvar. Den här artikeln visar hur du distribuera om en virtuell dator med Azure CLI eller Azure-portalen.

> [!NOTE]
> När du distribuerar om en virtuell dator, den temporära disken går förlorad och den dynamiska IP-adresser som är associerade med virtuella nätverksgränssnittet har uppdaterats. 

Du kan distribuera om en virtuell dator med något av följande alternativ. Du behöver bara välja ett alternativ att distribuera om den virtuella datorn:

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Installera senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in på Azure med hjälp av [az-inloggning](/cli/azure/reference-index#az_login).

Distribuera om den virtuella datorn med [az vm omdistribution](/cli/azure/vm#az_vm_redeploy). I följande exempel distribuerar om den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Använda Azure CLI 1.0
Installera den [senaste Azure CLI 1.0](../../cli-install-nodejs.md) och logga in på ditt Azure-konto. Se till att du är i Resource Manager-läge (`azure config mode arm`).

I följande exempel distribuerar om den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till den virtuella datorn kan du hittar detaljerad hjälp på [felsökning av SSH-anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [detaljerad SSH felsökningssteg](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Om du inte kommer åt ett program som körs på den virtuella datorn, du kan också läsa [felsökning av problem med programmet](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

