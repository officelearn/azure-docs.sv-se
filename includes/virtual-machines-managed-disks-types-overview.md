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
ms.openlocfilehash: 1556a7d482a6644831589937b1847b1d63e72693
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890992"
---
# <a name="what-disk-types-are-available-in-azure"></a>Vilka disktyper är tillgängliga i Azure?

Azure-hanterade diskar erbjuder för närvarande fyra disktyper, tre som är allmänt tillgänglig (GA) och ett som är för närvarande i förhandsversion. Dessa fyra disktyper varje har sina egna lämpliga målscenarier för kunden.

## <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av ultra solid-state-enheter (SSD) (förhandsversion), premium SSD, standard SSD och standard-hårddiskar (HDD) för hanterade diskar för att avgöra vad du ska använda.

|   | Ultra SSD (förhandsversion)   | Premium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Disktyp   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |I/o-intensiva arbetsbelastningar som SAP HANA, databaser för övre nivå (till exempel SQL, Oracle) och andra transaktion tunga arbetsbelastningar.   |Produktion och prestandakänsliga arbetsbelastningar   |Webbservrar, används företagsprogram och utveckling/testning   |Säkerhetskopiering, icke-kritiska, lågfrekvent åtkomst   |
|Diskstorlek   |65 536 gibibyte (GiB) (förhandsversion)   |4095 giB (GA), 32 767 GiB (förhandsversion)    |4095 (GA) GiB, 32 767 GiB (förhandsversion)   |4095 giB (GA), 32 767 GiB (förhandsversion)   |
|Högsta dataflöde   |2 000 MiB/s (förhandsversion)   |250 (GA) MiB/s, 750 MiB/s (förhandsversion)   |60 MiB/s (GA), 500 MiB/s (förhandsversion)   |60 Mib/s (GA), 500 MiB/s (förhandsversion)   |
|Max IOPS   |160,000 (förhandsversion)   |7500 (GA), 20 000 (förhandsversion)   |500 (GA), 2 000 (förhandsversion)   |500 (GA), 2 000 (förhandsversion)   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (förhandsversion)

Azure ultra SSD (förhandsversion) leverera högt dataflöde, hög IOPS och konsekvent låg latens disklagring för virtuella Azure IaaS-datorer. Vissa ytterligare fördelar med ultra SSD omfattar möjligheten att ändra dynamiskt prestanda för disken, tillsammans med dina arbetsbelastningar, utan att behöva starta om dina virtuella datorer. Ultra SSD lämpar sig för dataintensiva arbetsbelastningar som SAP HANA, översta databaser och transaktionen tunga arbetsbelastningar. Ultra SSD kan bara användas som datadiskar. Vi rekommenderar att du använder premium SSD som OS-diskar.

### <a name="performance"></a>Prestanda

När du etablerar ett ultra disk kan konfigureras oberoende kapacitet och prestanda för disken. Ultra SSD komma in flera fasta storlekar, från 4 GiB upp till 64 TiB och använder en modell för flexibla prestanda konfiguration där du kan konfigurera oberoende IOPS och dataflöden.

Några nyckelfunktioner i Ultra SSD är:

- Kapacitet för disk: Ultra SSD-kapacitet intervall från 4 GiB upp till 64 TiB.
- IOPs per disk: Ultra SSD stöder IOPS-gränserna för 300 IOPS/GiB, upp till högst 160 kB IOPS per disk. Kontrollera att den valda Disk-IOPS är mindre än VM IOPS för att uppnå IOPS som du etablerade. Minsta IOPS-disken är 100 IOPS.
- Diskdataflöde: Med ultra SSD dataflödesgräns av en enskild disk är 256 KiB/s för var och en etablerad IOPS, upp till högst 2000 Mbit/s per disk (där Mbit/s = 10 ^ 6 byte per sekund). Det minsta diskgenomflödet är 1 MiB.

### <a name="disk-size"></a>Diskstorlek

