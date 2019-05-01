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
ms.openlocfilehash: 0dda467c3906c217c18e1340f7dc2a464dcbccae
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744603"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) drivrutiner

NVIDIA Tesla (CUDA) drivrutiner för NC, NCv2, NCv3, ND och NDv2-seriens virtuella datorer (valfritt för NV-serien) stöds endast i operativsystemen som anges i följande tabell. Drivrutinen är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna finns på [NVIDIA:s](http://www.nvidia.com/) webbplats.

> [!TIP]
> Som ett alternativ till manuell installation av CUDA på en Windows Server-dator kan du distribuera en Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Windows Server 2016 förinstallation NVIDIA CUDA-drivrutiner, CUDA djupa Neurala nätverksbibliotek och andra verktyg.


| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID drivers

Microsoft distribuerar NVIDIA GRID drivrutinen installationsprogram för NV och NVv2-serien virtuella datorer som används som virtuella arbetsstationer eller till virtuella program. Installera endast de här drivrutinerna RUTNÄTET på NV virtuella datorer i Azure, endast på operativsystemen som anges i följande tabell. Dessa faktorer är licensiering för GRID virtuella GPU-programvara i Azure. Du behöver inte konfigurera en licensserver för NVIDIA vGPU programvara.

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 (upp till version 1803) | [RUTNÄTET 8.0 (425.31)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [RUTNÄTET 8.0 (425.31)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
