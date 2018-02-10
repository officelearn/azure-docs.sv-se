---
title: "Testresultat för Hyper-V-replikering mellan platser med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln innehåller information om prestandatestning för lokal till lokal replikering av Hyper-V virtuella datorer med hjälp av Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.openlocfilehash: f25bbca86fdbb480a4db7623d4ee8d296415a4be
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="test-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Testresultat för lokal till lokal Hyper-V-replikering med Site Recovery

Du kan använda Microsoft Azure Site Recovery för att dirigera och hantera replikering av virtuella datorer och fysiska servrar till Azure eller till ett sekundärt datacenter. Den här artikeln innehåller resultatet av prestandatester vi gjorde när replikering av Hyper-V virtuella datorer mellan två lokala datacenter.

## <a name="test-goals"></a>Testa mål

Målet för testning var att undersöka hur Azure Site Recovery utför under stabilt tillstånd. Stabilt tillstånd replikeringen sker när virtuella datorer har slutfört den inledande replikeringen och synkroniserar deltaändringar. Det är viktigt att mäta prestanda med hjälp av stabilt tillstånd eftersom den är tillstånd där de flesta virtuella datorer förblir om oväntade avbrott inträffar.

Testa distributionen består av två lokala webbplatser med en VMM-server på varje plats. Den här testdistributionen är typiska för en head office/filialdistribution, med huvudkontoret som fungerar som den primära platsen och avdelningskontoret som platsen för sekundära eller återställning.

## <a name="what-we-did"></a>Vi gjorde

Här är vad vi i testet klarade:

1. Skapa virtuella datorer med hjälp av VMM-mallar.
2. Starta virtuella datorer och avbilda baslinjen resultatmått än 12 timmar.
3. Skapade moln på den primära servern och återställa VMM-servrar.
4. Konfigurerade molnskydd i Azure Site Recovery, inklusive mappning av käll- och moln.
5. Aktivera skydd för virtuella datorer och ge dem till fullständiga inledande replikering.
6. Väntade några timmar innan systemet stabilisering.
7. Övervakad prestandamått än 12 timmar att säkerställa att alla virtuella datorer befann sig i ett replikeringstillstånd som krävs för dessa 12 timmar.
8. Mät delta mellan prestandamått baslinjen och Resultatstatistik för replikering.


## <a name="primary-server-performance"></a>Primär server-prestanda

* Hyper-V-replikering spårar asynkront ändringar i en loggfil med minsta mo på den primära servern.
* Hyper-V-replikering använder automatisk bibehålls minnescache för att minimera IOPS omkostnader för spårning. Den lagrar skriver till VHDX i minnet och rensningar i loggfilen dem före den tid som loggen skickas till återställningsplatsen. En disk som tömning sker också om skrivningar nått gränsen för ett förutbestämt.
* Diagrammet nedan visar stabilt tillstånd IOPS CPU-användningen för replikering. Vi kan se att IOPS försämras på grund av replikering är cirka 5% som är mycket liten.

