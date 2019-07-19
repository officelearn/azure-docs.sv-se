---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 508d13356a7dd221b0767a0cedd60b4f1402baf4
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286258"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla-drivrutiner (CUDA)

NVIDIA-Tesla (CUDA) för NC-, NCv2-, NCv3-, ND-och NDv2-seriens virtuella datorer (tillval för NV-serien) stöds bara på de operativ system som anges i följande tabell. Driv rutins nedladdnings länkar är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna finns på [NVIDIA:s](https://www.nvidia.com/) webbplats.

> [!TIP]
> Som ett alternativ till manuell CUDA av driv rutins installation på en virtuell Windows Server-dator kan du distribuera en Azure [data science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -avbildning. DSVM-versionerna för Windows Server 2016 förinstallera NVIDIA CUDA-drivrutiner, nätverks biblioteket CUDA djup neurala och andra verktyg.


| OS | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID-drivrutiner installations program för virtuella datorer i NV-och NVv3-serien som används som virtuella arbets stationer eller för virtuella program. Installera endast dessa RUTNÄTs driv rutiner på virtuella datorer i Azure NV-serien, bara på de operativ system som anges i följande tabell. Dessa driv rutiner inkluderar licensiering för virtuella GPU-program i Azure i Azure. Du behöver inte konfigurera en program licens Server för NVIDIA vGPU.

| OS | Drivrutin |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Rutnät 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Rutnät 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)  |
