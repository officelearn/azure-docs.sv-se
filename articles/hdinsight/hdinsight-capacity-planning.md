---
title: Klustret kapacitetsplanering i Azure HDInsight | Microsoft Docs
description: "Så här anger du ett HDInsight-kluster för kapacitet och prestanda."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: b4bdf3339e585a7b22a1945871f802854020fb94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Kapacitetsplanering för HDInsight-kluster

Planera för kapacitet önskade klustret genom att bestämma nödvändiga prestanda och skalning innan du distribuerar ett HDInsight-kluster. Den här planeringen hjälper dig att optimera både användbarhet och kostnader. Vissa klustret kapacitetsbeslut kan inte ändras efter distributionen. Ändrar parametrarna prestanda, ett kluster bort och återskapas utan att förlora lagrade data.

De viktiga frågorna om kapacitetsplanering finns:

* I vilka geografiska region bör du distribuera ditt kluster?
* Hur mycket lagringsutrymme behöver du?
* Vilken typ av kluster bör du distribuera?
* Storlek och typ av virtuell dator (VM) ska klusternoderna använda?
* Hur många arbetarnoder om klustret har?

## <a name="choose-an-azure-region"></a>Välj en Azure-region

Azure-regionen anger där klustret fysiskt har etablerats. Om du vill minimera svarstiden för läsning och skrivning ska klustret nära dina data.

