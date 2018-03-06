---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2, NCv3 och ND-serien - NVIDIA CUDA drivrutiner
| Distribution | Drivrutin |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 eller 7.4<br/><br/> CentOS 7.3 eller 7.4 | NVIDIA CUDA 9.1, drivrutinen gren R390 |

> [!IMPORTANT]
> Se till att du installerar eller uppgraderar till de senaste drivrutinerna för CUDA för din distribution. Drivrutiner som är äldre än version R390 kan ha problem med uppdaterade Linux kärnor.
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV-serien - NVIDIA RUTNÄTET drivrutiner

| Distribution | Drivrutin |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-baserad 7.3 | NVIDIA RUTNÄTET 5.2, drivrutinen gren R384|

> [!NOTE]
> Microsoft distribuerar NVIDIA RUTNÄTET drivrutinen installationsprogram för NV virtuella datorer. Installera endast drivrutinerna RUTNÄTET på NV virtuella Azure-datorer. De här drivrutinerna inkluderar licensiering för RUTNÄTET virtuella GPU-programvara i Azure.
>

> [!WARNING] 
> Installation av tredjepartsprogramvara på Red Hat-produkter kan påverka supportvillkoren för Red Hat. Läs [Knowledgebase-artikeln om Red Hat](https://access.redhat.com/articles/1067).
>
