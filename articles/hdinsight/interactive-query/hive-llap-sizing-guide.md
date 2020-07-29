---
title: Storleks guide för HDInsight-LLAP (Interactive Query Cluster)
description: LLAP storleks guide
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83664965"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Storleks guide för Azure HDInsight-interaktiv fråga kluster (Hive LLAP)

Det här dokumentet beskriver storleken på det interaktiva LLAP-klustret för HDInsight (Hive-kluster) för en typisk arbets belastning för att uppnå rimlig prestanda. Observera att rekommendationerna i det här dokumentet är allmänna rikt linjer och att vissa arbets belastningar kan kräva en   speciell justering.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Azure standard VM-typer för HDInsight-Interactive Query-kluster (LLAP)**

| Nodtyp      | Instans | Storlek     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 virtuella processorer, 56 GB RAM, 400 GB SSD   |
| Företag   | **D14 v2**        | **16 virtuella processorer, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 V2        | 4 virtuella processorer, 8 GB RAM, 40 GB SSD       |

***Obs! alla rekommenderade konfigurations värden baseras på D14 v2-typ Worker Node***  

### <a name="configuration"></a>**Inställningarna**    
| Konfigurationsnyckel      | Rekommenderat värde  | Beskrivning |
| :---        |    :----:   | :---     |
| garn. nodemanager. Resource. Memory-MB | 102400 (MB) | Totalt minne angivet i MB för alla garn behållare på en nod | 
| garn. Scheduler. maximum-Allocation-MB | 102400 (MB) | Maximal allokering för varje container förfrågan i RM, i MB. Minnes förfrågningar som är högre än det här värdet börjar gälla |
| garn. Scheduler. maximum-Allocation-virtuella kärnor | 12 |Det maximala antalet processor kärnor för varje container förfrågan i Resource Manager. Begär Anden som är högre än det här värdet börjar gälla. |
| garn. nodemanager. Resource. CPU-virtuella kärnor | 12 | Antal processor kärnor per NodeManager som kan tilldelas för behållare. |
| garn. Scheduler. Capacity. root. LLAP. Capacity | 80 (%) | LLAP-kö för garn kapacitets tilldelning  |
| Tez. am. Resource. Memory. MB | 4096 (MB) | Mängden minne i MB som ska användas av Tez-AppMaster |
| Hive. Server2. Tez. sessions. per. default. Queue | <number_of_worker_nodes> |Antalet sessioner för varje kö med namnet i Hive. Server2. Tez. default. queues. Det här talet motsvarar antalet frågans koordinatorer (Tez AMs) |
| Hive. Tez. container. size | 4096 (MB) | Angiven storlek på Tez-behållare i MB |
| hive.llap.daemon.num.executors | 12 | Antal körningar per LLAP daemon | 
| Hive. LLAP. io. trådpool. size | 12 | Storlek på trådpoolen för körningar |
| Hive. LLAP. daemon. garn. container. MB | 77824 (MB) | Totalt minne i MB som används av enskilda LLAP-daemonar (minne per daemon)
| Hive. LLAP. io. Memory. size | 235520 (MB) | Cachestorlek i MB per LLAP daemon tillhandahöll cache-minne för SSD är aktiverat |
| Hive. Auto. convert. Join. noconditionaltask. size | 2048 (MB) | minnes storlek i MB för kart koppling |

### <a name="llap-daemon-size-estimations"></a>**Storleks uppskattningar för LLAP daemon:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. bestämmande av total GARNs minnesallokering för alla behållare på en nod**    
Konfiguration: ***garn. nodemanager. Resource. Memory-MB***  

Det här värdet anger en högsta mängd minne i MB som kan användas av garn behållare på varje nod. Det angivna värdet måste vara mindre än den totala mängden fysiskt minne på den noden.   
Totalt minne för alla garn behållare på en nod = [Totalt fysiskt minne] – [minne för OS + andra tjänster]  
Ange det här värdet till ~ 90% av den tillgängliga RAM-storleken.  
För D14 v2 är det rekommenderade värdet **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. fastställer den maximala mängden minne per garn container förfrågan**  
Konfiguration: ***garn. Scheduler. maximum-Allocation-MB***

