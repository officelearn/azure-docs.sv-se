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
ms.openlocfilehash: 661849d2d5c42db70d366e48e4138008bc18b720
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902310"
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

Observera att NVIDIA-tillägget alltid kommer att installera latst-drivrutinen. Vi tillhandahåller länkar till den tidigare versionen här för kunder, som har beroende av en äldre version.

| OS | Drivrutin |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [GRID 9,1 (431,79)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) <br/><br/> [GRID 9,0 (431,02)](https://download.microsoft.com/download/8/C/C/8CC88D54-EB07-44D3-8FA9-B797B173ED04/431.02_grid_win10_server2016_server2019_64bit_international.exe) (. exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [GRID 9,1 (431,79)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)<br/><br/> [GRID 9,0 (431,02)](https://download.microsoft.com/download/B/D/6/BD6D1C34-E41F-4654-B5AB-F4DA9C08AA64/431.02_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (. exe)  |
