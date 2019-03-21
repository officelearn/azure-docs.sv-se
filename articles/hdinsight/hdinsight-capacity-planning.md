---
title: Kapacitetsplanering för kluster i Azure HDInsight
description: Så här anger du ett HDInsight-kluster för kapacitet och prestanda.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: hrasheed
ms.openlocfilehash: b35c5073f2b19523010528800c2a989d5de5b448
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848021"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Kapacitetsplanering för HDInsight-kluster

Planera för den önskade klusterkapacitet genom att fastställa nödvändiga prestanda och skalning innan du distribuerar ett HDInsight-kluster. Den här planeringen hjälper dig att optimera både användbarhet och kostnader. Vissa kluster kapacitetsbeslut kan inte ändras efter distributionen. Ändrar parametrarna prestanda, ett kluster bort och återskapas utan att förlora lagrade data.

De viktiga frågorna för kapacitetsplanering är:

* I vilka geografiska region bör du distribuera ett kluster?
* Hur mycket lagringsutrymme behöver du?
* Vilken typ av kluster bör du distribuera?
* Vilken storlek och typ av virtuell dator (VM) ska klusternoderna använda?
* Hur många arbetsnoder bör ha med ditt kluster?

## <a name="choose-an-azure-region"></a>Välj en Azure-region

Azure-regionen anger där klustret fysiskt har etablerats. För att minimera svarstiden för läsningar och skrivningar, ska klustret vara nära dina data.