Det här värdet anger den maximala allokeringen för varje container förfrågan i Resource Manager, i MB. Minnes förfrågningar som är större än det angivna värdet börjar gälla. Resurs hanteraren kan ge minne till behållare i steg om *garn. Scheduler. minimum-Allocation-MB* och får inte överskrida den storlek som anges av *garn. Scheduler. maximum-Allocation-MB*. Det angivna värdet får inte vara större än det totala antalet angivna minne för alla behållare på den nod som anges av *garn. nodemanager. Resource. Memory-MB*.    
För D14 v2 Worker-arbetsnoder är det rekommenderade värdet **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. fastställer maximal mängd virtuella kärnor per garn container-begäran**  
Konfiguration: ***garn. Scheduler. maximum-Allocation-virtuella kärnor***  

Det här värdet anger det maximala antalet virtuella processor kärnor för varje container förfrågan i Resource Manager. Att begära ett högre antal virtuella kärnor än det här värdet börjar inte gälla. Det är en global egenskap hos garn Scheduler. För LLAP daemon-behållare kan det här värdet anges till 75% av det totala antalet tillgängliga virtuella kärnor. Återstående 25% bör vara reserverad för NodeManager, DataNode och andra tjänster som körs på arbetsnoderna.  
Det finns 16 virtuella kärnor på D14 v2-VM: ar och 75% av totalt 16 virtuella kärnor kan användas av LLAP daemon-behållare.  
För D14 v2 är det rekommenderade värdet **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. antal samtidiga frågor**  
Konfiguration: ***Hive. Server2. Tez. sessions. per. default. Queue***

Det här konfiguration svärdet avgör antalet Tez-sessioner som kan startas parallellt. Dessa Tez-sessioner startas för var och en av de köer som anges av "Hive. Server2. Tez. default. Queues". Det motsvarar antalet Tez-AMs (fråga koordinatorer). Vi rekommenderar att du är samma som antalet arbetsnoder. Antalet Tez-AMs kan vara högre än antalet LLAP daemon-noder. Tez är ett primärt ansvar för att samordna frågekörningen och tilldela till motsvarande LLAP-daemon för körning. Behåll det här värdet i multipler av ett antal LLAP daemon-noder för att uppnå högre genomflöde.  

Standard-HDInsight-kluster har fyra LLAP-daemonar som körs på fyra arbetsnoder, så det rekommenderade värdet är **4**.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Tez-behållare och Tez program huvud storlek**    
Konfiguration: ***Tez. am. Resource. Memory. MB, Hive. Tez. container. size***  

*Tez. am. Resource. Memory. MB* – definierar programmets huvud storlek för Tez.  
Det rekommenderade värdet är **4096 MB**.
   
*Hive. Tez. container. size* -definierar mängden minne som anges för Tez container. Det här värdet måste anges mellan den minsta storleken för garn behållare (*garn. Scheduler. minimum-Allocation-MB*) och maximal container storlek för garn (*garn. Scheduler. maximum-Allocation-MB*). LLAP daemon-körningar använder det här värdet för att begränsa minnes användning per utförar.  
Det rekommenderade värdet är **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. LLAP för kös kap.**   
Konfiguration: ***garn. Scheduler. Capacity. root. LLAP. Capacity***  

