---
title: 'Azure Premium Storage: Design för prestanda på virtuella Linux-datorer | Microsoft-dokument'
description: Utforma högpresterande program med Azure premium SSD-hanterade diskar. Premium Storage erbjuder diskstöd med låg latens med hög prestanda för I/O-intensiva arbetsbelastningar som körs på virtuella Azure-datorer.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267149"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure premium storage: design för hög prestanda
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Ibland, vad som verkar vara en disk prestanda problem är faktiskt en flaskhals i nätverket. I dessa situationer bör du optimera [nätverksprestandan](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Om du funderar på att jämföra din disk, se vår artikel om [Benchmarking en disk](disks-benchmarks.md).
>
> Om den virtuella datorn stöder accelererade nätverk bör du se till att den är aktiverad. Om den inte är aktiverad kan du aktivera den på redan distribuerade virtuella datorer på både [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Innan du börjar, om du inte har använt Premium Storage tidigare, läser du först [välj en Azure-disktyp för virtuella IaaS-datorer](disks-types.md) och [skalbarhetsmål för premium-sidblobblagringskonton](../../storage/blobs/scalability-targets-premium-page-blobs.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Om du funderar på att jämföra din disk, se vår artikel om [Benchmarking en disk](disks-benchmarks.md).

Läs mer om tillgängliga disktyper: [Välj en disktyp](disks-types.md)  

För SQL Server-användare läser du artiklar om metodtips för prestanda för SQL Server:

* [Metodtips för prestanda för SQL Server i virtuella Azure-datorer](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage ger högsta prestanda för SQL Server i Azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)