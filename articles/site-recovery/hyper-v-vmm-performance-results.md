---
title: Testresultat för replikering av Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller information om prestandatestning för replikering av Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: c97247a14915ea3b8396f7179b4f8ca13bf92c8f
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079403"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Testresultat för Hyper-V-replikering till en sekundär plats

Den här artikeln innehåller resultatet av prestandatest vid replikering av Hyper-V-datorer i System Center Virtual Machine Manager (VMM)-moln till ett sekundärt datacenter.

## <a name="test-goals"></a>Testa mål

Målet med testning var att undersöka hur Site Recovery utför under replikering i stabilt tillstånd.

- Replikering i stabilt tillstånd inträffar när virtuella datorer har slutfört den inledande replikeringen och synkroniserar deltaändringar.
- Det är viktigt att mäta prestanda med hjälp av stabilt tillstånd, eftersom det är tillstånd där de flesta de virtuella datorerna kvar, såvida inte oväntade avbrott inträffar.
- Test-distribution består av två lokala platser, med en VMM-server på varje plats. Den här testdistributionen är typiska för en office distribution huvudet kontor/avdelningskontor med huvudkontoret som fungerar som den primära platsen och avdelningskontoret som platsen för sekundära eller återställning.

## <a name="what-we-did"></a>Det vi gjorde

Här är vad vi i testet skickar:

1. Skapade virtuella datorer med hjälp av VMM-mallar.
2. Igång virtuella datorer och avbildas prestandans basmått än 12 timmar.
3. Skapade moln på den primära servern och recovery VMM-servrar.
4. Konfigurerade replikering i Site Recovery, inklusive mappning mellan käll- och moln.
5. Aktiverat skydd för virtuella datorer och kunna slutföra den inledande replikeringen.
6. Ett par timmar för system stabiliserade kan ha väntat.
7. Avbildas prestandamått än 12 timmar, där alla virtuella datorer befann sig i ett replikeringstillstånd som krävs för de 12 timmarna.
8. Mätt delta mellan jämförelsemått för prestanda och prestandamått för replikering.


## <a name="primary-server-performance"></a>Primär server-prestanda

