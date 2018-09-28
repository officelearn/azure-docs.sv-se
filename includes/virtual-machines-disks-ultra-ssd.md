---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bb9a2a884439b00f52adfa9b7c1010a4610a77f7
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401600"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Ultra SSD (förhandsversion) Managed Disks för Azure-datorbelastningar

Azure Ultra SSD (förhandsversion) ger hög genomströmning, hög IOPS och konsekvent låg latens disklagring för virtuella Azure IaaS-datorer. Det här nya erbjudandet ger upp på rad prestanda på samma tillgänglighet nivåer som våra befintliga diskar-erbjudanden. Ytterligare fördelarna med Ultra SSD är möjligheten att ändra dynamiskt prestanda för disken tillsammans med dina arbetsbelastningar utan att behöva starta om dina virtuella datorer. Ultra SSD är lämpligt för dataintensiva arbetsbelastningar som SAP HANA, översta databaser och transaktionen tunga arbetsbelastningar.

## <a name="ultra-ssd-features"></a>Ultra SSD-funktioner

**Hanterade diskar**: Ultra SSD: er är endast tillgängliga som Managed Disks. Ultra SSD: er kan inte distribueras som en ohanterad Disk eller en Sidblob. När du skapar en hanterad Disk måste ange du disk-sku som UltraSSD_LRS och anger storleken på disken, IOPS, dataflöde som du behöver och Azure skapar och hanterar disken åt dig.  

**Virtuella datorer**: Ultra SSD: er är utformade att fungera med alla Premium SSD aktiverat Azure VM SKU: er, men när förhandsversionen VM-storlekar begränsas till ES/DS v3 VM-instanser.

**Dynamisk Prestandakonfiguration**: Ultra SSD-enheter kan du ändra dynamiskt prestanda för disken tillsammans med dina arbetsbelastningsbehov (IOPS och dataflöde) utan att behöva starta om dina virtuella datorer.

## <a name="scalability-and-performance-targets"></a>Mål för skalbarhet och prestanda

När du etablerar ett Ultra SSD har du möjlighet att konfigurera oberoende kapacitet och prestanda för disken. Ultra SSD kommer flera fasta storlekar från 4 GiB upp till 64 TiB och använder en modell för flexibla prestanda konfiguration där du kan konfigurera oberoende IOPS och dataflöden. Ultra SSD: er kan bara användas som datadiskar. Vi rekommenderar att du använder Premium SSD som OS-diskar.

Några nyckelfunktioner i Ultra SSD är:

- Diskkapacitet: Ultra SSD erbjuder en mängd olika diskstorlekar från 4 GiB upp till 64 TiB.
- IOPs per disk: Ultra SSD: er stöder IOPS-gränserna för 300 IOPS/GiB, upp till högst 160 kB IOPS per disk. Kontrollera att den valda Disk-IOPS är mindre än VM IOPS för att uppnå IOPS som du etablerade. Minsta IOPS-disken är 100 IOPS.
- Diskdataflöde: Med Ultra SSD dataflödesgräns av en enskild disk är 256 KiB/s för var och en etablerad IOPS, upp till högst 2000 Mbit/s per disk (där Mbit/s = 10 ^ 6 byte per sekund). Det minsta diskgenomflödet är 1 MiB.

I följande tabell sammanfattas de olika konfigurationerna som stöds för olika diskstorlekar:  

### <a name="ultra-ssd-managed-disk-offerings"></a>Erbjudanden för Ultra SSD Managed Disks

