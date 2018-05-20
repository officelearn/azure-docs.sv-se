---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9b36a8e90c20c8f0d08ca241e48e9306fb5b10f8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC, NCv2, NCv3 och ND-serien - drivrutiner för NVIDIA Tesla (CUDA)

Länkar för hämtning av drivrutiner i följande tabell är aktuella vid tiden för publikationen. De senaste drivrutinerna finns på [NVIDIA:s](http://www.nvidia.com/) webbplats.

> [!TIP]
> Du kan distribuera en Azure som ett alternativ till manuell installation av CUDA på en Windows Server-VM [datavetenskap virtuella](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Windows Server 2016 förinstallation NVIDIA CUDA drivrutiner, nätverksbibliotek CUDA djupa Neurala och andra verktyg.


| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>NV-serien - NVIDIA RUTNÄTET drivrutiner

Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV virtuella datorer. Installera endast drivrutinerna RUTNÄTET på NV virtuella Azure-datorer. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.

| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [RUTNÄTET 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [RUTNÄTET 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |