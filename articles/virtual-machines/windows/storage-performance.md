---
title: Optimera prestanda på virtuella Azure Lsv2-serie - Lagring
description: Lär dig hur du optimerar prestanda för din lösning på virtuella datorer i Lsv2-serien.
services: virtual-machines-windows
author: sasha-melamed
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 57b248908a02327f2521be05920259681a26817a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920237"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimera prestanda på virtuella datorer i Lsv2-serien

Virtuella datorer i Lsv2-serien stöder en mängd olika arbetsbelastningar som kräver hög I/O och dataflöde på lokal lagring i ett brett spektrum av program och branscher.  Lsv2-serien är idealisk för Big Data-, SQL-databaser, NoSQL-databaser, datalagring och stora transaktionsdatabaser, inklusive Cassandra, MongoDB, Cloudera och Redis.

Utformningen av virtuella datorer i Lsv2-serien maximerar AMD EPYC™ 7551-processorn för att ge bästa prestanda mellan processorn, minnet, NVMe-enheterna och de virtuella datorerna. Förutom att maximera maskinvaruprestandan är virtuella datorer i Lsv2-serien utformade för att fungera med behoven hos Windows och Linux-operativsystem för bättre prestanda med maskinvaran och programvaran.

Justering av programvara och maskinvara resulterade i den optimerade versionen av [Windows Server 2019 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), som släpptes i början av december 2018 till Azure Marketplace, som stöder maximal prestanda på NVMe-enheterna i virtuella datorer i Lsv2-serien.

Den här artikeln innehåller tips och förslag för att säkerställa att dina arbetsbelastningar och program uppnår maximal prestanda som utformats i de virtuella datorerna. Informationen på den här sidan uppdateras kontinuerligt när fler Lsv2-optimerade avbildningar läggs till på Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ kretsuppsättning arkitektur

Virtuella datorer i Lsv2-serien använder AMD EYPC™ serverprocessorer baserade på Zen-mikroarkitekturen. AMD utvecklade Infinity Fabric (IF) för EYPC™ som skalbar sammanlänkning för sin NUMA-modell som kan användas för on-die, on-package och multi-package communications. Jämfört med QPI (Quick-Path Interconnect) och UPI (Ultra-Path Interconnect) som används på Intels moderna monolitiska monolitiska processorer kan AMD:s många-NUMA-small-die-arkitektur medföra både prestandafördelar och utmaningar. Den faktiska effekten av minnesbandbredd och svarstidsbegränsningar kan variera beroende på vilken typ av arbetsbelastningar som körs.

## <a name="tips-for-maximizing-performance"></a>Tips för att maximera prestanda

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

* **Måste jag göra avsökningsjusteringar i Windows i Windows Server 2012 eller Windows Server 2016?**  
   NVMe-avsökning är endast tillgängligt på Windows Server 2019 på Azure.  

* **Kan jag växla tillbaka till en traditionell avbrottstjänstrutin (ISR)-modell?**  
   Virtuella datorer i Lsv2-serien är optimerade för NVMe-avsökning. Uppdateringar tillhandahålls kontinuerligt för att förbättra avsökningsresultatet.

* **Kan jag justera avsökningsinställningarna i Windows Server 2019?**  
   Avsökningsinställningarna kan inte justeras av användaren.
   
## <a name="next-steps"></a>Nästa steg

* Se specifikationer för alla [virtuella datorer som är optimerade för lagringsprestanda](sizes-storage.md) på Azure
