---
title: 'Azure Premium Storage: Design för prestanda på virtuella Windows-datorer | Microsoft Docs'
description: Utforma högpresterande program som använder Azure Premium Storage. Premium Storage erbjuder stöd för diskar med höga prestanda och låg latens för I/O-intensiva arbetsbelastningar som körs på Azure Virtual Machines.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c6de3144a87a5bfad38e1b33148f292b26c0f181
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658238"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Ibland är något som verkar vara problem med prestandan disk faktiskt en flaskhalsar i nätverket. I sådana situationer bör du optimera din [nätverksprestanda](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Om du vill jämföra disken är läsa vår artikel om [prestandamätningar en disk](disks-benchmarks.md).
>
> Om den virtuella datorn har stöd för accelererat nätverk, bör du kontrollera att den är aktiverad. Om den inte har aktiverats måste du aktivera det på redan distribuerade virtuella datorerna på både [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Innan du börjar, om du är nybörjare till Premium Storage, läsa den [väljer du en Azure-diskar för virtuella IaaS-datorer](disks-types.md) och [Azure Storage skalbarhets- och prestandamål för storage-konton](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
