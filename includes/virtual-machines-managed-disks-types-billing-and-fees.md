---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "73413014"
---
**Utgående data överföringar**: [utgående data överföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures Data Center) debiteras för bandbredds användning.

**Transaktioner**: du debiteras för antalet transaktioner som du utför på en standard-hanterad disk. För standard-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB. För standard-HDD betraktas varje IO-åtgärd som en enskild transaktion, oavsett I/O-storlek.

Detaljerad information om priser för Managed Disks, inklusive transaktionskostnader, finns [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Avgift för VM-reservation på Ultra disk

Virtuella Azure-datorer har möjlighet att ange om de är kompatibla med Ultra disks. En Ultra disk-kompatibel virtuell dator allokerar dedikerad bandbredds kapacitet mellan den virtuella Compute VM-instansen och block lagrings enheten för att optimera prestandan och minska svars tiden. Genom att lägga till den här funktionen på den virtuella datorn får du en reservations avgift som bara införs om du har aktiverat Ultra disk-kapacitet på den virtuella datorn utan att koppla en Ultra disk till den. När en Ultra disk är ansluten till den Ultra disk-kompatibla virtuella datorn gäller inte avgiften. Den här avgiften är per vCPU etablerad på den virtuella datorn. 

> [!Note]
> För [begränsade KÄRN VM-storlekar](../articles/virtual-machines/linux/constrained-vcpu.md)baseras reservations avgiften på det faktiska antalet virtuella processorer och inte begränsade kärnor. Reservations avgiften för Standard_E32-8s_v3 baseras på 32 kärnor. 

Se pris [sidan för Azure-diskar](https://azure.microsoft.com/pricing/details/managed-disks/) för pris information för Ultra disk.

### <a name="azure-disk-reservation"></a>Reservation av Azure-disk

Disk reservation är möjligheten att köpa ett års disk lagring i förväg till en rabatt, vilket minskar den totala kostnaden. När du köper en disk reservation väljer du en speciell disk-SKU i en mål region, till exempel 10 P30 (1TiB) Premium-SSD i regionen USA, östra 2 för ett år. Reservations upplevelsen liknar reserverade instanser av virtuella datorer. Du kan paketera VM-och disk reservationer för att maximera dina besparingar. För närvarande erbjuder Azure disks-reservation en års åtagande plan för Premium SSD-SKU: er från P30 (1TiB) till P80 (32 TiB) i alla produktions regioner. Mer information om priser för reserverade diskar finns på [sidan med priser för Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/).