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
ms.openlocfilehash: 8f3d57f8f1f3631421618e31e943606a16e6bf5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670027"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>Drivrutiner för NVIDIA Tesla (CUDA)

NVIDIA Tesla (CUDA) drivrutiner för NC, NCv2, NCv3 och ND-serien virtuella datorer (valfritt för NV-serien) stöds endast i operativsystemen som anges i följande tabell. Drivrutinen länkar är aktuella vid tiden för publikationen. De senaste drivrutinerna finns på [NVIDIA:s](http://www.nvidia.com/) webbplats.

> [!TIP]
> Du kan distribuera en Azure som ett alternativ till manuell installation av CUDA på en Windows Server-VM [datavetenskap virtuella](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Windows Server 2016 förinstallation NVIDIA CUDA drivrutiner, nätverksbibliotek CUDA djupa Neurala och andra verktyg.


| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>NVIDIA RUTNÄTET drivrutiner

Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV-serien VMs användas som virtuella arbetsstationer eller för virtuella program. Installera endast drivrutinerna RUTNÄTET på Azure NV virtuella datorer bara på de operativsystem som anges i följande tabell. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RUTNÄTET 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RUTNÄTET 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |