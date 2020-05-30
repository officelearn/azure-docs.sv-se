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
ms.openlocfilehash: d263a1fb329f5a18fec760413300296643768c1d
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84200509"
---
## <a name="premium-ssd"></a>Premium SSD

Azure Premium SSD levererar hög prestanda och disk support med låg latens för virtuella datorer med indata/utdata (i/o)-intensiva arbets belastningar. Om du vill dra nytta av hastighet och prestanda för Premium Storage-diskar kan du migrera befintliga VM-diskar till Premium-SSD. Premium-SSD är lämpliga för verksamhets kritiska produktions program. Premium-SSD kan bara användas med VM-serien som är Premium Storage-kompatibla.

Mer information om enskilda VM-typer och storlekar i Azure för Windows, inklusive vilka storlekar som är kompatibla med Premium Storage, finns i [storlekar för virtuella Windows-datorer](../articles/virtual-machines/windows/sizes.md). Mer information om enskilda VM-typer och storlekar i Azure för Linux, inklusive vilka storlekar är Premium Storage-kompatibla, finns i [storlekar för virtuella Linux-datorer](../articles/virtual-machines/linux/sizes.md). Från någon av dessa artiklar måste du kontrol lera varje enskild artikel för VM-storlek för att avgöra om den är Premium Storage-kompatibel.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

När du etablerar en Premium Storage-disk, till skillnad från standard lagring, garanterar du att disken har kapacitet, IOPS och data flöde. Om du till exempel skapar en P50-disk, etablerar Azure 4 095-GB lagrings kapacitet, 7 500 IOPS och 250 MB/s genom strömning för disken. Programmet kan använda hela eller delar av kapaciteten och prestandan. Premium SSD diskar har utformats för att tillhandahålla låg latens i millisekunder och mål-IOPS och data flöde som beskrivs i föregående tabell 99,9% av tiden.

## <a name="bursting"></a>Bursting "

Premium SSD storlekar som är mindre än P30 erbjuder nu disk burst och kan överföra sina IOPS per disk upp till 3 500 och bandbredden upp till 170 Mbit/s. Burst-överföring automatiseras och fungerar baserat på ett kredit system. Krediter samlas in automatiskt i en burst-Bucket när disk trafiken är lägre än det tillhandahållna prestanda målet och krediterna förbrukas automatiskt när trafiken överförs bortom målet, upp till den maximala burst-gränsen. Max burst-gränsen definierar taket för disk-IOPS & bandbredd även om du har burst-krediter att använda. Disk burst ger bättre tolerans på oförutsägbara ändringar av IO-mönster. Du kan använda den bäst för start och program för operativ system diskar med hög belastnings trafik.    

Disk burst-stöd kommer att aktive ras vid nya distributioner av tillämpliga disk storlekar som standard, utan någon användar åtgärd krävs. För befintliga diskar av tillämpliga storlekar kan du aktivera burst-överföring med något av följande två alternativ: koppla från och återanslut disken eller stoppa och starta om den anslutna virtuella datorn. Alla användbara disk storlekar börjar med en fullständig överförings kredit Bucket när disken är ansluten till en virtuell dator som har stöd för maximal varaktighet vid högsta burst-gräns på 30 minuter. Mer information om hur du använder burst-överföring på Azure-diskar finns [Premium SSD bursting](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transaktioner

För Premium-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB.

## <a name="standard-ssd"></a>Standard SSD

Azure standard SSD är ett kostnads effektivt lagrings alternativ som är optimerat för arbets belastningar som behöver konsekvent prestanda på lägre IOPS-nivåer. Standard SSD erbjuder en bra ingångs nivå för de som vill flytta till molnet, särskilt om du får problem med var Ian sen för arbets belastningar som körs på dina hård disk lösningar lokalt. Jämfört med standard hård diskar ger standard SSD bättre tillgänglighet, konsekvens, tillförlitlighet och latens. Standard SSD är lämpliga för webb servrar, låga IOPS-programservrar, lätt använda företags program och arbets belastningar för utveckling/testning. Som standard hård diskar är standard-SSD tillgängliga på alla virtuella Azure-datorer.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard SSD är utformad för att tillhandahålla en ensiffrig millisekunds fördröjning och IOPS och data flöde upp till de gränser som beskrivs i föregående tabell 99% av tiden. Faktisk IOPS och data flöde kan variera ibland beroende på trafik mönstren. Standard SSD ger mer konsekvent prestanda än hård diskar med lägre latens.

### <a name="transactions"></a>Transaktioner

För standard-SSD anses varje I/O-åtgärd som är mindre än eller lika med 256 KiB data flöde betraktas som en enda I/O-åtgärd. I/O-åtgärder som är större än 256 KiB-dataflöde betraktas som flera I/o med storleken 256 KiB. Dessa transaktioner har en fakturerings påverkan.

## <a name="standard-hdd"></a>Standard HDD

Azure standard-hårddiskar levererar tillförlitligt stöd för diskar med låg kostnad för virtuella datorer som kör svars känsliga arbets belastningar. Med standard lagring lagras data på hård diskar (HDD). Svars tid, IOPS och data flöde för Standard HDD diskar kan variera mycket mer jämfört med SSD-baserade diskar. Standard HDD diskar har utformats för att leverera Skriv fördröjningar under 10ms och Läs fördröjning under 20ms för de flesta i/o-åtgärder, men den faktiska prestandan kan variera beroende på i/o-storlek och arbets belastnings mönster. När du arbetar med virtuella datorer kan du använda standard diskar för hård diskar för utvecklings-och test scenarier och mindre kritiska arbets belastningar. Standard hård diskar är tillgängliga i alla Azure-regioner och kan användas med alla virtuella Azure-datorer.

### <a name="disk-size"></a>Diskstorlek
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transaktioner

För standard-HDD betraktas varje IO-åtgärd som en enskild transaktion, oavsett I/O-storlek. Dessa transaktioner har en fakturerings påverkan.

## <a name="billing"></a>Fakturering

När du använder hanterade diskar gäller följande saker:

- Disktyp
- hanterad disk storlek
- Ögonblicksbilder
- Utgående data överföringar
- Antal transaktioner

**Hanterad disk storlek**: hanterade diskar debiteras enligt den etablerade storleken. Azure mappar den etablerade storleken (avrundat uppåt) till den närmaste erbjudna disk storleken. Mer information om de disk storlekar som erbjuds finns i föregående tabeller. Varje disk mappar till ett erbjudande för allokerad disk storlek och debiteras enligt detta. Om du till exempel har upprättat en 200 GiB Standard SSD, mappas den till disk storleks erbjudandet för E15 (256 GiB). Faktureringen för en etablerad disk beräknas per timme genom att använda det månatliga priset för lagrings erbjudandet. Om du till exempel har upprättat en E10-disk och tagit bort den efter 20 timmar debiteras du för E10-erbjudandet som har klassificerats till 20 timmar. Detta gäller oavsett hur mycket faktisk data som skrivs till disken.

**Ögonblicks bilder**: ögonblicks bilder faktureras baserat på storleken som används. Om du till exempel skapar en ögonblicks bild av en hanterad disk med en etablerad kapacitet på 64 GiB och den faktiska använda data storleken på 10 GiB faktureras ögonblicks bilden endast för den använda data storleken på 10 GiB.
