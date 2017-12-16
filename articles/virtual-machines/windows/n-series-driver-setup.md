---
title: "Azure N-serien drivrutinen installationsprogrammet för Windows | Microsoft Docs"
description: "Hur du ställer in NVIDIA GPU drivrutiner för N-serien virtuella datorer som kör Windows i Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58ca4ea85b6097f7210a21db45791bb43b0e99ea
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Ställ in GPU drivrutiner för N-serien virtuella datorer som kör Windows Server
Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows Server 2016 eller Windows Server 2012 R2, installera stöds grafik drivrutinerna. Den här artikeln innehåller drivrutinen konfigurationsstegen när du distribuerar en virtuell dator i N-serien. Inställningsinformation för drivrutinen är också tillgängligt för [virtuella Linux-datorer](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Grundläggande specifikationerna, lagringskapacitet och diskinformation finns [GPU Windows VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]



## <a name="driver-installation"></a>Installation av drivrutiner

1. Anslut med fjärrskrivbord på varje virtuell dator N-serien.

2. Hämta, extrahera och installera drivrutinen som stöds för Windows-operativsystemet.

På Azure NV virtuella datorer startas om efter drivrutinsinstallationen av. På NC virtuella datorer är inte en omstart krävs.

## <a name="verify-driver-installation"></a>Verifiera installation av drivrutiner

Du kan kontrollera installationen av drivrutinen i Enhetshanteraren. Följande exempel visar lyckad konfiguration av Tesla K80-kort på en Azure NC-VM.

![Egenskaper för GPU-drivrutin](./media/n-series-driver-setup/GPU_driver_properties.png)

Om du vill fråga Enhetsstatus GPU kör den [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyg som installeras med drivrutinen.

1. Öppna en kommandotolk och ändra till den **C:\Program Files\NVIDIA Corporation\NVSMI** directory.

2. Kör `nvidia-smi`. Om drivrutinen är installerad visas utdata som liknar följande. Observera att **GPU-Util** visar **0%** om du använder en GPU arbetsbelastning på den virtuella datorn. Din version av drivrutinen och information GPU kan skilja sig från de som visas.

![NVIDIA Enhetsstatus](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktiveras RDMA aktiverade N-serien virtuella datorer som NC24r distribueras i samma tillgänglighetsuppsättning. Tillägget HpcVmDrivers måste läggas till installation av enhetsdrivrutiner för Windows nätverk som gör att RDMA-anslutning. Om du vill lägga till tillägg för virtuell dator till en RDMA-aktiverade N-serien VM, använda [Azure PowerShell](/powershell/azure/overview) cmdlets för Azure Resource Manager.

> [!NOTE]
> Endast Windows Server 2012 R2 stöder för närvarande, nätverkets RDMA på N-serien virtuella datorer.
> 

Om du vill installera den senaste versionen 1.1 HpcVMDrivers tillägg på en befintlig RDMA-kompatibla virtuell dator med namnet myVM i regionen USA, västra:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Mer information finns i [tillägg för virtuell dator och funktioner i Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

RDMA-nätverket har stöd för Message Passing Interface (MPI) trafik för program som körs med [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) eller Intel MPI 5.x. 


## <a name="next-steps"></a>Nästa steg

* Utvecklare som bygger GPU-snabbare program för NVIDIA Tesla GPU kan också hämta och installera den [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Mer information finns i [CUDA installationsguiden](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


