---
title: ta med fil
description: ta med fil
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f64645db782b055e1c544f257149411f29fc99d7
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "34806323"
---
## <a name="about-vhds"></a>Om virtuella hårddiskar
De virtuella hårddiskarna i Azure är .vhd-filer som lagras som sidblobar i standard- eller premium-lagringskonton i Azure. Mer information om sidblobar finns [Understanding block blobs and page blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/) (Förstå blockblobar och sidblobar). Mer information om premium-lagring finns i [High-performance premium storage and Azure VMs](../articles/virtual-machines/windows/premium-storage.md) (Premium-lagring och virtuella Azure-datorer med hög prestanda).

Azure stöder det fasta formatet för virtuella hårddiskar (VHD). Det fasta format lägger ut den logiska disken linjärt i filen så att diskförskjutning X lagras vid blob-förskjutning X. En liten sidfot i slutet av bloben beskriver den virtuella hårddiskens egenskaper. Fast format avfall utrymme ofta eftersom de flesta diskar med stora oanvända intervall i. Men Azure lagrar .vhd-filer i ett begränsat format så att du kan dra nytta av fördelarna med både fasta och dynamiska diskar på samma gång. Mer information finns i [komma igång med virtuella hårddiskar](https://technet.microsoft.com/library/dd979539.aspx).

VHD-filer i Azure som du vill använda som källa för att skapa diskar eller avbildningar är skrivskyddade, utom VHD-filer har överförts eller kopieras till Azure-lagring av användaren (som kan vara skrivskyddad eller skrivskyddad). När du skapar en disk eller avbildning gör Azure kopior av källan VHD-filer. Dessa kopior kan vara skrivskyddade eller läs och skriv, beroende på hur du använder den virtuella hårddisken.

När du skapar en virtuell dator från en avbildning skapar Azure en disk för den virtuella datorn som är en kopia av .vhd-filen som används som källa. För att skydda dig mot att du råkar radera dem av misstag placerar Azure en livslängd på alla .vhd-filer som används som källor för att skapa en avbildning, en operativsystemdisk eller en datadisk.

Innan du kan ta bort en .vhd-fil som används som källa måste du ta bort livslängden genom att ta bort disken eller avbildningen. Om du vill ta bort en .vhd-fil som används av en virtuell dator som en operativsystemdisk kan du ta bort den virtuella datorn, operativsystemdisken, och .vhd-filen som används som källa på en gång genom att ta bort den virtuella datorn och alla associerade diskar. Men det krävs att du genomför ett antal steg i en viss ordning för att ta bort en .vhd-fil som är en källa för en datadisk. Först kopplar du bort disken från den virtuella datorn, sedan tar du bort disken och sist tar du bort .vhd-filen.

> [!WARNING]
> Om du tar bort en .vhd-fil som används som källa från lagringen eller tar bort ditt lagringskonto kan Microsoft inte återställa dessa data åt dig.

## <a name="types-of-disks"></a>Typer av diskar 
Azure-diskar har en tillförlitlighet på 99,999 %. Azure-diskarna har konsekvent leverera företagsklass hållbarhet med ett branschledande noll % Felintervall Annualized.

Det finns tre prestandanivåer för lagring som du kan välja mellan när du skapar diskarna--Premium SSD-diskar, Standard SSD (förhandsversion) och HDD standardlagring. Det finns även två typer av diskar--ohanterad och hanteras.

### <a name="standard-hdd-disks"></a>Standard HDD-diskar
Standarddiskar HDD backas upp av hårddiskar och tillhandahålla kostnadseffektiv lagring. Standardlagring Hårddisk kan replikeras lokalt i ett datacenter eller vara geo-redundant med primära och sekundära datacenter. Mer information om storage-replikering finns [Azure Storage-replikering](../articles/storage/common/storage-redundancy.md). 

Mer information om hur du använder Standard HDD-diskar finns [standardlagring och diskar](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks-preview"></a>Standard SSD-diskar (förhandsgranskning)
Standard SSD-diskar är utformade för att hantera samma typ av arbetsbelastning som Standard HDD-diskar, men erbjuder mer konsekvent prestanda och tillförlitlighet än Hårddisk. Standard SSD-diskar kombinera element i Premium SSD och Standard HDD-diskar till formuläret en kostnadseffektiv lösning som bäst passar för program som webbservrar som inte behöver höga IOPS på diskar. Om det finns är Standard SSD-diskar rekommenderade distributionsalternativet för de flesta arbetsbelastningar. Standard SSD-diskar är bara tillgängliga som hanterade diskar och medan i förhandsgranskningen är bara tillgängliga i [Välj regioner](../articles/virtual-machines/windows/faq-for-disks.md) och med återhämtningstyp lokalt redundant lagring (LRS).

### <a name="premium-ssd-disks"></a>Premium SSD-diskar 
Premium SSD-diskar backas upp av SSD-enheter och ger stöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Vanligtvis kan du använda Premium SSD-diskar med storlekar som innehåller ”s” i serienamn. Till exempel finns Dv3-serien och Dsv3-serien, Dsv3-serien kan användas med Premium SSD-diskar.  Mer information finns i [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Ohanterade diskar
Ohanterade diskar är den traditionella typen av diskar som används av virtuella datorer. Med dessa diskar skapa egna storage-konto och ange att storage-konto när du skapar disken. Kontrollera att du inte lägger till för många diskar i samma lagringskonto, eftersom du kan överskrida den [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) av lagringskonto (20 000 IOPS, till exempel), vilket resulterar i de virtuella datorerna begränsas. Du måste lista ut hur du optimerar användningen av en eller flera lagringskonton för att få ut den bästa prestandan av dina virtuella datorer med ohanterade diskar.

### <a name="managed-disks"></a>Hanterade diskar 
Managed Disks hanterar skapandet/hanterandet av lagringskontot i bakgrunden och säkerställer att du inte behöver bekymra dig om lagringskontots skalbarhetsgränser. Du anger bara diskens storlek och prestandanivå (Standard/Premium) så skapar och hanterar Azure disken åt dig. Om du lägger till diskar eller skalar upp eller ned den virtuella datorn behöver du inte oroa dig om lagringsutrymmet som används. 

Du kan även hantera dina anpassade avbildningar i ett lagringskonto per Azure-region och använda dem för att skapa hundratals virtuella datorer i samma prenumeration. Mer information om Managed Disks finns i [översikten över Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Vi rekommenderar att du använder Azure Managed Disks för nya virtuella datorer och att du konverterar tidigare ohanterade diskar till hanterade diskar för att dra nytta av de många funktionerna som finns i Managed Disks.

### <a name="disk-comparison"></a>Diskjämförelse
Följande tabell innehåller en jämförelse av Standard HDD Standard SSD och Premium SSD för ohanterade och hanterade diskar för att avgöra vad du ska använda.

|    | Azure Premium-disk |Azure Standard SSD-Disk (förhandsgranskning)| Azure Standard HDD-Disk 
|--- | ------------------ | ------------------------------- | ----------------------- 
| Disktyp | Solid State-hårddiskar (SSD) | Solid State-hårddiskar (SSD) | Hårddiskar (HDD)  
| Översikt  | SSD-baserad högpresterande disksupport med låg fördröjning för virtuella datorer som kör I/O-intensiva arbetsbelastningar eller är värd för verksamhetskritisk produktionsmiljö |Mer konsekvent prestanda och tillförlitlighet än Hårddisk. Optimerad för lägsta IOPS arbetsbelastningar| HDD-baserade kostnadseffektivt disk för ovanligt åtkomst
| Scenario  | Produktion och prestandakänsliga arbetsbelastningar |Webbservrar, lätt används företagsprogram och utveckling och testning| Säkerhetskopiering, icke-kritiska, ovanligt åtkomst 
| Diskstorlek | P4: 32 GiB (hanterade diskar endast)<br>P6: 64 GiB (hanterade diskar endast)<br>P10: 128 GiB<br>P15: 256 GiB (hanterade diskar endast)<br>P20: 512 GiB<br>P30: 1024 GiB<br>P40: 2048 GiB<br>P 50: 4095 GiB |Hanterade diskar endast:<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB | Ohanterad diskar: GiB 1 – 4 TiB (4095 GiB) <br><br>Hanterade diskar:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB
| Maxdataflöde per disk | 250 MiB/s | Upp till 60 MiB/s | Upp till 60 MiB/s 
| Max-IOPS per disk | 7500 IOPS | Upp till 500 IOPS | Upp till 500 IOPS 