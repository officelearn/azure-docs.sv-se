---
title: 'Azure-Premium Storage: Design för prestanda på virtuella Windows-datorer | Microsoft Docs'
description: Skapa program med höga prestanda med Azure Premium Storage. Premium Storage erbjuder disk support med hög prestanda och låg latens för I/O-intensiva arbets belastningar som körs på Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 79bd41c08a566b55efb4a29084847848e001629f
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036043"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Ibland är det som verkar vara ett disk prestanda problem i själva verket en nätverks Flask hals. I dessa fall bör du optimera [nätverks prestandan](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Om du vill mäta din disk kan du läsa vår artikel om att [mäta en disk](disks-benchmarks.md).
>
> Om din virtuella dator har stöd för accelererat nätverk bör du kontrol lera att den är aktive rad. Om den inte är aktive rad kan du aktivera den på redan distribuerade virtuella datorer på både [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) och [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Innan du börjar, om du är nybörjare på Premium Storage, läser du först [Välj en Azure-disktyp för virtuella IaaS-datorer](disks-types.md) och [Azure Storage skalbarhets-och prestanda mål för lagrings konton](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Om du vill mäta din disk kan du läsa vår artikel om att [mäta en disk](disks-benchmarks.md).

Läs mer om tillgängliga disk typer: [Välj en disktyp](disks-types.md)  

För SQL Server användare läser du artiklar om bästa metoder för prestanda för SQL Server:

* [Metod tips för prestanda för SQL Server i Azure Virtual Machines](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage ger högsta prestanda för SQL Server i Azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)