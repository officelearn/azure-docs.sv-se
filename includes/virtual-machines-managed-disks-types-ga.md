---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2019
ms.locfileid: "58124688"
---
## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD leverera diskar med höga prestanda och låg fördröjning som stöd för virtuella datorer (VM) med indata/utdata (I/O)-intensiva arbetsbelastningar. Om du vill dra nytta av hastighet och prestanda för premium-lagringsdiskar, kan du migrera befintliga Virtuella datordiskar till Premium SSD: er. Premium SSD: er är lämpliga för verksamhetskritiska produktionsprogram.

### <a name="disk-size"></a>Diskstorlek

Storlekar som är markerade med en asterisk finns för närvarande i förhandsversion.

| Premium SSD-storlekar | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60* | P70* | P80* |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Diskstorleken i GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8 192 | 16,384 | 32,767 |
| IOPS per disk | Upp till 120 | Upp till 240 | Upp till 500 | Upp till 1 100 | Upp till 2 300 | Upp till 5 000 | Upp till 7 500 | Upp till 7 500 | Upp till 12 500 | Upp till 15 000 | Upp till 20 000 |
| Dataflöde per disk | Upp till 25 MiB/sek | Upp till 50 MiB/sek | Upp till 100 MiB/sek | Upp till 125 MiB/sek | Upp till 150 MiB/sek | Upp till 200 MiB/sek | Upp till 250 MiB/sek | Upp till 250 MiB/sek| Upp till 480 MiB/sek | Upp till 750 MiB/sek | Upp till 750 MiB/sek |

## <a name="standard-ssd"></a>Standard SSD

Azure standard SSD: er är ett alternativ för kostnadseffektiv lagring som optimerats för arbetsbelastningar som behöver konsekvent prestanda på lägre nivåer av IOPS. Standard SSD erbjuder en bra enklare upplevelse för dem som vill flytta till molnet, särskilt om det uppstår problem med variansen för arbetsbelastningar som körs på dina HDD-lösningar lokalt. Standard SSD: er ger bättre tillgänglighet, konsekvens, tillförlitlighet och svarstid jämfört med HDD-diskar. Standard SSD: er är lämpliga för webbservrar, programservrar för låg IOPS, används företagsprogram och arbetsbelastningar för utveckling och testning.

### <a name="disk-size"></a>Diskstorlek

Storlekar som är markerade med en asterisk finns för närvarande i förhandsversion.

| Standard SSD-storlekar | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Diskstorleken i GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8 192 | 16,384 | 32,767 |
| IOPS per disk | Upp till 120 | Upp till 240 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 1 300 | Upp till 2 000 | Upp till 2 000 |
| Dataflöde per disk |  Upp till 25 MiB/sek |  Upp till 50 MiB/sek  |  Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek| Upp till 300 MiB/sek |  Upp till 500 MiB/sek | Upp till 500 MiB/sek |

## <a name="standard-hdd"></a>Standard HDD

Azure standardhårddiskar leverera tillförlitlig, billig disksupport för virtuella datorer som kör latens-okänslig arbetsbelastningar. Den stöder även blobar, tabeller, köer och filer. Med standard-lagring lagras data på hårddiskar (HDD). När du arbetar med virtuella datorer kan använda du standarddiskar för SSD och HDD för utveckling och testning och mindre kritiska arbetsbelastningar. Standard-lagring är tillgängligt i alla Azure-regioner.

### <a name="disk-size"></a>Diskstorlek

Storlekar som är markerade med en asterisk finns för närvarande i förhandsversion.

| Disk av standardtyp | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Diskstorleken i GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8 192 | 16,384 | 32,767 |
| IOPS per disk | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 500 | Upp till 1 300 | Upp till 2 000 | Upp till 2 000 |
| Dataflöde per disk | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek| Upp till 300 MiB/sek | Upp till 500 MiB/sek | Upp till 500 MiB/sek |

## <a name="billing"></a>Fakturering

När du använder hanterade diskar gäller följande för debitering:

- Disktyp
- hanterad disk storlek
- Ögonblicksbilder
- Utgående dataöverföringar
- Antal transaktioner

**Hanterade diskstorlek**: hanterade diskar debiteras storleken du fått. Azure maps-Allokerad storlek (avrundas uppåt) till närmaste erbjudna diskens storlek. Information om diskstorlekar som erbjuds finns i föregående tabell. Varje disk som mappar till ett erbjudande för stöds allokerad disk storlek och debiteras därefter. Till exempel om du har etablerat en 200 GiB Standard SSD det mappas till disk storlek erbjudandet om E15 (256 GB). Debiteringen för etablerade diskar beräknas per timme med hjälp av det månatliga priset för Premium Storage-erbjudandet. Till exempel om du har etablerat en E10 disk och tar bort den efter 20 timmar faktureras du för erbjudandet E10 beräknat på 20 timmar. Det här är oavsett mängden faktiska data som skrivs till disken.

**Ögonblicksbilder**: Ögonblicksbilder debiteras baserat på storleken som används. Om du skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet för 64 GiB och storlek för data som används av 10 GiB debiteras till exempel ögonblicksbilden endast för data som används storleken på 10 GiB.