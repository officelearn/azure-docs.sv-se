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
ms.openlocfilehash: 279f4cb1d8891f64fb01f4f8abb6b23a004c2b4d
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268919"
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-drivrutiner

NVIDIA CUDA-drivrutiner för NC-, NCv2-, NCv3-, ND-och NDv2-seriens virtuella datorer (tillval för NV-serien) stöds bara på de Linux-distributioner som anges i följande tabell. Information om CUDA-drivrutinen är aktuell vid tidpunkten för publiceringen. De senaste CUDA-drivrutinerna och operativ system som stöds finns på [NVIDIA](https://developer.nvidia.com/cuda-zone) -webbplatsen. Se till att du installerar eller uppgraderar till de senaste CUDA-drivrutinerna för distributionen. 

> [!TIP]
> Som ett alternativ till manuell CUDA av driv rutins installation på en virtuell Linux-dator kan du distribuera en Azure [data science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -avbildning. DSVM-utgåvorna för Ubuntu 16,04 LTS eller CentOS 7,4 förinstallera NVIDIA CUDA-drivrutiner, CUDA djup neurala nätverks bibliotek och andra verktyg.


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft distribuerar NVIDIA GRID-drivrutiner installations program för virtuella datorer i NV-och NVv3-serien som används som virtuella arbets stationer eller för virtuella program. Installera endast dessa RUTNÄTs driv rutiner på virtuella datorer med Azure NV, endast på de operativ system som anges i följande tabell. Dessa driv rutiner inkluderar licensiering för virtuella GPU-program i Azure i Azure. Du behöver inte konfigurera en program licens Server för NVIDIA vGPU.

| Distribution | Drivrutin |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 till 7,7<br/><br/>CentOS-baserad 7,0 till 7,7<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA-rutnät 10,1, driv rutins gren R440|

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
