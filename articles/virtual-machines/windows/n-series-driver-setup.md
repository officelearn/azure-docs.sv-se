---
title: Azure N-serien GPU-drivrutinen installationsprogrammet för Windows | Microsoft Docs
description: Hur du ställer in NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Windows Server eller Windows i Azure
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d173f1f9048a46f3fb2500d225ec121157d5d960
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483554"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Installera NVIDIA GPU-drivrutiner på N-serien virtuella datorer som kör Windows 

Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows, måste NVIDIA GPU-drivrutiner installeras. Den [NVIDIA GPU-drivrutinen tillägget](../extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA CUDA- eller NÄTVERKSBASERADE drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg, till exempel Azure PowerShell eller Azure Resource Manager-mallar. Se den [NVIDIA GPU-drivrutinen tillägget dokumentation](../extensions/hpccompute-gpu-windows.md) operativsystem som stöds och distributionsanvisningar för.

Om du väljer att installera GPU-drivrutiner manuellt, innehåller den här artikeln operativsystem, drivrutiner och steg för installation och kontroll. Manuell installation av drivrutinsinformation är också tillgängligt för [virtuella Linux-datorer](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Grundläggande specifikationer, lagringskapacitet och diskinformation finns i [GPU Windows VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Drivrutinsinstallation

1. Ansluta med fjärrskrivbord till varje virtuell dator i N-serien.

2. Hämta, extrahera och installera drivrutinen som stöds för ditt Windows-operativsystem.

Efter installation av RUTNÄTET på en virtuell dator krävs en omstart. Efter installationen för CUDA-drivrutinen kan en omstart krävs inte.

## <a name="verify-driver-installation"></a>Kontrollera drivrutinsinstallation

Du kan kontrollera drivrutinsinstallation i Enhetshanteraren. I följande exempel visas en framgångsrik konfigurering av Tesla K80-kort på en Azure NC VM.

![Egenskaper för GPU-drivrutin](./media/n-series-driver-setup/GPU_driver_properties.png)

Om du vill fråga GPU-enhetstillstånd, kör den [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyg som installeras med drivrutinen.

1. Öppna en kommandotolk och ändra till den **C:\Program Files\NVIDIA Corporation\NVSMI** directory.

2. Kör `nvidia-smi`. Om drivrutinen har installerats, visas utdata som liknar följande. Den **GPU-Util** visar **0%** om du använder en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversion och GPU-detaljer för din kan skilja sig från de som visas.

![NVIDIA Enhetsstatus](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktiveras på RDMA-kompatibla N-serien virtuella datorer, till exempel NC24r som distribuerats i samma tillgänglighetsuppsättning eller i en enda placeringsgrupp i en skalningsuppsättning för virtuell dator. Tillägget HpcVmDrivers måste läggas till installation av enhetsdrivrutiner för Windows network RDMA-anslutning. Om du vill lägga till VM-tillägget för en N-serien med RDMA-aktiverade VM, använda [Azure PowerShell](/powershell/azure/overview) cmdletar för Azure Resource Manager.

Om du vill installera den senaste versionen 1.1 HpcVMDrivers tillägg på en befintlig RDMA-kompatibla virtuell dator med namnet myVM i regionen västra USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Mer information finns i [VM-tillägg och funktioner i Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

RDMA-nätverk stöder Message Passing Interface (MPI)-trafik för program som körs med [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) eller Intel MPI 5.x. 


## <a name="next-steps"></a>Nästa steg

* Utvecklare som skapar GPU-accelererade program för NVIDIA Tesla GPU: er kan också hämta och installera senast [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Mer information finns i den [CUDA installationsguide](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


