---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: eef61421de2a87750caac228d12888421f7442a8
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51716282"
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
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/>CentOS-baserade 7.3 eller 7.4 | NVIDIA GRID 6.2, drivrutinen gren R390|

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
