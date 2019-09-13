---
title: Kapacitets planering för kluster i Azure HDInsight
description: Identifiera viktiga frågor för kapacitets-och prestanda planering för ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: 64de4078fb529140859f1d4ff2e973fd081a5400
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916564"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Kapacitets planering för HDInsight-kluster

Innan du distribuerar ett HDInsight-kluster bör du planera för önskad kluster kapacitet genom att fastställa nödvändiga prestanda och skalning. Den här planeringen hjälper till att optimera både användbarhet och kostnader. Vissa kluster kapacitets beslut kan inte ändras efter distributionen. Om prestanda parametrarna ändras kan ett kluster demonteras och återskapas utan att lagra data går förlorade.

Viktiga frågor att be om kapacitets planering är:

* I vilken geografisk region ska du distribuera klustret?
* Hur mycket lagrings utrymme behöver du?
* Vilken kluster typ ska du distribuera?
* Vilken storlek och vilken typ av virtuell dator (VM) ska använda när klusternoderna används?
* Hur många arbetsnoder bör ditt kluster ha?

## <a name="choose-an-azure-region"></a>Välj en Azure-region

Azure-regionen fastställer var klustret är fysiskt allokerat. För att minimera svars tiden för läsningar och skrivningar bör klustret vara nära dina data.

