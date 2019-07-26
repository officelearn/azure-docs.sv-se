---
title: Test resultat för replikering av virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller information om prestanda testning för replikering av virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med hjälp av Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377222"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Test resultat för Hyper-V-replikering till en sekundär plats


Den här artikeln innehåller resultatet av prestanda testning när du replikerar virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till ett sekundärt Data Center.

## <a name="test-goals"></a>Test mål

Syftet med testningen var att undersöka hur Site Recovery utför under stabilt tillstånds replikering.

- Replikering av stabilt tillstånd sker när virtuella datorer har slutfört den inledande replikeringen och synkroniserar delta ändringar.
- Det är viktigt att mäta prestanda med stabilt tillstånd, eftersom det är det tillstånd som de flesta virtuella datorer är kvar på, om oväntade avbrott inträffar.
- Test distributionen består av två lokala platser, med en VMM-Server på varje plats. Den här test distributionen är typisk för distribution av huvud kontor och avdelnings kontor, med huvud kontor som fungerar som den primära platsen och avdelnings kontoret som sekundär plats eller återställnings plats.

## <a name="what-we-did"></a>Vad vi gjorde

Det här är vad vi gjorde i test passet:

1. Skapade virtuella datorer med VMM-mallar.
2. Startade virtuella datorer och hämtade bas linje prestanda mått under 12 timmar.
3. Skapade moln på den primära och återställnings VMM-servern.
4. Konfigurerad replikering i Site Recovery, inklusive mappning mellan käll-och återställnings moln.
5. Aktiverat skydd för virtuella datorer och har tillåtelse att slutföra den inledande replikeringen.
6. Väntade några timmar för system stabilisering.
7. Hämtade prestanda mått under 12 timmar, där alla virtuella datorer befann sig i ett förväntat replikeringstillstånd för de 12 timmarna.
8. Mätte delta mellan bas linje prestanda mått och prestanda mått för replikering.


## <a name="primary-server-performance"></a>Primär server prestanda