![Primär resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Hyper-V-replikering använder minne på den primära servern att optimera diskprestanda. I följande diagram visas minne försämras på alla servrar i det primära klustret är marginell. Minnet omkostnader visas är procentandelen av minne som används av replikeringen jämfört med den totala mängden minnet som är installerad på Hyper-V-servern.

![Primär resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V-replikering har minimal CPU-belastning. Visas i diagrammet är replikering i området % 2-3.

![Primär resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

## <a name="secondary-recovery-server-performance"></a>Sekundär (återställning) serverprestanda

Hyper-V-replikering använder en liten mängd minne på återställningsservern för att optimera antalet lagringsåtgärder. Diagrammet sammanfattar minnesanvändningen på återställningsservern. Minnet omkostnader visas är procentandelen av minne som används av replikeringen jämfört med den totala mängden minnet som är installerad på Hyper-V-servern.

![Sekundär resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

Mängden i/o-åtgärder på återställningsplatsen är en funktion av antalet skrivåtgärder på den primära platsen. Nu ska vi titta på de totala i/o-åtgärderna på återställningsplatsen jämfört med Totalt antal i/o-åtgärder och skrivåtgärder på den primära platsen. Diagrammen visar att det totala antalet IOPS på återställningsplatsen

* Runt 1,5 gånger skrivningen IOPS på primärt.
* Omkring 37% av totalt antal IOPS på den primära platsen.

![Sekundär resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Sekundär resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

## <a name="effect-on-network-utilization"></a>Effekt på belastningen på nätverket

Ett genomsnitt av 275 Mb per sekund av nätverksbandbredden används mellan de primära platsen och återställningsplatsen noderna (med komprimering aktiverad) mot en befintlig bandbredd på 5 Gb per sekund.

![Resultaten nätverksanvändning](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

## <a name="effect-on-vm-performance"></a>Inverkan på prestandan för VM

Ett viktigt övervägande är effekten av replikering för produktionsarbetsbelastningar som körs på de virtuella datorerna. Om den primära platsen etableras på lämpligt sätt för replikering, får inte det finnas någon inverkan på arbetsbelastningar. Hyper-V-replikering lightweight spårning mekanism säkerställer att arbetsbelastningar som körs i de virtuella datorerna inte påverkas under stabilt tillstånd. Detta illustreras i följande diagram.

Det här diagrammet visar IOPS som utförs av virtuella datorer som kör olika arbetsbelastningar före och efter att replikering har aktiverats. Du kan se att det finns ingen skillnad mellan två.

![Replik effekt resultat](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

Följande diagram visar genomströmning av virtuella datorer som kör olika arbetsbelastningar före och efter att replikering har aktiverats. Du kan se att replikeringen har ingen inverkan.

![Resultaten replik effekter](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

## <a name="conclusion"></a>Sammanfattning

Resultaten visar tydligt att Azure Site Recovery, tillsammans med Hyper-V-replikering ska skalas med minsta omkostnader för ett kluster för stora.  Azure Site Recovery tillhandahåller enkel distribution, replikering, hantering och övervakning. Hyper-V-replikering tillhandahåller infrastrukturen som behövs för replikering som lyckades skalning. För att planera en optimal distribution vi rekommenderar att du hämtar den [Hyper-V Replica Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Testa miljön information

### <a name="primary-site"></a>Primär plats

* Den primära platsen har ett kluster som innehåller fem Hyper-V-servrar som kör 470 virtuella datorer.
* De virtuella datorerna kör olika arbetsbelastningar och alla ha Azure Site Recovery-skydd är aktiverat.
* Lagring för klusternoden som en iSCSI SAN-nätverk. Modellen – Hitachi HUS130.
* Varje klusterserver har fyra nätverkskort (NIC) av en Gbps.
* Två av nätverkskort som är anslutna till ett privat nätverk för iSCSI och två är ansluten till ett externt företagsnätverk. En av de externa nätverk är reserverad för endast klusterkommunikation.

![Primär maskinvarukrav](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

| Server | RAM | Modell | Processor | Antal processorer | NIC | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i klustret: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 har 256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 |Jag Gbit/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| VMM Server |2 | | |2 |1 Gbit/s |Windows Server-databasen 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="secondary-recovery-site"></a>Platsen för sekundära (återställning)

* Den sekundära platsen har ett kluster med sex noder.
* Lagring för klusternoden som en iSCSI SAN-nätverk. Modellen – Hitachi HUS130.

![Primär maskinvarukraven](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

| Server | RAM | Modell | Processor | Antal processorer | NIC | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i klustret: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 @ 2,30 GHz |2 |Jag Gbit/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen |
| VMM Server |2 | | |2 |1 Gbit/s |Windows Server-databasen 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Server-arbetsbelastningar

* För testning plockats vi arbetsbelastningar som ofta används i kunden företagsscenarier.
* Vi använder [IOMeter](http://www.iometer.org) med arbetsbelastning-egenskap som sammanfattas i tabellen för att simulera.
* Alla IOMeter profiler är inställda på att skriva slumpmässiga byte att simulera sämsta skriva mönster för arbetsbelastningar.

| Arbetsbelastning | I/o-storlek (KB) | % Åtkomst | % Läs | Utestående I/o | I/o-mönster |
| --- | --- | --- | --- | --- | --- |
| Filserver |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Alla 100% slumpmässiga |
| SQL Server (volym 1) SQL Server (volume 2) |864 |100%100% |70%0% |88 |100% random100% sekventiella |
| Exchange |32 |100% |67% |8 |100% slumpmässiga |
| Workstation/VDI |464 |66%34% |70%95% |11 |Både slumpmässiga 100% |
| Webbserver-fil |4864 |33%34%33% |95%95%95% |888 |Alla 75% slumpmässiga |

### <a name="vm-configuration"></a>VM-konfiguration

* 470 virtuella datorer på det primära klustret.
* Alla virtuella datorer med VHDX-disken.
* Virtuella datorer som kör arbetsbelastningar som sammanfattas i tabellen. Alla har skapats med VMM-mallar.

| Arbetsbelastning | # Virtuella datorer | Minsta RAM-minne (GB) | Maximalt RAM-minne (GB) | Storleken för logisk disk (GB) per VM | Högsta IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Filserver |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webbserver |149 |.5 |1 |80 |6 |
| TOTALT |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery-inställningarna

* Azure Site Recovery har konfigurerats för lokalt till lokalt skydd
* VMM-servern har fyra moln som har konfigurerats, som innehåller servrar för Hyper-V-kluster och deras virtuella datorer.

| Primära VMM-moln | Skyddade virtuella datorer i molnet | Replikeringsfrekvens | Ytterligare återställningspunkter |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minuter |Ingen |
| PrimaryCloudRpo30s |47 |30 sekunder |Ingen |
| PrimaryCloudRpo30sArp1 |47 |30 sekunder |1 |
| PrimaryCloudRpo5m |235 |5 minuter |Ingen |

### <a name="performance-metrics"></a>Prestandamått

Tabellen sammanfattar prestandamått och räknare som har mätt i distributionen.

| Mått | Räknaren |
| --- | --- |
| Processor |\Processor(_Total)\% processortid |
| Tillgängligt minne |\Memory\Available megabyte |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM läsåtgärder (IOPS) per sekund |\Hyper-V virtuell lagringsenhet (<VHD>) \Read åtgärder/sek |
| VM skrivåtgärder (IOPS) per sekund |\Hyper-V virtuell lagringsenhet (<VHD>) \Write åtgärder/S |
| VM läsa genomflöde |\Hyper-V virtuell lagringsenhet (<VHD>) \Read byte/sek |
| Genomströmning för skrivning till VM |\Hyper-V virtuell lagringsenhet (<VHD>) \Write byte/sek |

## <a name="next-steps"></a>Nästa steg

[Konfigurera replikering mellan två lokala VMM platser](site-recovery-vmm-to-vmm.md)