HDInsight är tillgängligt i många Azure-regioner. För att hitta den närmaste regionen, se *HDInsight* -posten under *analys* i [produkter tillgängliga per region](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Välj lagrings plats och storlek

### <a name="location-of-default-storage"></a>Placering av standard lagring

Standard lagringen, antingen ett Azure Storage konto eller Azure Data Lake Storage, måste finnas på samma plats som klustret. Azure Storage är tillgängligt på alla platser. Data Lake Storage Gen1 är tillgängligt i vissa regioner – se den aktuella Data Lake Storage tillgänglighet under *lagring* i [Azure-produkter tillgängliga per region](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Plats för befintliga data

Om du redan har ett lagrings konto eller Data Lake Storage som innehåller dina data och vill använda den här lagringen som klustrets standard lagring, måste du distribuera klustret på samma plats.

### <a name="storage-size"></a>Lagringsstorlek

När du har distribuerat ett HDInsight-kluster kan du koppla ytterligare Azure Storage-konton eller komma åt andra Data Lake Storage. Alla dina lagrings konton måste finnas på samma plats som klustret. En Data Lake Storage kan vara på en annan plats, men detta kan leda till att det finns en Läs-och skriv fördröjning.

Azure Storage har vissa [kapacitets begränsningar](../azure-subscription-service-limits.md#storage-limits), medan data Lake Storage gen1 är praktiskt taget obegränsat.

Ett kluster har åtkomst till en kombination av olika lagrings konton. Vanliga exempel är:

* När mängden data är troligt vis större än lagrings kapaciteten för en enskild Blob Storage-behållare.
* När frekvensen för åtkomst till BLOB-behållaren kan överskrida tröskelvärdet där begränsning sker.
* När du vill göra data har du redan laddat upp till en BLOB-behållare som är tillgänglig för klustret.
* När du vill isolera olika delar av lagringen av säkerhets skäl eller för att förenkla administrationen.

För ett kluster med 48 noder rekommenderar vi 4 till 8 lagrings konton. Även om det kanske redan finns tillräckligt med lagrings utrymme ger varje lagrings konto ytterligare nätverks bandbredd för Compute-noderna. När du har flera lagrings konton ska du använda ett slumpmässigt namn för varje lagrings konto utan ett prefix. Syftet med slumpmässig namngivning minskar risken för Flask halsar i lagring (begränsning) eller vanliga läges problem i alla konton. Använd endast en behållare per lagrings konto för bättre prestanda.

## <a name="choose-a-cluster-type"></a>Välj en kluster typ

Kluster typen avgör vilken arbets belastning ditt HDInsight-kluster är konfigurerat för att köras, till exempel [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)eller [Apache Spark](https://spark.apache.org/). En detaljerad beskrivning av tillgängliga kluster typer finns i [Introduktion till Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Varje kluster typ har en angiven distributions sto pol Ogin som innehåller krav för storlek och antal noder.

## <a name="choose-the-vm-size-and-type"></a>Välj storlek och typ för virtuell dator

Varje kluster typ har en uppsättning nodtyper och varje nodtyp har olika alternativ för deras VM-storlek och-typ.

För att fastställa den optimala kluster storleken för ditt program kan du öka kluster kapaciteten och öka storleken enligt vad som anges. Du kan till exempel använda en simulerad arbets belastning eller en *Kanarie-fråga*. Med en simulerad arbets belastning kan du köra dina förväntade arbets belastningar i olika storleks kluster, vilket ökar storleken gradvis tills önskade prestanda uppnås. En Kanarie-fråga kan infogas med jämna mellanrum bland de andra produktions frågorna för att visa om klustret har tillräckligt med resurser.

VM-storlek och-typ bestäms av processor processor kraft, RAM-storlek och nätverks fördröjning:

* CPU: Storleken på den virtuella datorn avgör antalet kärnor. Fler kärnor, desto högre grad av parallell beräkning som varje nod kan uppnå. Vissa VM-typer har dessutom snabbare kärnor.

* RAM: Storleken på den virtuella datorn avgör också mängden tillgängligt RAM-minne på den virtuella datorn. För arbets belastningar som lagrar data i minnet för bearbetning, i stället för att läsa från disk, se till att arbetsnoderna har tillräckligt med minne för att passa data.

* Nätverks För de flesta kluster typer finns inte de data som bearbetas av klustret på den lokala disken, utan i stället i en extern lagrings tjänst som Data Lake Storage eller Azure Storage. Överväg nätverks bandbredden och data flödet mellan den virtuella noden och lagrings tjänsten. Nätverks bandbredden som är tillgänglig för en virtuell dator ökar vanligt vis med större storlekar. Mer information finns i [Översikt över VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Välj kluster skala

Ett klusters skala bestäms av antalet VM-noder. För alla kluster typer finns det nodtyper som har en bestämd skala, och nodtyper som stöder skalbarhet. Ett kluster kan till exempel kräva exakt tre [Apache ZooKeeper](https://zookeeper.apache.org/) noder eller två huvudnoder. Arbetsnoder som hanterar data bearbetning i en distribuerad miljö kan dra nytta av att skala ut genom att lägga till ytterligare arbetsnoder.

Beroende på din kluster typ ökar antalet arbetsnoder ytterligare beräknings kapacitet (till exempel fler kärnor), men kan också lägga till den totala mängd minne som krävs för hela klustret för att stödja minnes lagring av data som bearbetas. Precis som med valet av VM-storlek och-typ, uppnås det vanligt vis att välja rätt kluster skalning med hjälp av simulerade arbets belastningar eller Kanarie-frågor.

Du kan skala ut klustret så att det uppfyller kraven på högsta belastning och skala sedan tillbaka det när de extra noderna inte längre behövs. Med [funktionen](hdinsight-autoscale-clusters.md) för automatisk skalning kan du skala klustret automatiskt baserat på fördefinierade mått och tids inställningar. Mer information om hur du skalar klustren manuellt finns i [skala HDInsight-kluster](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Kluster livs cykel

Du debiteras för ett klusters livstid. Om det bara finns vissa gånger som du behöver klustret igång kan du [skapa kluster på begäran med hjälp av Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Du kan också skapa PowerShell-skript som etablerar och tar bort ditt kluster och sedan schemalägga dessa skript med hjälp av [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> När ett kluster tas bort, tas även dess standard Hive-metaarkiv bort. Använd ett externt metadatalagret som Azure Database eller [Apache Oozie](https://oozie.apache.org/)om du vill spara metaarkiv för nästa kluster som skapas på nytt.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolera fel i kluster jobb

Ibland kan fel uppstå på grund av parallell körningen av flera Maps och minska komponenter i ett kluster med flera noder. För att hjälpa till att isolera problemet kan du testa distribuerade tester genom att köra samtidiga flera jobb på ett enda arbets nods kluster och sedan utöka den här metoden för att köra flera jobb samtidigt i kluster som innehåller mer än en nod. Om du vill skapa ett HDInsight-kluster med en nod i Azure använder du alternativet *Anpassad (storlek, inställningar, appar)* och använder värdet 1 för *antalet arbetsnoder* i **kluster storleken** när du konfigurerar ett nytt kluster i portalen.


## <a name="quotas"></a>Kvoter

När du har fastställt storlek, skalning och typ för mål klustret, kontrollerar du prenumerationens aktuella kvot kapacitets gränser. När du når en kvot gräns kanske du inte kan distribuera nya kluster eller skala ut befintliga kluster genom att lägga till fler arbetsnoder. Den enda kvot gränsen är den processor kärnors kvot som finns på region nivå för varje prenumeration. Din prenumeration kan till exempel ha en gräns på 30 kärnor i regionen USA, östra. Gör så här om du behöver begära en kvot ökning:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **Hjälp + Support** längst ned till vänster på sidan.
1. Välj på **nytt support ärende**.
1. På sidan **ny supportbegäran** , under fliken **grundläggande** , väljer du följande alternativ:
   - **Typ av problem**: **Begränsningar för tjänster och prenumerationer (kvoter)**
   - **Prenumeration**: den prenumeration du vill ändra
   - **Kvot typ**: **HDInsight**
    
     ![Skapa en support förfrågan för att öka HDInsight Core-kvoten](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Välj **Nästa: Lösningar > >** .
1. På sidan **information** anger du en beskrivning av problemet, väljer allvarlighets graden för problemet, önskad kontakt metod och andra obligatoriska fält.
1. Välj **Nästa: Granska + skapa > >** .
1. På fliken **Granska och skapa** väljer du **skapa**.

> [!NOTE]  
> Om du behöver öka HDInsight Core-kvoten i en privat region [skickar du en vitlista-begäran](https://aka.ms/canaryintwhitelist).

Du kan [kontakta supporten för att begära en kvot ökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Det finns dock vissa fasta kvot gränser, till exempel om en enda Azure-prenumeration kan ha högst 10 000 kärnor. Mer information om dessa begränsningar finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera kluster i HDInsight med Apache Hadoop, Spark, Kafka och mycket mer](hdinsight-hadoop-provision-linux-clusters.md): Lär dig hur du konfigurerar och konfigurerar kluster i HDInsight med Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML-tjänster eller storm.
* [Övervaka kluster prestanda](hdinsight-key-scenarios-to-monitor.md): Lär dig mer om nyckel scenarier för att övervaka ditt HDInsight-kluster som kan påverka klustrets kapacitet.
