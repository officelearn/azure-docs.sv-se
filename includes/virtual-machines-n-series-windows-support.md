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
ms.openlocfilehash: 22d2ddc65616bbeb0c511afa6f60bc12c6ec67ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022054"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla-drivrutiner (CUDA)

NVIDIA Tesla-drivrutiner (CUDA) för NC, NCv2, NCv3, NCasT4_v3, ND och NDv2-seriens virtuella datorer (tillval för NV-serien) stöds bara på de operativ system som anges i följande tabell. Driv rutins nedladdnings länkar är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna finns på [NVIDIA:s](https://www.nvidia.com/) webbplats.

> [!TIP]
> Som ett alternativ till manuell CUDA av driv rutins installation på en virtuell Windows Server-dator kan du distribuera en Azure [data science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -avbildning. DSVM-versionerna för Windows Server 2016 förinstallera NVIDIA CUDA-drivrutiner, nätverks biblioteket CUDA djup neurala och andra verktyg.


| Operativsystem | Drivrutinen |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID-drivrutiner installations program för virtuella datorer i NV-och NVv3-serien som används som virtuella arbets stationer eller för virtuella program. Installera endast dessa RUTNÄTs driv rutiner på virtuella datorer i Azure NV-serien, bara på de operativ system som anges i följande tabell. Dessa driv rutiner inkluderar licensiering för virtuella GPU-program i Azure i Azure. Du behöver inte konfigurera en program licens Server för NVIDIA vGPU.

RUTNÄTs driv rutinerna som distribueras med Azure fungerar inte på virtuella datorer som inte är NV-serien, till exempel NC, NCv2, NCv3, ND och NDv2-seriens virtuella datorer. Det enda undantaget är NCas_T4_V3 VM-serien där RUTNÄTs driv rutinerna ska aktivera grafik funktionerna som liknar NV-serien.

Observera att NVIDIA-tillägget alltid kommer att installera den senaste driv rutinen. Vi tillhandahåller länkar till den tidigare versionen här för kunder, som har beroende av en äldre version.

För Windows Server 2019, Windows Server 2016 och Windows 10 (upp till build 2004):
- [GRID 11,1 (452,39)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [GRID 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

För Windows Server 2012 R2: 
- [GRID 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 
- [GRID 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
