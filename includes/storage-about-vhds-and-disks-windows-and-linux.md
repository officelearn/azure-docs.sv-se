---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa740cfb203f50dc97a06359774dae367a20252b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148030"
---
## <a name="about-vhds"></a>Om virtuella hårddiskar

De virtuella hårddiskarna i Azure är .vhd-filer som lagras som sidblobar i standard- eller premium-lagringskonton i Azure. Mer information om sidblobar finns [Understanding block blobs and page blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/) (Förstå blockblobar och sidblobar). Mer information om premium-lagring finns i [High-performance premium storage and Azure VMs](../articles/virtual-machines/windows/premium-storage.md) (Premium-lagring och virtuella Azure-datorer med hög prestanda).

Azure stöder det fasta formatet för virtuella hårddiskar (VHD). Det fasta format lägger ut den logiska disken linjärt i filen så att diskförskjutning X lagras vid blob-förskjutning X. En liten sidfot i slutet av bloben beskriver den virtuella hårddiskens egenskaper. Fast format avfall utrymme ofta eftersom de flesta diskar har stora oanvända intervall i dem. Men Azure lagrar .vhd-filer i ett begränsat format så att du kan dra nytta av fördelarna med både fasta och dynamiska diskar på samma gång. Mer information finns i [komma igång med virtuella hårddiskar](https://technet.microsoft.com/library/dd979539.aspx).

Alla VHD-filer i Azure som du vill använda som källa för att skapa diskar eller avbildningar är skrivskyddade, utom VHD-filer har överförts eller kopieras till Azure-lagring av användaren (som kan vara skrivskyddade eller skrivskyddad). När du skapar en disk eller avbildning gör Azure kopior av källan VHD-filer. Dessa kopior kan vara skrivskyddade eller läs och skriv, beroende på hur du använder den virtuella hårddisken.

När du skapar en virtuell dator från en avbildning skapar Azure en disk för den virtuella datorn som är en kopia av .vhd-filen som används som källa. För att skydda dig mot att du råkar radera dem av misstag placerar Azure en livslängd på alla .vhd-filer som används som källor för att skapa en avbildning, en operativsystemdisk eller en datadisk.

Innan du kan ta bort en .vhd-fil som används som källa måste du ta bort livslängden genom att ta bort disken eller avbildningen. Om du vill ta bort en .vhd-fil som används av en virtuell dator som en operativsystemdisk kan du ta bort den virtuella datorn, operativsystemdisken, och .vhd-filen som används som källa på en gång genom att ta bort den virtuella datorn och alla associerade diskar. Men det krävs att du genomför ett antal steg i en viss ordning för att ta bort en .vhd-fil som är en källa för en datadisk. Först kopplar du bort disken från den virtuella datorn, sedan tar du bort disken och sist tar du bort .vhd-filen.

> [!WARNING]
> Om du tar bort en .vhd-fil som används som källa från lagringen eller tar bort ditt lagringskonto kan Microsoft inte återställa dessa data åt dig.

## <a name="types-of-disks"></a>Typer av diskar

Azure-diskar har en tillförlitlighet på 99,999 %. Azure-diskar har konsekvent levereras tillförlitlighet i företagsklass, med en branschledande noll % Annualized Felfrekvens.

Det finns tre prestandanivåer för lagring som du kan välja mellan när du skapar diskar – Premium SSD-diskar, Standard SSD och HDD standardlagring. Dessutom finns två typer av diskar, ohanterade och hanterade.

### <a name="standard-hdd-disks"></a>Standard HDD-diskar

HDD-standarddiskar backas upp av hårddiskar och levererar kostnadseffektiv lagring. Standard HDD-lagring kan replikeras lokalt i ett datacenter eller vara geo-redundant med primära och sekundära datacenter. Mer information om storage-replikering finns i [Azure Storage-replikering](../articles/storage/common/storage-redundancy.md).

Mer information om hur du använder Standard HDD-diskar finns i [Standard Storage och diskar](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Standard SSD-diskar

Standard SSD-diskar är utformade för att hantera samma typ av arbetsbelastningar som Standard HDD-diskar, men erbjuder mer konsekventa prestanda och tillförlitlighet än HDD. Standard SSD-diskar kombinera element Premium SSD-diskar och Standard HDD-diskar i formulär en kostnadseffektiv lösning som bäst passar för program, t.ex. webbservrar som inte behöver hög IOPS på diskar. Tillgängliga Standard SSD-diskar är det rekommendera distributionsalternativet för de flesta arbetsbelastningar. Standard SSD-diskar är tillgängliga som Managed Disks i alla regioner, men är för närvarande endast tillgänglig med återhämtningstyp lokalt redundant lagring (LRS).

### <a name="premium-ssd-disks"></a>Premium SSD-diskar

Premium SSD-diskar backas upp av SSD-enheter och levererar högpresterande, låg latens disksupport för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Vanligtvis kan du använda Premium SSD-diskar med storlekar som inkluderar en ”s” i dataseriens namn. Exempelvis finns Dv3-serien och Dsv3-serien, Dsv3-serien kan användas med Premium SSD-diskar.  Mer information finns i [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Ohanterade diskar

Ohanterade diskar är den traditionella typen av diskar som används av virtuella datorer. Med dessa diskar, skapa ditt eget lagringskonto och ange det lagringskontot när du skapar disken. Kontrollera att du inte lägger till för många diskar i samma lagringskonto eftersom du kan överskrida den [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) för storage-konto (20 000 IOPS, till exempel), vilket resulterar i de virtuella datorerna begränsas. Du måste lista ut hur du optimerar användningen av en eller flera lagringskonton för att få ut den bästa prestandan av dina virtuella datorer med ohanterade diskar.

### <a name="managed-disks"></a>Hanterade diskar

Managed Disks hanterar skapandet/hanterandet av lagringskontot i bakgrunden och säkerställer att du inte behöver bekymra dig om lagringskontots skalbarhetsgränser. Du anger bara diskens storlek och prestandanivå (Standard/Premium) så skapar och hanterar Azure disken åt dig. Om du lägger till diskar eller skalar upp eller ned den virtuella datorn behöver du inte oroa dig om lagringsutrymmet som används.

Du kan även hantera dina anpassade avbildningar i ett lagringskonto per Azure-region och använda dem för att skapa hundratals virtuella datorer i samma prenumeration. Mer information om Managed Disks finns i [översikten över Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Vi rekommenderar att du använder Azure Managed Disks för nya virtuella datorer och att du konverterar tidigare ohanterade diskar till hanterade diskar för att dra nytta av de många funktionerna som finns i Managed Disks.

### <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av Standard HDD, SSD som Standard och Premium SSD för ohanterade och hanterade diskar för att avgöra vad du ska använda. Storlekar som är markerad med en asterisk finns för närvarande i förhandsversion.

|    | Azure Premium-disk |Azure Standard SSD-Disk | Azure Standard HDD-Disk
|--- | ------------------ | ------------------------------- | -----------------------
| Disktyp | Solid State-hårddiskar (SSD) | Solid State-hårddiskar (SSD) | Hårddiskar (HDD)  
| Översikt  | SSD-baserad högpresterande disksupport med låg fördröjning för virtuella datorer som kör I/O-intensiva arbetsbelastningar eller är värd för verksamhetskritisk produktionsmiljö |Mer konsekventa prestanda och tillförlitlighet än HDD. Optimerad för arbetsbelastningar med låg IOPS| HDD-baserad kostnadseffektiv disk för lågfrekvent åtkomst
| Scenario  | Produktion och prestandakänsliga arbetsbelastningar |Webbservrar, används företagsprogram och utveckling/testning| Säkerhetskopiering, icke-kritiska, lågfrekvent åtkomst
| Diskstorlek | P4: 32 giB (endast hanterade diskar)<br>P6: 64 giB (endast hanterade diskar)<br>P10: 128 GiB<br>P15: 256 GB (endast hanterade diskar)<br>P20: 512 GiB<br>P30: 1024 giB<br>P40: 2 048 giB<br>P50: 4095 giB<br>P60: 8 192 giB * (8 TiB)<br>P70: 16 384 giB * (16 TiB)<br>P80: 32 767 giB * (32 TiB) |Hanterade diskar endast:<br>E4: 32 GiB<br>E6: 64 GiB<br>E10: 128 GiB<br>E15: 256 GB<br>E20: 512 GiB<br>E30: 1024 giB<br>E40: 2 048 giB<br>E50: 4 095 giB<br>E60: 8 192 giB * (8 TiB)<br>E70: 16 384 giB * (16 TiB)<br> E80: 32 767 giB * (32 TiB) | Ohanterade diskar: 1 giB – 4 TiB (4095 GiB) <br><br>Hanterade diskar:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GB <br>S20: 512 GiB <br>S30: 1024 giB <br>S40: 2 048 giB<br>S50: 4 095 giB<br>S60: 8 192 giB * (8 TiB)<br>S70: 16 384 giB * (16 TiB)<br>S80: 32 767 giB * (32 TiB)
| Maxdataflöde per disk | P4: 25 MiB/s<br> P6: 50 MiB/s<br> P10: 100 MiB/s<br> P15: 125 MiB/s<br> P20: 150 MiB/s<br> P30: 200 MiB/s<br> P40-P50: 250 MiB/s<br> P60: 480 MiB/s *<br> P70-P80: 750 MiB/s * | E10-E50: Upp till 60 MiB/s<br> E60: Upp till 300 MiB/s *<br> E70-E80: 500 MiB/s *| S4 - S50: Upt o 60 MiB/s<br> S60: Upp till 300 MiB/s *<br> S70-S80: Upp till 500 MiB/s *
| Max-IOPS per disk | P4: 120 IOPS<br> P6: 240 IOPS<br> P10: 500 IOPS<br> P15: 1100 IOPS<br> P20: 2300 IOPS<br> P30: 5000 IOPS<br> P40-P50: 7500 IOPS<br> P60: 12 500 IOPS *<br> P70: 15 000 IOPS *<br> P80: 20 000 IOPS * | E10-E50: Upp till 500 IOPS<br> E60: Upp till 1300 IOPS *<br> E70-E80: Upp till 2000 IOPS * | S4-S50: Upp till 500 IOPS<br> S60: Upp till 1300 IOPS *<br> S70-S80: Upp till 2000 IOPS *
