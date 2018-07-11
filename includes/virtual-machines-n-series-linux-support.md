---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1813367a2d143f75fb51a3160dd00219c709c57b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935804"
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-drivrutiner

NVIDIA CUDA-drivrutiner för NC, NCv2, NCv3 och ND-serien virtuella datorer (valfritt för NV-serien) stöds endast på Linux-distributioner som visas i följande tabell. CUDA drivrutinsinformation är aktuella vid tidpunkten för publiceringen. De senaste drivrutinerna för CUDA, finns det [NVIDIA](https://developer.nvidia.com/cuda-zone) webbplats. Se till att du installerar eller uppgraderar till de senaste drivrutinerna för CUDA för din distribution. 

> [!TIP]
> Som ett alternativ till manuell installation av CUDA på en Linux VM kan du distribuera en Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM-versioner för Ubuntu 16.04 LTS och CentOS 7.4 förinstallation NVIDIA CUDA-drivrutiner, CUDA djupa Neurala nätverksbibliotek och andra verktyg.

| Distribution | Drivrutin |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/> CentOS-baserade 7.3 eller 7.4, CentOS-baserade 7.4 HPC | NVIDIA CUDA 9.1, drivrutinen gren R390 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID drivrutinen installationsprogram för NV-serien virtuella datorer som används som virtuella arbetsstationer eller till virtuella program. Installera endast de här drivrutinerna RUTNÄTET på NV virtuella datorer i Azure, endast för distributioner som visas i följande tabell. Dessa faktorer är licensiering för GRID virtuella GPU-programvara i Azure.

| Distribution | Drivrutin |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/>CentOS-baserade 7.3 eller 7.4 | NVIDIA GRID 6.2, drivrutinen gren R390|



> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
