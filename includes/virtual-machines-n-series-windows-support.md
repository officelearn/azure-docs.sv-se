---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a07f1622f2d41d105c79fa5354d97aa079567e62
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935779"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) drivrutiner

NVIDIA Tesla (CUDA) drivrutiner för NC, NCv2, NCv3 och ND-serien virtuella datorer (valfritt för NV-serien) stöds endast i operativsystemen som anges i följande tabell. Drivrutinen är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna finns på [NVIDIA:s](http://www.nvidia.com/) webbplats.

> [!TIP]
> Som ett alternativ till manuell installation av CUDA på en Windows Server-dator kan du distribuera en Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Windows Server 2016 förinstallation NVIDIA CUDA-drivrutiner, CUDA djupa Neurala nätverksbibliotek och andra verktyg.


| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID drivrutinen installationsprogram för NV-serien virtuella datorer som används som virtuella arbetsstationer eller till virtuella program. Installera endast de här drivrutinerna RUTNÄTET på NV virtuella datorer i Azure, endast på operativsystemen som anges i följande tabell. Dessa faktorer är licensiering för GRID virtuella GPU-programvara i Azure.

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RUTNÄTET 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RUTNÄTET 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |