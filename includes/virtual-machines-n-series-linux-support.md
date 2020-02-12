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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135042"
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-drivrutiner

NVIDIA CUDA-drivrutiner för NC-, NCv2-, NCv3-, ND-och NDv2-seriens virtuella datorer (tillval för NV-serien) stöds bara på de Linux-distributioner som anges i följande tabell. Information om CUDA-drivrutinen är aktuell vid tidpunkten för publiceringen. De senaste CUDA-drivrutinerna finns på [NVIDIA](https://developer.nvidia.com/cuda-zone) -webbplatsen. Se till att du installerar eller uppgraderar till de senaste CUDA-drivrutinerna för distributionen. 

> [!TIP]
> Som ett alternativ till manuell CUDA av driv rutins installation på en virtuell Linux-dator kan du distribuera en Azure [data science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -avbildning. DSVM-utgåvorna för Ubuntu 16,04 LTS eller CentOS 7,4 förinstallera NVIDIA CUDA-drivrutiner, CUDA djup neurala nätverks bibliotek och andra verktyg.

| Distribution | Drivrutin |
| --- | -- | 
| Ubuntu 16,04 LTS, 18,04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> CentOS-baserad 7,3, 7,4, 7,5, 7,6, CentOS-baserad 7,4 HPC | NVIDIA CUDA 10,1, driv rutins gren R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID-drivrutiner installations program för virtuella datorer i NV-och NVv3-serien som används som virtuella arbets stationer eller för virtuella program. Installera endast dessa RUTNÄTs driv rutiner på virtuella datorer med Azure NV, endast på de operativ system som anges i följande tabell. Dessa driv rutiner inkluderar licensiering för virtuella GPU-program i Azure i Azure. Du behöver inte konfigurera en program licens Server för NVIDIA vGPU.

| Distribution | Drivrutin |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 till 7,6<br/><br/>CentOS-baserad 7,0 till 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA-rutnät 10,1, driv rutins gren R440|

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
