---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: df7d8815eeb588e4e99041844642b6721e25dad7
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585842"
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-drivrutiner

NVIDIA CUDA-drivrutiner för NC, NCv2, NCv3, ND och NDv2-seriens virtuella datorer (valfritt för NV-serien) stöds endast på Linux-distributioner som visas i följande tabell. CUDA drivrutinsinformation är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna för CUDA, finns det [NVIDIA](https://developer.nvidia.com/cuda-zone) webbplats. Se till att du installerar eller uppgraderar till de senaste drivrutinerna för CUDA för din distribution. 

> [!TIP]
> Som ett alternativ till manuell installation av CUDA på en Linux VM kan du distribuera en Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Ubuntu 16.04 LTS och CentOS 7.4 förinstallation NVIDIA CUDA-drivrutiner, CUDA djupa Neurala nätverksbibliotek och andra verktyg.

| Distribution | Drivrutin |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/> CentOS-baserade 7.3 eller 7.4, CentOS-baserade 7.4 HPC | NVIDIA CUDA 10.0, drivrutinen gren R410 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID drivrutinen installationsprogram för NV och NVv2-serien virtuella datorer som används som virtuella arbetsstationer eller till virtuella program. Installera endast de här drivrutinerna RUTNÄTET på NV virtuella datorer i Azure, endast på operativsystemen som anges i följande tabell. Dessa faktorer är licensiering för GRID virtuella GPU-programvara i Azure. Du behöver inte konfigurera en licensserver för NVIDIA vGPU programvara.

| Distribution | Drivrutin |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/>CentOS-baserade 7.3 eller 7.4 | NVIDIA GRID 7, drivrutinen gren R410|

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
