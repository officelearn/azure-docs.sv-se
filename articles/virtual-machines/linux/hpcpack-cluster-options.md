---
title: Alternativ för Linux HPC Pack-kluster i Azure | Microsoft Docs
description: Läs mer om alternativen med Microsoft HPC Pack för att skapa och hantera en Linux databehandling med höga prestanda (HPC)-kluster i Azure-molnet
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 79600909387b1876b112219b5b9b1e45ba4054b7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340083"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Alternativ med HPC Pack för att skapa och hantera ett kluster för Linux HPC-arbetsbelastningar i Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Den här artikeln fokuserar på Azure-alternativ för att köra Linux-arbetsbelastningar med HPC Pack. Det finns också alternativ för att köra [Windows HPC-arbetsbelastningar med HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>HPC Pack-kluster i Azure virtuella datorer och VM scale sets
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
* (GitHub) [Uppdatering 1 för HPC Pack 2016-kluster-mallar](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-kluster-mallar](https://github.com/MsHpcPack/HPCPack2012R2)
* (Snabbstart) [Skapa ett HPC Pack 2012 R2-kluster med Linux-beräkningsnoder](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Azure VM-avbildningar
Bläddra [HPC Pack-avbildningar på Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) om du vill bygga ett eget HPC Pack-kluster i Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>HPC Pack 2012 R2-kluster i den klassiska distributionsmodellen
* [Skapa en Linux HPC-kluster med skriptet för HPC Pack IaaS-distribution](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Självstudier: Kom igång med Linux-beräkningsnoder i ett HPC Pack-kluster i Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Självstudie: Kör NAMD med Microsoft HPC Pack på Linux-beräkningsnoder i Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Självstudie: Kör OpenFOAM med Microsoft HPC Pack på ett Linux RDMA-kluster i Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Självstudie: Kör STAR-CCM + med Microsoft HPC Pack på ett Linux RDMA-kluster i Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Jobbet submisssion
* [Skicka jobb till ett HPC Pack-kluster i Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




