---
title: Viktig information för Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 54e28a9d434500915aa8cc8e07ade3592e5aa96c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185606"
---
# <a name="release-notes"></a>Viktig information

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med Apache Hadoop med öppen källkod och Apache Spark på Azure.

## <a name="release-date-11072019"></a>Utgivnings datum: 11/07/2019

Den här versionen gäller både för HDInsight 3,6 och 4,0.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [artikeln om versions hantering i HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nya funktioner

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (HIB) (för hands version)

Med HDInsight Identity Broker (HIB) kan användare logga in på Apache Ambari med Multi-Factor Authentication (MFA) och hämta de nödvändiga Kerberos-biljetterna utan att behöva lösen ords-hashar i Azure Active Directory Domain Services (AAD-DS). För närvarande är HIB endast tillgängligt för kluster som distribueras via ARM-mallen.

### <a name="kafka-rest-api-proxy-preview"></a>Kafka REST API-proxy (för hands version)

Kafka REST API-proxy tillhandahåller en distribution av en REST-proxy med hög tillgänglighet med Kafka-kluster via säker AAD-auktorisering och OAuth-protokoll. 

### <a name="auto-scale"></a>Autoskala

Autoskalning för Azure HDInsight är nu allmänt tillgängligt i alla regioner för Apache Spark-och Hadoop-kluster typer. Den här funktionen gör det möjligt att hantera stora data analys arbets belastningar på ett mer kostnads effektivt och produktivt sätt. Nu kan du optimera användningen av HDInsight-kluster och bara betala för det du behöver.

Beroende på dina behov kan du välja mellan inläsningsbaserad och schemabaserad autoskalning. Med load-based autoskalning kan du skala kluster storleken upp och ned baserat på de aktuella resurs behoven när den schemabaserade autoskalning kan ändra kluster storleken baserat på ett fördefinierat schema. 

Stöd för automatisk skalning för HBase och LLAP-arbetsbelastning är också offentlig för hands version. Mer information finns i [skala Azure HDInsight-kluster automatiskt](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>HDInsight-accelererade skrivningar för Apache HBase 

Accelererade skrivningar använder Azure Premium SSD-hanterade diskar för att förbättra prestanda hos Apache HBase Write Ahead Log (WAL). Mer information finns i [Azure HDInsight-accelererade skrivningar för Apache HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes).

### <a name="custom-ambari-db"></a>Anpassad Ambari-databas

HDInsight erbjuder nu en ny kapacitet som gör det möjligt för kunder att använda sin egen SQL-databas för Ambari. Nu kan kunderna välja rätt SQL DB för Ambari och enkelt uppgradera den baserat på deras egna krav för företags tillväxt. Distributionen görs med en Azure Resource Manager-mall. Mer information finns i [Konfigurera HDInsight-kluster med en anpassad Ambari-databas](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Virtuella datorer i F-serien är nu tillgängliga med HDInsight

Virtuella datorer i F-serien (VM) är ett bra alternativ för att komma igång med HDInsight med krav på ljus bearbetning. F-serien har ett lägre listpris per timme och har bästa prisprestanda i Azure-portföljen baserat på Azure-beräkningsenhet (ACU, Azure Compute Unit) per virtuell processor. Mer information finns i [välja rätt VM-storlek för ditt Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Utfasning

### <a name="g-series-virtual-machine-deprecation"></a>Utfasning av virtuella datorer i G-serien
I den här versionen erbjuds virtuella datorer i G-serien inte längre i HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>DV1 för virtuell dator
I den här versionen är användningen av virtuella DV1-datorer med HDInsight inaktuell. Alla kund förfrågningar för DV1 kommer att behandlas automatiskt med Dv2. Det finns ingen pris skillnad mellan DV1-och Dv2-VM: ar.

## <a name="behavior-changes"></a>Beteende ändringar

### <a name="cluster-managed-disk-size-change"></a>Storleks ändring för kluster hanterad disk
HDInsight tillhandahåller hanterat disk utrymme med klustret. I den här versionen ändras den hanterade disk storleken för varje nod i det nya skapade klustret till 128 GB.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Från och med december används skalnings uppsättningar för virtuella Azure-datorer i stället. Läs mer om [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBase 2,0 till 2,1
I den kommande HDInsight 4,0-versionen kommer HBase-versionen att uppgraderas från version 2,0 till 2,1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Utfasning av virtuella datorer i A-serien för ESP-kluster
Virtuella datorer i A-serien kan orsaka problem med ESP-kluster på grund av relativt låg processor och minnes kapacitet. I den kommande versionen är virtuella datorer i A-serien inaktuella för att skapa nya ESP-kluster.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring har ändrats för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 AD HDInsight 3,6 [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
