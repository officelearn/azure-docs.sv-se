---
title: Konfiguration av GPU-drivrutin i Azure N-serien för Windows
description: Konfigurera NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Windows Server eller Windows i Azure
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 631266f983886e3ca34d609b425f8a71b808b39f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919404"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Installera NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Windows 

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som kör Windows måste NVIDIA GPU-drivrutiner installeras. [NVIDIA GPU Driver Extension](../extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA CUDA- eller GRID-drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure PowerShell- eller Azure Resource Manager-mallar. Se [dokumentationen](../extensions/hpccompute-gpu-windows.md) för NVIDIA GPU Driver Extension för operativsystem och driftsättningssteg som stöds.

Om du väljer att installera GPU-drivrutiner manuellt innehåller den här artikeln operativsystem, drivrutiner och installations- och verifieringssteg som stöds. Information om manuell drivrutinsinställning är också tillgänglig för [virtuella Linux-datorer](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Grundläggande specifikationer, lagringskapacitet och diskinformation finns i [GPU Windows VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Installation av drivrutiner

1. Anslut med fjärrskrivbord till varje virtuell dator i N-serien.

2. Hämta, extrahera och installera drivrutinen som stöds för operativsystemet Windows.

Efter installation av GRID-drivrutin på en virtuell dator krävs en omstart. Efter installationen av CUDA-drivrutinen krävs ingen omstart.

## <a name="verify-driver-installation"></a>Verifiera installationen av drivrutiner

Observera att Nvidias kontrollpanel endast är tillgänglig med grid-drivrutinsinstallationen. Om du har installerat CUDA-drivrutiner kommer Nvidia-kontrollpanelen inte att synas.

Du kan verifiera drivrutinsinstallationen i Enhetshanteraren. Följande exempel visar en lyckad konfiguration av Tesla K80-kortet på en virtuell Azure NC-dator.

![Egenskaper för GPU-drivrutin](./media/n-series-driver-setup/GPU_driver_properties.png)

Om du vill fråga om gpu-enhetens tillstånd kör du [kommandoradsverktyget nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) som är installerat med drivrutinen.

1. Öppna en kommandotolk och ändra till katalogen **C:\Program\NVIDIA Corporation\NVSMI.**

2. Kör `nvidia-smi`. Om drivrutinen är installerad visas utdata som liknar följande. **GPU-Util** visar **0%** om du inte för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversionen och GPU-informationen kan skilja sig från de som visas.

![STATUS FÖR NVIDIA-enheter](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktiveras på virtuella datorer i N-serien RDMA, till exempel NC24r som distribueras i samma tillgänglighetsuppsättning eller i en enda placeringsgrupp i en skalningsuppsättning för virtuella datorer. HpcVmDrivers-tillägget måste läggas till för att installera drivrutiner för Windows-nätverk som aktiverar RDMA-anslutning. Om du vill lägga till VM-tillägget i en virtuell dator med RDMA-aktiverad filserie använder du Azure PowerShell-cmdlets för Azure Resource Manager. [Azure PowerShell](/powershell/azure/overview)

Så här installerar du den senaste versionen 1.1 HpcVMDrivers-tillägget på en befintlig VIRTUELL-kompatibel VIRTUELL MED NAMNET MYVM i västra USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Mer information finns i [Tillägg och funktioner för virtuella datorer för Windows](extensions-features.md).

RDMA-nätverket stöder MPI-trafik (Message Passing Interface) för program som körs med [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) eller Intel MPI 5.x. 


## <a name="next-steps"></a>Nästa steg

* Utvecklare bygga GPU-accelererade applikationer för NVIDIA Tesla GPU kan också ladda ner och installera den senaste [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Mer information finns i [CUDA-installationsguiden](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


