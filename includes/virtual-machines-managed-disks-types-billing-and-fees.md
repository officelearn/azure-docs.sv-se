---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73413014"
---
**Utgående dataöverföringar:** [Utgående dataöverföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azure-datacenter) medför fakturering för bandbreddsanvändning.

**Transaktioner**: Du debiteras för antalet transaktioner som du utför på en vanlig hanterad disk. För standard-SSD-enheter betraktas varje I/O-åtgärd som är mindre än eller lika med 256 KiB dataflöde som en enda I/O-åtgärd. I/O-operationer större än 256 KiB dataflöde anses vara flera I/Os av storlek 256 KiB. För standard-hårddiskar betraktas varje I/O-åtgärd som en enda transaktion, oavsett I/O-storlek.

Detaljerad information om priser för hanterade diskar, inklusive transaktionskostnader, finns i [Priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Bokningsavgift för virtuell dator med Ultra disk

Virtuella Azure-datorer har möjlighet att ange om de är kompatibla med ultradiskar. En ultradiskkompatibel virtuell dator allokerar dedikerad bandbreddskapacitet mellan beräknings-VM-instansen och blocklagringsskalenheten för att optimera prestanda och minska svarstiden. Genom att lägga till den här funktionen på den virtuella datorn resulterar det i en reservationsavgift som endast införs om du har aktiverat ultradiskkapacitet på den virtuella datorn utan att ansluta en ultradisk till den. När en ultradisk är ansluten till den ultradiskkompatibla virtuella datorn tillämpas inte den här laddningen. Den här avgiften är per vCPU som etablerats på den virtuella datorn. 

> [!Note]
> För [begränsade kärnstorlekar](../articles/virtual-machines/linux/constrained-vcpu.md)för virtuella datorer baseras reservationsavgiften på det faktiska antalet virtuella processorer och inte på de begränsade kärnorna. För Standard_E32-8s_v3 baseras bokningsavgiften på 32 kärnor. 

Se prissidan för [Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) för prisinformation för ultradiskar.

### <a name="azure-disk-reservation"></a>Reservation för Azure-disk

Diskbokning är möjligheten att köpa ett års disklagring i förväg med rabatt, vilket minskar din totala kostnad. När du köper en diskreservation väljer du en specifik disk-SKU i en målregion, till exempel 10 P30 (1TiB) premium-SSD:er i östra USA 2-regionen under en ettårsperiod. Reservationsupplevelsen liknar reserverade VM-instanser (Virtual Machine). Du kan paketera VM- och diskreservationer för att maximera dina besparingar. För tillfället erbjuder Azure Disks Reservation ett års åtagandeplan för premium SSD SKU:er från P30 (1TiB) till P80 (32 TiB) i alla produktionsregioner. Mer information om prissättningen för reserverade diskar finns på [prissidan för Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/).