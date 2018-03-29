---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5dc0636fa4bfede460b1c8cd8bdab5628a110578
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2, NCv3 och ND-serien - NVIDIA CUDA drivrutiner

CUDA drivrutinsinformation i följande tabell är aktuella vid tiden för publikationen. De senaste drivrutinerna för CUDA, finns det [NVIDIA](https://developer.nvidia.com/cuda-zone) webbplats. Se till att du installerar eller uppgraderar till de senaste drivrutinerna för CUDA för din distribution. 

> [!TIP]
> Du kan distribuera en Azure som ett alternativ till manuell installation av CUDA på en Linux-VM [datavetenskap virtuella](../articles/machine-learning/data-science-virtual-machine/overview.md) bild. DSVM utgåvor för Ubuntu 16.04 LTS eller CentOS 7.4 förinstallation NVIDIA CUDA drivrutiner, nätverksbibliotek CUDA djupa Neurala och andra verktyg.

| Distribution | Drivrutin |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/> CentOS 7.3 eller 7.4, CentOS-baserade 7.4 HPC | NVIDIA CUDA 9.1, drivrutinen gren R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>NV-serien - NVIDIA RUTNÄTET drivrutiner

Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV virtuella datorer. Installera endast drivrutinerna RUTNÄTET på NV virtuella Azure-datorer. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.

| Distribution | Drivrutin |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-baserad 7.3 | NVIDIA RUTNÄTET 5.2, drivrutinen gren R384|



> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
