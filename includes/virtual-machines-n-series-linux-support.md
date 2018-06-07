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
ms.openlocfilehash: 0264f92fa10bd503a2811ce40ee0b8d4edd5f3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669840"
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA drivrutiner

NVIDIA CUDA drivrutiner för NC, NCv2, NCv3 och ND-serien virtuella datorer (valfritt för NV-serien) stöds endast på Linux-distributioner som visas i följande tabell. CUDA drivrutinsinformation är aktuella vid tiden för publikationen. De senaste drivrutinerna för CUDA, finns det [NVIDIA](https://developer.nvidia.com/cuda-zone) webbplats. Se till att du installerar eller uppgraderar till de senaste drivrutinerna för CUDA för din distribution. 

> [!TIP]
> Du kan distribuera en Azure som ett alternativ till manuell installation av CUDA på en Linux-VM [datavetenskap virtuella](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM utgåvor för Ubuntu 16.04 LTS eller CentOS 7.4 förinstallation NVIDIA CUDA drivrutiner, nätverksbibliotek CUDA djupa Neurala och andra verktyg.

| Distribution | Drivrutin |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/> CentOS-baserade 7.3 eller 7.4, CentOS-baserade 7.4 HPC | NVIDIA CUDA 9.1, drivrutinen gren R390 |

### <a name="nvidia-grid-drivers"></a>NVIDIA RUTNÄTET drivrutiner

Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV-serien VMs användas som virtuella arbetsstationer eller för virtuella program. Installera endast drivrutinerna RUTNÄTET på Azure NV VMs endast för distributioner som visas i följande tabell. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.

| Distribution | Drivrutin |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/>CentOS 7.3 eller 7.4 | NVIDIA RUTNÄTET 6.0, drivrutinen gren R390|



> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
