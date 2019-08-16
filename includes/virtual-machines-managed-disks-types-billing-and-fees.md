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
ms.openlocfilehash: 6f6d373d6dd21f3fd04ca434678ec798146a0ffa
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512694"
---
**Utgående data överföringar**: [Utgående data överföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures Data Center) debiteras för bandbredds användning.

**Transaktioner**: Du faktureras för antalet transaktioner som du utför på en standard-hanterad disk. För standard-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB. För standard-HDD betraktas varje IO-åtgärd som en enskild transaktion, oavsett I/O-storlek.

Detaljerad information om priser för Managed Disks, inklusive transaktionskostnader, finns [Managed disks prissättning](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Avgift för VM-reservation på Ultra disk

Virtuella Azure-datorer har möjlighet att ange om de är kompatibla med Ultra disks. En Ultra disk-kompatibel virtuell dator allokerar dedikerad bandbredds kapacitet mellan den virtuella Compute VM-instansen och block lagrings enheten för att optimera prestandan och minska svars tiden. Genom att lägga till den här funktionen på den virtuella datorn får du en reservations avgift som bara införs om du har aktiverat Ultra disk-kapacitet på den virtuella datorn utan att koppla en Ultra disk till den. När en Ultra disk är ansluten till den Ultra disk-kompatibla virtuella datorn gäller inte avgiften. Den här avgiften är per vCPU etablerad på den virtuella datorn.

Se pris [sidan för Azure-diskar](https://azure.microsoft.com/pricing/details/managed-disks/) för pris information för Ultra disk.