HDInsight finns i många Azure-regioner. Du hittar den närmaste regionen i *HDInsight Linux* posten under *Data + analys* i [Azure produkter tillgängliga efter Region](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Välj lagringsplats och storlek

### <a name="location-of-default-storage"></a>Platsen för standardlagring

Standard-lagringen, ett Azure Storage-konto eller Azure Data Lake Store måste vara på samma plats som klustret. Azure Storage är tillgänglig på alla platser. Data Lake Store är tillgänglig i vissa regioner – Se aktuella Data Lake Store-tillgänglighet under *lagring* i [Azure produkter tillgängliga efter Region](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Platsen för befintliga data

Om du redan har ett lagringskonto eller Datasjölager som innehåller dina data och vill använda den här lagringen som standard klusterlagringen, måste du distribuera ditt kluster där samma.

### <a name="storage-size"></a>Lagringsstorlek

När du har ett HDInsight-kluster som har distribuerats kan du bifoga ytterligare Azure Storage-konton eller åtkomst till andra Data Lake butiker. Alla lagringskonton måste finnas på samma plats som klustret. Ett Data Lake Store kan vara på en annan plats, men detta kan innebära att vissa svarstid för läsning och skrivning av data.

Azure Storage har vissa [kapacitetsbegränsningar](../azure-subscription-service-limits.md#storage-limits), medan Data Lake Store är praktiskt taget obegränsade.

Ett kluster kan komma åt en kombination av olika lagringskonton. Vanliga exempel:

* När mängden data som riskerar att överskrida lagringskapaciteten för en enda blob storage-behållare.
* När åtkomst till blob-behållare kan överstiga tröskelvärdet var begränsning inträffar.
* När du vill göra data har du redan överfört till en blob-behållare som är tillgängliga för klustret.
* När du vill isolera olika delar av lagring på grund av säkerhet eller för att förenkla administrationen.

Vi rekommenderar 4 till 8 storage-konton i ett kluster 48-noder. Även om det kan redan finnas tillräckligt med totalt lagringsutrymme, tillhandahåller varje lagringskonto ytterligare nätverk bandbredd för compute-noder. När du har flera lagringskonton, använder du ett slumpmässigt namn för varje lagringskonto, utan ett prefix. Syftet med slumpmässiga naming är att minska risken för lagring flaskhalsar (begränsning) eller gemensamma läge fel alla konton. Använd endast en behållare per lagringskonto för bättre prestanda.

## <a name="choose-a-cluster-type"></a>Välj en typ av kluster

Typ av kluster anger arbetsbelastningen ditt HDInsight-kluster är konfigurerat för att köras, till exempel Hadoop, Storm, Kafka eller Spark. 
<!-- For a detailed description of the available cluster types, see [HDInsight Architecture](hdinsight-architecture.md). -->
Varje typ av kluster har en specifik distributionstopologi som innehåller kraven för storlek och antalet noder.

## <a name="choose-the-vm-size-and-type"></a>Välj VM-storlek och typ

Varje typ av kluster har en uppsättning nodtyper och varje nodtyp har specifika alternativ för deras VM-storlek och typ.

Ta reda optimala klusterstorleken för ditt program kan du mäta klustret kapacitet och öka storleken som anges. Du kan till exempel använda en simulerad arbetsbelastning eller ett *Kanarieöarna frågan*. Med en simulerad arbetsbelastning kör du förväntade arbetsbelastningar på olika storlek kluster gradvis öka storleken på tills önskad prestanda uppnås. En Kanarieöarna fråga kan infogas med jämna mellanrum mellan produktion frågor ska visas om klustret har tillräckligt med resurser.

VM-storlek och typ bestäms av CPU-belastningen ström, ram-storlek och svarstid för nätverk:

* Processor: VM-storlek avgör antal kärnor. Flera kärnor, större grad av parallella beräkning uppnå varje nod. Dessutom har vissa typer av VM snabbare kärnor.

* RAM-minne: VM-storlek avgör också mängden RAM-minne som är tillgängliga i den virtuella datorn. Ha tillräckligt med minne för att passa informationen för arbetsbelastningar som lagrar data i minnet för bearbetning, i stället för att läsa från disken, kontrollera din arbetarnoder.

* Nätverk: För de flesta klustertyper data bearbetas av klustret är inte på lokal disk, utan i en extern storage-tjänst, till exempel Data Lake Store eller Azure Storage. Överväg att nätverkets bandbredd och dataflöde mellan noden VM och storage-tjänst. Nätverkets bandbredd tillgänglig för en virtuell dator ökar vanligtvis med större storlek. Mer information finns i [VM-storlekar översikt](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Välj klustret skalan

Skala ett kluster bestäms av antalet Virtuella noder. För alla typer av klustret finns nodtyper som har en specifik skala och nodtyper som har stöd för skalbar. Ett kluster kan till exempel kräva exakt tre ZooKeeper-noder eller två Head-noder. Arbetsnoder som gör databehandling i en distribuerad sätt kan dra nytta skala ut, genom att lägga till ytterligare arbetsnoderna.

Beroende på din typ av kluster, ökar antalet arbetarnoder lägger till ytterligare beräkningar kapacitet (till exempel flera kärnor), men kan också lägga till den totala mängden minne som krävs att stödja InMemory-lagring av data som bearbetas hela klustret. Precis som med valet av VM-storlek och typ, uppnås välja rätt klustret skala vanligtvis empiriskt, med hjälp av simulerad arbetsbelastningar eller Kanarieöarna frågor.

Du kan skala ut klustret för att uppfylla kraven för inläsning av belastning och skala ned när de extra noderna inte längre behövs.
<!-- - see [Scaling - best practices](hdinsight-scaling-best-practices.md). -->

### <a name="cluster-lifecycle"></a>Klustret livscykel

Du debiteras för ett kluster livslängd. Om det finns endast vissa tider som du behöver ditt kluster upp och körs, kan du skapa på begäran-kluster med Azure Data Factory.
<!-- [create on-demand clusters using Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). -->
Du kan också skapa PowerShell-skript som etablerar och ta bort klustret och sedan schemalägga dessa skript med hjälp av [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> När ett kluster tas bort raderas även dess standard Hive metastore. Använd en extern metadataarkivet, t.ex Azure-databas eller Oozie för att bevara metastore för nästa återskapar för klustret.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolera klustret jobbfel

Ibland fel kan inträffa på grund av parallell körning av flera kartan och minska komponenter på ett flernodigt kluster. För att isolera problemet, distribuerade testar genom att köra samtidiga expandera flera jobb på en nod i klustret den här metoden körs flera jobb samtidigt på kluster som innehåller fler än en nod. Så här skapar du ett HDInsight-kluster med en nod i Azure i *avancerade* alternativet.

Du kan också installera en enda nod utvecklingsmiljö på den lokala datorn och testa det lösningen. Hortonworks innehåller en enda nod lokala utvecklingsmiljö för Hadoop-baserade lösningar som är användbar för inledande bevis på koncept, utveckling och testning. Mer information finns i [Hortonworks Sandbox](http://hortonworks.com/products/hortonworks-sandbox/).

För att identifiera problemet på en enda nod lokala klustret kan du köra misslyckade jobb igen och justera indata eller använda mindre datauppsättningar. Hur du kör dessa jobb beror på plattformen och typ av program.

## <a name="quotas"></a>Kvoter

När du har fastställt din målkluster VM-storlek, skala och typ, kan du kontrollera aktuella kvotgränserna av kapacitet för din prenumeration. När du har kommit en kvotgräns kanske du inte att distribuera nya kluster eller skala ut befintliga kluster genom att lägga till flera arbetsnoderna. Den vanligaste kvot uppnåtts är processorkärnor kvoten som finns vid prenumeration, region och VM-serien nivåer. Din prenumeration kan exempelvis ha 200 kärnor överstiger, med en 30 core gräns i din region och maximalt 30 kärnor på VM-instanser. Du kan [kontakta supporten om du vill begära en ökad kvot](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Men det finns vissa fast kvotgränserna, till exempel en enda Azure-prenumeration kan ha högst 10 000 kärnor. Mer information om dessa begränsningar finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Nästa steg

* [Ställ in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](hdinsight-hadoop-provision-linux-clusters.md): Lär dig hur du skapar och konfigurerar kluster i HDInsight med Hadoop, Spark, Kafka, interaktiva Hive, HBase, R Server eller Storm.
* [Övervaka klusterprestanda](hdinsight-key-scenarios-to-monitor.md): Lär dig mer om viktiga scenarier för att övervaka ditt HDInsight-kluster som kan påverka ditt kluster kapacitet.