HDInsight är tillgängligt i många Azure-regioner. Den närmaste regionen finns i den *HDInsight* posten under *Analytics* i [tillgängliga produkter efter Region](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Välj lagringsplats och storlek

### <a name="location-of-default-storage"></a>Platsen för standardlagring

Standardlagring, antingen ett Azure Storage-konto eller Azure Data Lake Storage måste vara på samma plats som klustret. Azure Storage är tillgängligt på alla platser. Data Lake Storage Gen1 är tillgänglig i vissa regioner – finns i den aktuella Data Lake Storage-tillgängligheten under *Storage* i [Azure-produkter tillgängliga efter Region](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Platsen för befintliga data

Om du redan har ett lagringskonto eller Data Lake Storage som innehåller dina data och vill använda den här lagringen som standard klusterlagring, måste du distribuera ditt kluster på den samma platsen.

### <a name="storage-size"></a>Lagringsstorlek

När du har ett HDInsight-kluster som har distribuerats kan du koppla ytterligare Azure Storage-konton eller få åtkomst till andra Data Lake Storage. Alla lagringskonton måste finnas på samma plats som klustret. Ett Data Lake-lagring kan vara på en annan plats, men detta kan innebära att vissa data läsning och skrivning.

Azure Storage har några [kapacitetsbegränsningar](../azure-subscription-service-limits.md#storage-limits), medan Data Lake Storage Gen1 är praktiskt taget obegränsade.

Ett kluster kan komma åt en kombination av olika lagringskonton. Vanliga exempel:

* När mängden data som riskerar att överskrida lagringskapaciteten för en enda blob storage-behållare.
* När frekvensen för åtkomst till blob-behållaren kan överskrida tröskelvärdet där begränsning inträffar.
* När du vill göra data har du redan överfört till en blob-behållare som är tillgängliga för klustret.
* När du vill att isolera olika delar av lagring på grund av säkerhet eller för att förenkla administrationen.

För ett kluster med 48 noder rekommenderar vi 4 till 8 storage-konton. Även om det kan redan finnas tillräckligt med totalt lagringsutrymme, tillhandahåller varje lagringskonto ytterligare nätverkets bandbredd för compute-noder. När du har flera lagringskonton kan du använda ett slumpmässigt namn för varje lagringskonto, utan ett prefix. Syftet med slumpmässiga naming minskar risken för storage flaskhalsar (begränsningen) eller läge för vanliga fel för alla konton. Använd bara en behållare per lagringskonto för bättre prestanda.

## <a name="choose-a-cluster-type"></a>Välj en typ av kluster

Klustertypen anger arbetsbelastningen i ditt HDInsight-kluster är konfigurerad för att köras som [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/), eller [ Apache Spark](https://spark.apache.org/). En detaljerad beskrivning av de tillgängliga typerna finns i [introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Varje typ av kluster har en specifik distributionstopologi som innehåller kraven för storlek och antalet noder.

## <a name="choose-the-vm-size-and-type"></a>Välj VM-storlek och typ

Varje typ av kluster har en uppsättning nodtyper och varje nodtyp har specifika alternativ för VM-storlek och typ.

Du kan benchmark klusterkapacitet och öka storleken som anges för att fastställa optimal klusterstorleken för ditt program. Du kan till exempel använda en simulerade arbetsbelastning eller ett *kontrollvärde fråga*. Med en simulerad arbetsbelastning kör du din förväntade arbetsbelastningar på en annan storlek för kluster gradvis öka diskstorleken tills önskad prestanda uppnås. En kontrollvärde fråga kan infogas regelbundet bland andra frågor för produktion att visa om klustret har tillräckligt med resurser.

VM-storlek och typ bestäms av CPU-belastningen ström, RAM-storleken och svarstid för nätverk:

* CPU: VM-storleken avgör hur många kärnor. Fler kärnor, desto större grad av parallell beräkning varje nod kan uppnå. Vissa typer av virtuella datorer har också snabbare kärnor.

* RAM: VM-storleken avgör också mängden RAM-minne tillgängligt på den virtuella datorn. Ha tillräckligt med minne för att passa informationen för arbetsbelastningar som lagrar data i minnet för bearbetning i stället för att läsa från disken, se till att dina arbetarnoder.

* Nätverk: För de flesta klustertyper av är data som bearbetas av klustret inte på den lokala disken, men i en extern storage-tjänst, till exempel Data Lake Storage eller Azure Storage. Överväg att nätverkets bandbredd och dataflödet mellan noden VM och storage-tjänsten. Nätverkets bandbredd tillgänglig för en virtuell dator ökar vanligtvis med större storlekar. Mer information finns i [Virtuella datorer översikt](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Välj skala för kluster

Skala ett kluster bestäms av antalet Virtuella noder. För samtliga klustertyper finns nodtyper som har en specifik skala och nodtyper som har stöd för skalbar. Ett kluster kan till exempel kräva exakt tre [Apache ZooKeeper](https://zookeeper.apache.org/) noder eller två huvudnoder. Arbetsnoder som gör databearbetning i ett distribuerat sätt dra nytta av utskalning, genom att lägga till ytterligare arbetsnoder.

Beroende på din typ av kluster, ökar antalet arbetsnoder lägger till ytterligare beräkningskapacitet (till exempel fler kärnor), men kan också lägga till den totala mängden minne som krävs att stödja minnesintern lagring av data som bearbetas hela klustret. Precis som med valet av VM-storlek och typ, är att välja rätt kluster skalan vanligtvis nått empiriskt, med simulerade arbetsbelastningar eller kontrollvärde frågor.

Du kan skala ut klustret för att möta toppefterfrågan belastningen och sedan skala ned när dessa extra noder inte längre behövs. Mer information finns i [skala HDInsight-kluster](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Klustrets livscykel

Du debiteras för ett kluster livslängd. Om det finns endast specifika tidpunkter som du behöver dina kluster vara igång och körs, kan du [Skapa kluster på begäran med Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Du kan också skapa PowerShell-skript som etablerar eller ta bort ditt kluster och sedan schemalägger du dessa skript med hjälp av [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> När ett kluster tas bort så raderas även dess standard Hive-metaarkiv. Använda extern metadatalagring som Azure Database för att bevara metaarkiv för nästa nytt skapa ett kluster eller [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolera kluster jobbfel

Ibland fel kan inträffa på grund av parallell körning av flera maps och minska komponenter i ett kluster med flera noder. För att isolera problemet, försök distribuerade testning genom att köra samtidiga expandera sedan den här metoden för att köra flera jobb samtidigt på kluster som innehåller fler än en nod i flera jobb på en nod i klustret. Du kan skapa ett HDInsight-kluster med en nod i Azure med den *avancerade* alternativet.

Du kan också installera en nod-utvecklingsmiljö på den lokala datorn och testa lösningen där. Hortonworks innehåller en enda nod lokal utvecklingsmiljö för Hadoop-baserade lösningar som är användbara för inledande funktionstest av, utveckling och testning. Mer information finns i [Hortonworks Sandbox](https://hortonworks.com/products/hortonworks-sandbox/).

För att identifiera problemet på ett lokalt kluster för en nod kan du köra misslyckade jobb och justera indata eller använda mindre datauppsättningar. Hur du kör dessa jobb beror på vilken plattform och typ av program.

## <a name="quotas"></a>Kvoter

När du har fastställt din målklustret VM-storlek, skala och typ, kan du kontrollera aktuella kvotgränserna av kapacitet för din prenumeration. När du når en kvotgräns kan kanske du inte att distribuera nya kluster eller skala ut befintliga kluster genom att lägga till fler arbetsnoder. Endast kvotgränsen är kvoten CPU-kärnor som finns på regionsnivån för varje prenumeration. Din prenumeration kan exempelvis ha 30 kärngräns i regionen östra USA. Om du vill öka kvoten gör du följande:

1. Gå till Azure Portal
1. Klicka på **hjälp och Support** längst ned till vänster på sidan.
1. Klicka på **ny supportbegäran**.
1. På den **ny supportbegäran** sidan under **grunderna** väljer du följande alternativ:
   - **Typ av problem**: **Begränsningar för tjänsten och -prenumeration (kvoter)**
   - **Prenumeration**: den prenumeration som du vill ändra
   - **Typ av kvot**: **HDInsight**
    
     ![Skapa en supportbegäran om att öka kärnkvoten för HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Klicka på **Nästa**.
1. På den **information** , ange en beskrivning av problemet, Välj problemets allvarlighetsgrad och välj önskad kontaktmetod.
1. Klicka på **Nästa: Granska + skapa**.
1. På den **granska + skapa** fliken **skapa**.

> [!NOTE]  
> Om du vill öka kärnkvoten för HDInsight i ett privat område [begära en lista över tillåtna](https://aka.ms/canaryintwhitelist).

Du kan [kontakta supporten för att öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Men det finns vissa fast kvotgränser, till exempel en enskild Azure-prenumeration kan ha högst 10 000 kärnor. Mer information om dessa begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Spark, Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md): Lär dig mer om att installera och konfigurera kluster i HDInsight med Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML-tjänster eller Storm.
* [Övervaka klusterprestanda](hdinsight-key-scenarios-to-monitor.md): Läs mer om viktiga scenarier för att övervaka ditt HDInsight-kluster som kan påverka din klustrets kapacitet.
