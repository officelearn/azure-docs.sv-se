---
title: Optimera prestanda på virtuella Azure Lsv2-serie - Lagring
description: Lär dig hur du optimerar prestanda för din lösning på virtuella datorer i Lsv2-serien.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034761"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimera prestanda på virtuella datorer i Lsv2-serien

Virtuella datorer i Lsv2-serien stöder en mängd olika arbetsbelastningar som kräver hög I/O och dataflöde på lokal lagring i ett brett spektrum av program och branscher.  Lsv2-serien är idealisk för Big Data-, SQL-databaser, NoSQL-databaser, datalagring och stora transaktionsdatabaser, inklusive Cassandra, MongoDB, Cloudera och Redis.

Utformningen av virtuella datorer i Lsv2-serien maximerar AMD EPYC™ 7551-processorn för att ge bästa prestanda mellan processorn, minnet, NVMe-enheterna och de virtuella datorerna. I samarbete med partners i Linux finns flera versioner tillgängliga Azure Marketplace som är optimerade för prestanda i Lsv2-serien och som för närvarande inkluderar:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

Den här artikeln innehåller tips och förslag för att säkerställa att dina arbetsbelastningar och program uppnår maximal prestanda som utformats i de virtuella datorerna. Informationen på den här sidan uppdateras kontinuerligt när fler Lsv2-optimerade avbildningar läggs till på Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ kretsuppsättning arkitektur

Virtuella datorer i Lsv2-serien använder AMD EYPC™ serverprocessorer baserade på Zen-mikroarkitekturen. AMD utvecklade Infinity Fabric (IF) för EYPC™ som skalbar sammanlänkning för sin NUMA-modell som kan användas för on-die, on-package och multi-package communications. Jämfört med QPI (Quick-Path Interconnect) och UPI (Ultra-Path Interconnect) som används på Intels moderna monolitiska monolitiska processorer kan AMD:s många-NUMA-small-die-arkitektur medföra både prestandafördelar och utmaningar. Den faktiska effekten av minnesbandbredd och svarstidsbegränsningar kan variera beroende på vilken typ av arbetsbelastningar som körs.

## <a name="tips-to-maximize-performance"></a>Tips för att maximera prestanda

* Om du laddar upp en anpassad Linux GuestOS för din arbetsbelastning, observera att Accelerated Networking kommer att vara **OFF** som standard. Om du tänker aktivera Accelererat nätverk aktiverar du det när den virtuella datorn skapas för bästa prestanda.

* Maskinvaran som driver virtuella datorer i Lsv2-serien använder NVMe-enheter med åtta I/O-köpar (QP)s. Varje NVMe-enhets-I/O-kö är faktiskt ett par: en inlämningskön och en slutförandekö. NVMe-drivrutinen är inställd för att optimera utnyttjandet av dessa åtta I/O-QPs genom att distribuera I/O:er i ett round robin-schema. Kör åtta jobb per enhet för att matcha för att få max prestanda.

* Undvik att blanda NVMe-administratörskommandon (till exempel NVMe SMART-informationsfråga osv.) med NVMe I/O-kommandon under aktiva arbetsbelastningar. Lsv2 NVMe-enheter backas upp av Hyper-V NVMe Direct-teknik, som växlar till "långsamt läge" när alla NVMe-administratörskommandon väntar. Lsv2-användare kan se en dramatisk prestandanedgång i NVMe I/O-prestanda om det händer.

* Lsv2-användare bör inte förlita sig på enhet NUMA-information (alla 0) som rapporteras inifrån den virtuella datorn för dataenheter för att bestämma NUMA-tillhörigheten för sina appar. Det rekommenderade sättet för bättre prestanda är att sprida arbetsbelastningar över processorer om möjligt.

* Det maximala ködjupet som stöds per I/O-köpar för Lsv2 VM NVMe-enhet är 1024 (jämfört med Amazon i3 QD 32-gräns). Lsv2-användare bör begränsa sina (syntetiska) benchmarking arbetsbelastningar till ködjup 1024 eller lägre för att undvika att utlösa kö fullständiga villkor, vilket kan minska prestanda.

## <a name="utilizing-local-nvme-storage"></a>Använda lokal NVMe-lagring

Lokal lagring på 1,92 TB NVMe-disken på alla virtuella Lsv2-datorer är efemär. Under en lyckad standardomstart av den virtuella datorn kommer data på den lokala NVMe-disken att finnas kvar. Data kommer inte att finnas kvar på NVMe om den virtuella datorn distribueras om, avallokeras eller tas bort. Data kvarstår inte om ett annat problem gör att den virtuella datorn, eller maskinvaran som körs på, blir felaktig. När detta inträffar raderas alla data på den gamla värden på ett säkert sätt.

Det kommer också att finnas fall när den virtuella datorn måste flyttas till en annan värddator, till exempel under en planerad underhållsåtgärd. Planerade underhållsåtgärder och vissa maskinvarufel kan förutses med [schemalagda händelser](scheduled-events.md). Schemalagda händelser bör användas för att hålla dig uppdaterad om eventuella förväntade underhålls- och återställningsåtgärder.