|Diskstorlek (GiB)  |IOPS Caps  |Taket för dataflöde (Mbit/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1 024 – 65 536 (storlekar i det här intervallet som ökar i steg om 1 TiB)     |160,000         |2,000         |

### <a name="preview-scope-and-limitations"></a>Förhandsversion av omfång och begränsningar

I förhandsversionen ultra SSD:

- Stöds i östra USA 2 i en enda tillgänglighetszon  
- Kan bara användas med tillgänglighetszoner (tillgänglighetsuppsättningar och enkel VM-distributioner utanför zoner kommer inte har möjlighet att koppla ett ultra disk)
- Stöds endast på ES/DS v3 virtuella datorer
- Finns bara tillgängliga i datadiskar och endast stöd för 4k som fysisk sektorstorlek  
- Kan bara skapas som tomma diskar  
- För närvarande kan endast distribueras med hjälp av Azure Resource Manager-mallar, CLI och python SDK.
- Stöder ännu inte diskbilder, VM-avbildningar, tillgänglighetsuppsättningar, VM-skalningsuppsättningar och Azure-diskkryptering.
- Stöder ännu inte integrering med Azure Backup eller Azure Site Recovery.
- Precis som med [de flesta förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), den här funktionen ska inte användas för produktionsarbetsbelastningar fram till allmän tillgänglighet (GA).

## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD leverera diskar med höga prestanda och låg fördröjning som stöd för virtuella datorer (VM) med indata/utdata (I/O)-intensiva arbetsbelastningar. Om du vill dra nytta av hastighet och prestanda för premium-lagringsdiskar, kan du migrera befintliga Virtuella datordiskar till Premium SSD: er. Premium SSD: er är lämpliga för verksamhetskritiska produktionsprogram.

### <a name="disk-size"></a>Diskstorlek

Storlekar som är markerade med en asterisk finns för närvarande i förhandsversion.

| Premium SSD-storlekar  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*              | P70*              | P80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8 192     | 16,384     | 32,767     |
| IOPS per disk       | Upp till 120 | Upp till 240              | Upp till 500              | Upp till 1 100 | Upp till 2 300              | Upp till 5 000              | Upp till 7 500             | Upp till 7 500              | Upp till 12 500              | Upp till 15 000              | Upp till 20 000              |
| Dataflöde per disk | Upp till 25 MiB/sek | Upp till 50 MiB/sek | Upp till 100 MiB/sek | Upp till 125 MiB/sek | Upp till 150 MiB/sek | Upp till 200 MiB/sek | Upp till 250 MiB/sek | Upp till 250 MiB/sek| Upp till 480 MiB/sek | Upp till 750 MiB/sek | Upp till 750 MiB/sek |

## <a name="standard-ssd"></a>Standard SSD

Azure standard SSD: er är ett alternativ för kostnadseffektiv lagring som optimerats för arbetsbelastningar som behöver konsekvent prestanda på lägre nivåer av IOPS. Standard SSD erbjuder en bra enklare upplevelse för dem som vill flytta till molnet, särskilt om det uppstår problem med variansen för arbetsbelastningar som körs på dina HDD-lösningar lokalt. Standard SSD: er ger bättre tillgänglighet, konsekvens, tillförlitlighet och svarstid jämfört med HDD-diskar. Standard SSD: er är lämpliga för webbservrar, programservrar för låg IOPS, används företagsprogram och arbetsbelastningar för utveckling och testning.

### <a name="disk-size"></a>Diskstorlek

Storlekar som är markerade med en asterisk finns för närvarande i förhandsversion.

| Standard SSD-storlekar  | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB           | 128             | 256             | 512            | 1,024  | 2,048            | 4,095     | 8 192     | 16,384     | 32,767    |
| IOPS per disk       | Upp till 500              | Upp till 500              | Upp till 500              | Upp till 500 | Upp till 500              | Upp till 500              | Upp till 500             | Upp till 500              | Upp till 1 300              | Upp till 2 000              | Upp till 2 000              |
| Dataflöde per disk |  Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek| Upp till 300 MiB/sek |  Upp till 500 MiB/sek | Upp till 500 MiB/sek |

## <a name="standard-hdd"></a>Standard HDD

Azure standardhårddiskar leverera tillförlitlig, billig disksupport för virtuella datorer som kör latens-okänslig arbetsbelastningar. Den stöder även blobar, tabeller, köer och filer. Med standard-lagring lagras data på hårddiskar (HDD). När du arbetar med virtuella datorer kan använda du standarddiskar för SSD och HDD för utveckling och testning och mindre kritiska arbetsbelastningar. Standard-lagring är tillgängligt i alla Azure-regioner.

### <a name="disk-size"></a>Diskstorlek

Storlekar som är markerade med en asterisk finns för närvarande i förhandsversion.

| Disk av standardtyp  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8 192     | 16,384     | 32,767     |
| IOPS per disk       | Upp till 500              | Upp till 500              | Upp till 500              | Upp till 500 | Upp till 500              | Upp till 500              | Upp till 500             | Upp till 500              | Upp till 1 300              | Upp till 2 000              | Upp till 2 000              |
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
