---
title: Windows HPC Pack klustret alternativ i Azure | Microsoft Docs
description: Lär dig mer om alternativen with Microsoft HPC Pack för att skapa och hantera en Windows med höga prestanda HPC-kluster i Azure-molnet
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Alternativ med HPC Pack för att skapa och hantera ett kluster för Windows HPC-arbetsbelastningar i Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Den här artikeln fokuserar på Azure alternativ för att skapa HPC Pack kluster om du vill köra Windows arbetsbelastningar. Det finns alternativ för att skapa HPC Pack kluster att köra [Linux HPC-arbetsbelastning](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>HPC Pack kluster i Azure Virtual Machines och skalningsuppsättningar
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
* (GitHub) [HPC Pack 2016 uppdatering 1 klustret mallar](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-kluster mallar](https://github.com/MsHpcPack/HPCPack2012R2)
* (Snabbstart) [Skapar ett HPC Pack 2012 R2-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Snabbstart) [Skapar ett HPC Pack 2012 R2-kluster med nod-avbildning för anpassade beräkningen](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM-avbildningar
Bläddra [HPC Pack avbildningar i Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) om du vill skapa egna HPC Pack kluster i Azure.


### <a name="tutorials"></a>Självstudier
* [Självstudier: Distribuera ett HPC Pack 2016-kluster i Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Hantera ett HPC Pack 2016-kluster i Azure med Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>HPC Pack 2012 R2 Klusterdistribution i den klassiska distributionsmodellen
* [Skapa ett HPC-kluster med HPC Pack IaaS-distributionsskriptet](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Självstudier: Kom igång med HPC Pack-kluster i Azure för att köra arbetsbelastningar för Excel och SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Hantera compute-noder i ett HPC Pack kluster i Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Växa eller krympa Azure-beräkningsresurser i ett HPC Pack-kluster](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Ställa in ett RDMA-Windows-kluster med HPC Pack som kör MPI-program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Burst till Azure från HPC Pack 2012 R2
* [Burst to Azure Worker-instanser med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Burst to Azure Batch med HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Självstudier: Skapa en hybrid-kluster med HPC Pack i Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Jobbet

* [Skicka jobb till ett HPC Pack kluster i Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