* Hyper-V-replikering (används av Site Recovery) asynkront spårar ändringar i en loggfil med minsta storage försämras på den primära servern.
* Hyper-V-replikering använder lokal behålla cacheminnet för att minimera IOPS omkostnader för spårning. Den lagrar skriver till VHDX i minnet och cachetömningar dem i loggfilen innan loggen skickas till återställningsplatsen. En disk som tömning sker även om skrivningar uppnår en förutbestämd gräns.
* Diagrammet nedan visar stabilt IOPS CPU-användningen för replikering. Vi kan se att IOPS försämras på grund av replikering är cirka 5%, vilket är mycket liten.

  ![Primär resultat](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V-replikering använder minne på den primära servern för att optimera prestanda för diskar. I följande diagram visas är minne försämras på alla servrar i det primära klustret marginell. Den memory overhead är procentandelen minne som används av replikering, jämfört med den totala mängden minnet som är installerade på Hyper-V-servern.

![Primär resultat](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V-replikering har lägsta CPU-belastning. I diagrammet visas är replikering mellan 2 – 3%.

![Primär resultat](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Sekundär server-prestanda

Hyper-V-replikering använder en liten mängd minne på återställningsservern, för att optimera antalet lagringsåtgärder. Diagrammet innehåller en sammanfattning av minnesanvändning på återställningsservern. Den memory overhead är procentandelen minne som används av replikering, jämfört med den totala mängden minnet som är installerade på Hyper-V-servern.

![Sekundär resultat](./media/hyper-v-vmm-performance-results/IC744916.png)

Hur mycket i/o-åtgärder på återställningsplatsen är en funktion av antalet skrivåtgärder på den primära platsen. Nu ska vi titta på de totala i/o-åtgärderna på återställningsplatsen i jämförelse med de totala i/o-åtgärderna och skrivåtgärder på den primära platsen. Diagrammen visar att det totala antalet IOPS på återställningsplatsen är

* Runt 1,5 gånger Skriv-IOPS på primärt.
* Cirka 37% av det totala antalet IOPS på den primära platsen.

![Sekundär resultat](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundär resultat](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Effekt på belastningen på nätverket

Ett genomsnitt av 275 Mb per sekund av nätverkets bandbredd användes mellan de primära servern och recovery noderna (vid komprimering aktiverat) mot en befintlig bandbredd på 5 Gb per sekund.

![Resultaten nätverksanvändning](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Effekt på prestanda för virtuella datorer

Ett viktigt övervägande är effekten av replikering på produktionsarbetsbelastningar som körs på de virtuella datorerna. Om den primära platsen är rätt etableras för replikering, det får inte vara någon inverkan på arbetsbelastningar. Hyper-V-replikering lightweight spåra mekanism säkerställer att arbetsbelastningar som körs på de virtuella datorerna inte påverkas under replikering i stabilt tillstånd. Detta illustreras i följande diagram.

Det här diagrammet visar IOPS utförs av virtuella datorer som kör olika arbetsbelastningar, före och efter replikering har aktiverats. Du kan se att det finns ingen skillnad mellan två.

![Repliken effekt resultat](./media/hyper-v-vmm-performance-results/IC744920.png)

I följande diagram visas dataflödet virtuella datorer som kör olika arbetsbelastningar, före och efter replikering har aktiverats. Du kan se att replikeringen har ingen betydande inverkan.

![Resultaten repliken effekter](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Sammanfattning

Resultaten visar tydligt att Site Recovery, tillsammans med Hyper-V-replikering, skalar bra med minsta användning för ett stort kluster. Site Recovery tillhandahåller enkel distribution, replikering, hantering och övervakning. Hyper-V-replikering innehåller infrastrukturen som behövs för lyckad replikering skalning. 

## <a name="test-environment-details"></a>Testa miljöinformation

### <a name="primary-site"></a>Primär plats

* Den primära platsen har ett kluster med fem Hyper-V-servrar, 470 virtuella datorer som körs.
* De virtuella datorerna kör olika arbetsbelastningar och alla har Site Recovery-skydd är aktiverat.
* Lagring för klusternoden tillhandahålls av ett iSCSI SAN-nätverk. Hitachi HUS130-modell.
* Varje klusterserver har fyra nätverkskort (NIC) av en Gbit/s varje.
* Två av nätverkskort som är anslutna till ett privat nätverk för iSCSI och två är anslutna till ett externt företagsnätverk. En av de externa nätverk är reserverad för endast klusterkommunikation.

![Primära maskinvarukraven](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modell | Processor | Antal processorer | Nätverkskort | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i klustret: <br />ESTLAB HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 har 256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 |Jag Gbit/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| VMM-servern |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x 64) och VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundär plats

* Den sekundära platsen har ett redundanskluster sex noder.
* Lagring för klusternoden tillhandahålls av ett iSCSI SAN-nätverk. Hitachi HUS130-modell.

![Primära maskinvarukraven](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modell | Processor | Antal processorer | Nätverkskort | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i klustret: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2,30 GHz |2 |Jag Gbit/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| ESTLAB HOST17 |128 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| VMM-servern |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x 64) och VMM 2012 R2 |

### <a name="server-workloads"></a>Server-arbetsbelastningar

* Vi valt arbetsbelastningar som ofta används i enterprise-kundscenarier för testning.
* Vi använder [IOMeter](http://www.iometer.org) med arbetsbelastningen-egenskap som sammanfattas i tabellen för att simulera.
* Alla IOMeter profiler är inställda på att skriva slumpmässiga byte att simulera sämsta skrivmönster för arbetsbelastningar.

| Arbetsbelastning | I/o-storlek (KB) | % Åtkomst | % Läs | Utestående I/o | I/o-mönster |
| --- | --- | --- | --- | --- | --- |
| Filserver |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Alla 100% slumpmässiga |
| SQL Server (volym 1) SQL Server (volym 2) |864 |100%100% |70%0% |88 |100% random100% sekventiella |
| Exchange |32 |100% |67% |8 |100% slumpmässiga |
| Arbetsstationen/VDI |464 |66%34% |70%95% |11 |Både slumpmässiga 100% |
| Web-filserver |4864 |33%34%33% |95%95%95% |888 |Alla 75% slumpmässiga |

### <a name="vm-configuration"></a>Konfiguration av virtuell dator

* 470 virtuella datorer på det primära klustret.
* Alla virtuella datorer med VHDX-disktyper.
* Virtuella datorer som kör arbetsbelastningar som sammanfattas i tabellen. Alla har skapats med VMM-mallar.

| Arbetsbelastning | # Virtuella datorer | Minsta RAM-minne (GB) | Maximalt RAM-minne (GB) | Logisk diskstorlek (GB) per virtuell dator | Högsta IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Filserver |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webbserver |149 |.5 |1 |80 |6 |
| TOTALT |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery-inställningar

* Site Recovery har konfigurerats för lokalt till lokalt skydd
* VMM-servern har fyra moln har konfigurerats, som innehåller servrar för Hyper-V-kluster och sina virtuella datorer.

| Primär VMM-moln | Skyddade virtuella datorer | Replikeringsfrekvens | Ytterligare återställningspunkter |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minuter |Ingen |
| PrimaryCloudRpo30s |47 |30 sekunder |Ingen |
| PrimaryCloudRpo30sArp1 |47 |30 sekunder |1 |
| PrimaryCloudRpo5m |235 |5 min |Ingen |

### <a name="performance-metrics"></a>Prestandamått

Tabellen sammanfattas de prestandamått och räknare som har mätt i distributionen.

| Mått | Räknare |
| --- | --- |
| Processor |\Processor(_Total)\% processortid |
| Ledigt minne |\Memory\Available megabyte |
| IOPS |\PhysicalDisk (_Total) \Disk disköverföringar/sek |
| VM läsåtgärder (IOPS) per sekund |\Hyper-V virtuell lagringsenhet (<VHD>) \Read/sek |
| VM skrivåtgärder (IOPS) / sek |\Hyper-V virtuell lagringsenhet (<VHD>) \Write åtgärder/S |
| Läsningsdataflöde som virtuell dator |\Hyper-V virtuell lagringsenhet (<VHD>) \Read byte/sek |
| Genomströmning för skrivning av virtuell dator |\Hyper-V virtuell lagringsenhet (<VHD>) \Write byte/sek |

## <a name="next-steps"></a>Nästa steg

[Konfigurera replikering](hyper-v-vmm-disaster-recovery.md)
