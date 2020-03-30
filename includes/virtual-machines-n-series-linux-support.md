---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5dccdb6c357635e78b076b1560bf6c0c62c03753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135042"
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-drivrutiner

NVIDIA CUDA-drivrutiner för NC, NCv2, NCv3, ND och NDv2-seriens virtuella datorer (valfritt för NV-serien) stöds endast på Linux-distributionerna som anges i följande tabell. CUDA-drivrutinsinformationen är aktuell vid publiceringstillfället. För de senaste CUDA-drivrutinerna, besök [NVIDIA:s](https://developer.nvidia.com/cuda-zone) webbplats. Se till att du installerar eller uppgraderar till de senaste CUDA-drivrutinerna för distributionen. 

> [!TIP]
> Som ett alternativ till manuell CUDA-drivrutinsinstallation på en Virtuell Linux-dator kan du distribuera en [virtuell Azure Data Science-datoravbildning.](../articles/machine-learning/data-science-virtual-machine/overview.md) DSVM-utgåvorna för Ubuntu 16.04 LTS eller CentOS 7.4 förinstallerar NVIDIA CUDA-drivrutiner, CUDA Deep Neural Network Library och andra verktyg.

| Distribution | Drivrutin |
| --- | -- | 
| Ubuntu 16,04 LTS, 18,04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> CentOS-baserade 7,3, 7,4, 7,5, 7,6, CentOS-baserade 7,4 HPC | NVIDIA CUDA 10.1, förargrena sig R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft omfördelar NVIDIA GRID-drivrutinsinstallatörer för virtuella nv- och NVv3-serie virtuella datorer som används som virtuella arbetsstationer eller för virtuella program. Installera endast dessa GRID-drivrutiner på virtuella Azure NV-datorer, endast på de operativsystem som anges i följande tabell. Dessa drivrutiner inkluderar licensiering för GRID Virtual GPU Software i Azure. Du behöver inte konfigurera en LICENSserver för NVIDIA vGPU-programvara.

| Distribution | Drivrutin |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 till 7,6<br/><br/>CentOS-baserade 7,0 till 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10.1, drivrutinsgren R440|

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