|Diskstorlek (GiB)  |IOPS Caps  |Taket för dataflöde (Mbit/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19 200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1 024 – 65 536 (storlekar i det här intervallet som ökar i steg om 1 TiB)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder Ultra SSD, gäller följande för debitering:

- Hanterade diskens storlek
- Hanterad Disk etablerad IOPS
- Dataflöde för hanterad Disk
- Ultra SSD VM reservation avgift

### <a name="managed-disk-size"></a>Hanterade diskens storlek

Hanterade diskar faktureras med storlekarna som är etablerade. Azure mappar den etablerade storleken (avrundad uppåt) till närmaste diskstorlekserbjudande. Se tabellen i skalbarhets- och prestandamål ovan för information om diskstorlekar som erbjuds. Varje disk som mappar till en etablerad diskstorleken som stöds och därefter faktureras per timme. Om du har etablerat en 200 GiB Ultra SSD-Disk och tar bort den efter 20 timmar, det mappas till disk storlek erbjudandet om 256 GB och du debiteras för 256 GB för 20 timmar. Det här är oavsett mängden faktiska data som skrivs till disken.

### <a name="managed-disk-provisioned-iops"></a>Hanterad Disk etablerad IOPS

IOPS är antalet begäranden som programmet skickar till diskar i en sekund. En i/o-åtgärd kan vara sekventiella eller slumpmässiga, läser eller skriver. Etablerad IOPS, t.ex. diskstorlek faktureras per timme. Se tabellen i skalbarhets- och prestandamål ovan för information om den disk som erbjuds av IOPS.

### <a name="managed-disk-provisioned-throughput"></a>Dataflöde för hanterad Disk

Dataflödet är mängden data som ditt program skickar till diskar i ett visst intervall, mätt i byte per sekund. Om ditt program fungerar stora indata/utdata-åtgärder, kräver hög genomströmning.  

Det finns en relation mellan dataflöde och IOPS, enligt följande formel: IOPS x i/o-storlek = dataflöde

Det är därför viktigt att fastställa optimal dataflöde och IOPS-värden som krävs för ditt program. När du försöker optimera en hämtar den andra också påverkas. Vi rekommenderar att från och med en genomströmning som motsvarar 16 KiB i/o-storlek och justera eventuellt större dataflöde.

Se tabellen i skalbarhets- och prestandamål ovan för information om stöds diskgenomflödet på Ultra SSD. Det etablerade dataflödet debiteras per timme per Mbit/s etablerade som diskens storlek och IOPS.

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM reservation avgift

Introducerar vi en funktion på den virtuella datorn som anger den virtuella datorn är Ultra SSD som är kompatibla. Ett Ultra SSD kompatibel virtuell dator allokerar dedikerad bandbreddskapacitet mellan beräkningsinstans för virtuell dator och blockera lagringsskalningsenhet att optimera prestanda och minska svarstiden. Lägger till den här funktionen på VM-resultatet reservation kostar pengar som endast gäller om du har aktiverat Ultra SSD-kapaciteten på den virtuella datorn utan att koppla ett Ultra SSD-disk till den. När ett Ultra SSD-disken är ansluten till de Ultra SSD kompatibla virtuella datorer, skulle inte den här avgiften tillämpas. Denna kostnad är per virtuell processor har etablerats på den virtuella datorn.

Referera till den [Azure Disks sidan med priser](https://azure.microsoft.com/pricing/details/managed-disks/) pris för nya Ultra SSD-diskar information som är tillgängliga i en begränsad förhandsversion.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Ultra SSD förhandsversion omfång och begränsningar

Förhandsversionen Ultra SSD-diskar:

- Inledningsvis stöds i östra USA 2 i en enda Tillgänglighetszon  
- Kan bara användas med Tillgänglighetszoner (Tillgänglighetsuppsättningar och enkel VM-distributioner utanför zoner kommer inte har möjlighet att koppla ett Ultra SSD-Disk)
- Stöds endast på ES/DS v3 virtuella datorer
- Finns bara tillgängliga i datadiskar och endast stöd för 4k som fysisk sektorstorlek  
- Kan bara skapas som tomma diskar  
- För närvarande kan endast distribueras med hjälp av Resource Manager-mallar, CLI och SDK för Python.
- Kommer inte (ännu) support disk ögonblicksbilder, avbildningar av Virtuella datorer, Tillgänglighetsuppsättningar, VM-Skalningsuppsättningar och Azure Disk Encryption.
- Inte (ännu) stöd för integrering med Azure Backup eller Azure Site Recovery.
- Precis som med [de flesta förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), den här funktionen ska inte användas för produktionsarbetsbelastningar fram till allmän tillgänglighet (GA).