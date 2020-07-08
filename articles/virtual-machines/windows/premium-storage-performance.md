---
title: 'Azure Premium Storage: design för prestanda på virtuella Windows-datorer | Microsoft Docs'
description: Skapa högpresterande program med hjälp av Azure Premium SSD-hanterade diskar. Premium Storage erbjuder disk support med hög prestanda och låg latens för I/O-intensiva arbets belastningar som körs på Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 06ac680ef48d9602f6e7487eb9ebecee9f3ae080
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84017619"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium-lagring: design för hög prestanda
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Ibland är det som verkar vara ett disk prestanda problem i själva verket en nätverks Flask hals. I dessa fall bör du optimera [nätverks prestandan](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Om du vill mäta din disk kan du läsa vår artikel om att [mäta en disk](disks-benchmarks.md).
>
> Om din virtuella dator har stöd för accelererat nätverk bör du kontrol lera att den är aktive rad. Om den inte är aktive rad kan du aktivera den på redan distribuerade virtuella datorer på både [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Innan du börjar, om du är nybörjare på Premium Storage, läser du först [Välj en Azure-disktyp för virtuella datorer med IaaS](disks-types.md) och [skalbarhets mål för Premium Page Blob Storage-konton](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Om du vill mäta din disk kan du läsa vår artikel om att [mäta en disk](disks-benchmarks.md).

Läs mer om tillgängliga disk typer: [Välj en disk typ](disks-types.md)  

För SQL Server användare läser du artiklar om bästa metoder för prestanda för SQL Server:

* [Metod tips för prestanda för SQL Server i Azure Virtual Machines](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Azure Premium Storage ger högsta prestanda för SQL Server i Azure VM](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
