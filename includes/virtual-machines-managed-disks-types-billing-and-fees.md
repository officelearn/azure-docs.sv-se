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
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968621"
---
**Utgående data överföringar**: [Utgående data överföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures Data Center) debiteras för bandbredds användning.

**Transaktioner**: Du faktureras för antalet transaktioner som du utför på en standard-hanterad disk. För standard-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB. För standard-HDD betraktas varje IO-åtgärd som en enskild transaktion, oavsett I/O-storlek.

Detaljerad information om priser för Managed Disks, inklusive transaktionskostnader, finns [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Avgift för VM-reservation på Ultra disk

Virtuella Azure-datorer har möjlighet att ange om de är kompatibla med Ultra disks. En Ultra disk-kompatibel virtuell dator allokerar dedikerad bandbredds kapacitet mellan den virtuella Compute VM-instansen och block lagrings enheten för att optimera prestandan och minska svars tiden. Genom att lägga till den här funktionen på den virtuella datorn får du en reservations avgift som bara införs om du har aktiverat Ultra disk-kapacitet på den virtuella datorn utan att koppla en Ultra disk till den. När en Ultra disk är ansluten till den Ultra disk-kompatibla virtuella datorn gäller inte avgiften. Den här avgiften är per vCPU etablerad på den virtuella datorn. 

> [!Note]
> För [begränsade KÄRN VM-storlekar](../articles/virtual-machines/linux/constrained-vcpu.md)baseras reservations avgiften på det faktiska antalet virtuella processorer och inte begränsade kärnor. Reservations avgiften för Standard_E32-8s_v3 baseras på 32 kärnor. 

Se pris [sidan för Azure-diskar](https://azure.microsoft.com/pricing/details/managed-disks/) för pris information för Ultra disk.
