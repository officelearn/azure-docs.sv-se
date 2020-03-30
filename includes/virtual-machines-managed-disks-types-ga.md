---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386111"
---
## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD-enheter ger diskstöd med hög prestanda och låg latens för virtuella datorer med indata/utdata (IO)-intensiva arbetsbelastningar. Om du vill dra nytta av hastigheten och prestandan hos premiumlagringsdiskar kan du migrera befintliga VM-diskar till Premium SSD-enheter. Premium SSD är lämpliga för verksamhetskritiska produktionsapplikationer. Premium SSD-enheter kan endast användas med VM-serier som är premiumlagringskompatibla.

Mer information om enskilda vm-typer och storlekar i Azure för Windows, inklusive vilka storlekar som är premiumlagringskompatibla, finns i [Storlekarna för virtuella datorer i Windows.](../articles/virtual-machines/windows/sizes.md) Mer information om enskilda vm-typer och storlekar i Azure för Linux, inklusive vilka storlekar som är premiumlagringskompatibla, finns i [Storlekar för virtuella linux-datorer](../articles/virtual-machines/linux/sizes.md). Från någon av dessa artiklar måste du kontrollera varje enskild vm-storleksartikel för att avgöra om den är premiumlagringskompatibel.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

När du etablerar en premiumlagringsdisk, till skillnad från standardlagring, garanteras du kapaciteten, IOPS- och dataflödet för disken. Om du till exempel skapar en P50-disk, azure bestämmelser 4,095-GB lagringskapacitet, 7.500 IOPS och 250-MB / s dataflöde för den disken. Ditt program kan använda hela eller delar av kapaciteten och prestandan. Premium SSD-diskar är utformade för att ge låga ensiffriga millisekunders svarstider och mål-IOPS och dataflöde som beskrivs i föregående tabell 99,9 % av tiden.

## <a name="bursting"></a>Spricker

Premium SSD storlekar mindre än P30 erbjuder nu disk spricker och kan brista sina IOPS per disk upp till 3.500 och deras bandbredd upp till 170 Mbps. Bursting är automatiserad och fungerar baserat på ett kreditsystem. Krediter ackumuleras automatiskt i en burst-bucket när disktrafiken ligger under det etablerade prestandamålet och krediter automatiskt förbrukas när trafiken spricker utanför målet, upp till max burst-gränsen. Max burst-gränsen definierar taket för disk IOPS & bandbredd även om du har burst krediter att konsumera från. Disksprickning ger bättre tolerans för oförutsägbara ändringar av IO-mönster. Du kan bäst utnyttja den för OS diskstart och applikationer med taggig trafik.    

Stöd för avspricker av diskar aktiveras på nya distributioner av tillämpliga diskstorlekar som standard, utan att någon användaråtgärd krävs. För befintliga diskar av tillämpliga storlekar kan du aktivera spricker med något av två alternativ: koppla bort och sätt tillbaka disken eller stoppa och starta om den anslutna virtuella datorn. Alla burst tillämpliga diskstorlekar börjar med en fullständig burst kredit bucket när disken är ansluten till en virtuell dator som stöder en max varaktighet vid topp burst gräns på 30 minuter. Mer information om hur bursting fungerar på Azure Diskar finns i [Premium SSD-bursting](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transaktioner

För premium-SSD-enheter betraktas varje I/O-åtgärd som är mindre än eller lika med 256 KiB dataflöde som en enda I/O-åtgärd. I/O-operationer större än 256 KiB dataflöde anses vara flera I/Os av storlek 256 KiB.

## <a name="standard-ssd"></a>Standard SSD

Azure standard SSD är ett kostnadseffektivt lagringsalternativ optimerat för arbetsbelastningar som behöver konsekvent prestanda på lägre IOPS-nivåer. Standard SSD erbjuder en bra ingångsnivå upplevelse för dem som vill flytta till molnet, särskilt om du upplever problem med variansen av arbetsbelastningar som körs på din hårddisk lösningar på plats. Jämfört med vanliga hårddiskar ger standard-SSD-enheter bättre tillgänglighet, konsekvens, tillförlitlighet och svarstid. Standard-SSD:er är lämpliga för webbservrar, låga IOPS-programservrar, lätt använda företagsprogram och arbetsbelastningar för utveckling/test. Precis som vanliga hårddiskar är standard-SSD-enheter tillgängliga på alla virtuella Azure-datorer.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard-SSD-enheter är utformade för att ge ensiffriga millisekunders svarstider och IOPS och dataflöde upp till de gränser som beskrivs i föregående tabell 99 % av tiden. Faktiska IOPS och dataflöde kan variera ibland beroende på trafikmönster. Standard-SSD-enheter ger mer konsekvent prestanda än hårddiskarna med den lägre latensen.

### <a name="transactions"></a>Transaktioner

För standard-SSD-enheter betraktas varje I/O-åtgärd som är mindre än eller lika med 256 KiB dataflöde som en enda I/O-åtgärd. I/O-operationer större än 256 KiB dataflöde anses vara flera I/Os av storlek 256 KiB. Dessa transaktioner har en faktureringseffekt.

## <a name="standard-hdd"></a>Standard HDD

Azure-standard-hårddiskar ger tillförlitligt diskstöd till låg kostnad för virtuella datorer som kör latenskänsliga arbetsbelastningar. Med standardlagring lagras data på hårddiskar. Svarstid, IOPS och Dataflöde för standard hdd-diskar kan variera mer i förhållande till SSD-baserade diskar. Standard HDD Diskar är utformade för att leverera skriv latenser under 10ms och läsa latenser under 20ms för de flesta IO-åtgärder, men den faktiska prestanda kan variera beroende på IO storlek och arbetsbelastning mönster. När du arbetar med virtuella datorer kan du använda vanliga hårddiskar för utvecklings-/testscenarier och mindre kritiska arbetsbelastningar. Standard-hårddiskar är tillgängliga i alla Azure-regioner och kan användas med alla virtuella Azure-datorer.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transaktioner

För standard-hårddiskar betraktas varje I/O-åtgärd som en enda transaktion, oavsett I/O-storlek. Dessa transaktioner har en faktureringseffekt.

## <a name="billing"></a>Fakturering

När du använder hanterade diskar gäller följande faktureringsöverväganden:

- Disktyp
- hanterad diskStorlek
- Ögonblicksbilder
- Övergående dataöverföringar
- Antal transaktioner

**Hanterad diskstorlek:** hanterade diskar faktureras på den etablerade storleken. Azure mappar den etablerade storleken (avrundad uppåt) till närmaste erbjudna diskstorlek. Mer information om diskstorlekar som erbjuds finns i föregående tabeller. Varje disk mappar till ett etablerat diskstorlekserbjudande som stöds och faktureras därefter. Om du till exempel har etablerat en 200 GiB Standard SSD mappas den till det skivstorlekserbjudande som erbjuds av E15 (256 GiB). Fakturering för alla etablerade diskar beräknas proportionellt per timme med hjälp av månadspriset för Premium Storage-erbjudandet. Om du till exempel har etablerat en E10-disk och tagit bort den efter 20 timmar debiteras du för E10-erbjudandet som är proportionellt till 20 timmar. Detta oavsett mängden faktiska data som skrivs till disken.

**Ögonblicksbilder:** Ögonblicksbilder faktureras baserat på den storlek som används. Om du till exempel skapar en ögonblicksbild av en hanterad disk med etablerad kapacitet på 64 GiB och den faktiska använda datastorleken på 10 GiB, faktureras ögonblicksbilden endast för den använda datastorleken på 10 GiB.
