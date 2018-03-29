---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 426340a5b452124dcba4f51f2ec2c1e2ec0f54b2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC, NCv2, NCv3 och ND-serien - drivrutiner för NVIDIA Tesla (CUDA)

Länkar för hämtning av drivrutiner i följande tabell är aktuella vid tiden för publikationen. De senaste drivrutinerna finns på [NVIDIA:s](http://www.nvidia.com/) webbplats.

> [!TIP]
> Du kan distribuera en Azure som ett alternativ till manuell installation av CUDA på en Windows Server-VM [datavetenskap virtuella](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Windows Server 2016 förinstallation NVIDIA CUDA drivrutiner, nätverksbibliotek CUDA djupa Neurala och andra verktyg.


| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>NV-serien - NVIDIA RUTNÄTET drivrutiner

Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV virtuella datorer. Installera endast drivrutinerna RUTNÄTET på NV virtuella Azure-datorer. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [RUTNÄTET 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RUTNÄTET 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |