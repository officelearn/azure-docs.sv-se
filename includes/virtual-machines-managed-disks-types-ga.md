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
ms.openlocfilehash: 885bc1c627626ee7ba4f391be31131b18fa1ab39
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65212047"
---
## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD leverera diskar med höga prestanda och låg fördröjning som stöd för virtuella datorer (VM) med indata/utdata (I/O)-intensiva arbetsbelastningar. Om du vill dra nytta av hastighet och prestanda för premium-lagringsdiskar, kan du migrera befintliga Virtuella datordiskar till Premium SSD: er. Premium SSD: er är lämpliga för verksamhetskritiska produktionsprogram.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

När du etablerar en disk för premium storage, till skillnad från standardlagring, garanterat kapacitet, IOPS och dataflöde på disken. Exempelvis kan etablerar du skapa en P50-disk Azure 4 095 GB lagringskapacitet, 7 500 IOPS och 250 MBIT/s genomströmning för disken. Ditt program kan använda hela eller delar av kapacitet och prestanda. Premium SSD-diskar är utformade för att leverera målet prestanda 99,9% av tiden.

## <a name="standard-ssd"></a>Standard SSD

Azure standard SSD: er är ett alternativ för kostnadseffektiv lagring som optimerats för arbetsbelastningar som behöver konsekvent prestanda på lägre nivåer av IOPS. Standard SSD erbjuder en bra enklare upplevelse för dem som vill flytta till molnet, särskilt om det uppstår problem med variansen för arbetsbelastningar som körs på dina HDD-lösningar lokalt. Standard SSD: er ger bättre tillgänglighet, konsekvens, tillförlitlighet och svarstid jämfört med HDD-diskar. Standard SSD: er är lämpliga för webbservrar, programservrar för låg IOPS, används företagsprogram och arbetsbelastningar för utveckling och testning.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard SSD-enheter är utformade för att tillhandahålla ensiffrig svarstid för de flesta i/o-åtgärder och leverera IOPS och dataflöde upp till de gränser som beskrivs i föregående tabell 99% av tiden. Faktiska IOPS och dataflöden varierar ibland beroende på trafikmönster. Standard SSD-enheter ger mer konsekventa prestanda än HDD-diskar med kortare svarstider.

## <a name="standard-hdd"></a>Standard HDD

Azure standardhårddiskar leverera tillförlitlig, billig disksupport för virtuella datorer som kör latens-okänslig arbetsbelastningar. Den stöder även blobar, tabeller, köer och filer. Med standard-lagring lagras data på hårddiskar (HDD). När du arbetar med virtuella datorer kan använda du standarddiskar för SSD och HDD för utveckling och testning och mindre kritiska arbetsbelastningar. Standard-lagring är tillgängligt i alla Azure-regioner.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Fakturering

När du använder hanterade diskar gäller följande för debitering:

- Disktyp
- hanterad disk storlek
- Ögonblicksbilder
- Utgående dataöverföringar
- Antal transaktioner

**Hanterade diskstorlek**: hanterade diskar debiteras storleken du fått. Azure maps-Allokerad storlek (avrundas uppåt) till närmaste erbjudna diskens storlek. Information om diskstorlekar som erbjuds finns i föregående tabell. Varje disk som mappar till ett erbjudande för stöds allokerad disk storlek och debiteras därefter. Till exempel om du har etablerat en 200 GiB Standard SSD det mappas till disk storlek erbjudandet om E15 (256 GB). Debiteringen för etablerade diskar beräknas per timme med hjälp av det månatliga priset för Premium Storage-erbjudandet. Till exempel om du har etablerat en E10 disk och tar bort den efter 20 timmar faktureras du för erbjudandet E10 beräknat på 20 timmar. Det här är oavsett mängden faktiska data som skrivs till disken.

**Ögonblicksbilder**: Ögonblicksbilder debiteras baserat på storleken som används. Om du skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet för 64 GiB och storlek för data som används av 10 GiB debiteras till exempel ögonblicksbilden endast för data som används storleken på 10 GiB.