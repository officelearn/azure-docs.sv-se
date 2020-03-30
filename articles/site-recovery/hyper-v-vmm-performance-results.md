---
title: Testa Hyper-V VM-replikering till en sekundär plats med VMM med Azure Site Recovery
description: Den här artikeln innehåller information om prestandatestning för replikering av virtuella hyper-virtuella datorer i VMM-moln till en sekundär plats med Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73663175"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Testresultat för Hyper-V-replikering till en sekundär plats


Den här artikeln innehåller resultat av prestandatestning vid replikerande av virtuella virtuella datorer i VMM-moln (System Center Virtual Machine Manager) till ett sekundärt datacenter.

## <a name="test-goals"></a>Testmål

Målet med testningen var att undersöka hur site recovery presterar under steady state-replikering.

- Replikering av stabilt tillstånd sker när virtuella datorer har slutfört den första replikeringen och synkroniserar deltaändringar.
- Det är viktigt att mäta prestanda med hjälp av stabilt tillstånd, eftersom det är det tillstånd där de flesta virtuella datorer finns kvar, om inte oväntade avbrott inträffar.
- Testdistributionen bestod av två lokala platser, med en VMM-server på varje plats. Den här testdistributionen är typisk för en distribution av huvudkontor/filialkontor, med huvudkontor som fungerar som den primära platsen och filialkontoret som sekundär- eller återställningsplats.

## <a name="what-we-did"></a>Vad vi gjorde

Här är vad vi gjorde i testpasset:

1. Skapade virtuella datorer med VMM-mallar.
2. Startade virtuella datorer och fångade baslinjeprestandamått över 12 timmar.
3. Skapade moln på vmm-servrarna för primär och återställning.
4. Konfigurerad replikering i Site Recovery, inklusive mappning mellan käll- och återställningsmoln.
5. Aktiverat skydd för virtuella datorer och tillät dem att slutföra den första replikeringen.
6. Väntade ett par timmar på systemstabilisering.
7. Hämtade prestandamått över 12 timmar, där alla virtuella datorer förblev i ett förväntat replikeringstillstånd under dessa 12 timmar.
8. Mätte deltat mellan baslinjeprestandamåtten och replikeringsprestandamåtten.


## <a name="primary-server-performance"></a>Primär serverprestanda

