---
title: Viktig information för Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435434"
---
# <a name="release-notes"></a>Viktig information

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med Apache Hadoop med öppen källkod och Apache Spark på Azure.

## <a name="release-date-12172019"></a>Utgivnings datum: 12/17/2019

Den här versionen gäller både för HDInsight 3,6 och 4,0.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [artikeln om versions hantering i HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nya funktioner

### <a name="service-tags"></a>Tjänsttaggar
Service märken fören klar säkerheten för virtuella Azure-datorer och virtuella Azure-nätverk genom att göra det möjligt att enkelt begränsa nätverks åtkomsten till Azure-tjänsterna. Du kan använda tjänst Taggar i reglerna för nätverks säkerhets gruppen (NSG) för att tillåta eller neka trafik till en viss Azure-tjänst globalt eller per Azure-region. Azure tillhandahåller underhåll av IP-adresser som är underliggande för varje tagg. HDInsight Service-taggar för nätverks säkerhets grupper (NSG: er) är grupper med IP-adresser för hälso-och hanterings tjänster. Dessa grupper bidrar till att minimera komplexiteten vid skapande av säkerhets regler. HDInsight-kunder kan aktivera Service tag via Azure Portal, PowerShell och REST API. Mer information finns i [tjänst taggar för nätverks säkerhets grupper (NSG) för Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Anpassad Ambari-databas
Med HDInsight kan du nu använda din egen SQL-databas för Apache Ambari. Du kan konfigurera den här anpassade Ambari-databasen från Azure Portal eller via Resource Manager-mall.  Med den här funktionen kan du välja rätt SQL-databas för bearbetnings-och kapacitets behoven. Du kan också uppgradera enkelt för att matcha företagets tillväxt krav. Mer information finns i [Konfigurera HDInsight-kluster med en anpassad Ambari-databas](hdinsight-custom-ambari-db.md).

![Anpassad Ambari-databas](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Utfasning
Inga utfasningar för den här versionen. För att komma igång med kommande inaktuella ändringar, se [kommande ändringar](#upcoming-changes).

## <a name="behavior-changes"></a>Funktionalitetsförändringar
Inga beteende ändringar för den här versionen. Om du vill komma igång med kommande funktions ändringar, se [kommande ändringar](#upcoming-changes).

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Transport Layer Security (TLS) 1,2-tvång
Transport Layer Security (TLS) och Secure Sockets Layer (SSL) är krypterings protokoll som ger kommunikations säkerhet i ett dator nätverk. Mer information finns i [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Även om Azure HDInsight-kluster accepterar TLS 1,2-anslutningar på offentliga HTTPS-slutpunkter, stöds TLS 1,1 fortfarande för bakåtkompatibilitet med äldre klienter.

Från och med nästa version kommer du att kunna välja och konfigurera dina nya HDInsight-kluster så att endast TLS 1,2-anslutningar accepteras. 

Senare under året, med början den 6/30/2020, kommer Azure HDInsight att verkställa TLS 1,2 eller senare versioner för alla HTTPS-anslutningar. Vi rekommenderar att du ser till att alla klienter är redo att hantera TLS 1,2 eller senare versioner.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Från och med Feburary 2020 (det exakta datumet kommer att meddelas senare) kommer HDInsight att använda skalnings uppsättningar för virtuella Azure-datorer i stället. Läs mer om [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Ändra storlek på ESP Spark-klusternod 
I den kommande versionen:
- Den minsta tillåtna buffertstorleken för ESP Spark-kluster kommer att ändras till Standard_D13_V2. 
- Virtuella datorer i a-serien är inaktuella för att skapa nya ESP-kluster, eftersom virtuella datorer i A-serien kan orsaka problem med ESP-kluster på grund av relativt låg processor-och minnes kapacitet.

### <a name="hbase-20-to-21"></a>HBase 2,0 till 2,1
I den kommande HDInsight 4,0-versionen kommer HBase-versionen att uppgraderas från version 2,0 till 2,1.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Vi har utökat HDInsight 3,6-stöd till 31 december 2020. Du hittar mer information i de [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

Ingen komponent versions ändring för HDInsight 4,0.

Apache Zeppelin på HDInsight 3,6:0.7.0--> 0.7.3. 

Du kan hitta de senaste komponent versionerna från [det här dokumentet](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="new-regions"></a>Nya regioner

### <a name="uae-north"></a>Förenade Arabemiraten, norra
Hanterings-IP: er för Förenade Arabemiraten Nord är: `65.52.252.96` och `65.52.252.97`.