Det här värdet anger en procent andel av kapaciteten som har tilldelats LLAP-kön. Kapacitets tilldelningarna kan ha olika värden för olika arbets belastningar beroende på hur garn köerna har kon figurer ATS. Om arbets belastningen är skrivskyddad kan du ställa in den så hög som 90% av kapaciteten. Men om arbets belastningen är blandning av uppdaterings-/borttagnings-/sammanfognings åtgärder med hjälp av hanterade tabeller, rekommenderar vi att du ger 80% av kapaciteten för LLAP-kön. Den återstående 20% kapaciteten kan användas av andra aktiviteter, till exempel komprimering osv. för att allokera behållare från standard kön. På så sätt kan uppgifter i standard kön inte berövas garn resurser.    
För D14v2 Worker-arbetsnoder är det rekommenderade värdet för LLAP-kön **80**.   
(För skrivskyddade arbets belastningar kan det ökas upp till 90 på lämpligt sätt.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP daemon container-storlek**    
Konfiguration: ***Hive. LLAP. daemon. garn. container. MB***  
   
LLAP Daemon körs som en garn behållare på varje arbetsnod. Den totala minnes storleken för LLAP daemon-behållaren beror på följande faktorer,    
*  Konfigurationer av garn behållarens storlek (garn. Scheduler. minimum-Allocation-MB, garn. Scheduler. maximum-Allocation-MB, garn. nodemanager. Resource. Memory-MB)
*  Antalet Tez-AMs på en nod
*  Totalt minne som kon figurer ATS för alla behållare i en nod och LLAP-kös Kap kap.  

Det minne som krävs av Tez program hanterare (Tez) kan beräknas på följande sätt.  
För det interaktiva HDInsight-klustret finns det en Tez per arbets nod som fungerar som en fråga-koordinator. Antalet Tez-AMs kan konfigureras baserat på ett antal samtidiga frågor som ska hanteras.
Vi rekommenderar att du har 4 GB minne per Tez.

Tez AM minne per nod = [ceil (antal Tez AMs/antal LLAP daemon-noder)] x [Tez AM container storlek]  
För D14 v2 har standard konfigurationen fyra Tez-AMs och fyra LLAP daemon-noder.  
Tez AM minne per nod = (ceil (4/4) x 4 GB) = 4 GB

Det totala tillgängliga minnet för LLAP kö per arbets nod kan beräknas på följande sätt: det här värdet beror på den totala mängden minne som är tillgängligt för alla garn behållare på en nod (*garn. nodemanager. Resource. Memory-MB*) och procent andelen kapacitet som kon figurer ATS för LLAP Queue (*garn. Scheduler. Capacity. root. LLAP. Capacity*).  
Totalt minne för LLAP-kön på arbetsnoden = totalt tillgängligt minne för alla garn behållare på en nod x procent andel av kapaciteten för LLAP-kön.  
För D14 v2 är det här värdet [100 GB x 0,80] = 80 GB.

Storleken på LLAP daemon-behållare beräknas på följande sätt:

**LLAP daemon container size = [totalt minne tillgängligt för LLAP-kö] – [Tez AM minne per nod]**  
    
För D14 v2 Work Node, HDI 4,0 – det rekommenderade värdet är (80 GB-4 GB)) = **76 GB**   
(För HDI 3,6 är det rekommenderade värdet **74 GB** eftersom du bör reservera ytterligare ~ 2 GB för skjutreglaget.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. fastställer antalet körningar per LLAP daemon**  
Konfiguration: ***hive.llap.daemon.num.executors***, ***Hive. LLAP. io. trådpool. size***

***hive.llap.daemon.num.executors***:   
Den här konfigurationen styr antalet körningar som kan köra aktiviteter parallellt per LLAP daemon. Det här värdet beror på antalet virtuella kärnor, mängden minne som tilldelas per utförar och mängden totalt minne som är tillgängligt för LLAP daemon. Normalt vill vi att det här värdet ska vara så nära som möjligt för antalet virtuella kärnor.
Det finns 16 virtuella kärnor på D14 v2-virtuella datorer. Alla virtuella kärnor kan dock inte utföras eftersom andra tjänster som NodeManager, DataNode, mått övervakare osv. också behöver en del av tillgänglig virtuella kärnor. 

Om du behöver justera antalet körningar rekommenderar vi att du funderar 4 GB minne per utförar enligt *Hive. Tez. container. size* och se till att det totala minne som krävs för alla körningar inte överskrider det totala tillgängliga minnet för LLAP daemon-behållare.  
Det här värdet kan konfigureras upp till 75% av den totala virtuella kärnor som är tillgänglig på den noden.  
För D14 v2 är det rekommenderade värdet (. 75 X 16) = **12**

***Hive. LLAP. io. trådpool. size***:   
Det här värdet anger storleken på trådpoolen för körningar. Eftersom körningar har åtgärd ATS som de har angetts kommer det att vara samma som antalet körningar per LLAP daemon.   
För D14 v2 är det rekommenderade värdet **12**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. avgör storleken på LLAP daemon-cachen**  
Konfiguration: ***Hive. LLAP. io. Memory. size***

LLAP daemon container Memory består av följande komponenter:
*  Huvud rum
*  Heap-minne som används av exekverare (XMX)
*  Minnes intern cache per daemon (minnes storlek för ledigt minne, ej tillämpbar när SSD-cachen är aktive rad)
*  Metadata för minnes intern cache (gäller endast när SSD-cachen är aktive rad)

**Utrymmes storlek**: den här storleken anger en del av ett minnes minne som används för Java VM-overhead (Metaspace, tråds tack, GC-datastrukturer osv.). I allmänhet är den här overheadkostnaden cirka 6% av heap-storleken (XMX). För att vara på sidan säkrare kan det här värdet beräknas som 6% av den totala minnes storleken för LLAP daemon.  
För D14 v2 är det rekommenderade värdet ceil (76 GB x 0,06) ~ = **5 GB**.  

**Heap-storlek (XMX)**: det är mängden heap-minne som är tillgängligt för alla körningar.
Total Heap-storlek = antal körningar x 4 GB  
För D14 v2 är det här värdet 12 x 4 GB = **48 GB**  

Om SSD-cachen är inaktive rad är minnet i minnet den mängd minne som är kvar efter att ha tagit slut på utrymme och Heap-storlek från LLAP daemon-behållarens storlek.

Storleks beräkningen för cachen varierar när SSD-cachen är aktive rad.
Om du ställer in *Hive. LLAP. io. allokerare. mmap* = True aktive ras SSD-cachelagring.
När SSD-cachen är aktive rad kommer en del av minnet att användas för att lagra metadata för SSD-cachen. Metadata lagras i minnet och förväntas vara ~ 10% av storleken på SSD-cachen.   
Storlek på SSD-cache i minnet = [LLAP daemon container size]-[huvud rum + Heap-storlek]  
För D14 v2, med HDI 4,0, SSD cache i minnes storlek = [76 GB]-[5 GB + 48 GB] = **23 GB**  
För D14 v2, med HDI 3,6, SSD cache i minnes storlek = [76 GB]-[5 GB + 48 GB + 2 GB skjutreglage] = **21 GB**

Om storleken på tillgängligt minne för lagring av SSD cache-metadata kan du beräkna storleken på SSD-cachen som kan användas.  
Storlek på minnes intern metadata för SSD cache = 10% av storleken på SSD-cachen       
Storlek på SSD cache = storleken på minnesbaserade metadata för SSD-cache x 10  

För D14 v2 och HDI 4,0 är den rekommenderade storleken på SSD-cache = 23 GB x 10 = **230 GB**  
För D14 v2 och HDI 3,6 är den rekommenderade storleken på SSD-cache = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Justera kart kopplings minne**   
Konfiguration: ***Hive. Auto. convert. Join. noconditionaltask. size***

Kontrol lera att du har *Hive. Auto. convert. Join. noconditionaltask* aktiverat för att den här parametern ska börja gälla.
Med den här konfigurationen kan användaren ange storleken på de tabeller som får plats i minnet för att mappa koppling. Om summan av storleken på n-1 tabeller eller partitioner för n-vägs koppling är mindre än det konfigurerade värdet väljs kart kopplingen. Minnes storleken för LLAP-utförar ska användas för att beräkna tröskelvärdet för autoconvert för att mappa koppling.
Varje utförar antas ha 4 GB Heap-storlek, men alla är inte tillgängliga för kart anslutning. Vissa heap-minnen används för att sortera buffertar, blanda buffertar, hash-tabeller osv. Du kan alltså ge 50% av 4 GB heap-minne för kart anslutning.  
Obs! det här värdet kan kräva justeringar som är lämpliga för din arbets belastning. Om värdet anges för lågt får inte funktionen konvertera in. Om du ställer in den för hög kan det medföra att minnes undantagen eller GC pausas, vilket kan leda till försämrade prestanda.  
För D14 v2, med 4 GB minne per utförar, rekommenderar vi att du anger det här värdet till **2048 MB**.


#### <a name="next-steps"></a>**Nästa steg**
Om du inte kan lösa problemet med dessa värden kan du gå till något av följande...

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Andra referenser:**
  * [Konfigurera andra egenskaper för LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Konfigurera heap-serverns Heap-storlek](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Mappa kopplings minnes storlek för LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez körnings motor egenskaper](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [LLAP djup i Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