* Hyper-V Replica (används av Site Recovery) spårar asynkront ändringar i en loggfil, med minsta möjliga lagringsutrymme på den primära servern.
* Hyper-V Replica använder självförverkligad minnescache för att minimera IOPS-omkostnader för spårning. Den lagrar skrivningar till VHDX i minnet och spolar dem i loggfilen innan den tid som loggen skickas till återställningsplatsen. En diskspolning inträffar också om skrivarna når en förutbestämd gräns.
* Diagrammet nedan visar IOPS-omkostnader för replikering i stabilt tillstånd. Vi kan se att IOPS overhead på grund av replikering är cirka 5%, vilket är ganska låg.

  ![Primära resultat](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica använder minne på den primära servern för att optimera diskprestanda. Som visas i följande diagram är minnets omkostnader på alla servrar i det primära klustret marginellt. Minnesomkostnaderna som visas är den procentandel minne som används av replikeringen, jämfört med det totala installerade minnet på Hyper-V-servern.

![Primära resultat](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica har minsta CPU-omkostnader. Som visas i diagrammet ligger replikeringsomkostnaderna i intervallet 2-3 %.

![Primära resultat](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Sekundär serverprestanda

Hyper-V Replica använder en liten mängd minne på återställningsservern för att optimera antalet lagringsåtgärder. Diagrammet sammanfattar minnesanvändningen på återställningsservern. Minnesomkostnaderna som visas är den procentandel minne som används av replikeringen, jämfört med det totala installerade minnet på Hyper-V-servern.

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744916.png)

Mängden I/O-åtgärder på återställningsplatsen är en funktion av antalet skrivåtgärder på den primära platsen. Låt oss titta på den totala I/O-åtgärderna på återställningsplatsen i jämförelse med de totala I/O-operationerna och skrivåtgärderna på den primära platsen. Diagrammen visar att den totala IOPS på återställningsplatsen

* Runt 1,5 gånger skriva IOPS på den primära.
* Cirka 37 % av den totala IOPS på den primära platsen.

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Effekt på nätverksanvändningen

I genomsnitt användes 275 Mb per sekund av nätverksbandbredden mellan de primära noderna och återställningsnoderna (med komprimering aktiverat), mot en befintlig bandbredd på 5 Gb per sekund.

![Resultat nätverksutnyttjande](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Effekt på vm-prestanda

En viktig faktor är effekten av replikering på produktionsarbetsbelastningar som körs på de virtuella datorerna. Om den primära platsen är tillräckligt etablerad för replikering bör det inte påverka arbetsbelastningarna. Hyper-V-replikens lätta spårningsmekanism säkerställer att arbetsbelastningar som körs i de virtuella datorerna inte påverkas under steady-state-replikering. Detta illustreras i följande diagram.

Det här diagrammet visar IOPS som utförs av virtuella datorer som kör olika arbetsbelastningar, före och efter att replikeringen har aktiverats. Du kan konstatera att det inte finns någon skillnad mellan de två.

![Resultat av replikeffekt](./media/hyper-v-vmm-performance-results/IC744920.png)

Följande diagram visar dataflödet för virtuella datorer som kör olika arbetsbelastningar, före och efter replikering aktiverades. Du kan observera att replikeringen inte har någon signifikant inverkan.

![Replikeffekter för resultat](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Slutsats

Resultaten visar tydligt att Site Recovery, tillsammans med Hyper-V Replica, skalas väl med minsta omkostnader för ett stort kluster. Site Recovery ger enkel distribution, replikering, hantering och övervakning. Hyper-V Replica tillhandahåller den nödvändiga infrastrukturen för framgångsrik replikeringsskalning. 

## <a name="test-environment-details"></a>Information om testmiljö

### <a name="primary-site"></a>Primär plats

* Den primära platsen har ett kluster som innehåller fem Hyper-V-servrar som kör 470 virtuella datorer.
* De virtuella datorerna kör olika arbetsbelastningar och alla har skydd för webbplatsåterställning aktiverat.
* Lagring för klusternoden tillhandahålls av en iSCSI SAN. Modell – Hitachi HUS130.
* Varje klusterserver har fyra nätverkskort på en Gbps vardera.
* Två av nätverkskorten är anslutna till ett privat iSCSI-nätverk och två är anslutna till ett externt företagsnätverk. Ett av de externa nätverken är endast reserverat för klusterkommunikation.

![Primära maskinvarukrav](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modell | Processor | Antal processorer | NIC | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i klustret: <br />ESTLAB-VÄRD11<br />ESTLAB-VÄRD12<br />ESTLAB-VÄRD13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 har 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-roll |
| VMM-server |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundär plats

* Den sekundära platsen har ett redundanskluster för sexnoder.
* Lagring för klusternoden tillhandahålls av en iSCSI SAN. Modell – Hitachi HUS130.

![Primär maskinvaruspecifikation](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modell | Processor | Antal processorer | NIC | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i klustret: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-roll |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-roll |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V-roll |
| VMM-server |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Serverarbetsbelastningar

* I testsyfte valde vi arbetsbelastningar som vanligen används i företagskundscenarier.
* Vi använder [IOMeter](http://www.iometer.org) med arbetsbelastningsegenskaperna sammanfattade i tabellen för simulering.
* Alla IOMeter-profiler är inställda på att skriva slumpmässiga byte för att simulera värsta skrivmönster för arbetsbelastningar.

| Arbetsbelastning | I/O-storlek (KB) | % åtkomst | %Läs | Utestående I/Os | I/O-mönster |
| --- | --- | --- | --- | --- | --- |
| Filserver |4<br />8<br />16<br />32<br />64 |60 %<br />20 %<br />5 %<br />5 %<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Alla 100% slumpmässiga |
| SQL Server (volym 1)<br />SQL Server (volym 2) |8<br />64 |100 %<br />100 % |70 %<br />0 % |8<br />8 |100% slumpmässigt<br />100% sekventiell |
| Exchange |32 |100 % |67 % |8 |100% slumpmässigt |
| Arbetsstation/VDI |4<br />64 |66%<br />34% |70 %<br />95% |1<br />1 |Båda 100% slumpmässiga |
| Webbfilsserver |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Alla 75% slumpmässiga |

### <a name="vm-configuration"></a>Vm-konfiguration

* 470 virtuella datorer i det primära klustret.
* Alla virtuella datorer med VHDX-disk.
* Virtuella datorer som kör arbetsbelastningar summeras i tabellen. Alla skapades med VMM-mallar.

| Arbetsbelastning | # Virtuella datorer | Minsta RAM -minne (GB) | Maximalt RAM-minne (GB) | Logisk diskstorlek (GB) per virtuell dator | Maximalt IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Filserver |50 |1 |2 |552 |22 |
| Vdi |149 |.5 |1 |80 |6 |
| Webbserver |149 |.5 |1 |80 |6 |
| TOTALT |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Inställningar för återställning av webbplats

* Site Recovery har konfigurerats för lokalt till lokalt skydd
* VMM-servern har fyra moln konfigurerade, som innehåller Hyper-V-klusterservrarna och deras virtuella datorer.

| Primärt VMM-moln | Skyddade virtuella datorer | Replikeringsfrekvens | Ytterligare återställningspunkter |
| --- | --- | --- | --- |
| PrimärCloudRpo15m |142 |15 minuter |Inget |
| PrimärCloudRpo30s |47 |30 sek |Inget |
| PrimärCloudRpo30sArp1 |47 |30 sek |1 |
| PrimärCloudRpo5m |235 |5 min |Inget |

### <a name="performance-metrics"></a>Prestandamått

Tabellen sammanfattar prestandamått och räknare som mättes i distributionen.

| Mått | Räknare |
| --- | --- |
| Processor |\Processor(_Total)\% processortid |
| Tillgängligt minne |\Minne\Tillgängligt MBytes |
| IOPS |\PhysicalDisk(_Total)\Disköverföringar/sek |
| VM-läsa (IOPS) operationer/sek |\Hyper-V virtuell lagringsenhet(\<VHD>)\Läsåtgärder/sek |
| VM-skrivåtgärder (IOPS)/sek |\Hyper-V virtuell lagringsenhet(\<VHD>)\Skrivåtgärder/S |
| Vm läsa dataflöde |\Hyper-V virtuell lagringsenhet(\<VHD->)\Läs byte/sek |
| Datorskrivningsdataflöde |\Hyper-V virtuell lagringsenhet(\<VHD->)\Skriv byte/sek |

## <a name="next-steps"></a>Nästa steg

[Konfigurera replikering](hyper-v-vmm-disaster-recovery.md)
