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
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: c5b8c16e076be3002425ceeac377043cea1a40a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Alternativ med HPC Pack för att skapa och hantera ett kluster för Windows HPC-arbetsbelastningar i Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Den här artikeln fokuserar på alternativ för att skapa HPC Pack kluster om du vill köra Windows arbetsbelastningar. Det finns alternativ för att skapa HPC Pack kluster att köra [Linux HPC-arbetsbelastning](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>HPC Pack kluster i Azure Virtual Machines och skalningsuppsättningar
### <a name="azure-templates"></a>Azure-mallar
* (GitHub) [HPC Pack 2016 klustret mallar](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2-kluster mallar](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [HPC Pack kluster för Windows-arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [HPC Pack kluster för Excel arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Snabbstart) [Skapa ett HPC-kluster](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Snabbstart) [Skapa ett HPC-kluster med nod-avbildning för anpassade beräkningen](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM-avbildningar
* [Huvudnod i HPC Pack 2016 på Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [HPC Pack 2016 compute-nod på Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [HPC Pack 2016 huvudnod i Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [HPC Pack 2016 compute-nod på Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Huvudnod i HPC Pack 2012 R2 på Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [HPC Pack 2012 R2-beräkningsnod i Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [HPC Pack beräkningsnod med Excel på Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>PowerShell-distributionsskriptet för HPC Pack 2012 R2
* [Skapa ett HPC-kluster med HPC Pack IaaS-distributionsskriptet](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Självstudier
* [Självstudier: Distribuera ett HPC Pack 2016-kluster i Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Självstudier: Kom igång med HPC Pack-kluster i Azure för att köra arbetsbelastningar för Excel och SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Manuell distribution med Azure-portalen
* [Ställ in huvudnod i HPC Pack-kluster i en Azure VM](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Klusterhantering
* [Hantera compute-noder i ett HPC Pack kluster i Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Växa eller krympa Azure-beräkningsresurser i ett HPC Pack-kluster](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Skicka jobb till ett HPC Pack kluster i Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Hantering av jobb i HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)
* [Hantera ett HPC Pack 2016-kluster i Azure med Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>Burst med rollen arbetsnoder 
* [Burst till Azure worker-instanser med HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Självstudier: Skapa en hybrid-kluster med HPC Pack i Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Lägg till Azure ”burst” noder i ett HPC Pack huvudnod i Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Burst med Azure Batch
* [Burst to Azure Batch med HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>RDMA kluster för MPI arbetsbelastningar
* [Ställa in ett RDMA-Windows-kluster med HPC Pack som kör MPI-program](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