* Hyper-V-replikering (används av Site Recovery) asynkront spårar ändringar i en loggfil, med minsta möjliga lagrings belastning på den primära servern.
* Hyper-V-replikering använder sig av fristående cacheminne för att minimera IOPS-omkostnader för spårning. Den lagrar skrivningar till VHDX i minnet och tömmer dem till logg filen före den tid som loggen skickas till återställnings platsen. En disk tömning sker också om skrivningar når en fördefinierad gräns.
* I diagrammet nedan visas den stabila statusen IOPS för replikering. Vi kan se att IOPS-belastningen på grund av replikering är cirka 5%, vilket är ganska lågt.

  ![Primära resultat](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V-replikering använder minne på den primära servern för att optimera disk prestanda. Som du ser i följande diagram är minnes belastningen på alla servrar i det primära klustret marginaler. Den minnes kapacitet som visas är procent andelen minne som används av replikering, jämfört med det totala installerade minnet på Hyper-V-servern.

![Primära resultat](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V-replikering har lägsta CPU-belastning. Som det visas i diagrammet är replikeringens kostnader i intervallet 2-3%.

![Primära resultat](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Sekundär server prestanda

Hyper-V-replikering använder en liten mängd minne på återställnings servern för att optimera antalet lagrings åtgärder. Diagrammet sammanfattar minnes användningen på återställnings servern. Den minnes kapacitet som visas är procent andelen minne som används av replikering, jämfört med det totala installerade minnet på Hyper-V-servern.

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744916.png)

Mängden i/O-åtgärder på återställnings platsen är en funktion av antalet skriv åtgärder på den primära platsen. Nu ska vi titta på de totala I/O-åtgärderna på återställnings platsen jämfört med de totala I/O-åtgärderna och skriv åtgärderna på den primära platsen. Diagrammen visar att den totala IOPS på återställnings platsen är

* Cirka 1,5 gånger skrivs IOPS på den primära.
* Cirka 37% av det totala antalet IOPS på den primära platsen.

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundära resultat](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Påverkan på nätverks användning

Ett genomsnitt på 275 MB per sekund av nätverks bandbredden användes mellan primära noder och återställnings noder (med komprimering aktiverat), mot en befintlig bandbredd på 5 GB per sekund.

![Resultat av nätverks användning](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Inverkan på VM-prestanda

Ett viktigt övervägande är effekten av replikering av produktions arbets belastningar som körs på de virtuella datorerna. Om den primära platsen har allokerats för replikering bör det inte påverka arbets belastningarna. Hyper-V-replikeringens mekanism för enkel spårning säkerställer att arbets belastningar som körs på de virtuella datorerna inte påverkas under stabilt tillstånd. Detta illustreras i följande diagram.

I det här diagrammet visas IOPS som utförs av virtuella datorer som kör olika arbets belastningar, innan och efter att replikering har Aktiver ATS. Du kan observera att det inte finns någon skillnad mellan de två.

![Resultat av replik effekter](./media/hyper-v-vmm-performance-results/IC744920.png)

I följande diagram visas data flödet för virtuella datorer som kör olika arbets belastningar, innan och efter att replikering har Aktiver ATS. Du kan observera att replikeringen inte har någon betydande inverkan.

![Resultat repliks effekter](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Sammanfattning

Resultaten visar tydligt att Site Recovery, tillsammans med Hyper-V-replikering, skalar väl med lägsta belastning för ett stort kluster. Site Recovery tillhandahåller enkel distribution, replikering, hantering och övervakning. Hyper-V-replikering tillhandahåller den infrastruktur som krävs för skalning av lyckade replikeringar. 

## <a name="test-environment-details"></a>Test miljö information

### <a name="primary-site"></a>Primär plats

* Den primära platsen har ett kluster som innehåller fem Hyper-V-servrar, som kör 470 virtuella datorer.
* De virtuella datorerna kör olika arbets belastningar och har Site Recovery skydd aktiverat.
* Lagring för klusternoden tillhandahålls av ett iSCSI SAN. Model – Hitachi HUS130.
* Varje kluster Server har fyra nätverkskort (NIC) på en Gbit/s.
* Två nätverkskort är anslutna till ett privat iSCSI-nätverk och två är anslutna till ett externt företags nätverk. Ett av de externa nätverken är reserverat för enbart kluster kommunikation.

![Primära maskin varu krav](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modell | Processor | Antal processorer | Nätverkskort | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i kluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 har 256 |Dell™ PowerEdge-™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2.20 GHz |4 |I Gbit/s x 4 |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| VMM-servern |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundär plats

* Den sekundära platsen har ett kluster med sex noder.
* Lagring för klusternoden tillhandahålls av ett iSCSI SAN. Model – Hitachi HUS130.

![Primär maskin varu specifikation](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modell | Processor | Antal processorer | Nätverkskort | Programvara |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-servrar i kluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge-™ R720 |Intel (r) Xeon (r) CPU E5-2630 0 \@ 2.30 GHz |2 |I Gbit/s x 4 |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge-™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge-™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Data Center 2012 R2 (x64) + Hyper-V-roll |
| VMM-servern |2 | | |2 |1 Gbit/s |Windows Server-databas 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Server arbets belastningar

* I test syfte har vi valt arbets belastningar som ofta används i företags kund scenarier.
* Vi använder [IOMeter](http://www.iometer.org) med den arbets belastnings egenskap som sammanfattas i tabellen för simulering.
* Alla IOMeter-profiler är inställda på att skriva slumpmässiga bytes för att simulera värsta tänkbara Skriv mönster för arbets belastningar.

| Arbetsbelastning | I/O-storlek (KB) | % Åtkomst | %Read | Utestående I/o | I/O-mönster |
| --- | --- | --- | --- | --- | --- |
| Filserver |4<br />8<br />16<br />32<br />64 |60%<br />20 %<br />5 %<br />5 %<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Alla 100% slumpmässiga |
| SQL Server (volym 1)<br />SQL Server (volym 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% slumpmässig<br />100% sekventiell |
| Exchange |32 |100% |67% |8 |100% slumpmässig |
| Arbets Station/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Både 100% slumpmässig |
| Webb fil Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Alla 75% slumpmässiga |

### <a name="vm-configuration"></a>Konfiguration av virtuell dator

* 470 virtuella datorer i det primära klustret.
* Alla virtuella datorer med VHDX-diskar.
* Virtuella datorer som kör arbets belastningar sammanfattade i tabellen. Alla har skapats med VMM-mallar.

| Arbetsbelastning | Antal virtuella datorer | Minsta RAM-minne (GB) | Maximalt RAM-minne (GB) | Logisk disk storlek (GB) per virtuell dator | Högsta IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Filserver |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webbserver |149 |.5 |1 |80 |6 |
| TOTALT |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery inställningar

* Site Recovery konfigurerades för lokalt skydd
* VMM-servern har fyra moln konfigurerade, som innehåller Hyper-V-kluster servrar och deras virtuella datorer.

| Primärt VMM-moln | Skyddade virtuella datorer | Replikeringsfrekvens | Ytterligare återställnings punkter |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minuter |Inga |
| PrimaryCloudRpo30s |47 |30 sekunder |Inga |
| PrimaryCloudRpo30sArp1 |47 |30 sekunder |1 |
| PrimaryCloudRpo5m |235 |5 min |Inga |

### <a name="performance-metrics"></a>Prestandamått

I tabellen sammanfattas de prestanda mått och räknare som mättes i distributionen.

| Mått | Räknare |
| --- | --- |
| Processor |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` processortidation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Ledigt minne |\Memory\Available MB |
| IOPS |\PhysicalDisk (_ total) \ disk överföringar/SEK |
| VM-Läs åtgärder/SEK |\Hyper-V virtuell lagrings enhet\<(VHD >) \Read åtgärder/SEK |
| Åtgärder för VM-skrivning (IOPS)/SEK |\Hyper-V virtuell lagrings enhet\<(VHD >) \Write åtgärder/S |
| Läst data flöde för virtuell dator |\Hyper-V virtuell lagrings enhet\<(VHD >) \Read byte/s |
| Skriv data flöde för virtuell dator |\Hyper-V virtuell lagrings enhet\<(VHD >) \Write byte/s |

## <a name="next-steps"></a>Nästa steg

[Konfigurera replikering](hyper-v-vmm-disaster-recovery.md)
