---
title: Skriptexempel Azure CLI - montera operativsystemdisk | Microsoft Docs
description: Skriptexempel Azure CLI - montera operativsystemdisk
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c32ea5e6cade34a9c8dac0eab523ebcaa10ef039
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Felsöka en operativsystemdisk för virtuella datorer

Det här skriptet monterar operativsystemdisken för en misslyckad eller problematiska virtuell dator som en datadisk till en andra virtuell dator. Detta kan vara användbar vid felsökning av disk problem eller återställa data. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ vm visa](https://docs.microsoft.com/cli/azure/vm#az_vm_show) | Returnera en lista över virtuella datorer. I det här fallet används frågealternativet att returnera den virtuella disken för operativsystemet. Det här värdet läggs sedan till ett variabelnamn 'uri'. |
| [AZ virtuella ta bort](https://docs.microsoft.com/cli/azure/vm#az_vm_delete) | Tar bort en virtuell dator. |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar en virtuell dator.  |
| [Koppla AZ virtuell disk](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) | Bifogar en disk till en virtuell dator. |
| [AZ vm lista-ip-adresser](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | Returnerar IP-adresser för en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
