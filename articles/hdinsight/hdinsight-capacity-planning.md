---
title: Planering av klusterkapacitet i Azure HDInsight
description: Identifiera viktiga frågor för kapacitets- och prestandaplanering för ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272648"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Kapacitetsplanering för HDInsight-kluster

Planera för önskad klusterkapacitet innan du distribuerar ett HDInsight-kluster genom att bestämma den prestanda och skala som behövs. Den här planeringen hjälper till att optimera både användbarhet och kostnader. Vissa beslut om klusterkapacitet kan inte ändras efter distributionen. Om prestandaparametrarna ändras kan ett kluster demonteras och återskapas utan att förlora lagrade data.

De viktigaste frågorna att ställa för kapacitetsplanering är:

* I vilken geografisk region ska du distribuera klustret?
* Hur mycket lagringsutrymme behöver du?
* Vilken klustertyp ska du distribuera?
* Vilken storlek och typ av virtuell dator (VM) ska klusternoderna använda?
* Hur många arbetsnoder ska klustret ha?

## <a name="choose-an-azure-region"></a>Välj en Azure-region

Azure-regionen avgör var klustret är fysiskt etablerat. För att minimera svarstiden för läsningar och skrivningar bör klustret vara nära dina data.

HDInsight är tillgängligt i många Azure-regioner. Information om hur du hittar närmaste region finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Välj lagringsplats och storlek

### <a name="location-of-default-storage"></a>Plats för standardlagring