Om en planerad underhållshändelse kräver att den virtuella datorn återskapas på en ny värd med tomma lokala diskar, måste data synkroniseras om (igen, med alla data på den gamla värden som raderas på ett säkert sätt). Detta beror på att virtuella datorer i Lsv2-serien för närvarande inte stöder direktmigrering på den lokala NVMe-disken.

Det finns två lägen för planerat underhåll.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standard-VM kundkontrollerat underhåll

- Den virtuella datorn flyttas till en uppdaterad värd under ett 30-dagarsfönster.
- Lsv2 lokala lagringsdata kan gå förlorade, så säkerhetskopieringsdata före händelsen rekommenderas.

### <a name="automatic-maintenance"></a>Automatiskt underhåll

- Inträffar om kunden inte utför kundkontrollerat underhåll eller i händelse av nödprocedurer, till exempel en säkerhets-zero-day-händelse.
- Avsedd att bevara kunddata, men det finns en liten risk för en vm-frysning eller omstart.
- Lsv2 lokala lagringsdata kan gå förlorade, så säkerhetskopieringsdata före händelsen rekommenderas.

För kommande servicehändelser använder du den kontrollerade underhållsprocessen för att välja en tid som passar dig bäst för uppdateringen. Före evenemanget kan du säkerhetskopiera dina data i premiumlagring. När underhållshändelsen är klar kan du returnera dina data till den uppdaterade virtuella Lsv2-virtuella datorernas lokala NVMe-lagring.

Scenarier som underhåller data på lokala NVMe-diskar är:

- Den virtuella datorn körs och är felfri.
- Den virtuella datorn startas om på plats (av dig eller Azure).
- Den virtuella datorn pausas (stoppas utan avallokering).
- Huvuddelen av de planerade underhållsserviceerna.

Scenarier som på ett säkert sätt raderar data för att skydda kunden är:

- Den virtuella datorn distribueras om, stoppas (avallokeras) eller tas bort (av dig).
- Den virtuella datorn blir felaktig och måste tjänsten läka till en annan nod på grund av ett maskinvaruproblem.
- Ett litet antal av de planerade underhållsserviceåtgärder som kräver att den virtuella datorn omfördelas till en annan värd för service.

Mer information om alternativ för säkerhetskopiering av data i lokal lagring finns i [Säkerhetskopiering och haveriberedskap för Azure IaaS-diskar](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Hur börjar jag distribuera virtuella datorer i Lsv2-serien?**  
   Precis som alla andra virtuella datorer använder du [Portal,](quick-create-portal.md) [Azure CLI](quick-create-cli.md)eller [PowerShell](quick-create-powershell.md) för att skapa en virtuell dator.

* **Kommer ett enda NVMe-diskfel att alla virtuella datorer på värden misslyckas?**  
   Om ett diskfel upptäcks på maskinvarunoden är maskinvaran i ett feltillstånd. När detta inträffar avlegerar sig alla virtuella datorer på noden automatiskt och flyttas till en felfri nod. För virtuella datorer i Lsv2-serien innebär detta att kundens data på den felaktiga noden också raderas säkert och måste återskapas av kunden på den nya noden. Som nämnts, innan livemigrering blir tillgänglig på Lsv2, kommer data på den felaktiga noden att flyttas proaktivt med de virtuella datorerna när de överförs till en annan nod.

* **Måste jag göra några justeringar för att rq_affinity för prestanda?**  
   Inställningen rq_affinity är en mindre justering när du använder de absolut maximala in-/utdataoperationerna per sekund (IOPS). När allt annat fungerar bra, sedan försöka ställa in rq_affinity till 0 för att se om det gör någon skillnad.

* **Måste jag ändra blk_mq inställningar?**  
   RHEL/CentOS 7.x använder automatiskt blk-mq för NVMe-enheterna. Inga konfigurationsändringar eller inställningar är nödvändiga. Inställningen scsi_mod.use_blk_mq är endast för SCSI och användes under Lsv2 Preview eftersom NVMe-enheterna var synliga i gäst-virtuella datorer som SCSI-enheter. För närvarande är NVMe-enheterna synliga som NVMe-enheter, så SCSI blk-mq-inställningen är irrelevant.

* **Behöver jag ändra "fio"?**  
   För att få maximal IOPS med ett prestandamätningsverktyg som "fio" i storlekarna L64v2 och L80v2 VM ställer du in "rq_affinity" till 0 på varje NVMe-enhet.  Den här kommandoraden anger till exempel "rq_affinity" till noll för alla 10 NVMe-enheter i en virtuell L80v2-dator:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Observera också att bästa prestanda erhålls när I / O görs direkt till var och en av de råa NVMe-enheter utan partitionering, inga filsystem, ingen RAID 0 config, etc. Innan du startar en testsession, se till att konfigurationen är i ett känt färskt/rent tillstånd genom att köras `blkdiscard` på var och en av NVMe-enheterna.
   
## <a name="next-steps"></a>Nästa steg

* Se specifikationer för alla [virtuella datorer som är optimerade för lagringsprestanda](sizes-storage.md) på Azure