Standardlagringen, antingen ett Azure Storage-konto eller Azure Data Lake Storage, måste finnas på samma plats som klustret. Azure Storage är tillgängligt på alla platser. DataSjölagring Gen1 är tillgängligt i vissa regioner – se den aktuella [tillgängligheten för datasjölagring](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Plats för befintliga data

Om du redan har ett lagringskonto eller DataSjölagring som innehåller dina data och vill använda den här lagringen som klustrets standardlagring måste du distribuera klustret på samma plats.

### <a name="storage-size"></a>Lagringsstorlek

När du har distribuerat ett HDInsight-kluster kan du bifoga ytterligare Azure Storage-konton eller komma åt andra DataSjölagring. Alla dina lagringskonton måste finnas på samma plats som klustret. En Data Lake Storage kan vara på en annan plats, även om detta kan medföra vissa dataläs/skrivfördröjning.

Azure Storage har vissa [kapacitetsgränser,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)medan Data Lake Storage Gen1 är praktiskt taget obegränsad.

Ett kluster kan komma åt en kombination av olika lagringskonton. Typiska exempel är:

* När mängden data sannolikt kommer att överstiga lagringskapaciteten för en enda blob lagringsbehållare.
* När åtkomsthastigheten till blob-behållaren kan överskrida tröskelvärdet där begränsning sker.
* När du vill göra data har du redan laddat upp till en blob-behållare tillgänglig för klustret.
* När du vill isolera olika delar av lagringen av säkerhetsskäl eller förenkla administrationen.

För bättre prestanda, använd bara en behållare per lagringskonto.

## <a name="choose-a-cluster-type"></a>Välj en klustertyp

Klustertypen avgör vilken arbetsbelastning ditt HDInsight-kluster är konfigurerat för att köra, till exempel [Apache Hadoop,](https://hadoop.apache.org/) [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)eller [Apache Spark](https://spark.apache.org/). En detaljerad beskrivning av tillgängliga klustertyper finns i [Introduktion till Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Varje klustertyp har en specifik distributionstopologi som innehåller krav för storleken och antalet noder.

## <a name="choose-the-vm-size-and-type"></a>Välj storlek och typ av virtuell dator

Varje klustertyp har en uppsättning nodtyper och varje nodtyp har specifika alternativ för sin vm-storlek och typ.

Om du vill bestämma den optimala klusterstorleken för ditt program kan du jämföra klusterkapacitet och öka storleken enligt vad som anges. Du kan till exempel använda en simulerad arbetsbelastning eller en *kanariefråga*. Med en simulerad arbetsbelastning kör du dina förväntade arbetsbelastningar på kluster av olika storlek, vilket ökar storleken tills önskad prestanda har uppnåtts. En kanariefågelfråga kan infogas med jämna mellanrum bland de andra produktionsfrågorna för att visa om klustret har tillräckligt med resurser.

Mer information om hur du väljer rätt vm-familj för din arbetsbelastning finns i [Välja rätt vm-storlek för klustret](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Välj klusterskala

Ett klusters skala bestäms av antalet vm-noder. För alla klustertyper finns det nodtyper som har en viss skala och nodtyper som stöder utskalning. Ett kluster kan till exempel kräva exakt tre [Apache ZooKeeper-noder](https://zookeeper.apache.org/) eller två head-noder. Arbetsnoder som bearbetar data på ett distribuerat sätt kan dra nytta av att skala ut genom att lägga till ytterligare arbetsnoder.

Beroende på klustertypen lägger ökande antal arbetsnoder till ytterligare beräkningskapacitet (till exempel fler kärnor), men kan också öka den totala mängden minne som krävs för hela klustret för att stödja minneslagring av data som bearbetas. Precis som med valet av vm-storlek och typ uppnås vanligtvis valet av rätt klusterskala empiriskt med hjälp av simulerade arbetsbelastningar eller kanariefrågor.

Du kan skala ut klustret för att uppfylla belastningskraven och sedan skala ned det när de extra noderna inte längre behövs. [Med funktionen Automatisk skalning](hdinsight-autoscale-clusters.md) kan du automatiskt skala klustret baserat på förutbestämda mått och tidpunkter. Mer information om hur du skalar klustren manuellt finns i [Skala HDInsight-kluster](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Klustrets livscykel

Du debiteras för ett klusters livstid. Om det bara finns specifika tider som du behöver igång [klustret](hdinsight-hadoop-create-linux-clusters-adf.md)kan du skapa kluster på begäran med Azure Data Factory . Du kan också skapa PowerShell-skript som etablerar och tar bort klustret och sedan schemalägga skripten med [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> När ett kluster tas bort tas även dess standarda Hive-metabutik bort. Om du vill spara metabutiken för nästa kluster återskapar du använder ett externt metadataarkiv som Azure Database eller [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolera klusterjobbfel

Ibland kan fel uppstå på grund av parallell körning av flera kartor och minska komponenter i ett kluster med flera noder. Om du vill isolera problemet kan du prova distribuerade tester genom att köra samtidiga jobb i ett enda arbetsnodkluster och sedan expandera den här metoden för att köra flera jobb samtidigt i kluster som innehåller mer än en nod. Om du vill skapa ett HDInsight-kluster med en nod i Azure använder du alternativet *Custom(size,settings,apps)* och använder värdet 1 för *antal arbetsnoder* i avsnittet **Klusterstorlek** när du etablerar ett nytt kluster i portalen.

## <a name="quotas"></a>Kvoter

När du har fastställt storleken på den virtuella datorn i målklustret, skala och skriva in kontrollerar du de aktuella kvotkapacitetsgränserna för din prenumeration. När du når en kvotgräns kanske du inte kan distribuera nya kluster eller skala ut befintliga kluster genom att lägga till fler arbetsnoder. Den enda kvotgränsen är cpu-kärnorna kvot som finns på regionnivå för varje prenumeration. Din prenumeration kan till exempel ha 30 kärngräns i regionen östra USA. 

Så här kontrollerar du tillgängliga kärnor:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till **översiktssidan** för HDInsight-klustret. 
3. Klicka på **Kvotgränser**på den vänstra menyn.

   På sidan visas antalet kärnor som används, antalet tillgängliga kärnor och de totala kärnorna.

Om du behöver begära en kvotökning gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Stöd + stöd** längst ned till vänster på sidan.
1. Välj **Ny supportbegäran**.
1. På sidan **Ny supportbegäran** väljer du följande alternativ under fliken **Grunderna:**

   - **Ärendetyp**: **Service- och prenumerationsgränser (kvoter)**
   - **Prenumeration:** prenumerationen du vill ändra
   - **Typ av kvot**: **HDInsight**

     ![Skapa en supportbegäran för att öka kärnkvoten hdinsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Välj **Nästa: Lösningar >>**.
1. På sidan **Information** anger du en beskrivning av problemet, väljer allvarlighetsgraden för problemet, önskad kontaktmetod och andra obligatoriska fält.
1. Välj **Nästa: Granska + skapa >>**.
1. Välj **Skapa**på fliken **Granska + skapa** .

> [!NOTE]  
> Om du behöver öka kärnkvoten HDInsight i en privat region [skickar du en begäran om vitlista](https://aka.ms/canaryintwhitelist).

Du kan [kontakta supporten för att begära en kvotökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Det finns dock vissa fasta kvotgränser, till exempel kan en enda Azure-prenumeration ha högst 10 000 kärnor. Mer information om dessa begränsningar finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Spark, Kafka med mera:](hdinsight-hadoop-provision-linux-clusters.md)Lär dig hur du konfigurerar och konfigurerar kluster i HDInsight med Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services eller Storm.
* [Övervaka klusterprestanda:](hdinsight-key-scenarios-to-monitor.md)Lär dig mer om viktiga scenarier som övervakar för ditt HDInsight-kluster som kan påverka klustrets kapacitet.
