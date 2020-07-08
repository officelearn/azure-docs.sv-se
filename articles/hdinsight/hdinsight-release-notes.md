---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564408"
---
# <a name="release-notes"></a>Viktig information

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

## <a name="release-date-06112020"></a>Utgivnings datum: 06/11/2020

Den här versionen gäller både för HDInsight 3,6 och 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder virtuella Azure-datorer för att etablera klustret nu. I den här versionen börjar nya HDInsight-kluster börja använda skalnings uppsättningen för virtuella Azure-datorer. Ändringen rullas gradvis. Du bör inte förvänta dig några ändringar. Läs mer om [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Starta om virtuella datorer i HDInsight-kluster
I den här versionen stöder vi omstart av virtuella datorer i HDInsight-klustret för att starta om noder som inte svarar. För närvarande kan du bara göra det via API, PowerShell-och CLI-stöd är på väg. Mer information om API: et finns i [det här dokumentet](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Utfasning
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Utfasning av Spark 2.1 och 2.2 i HDInsight 3.6 Spark-kluster
Från och med den 1 2020 juli kan kunder inte skapa nya Spark-kluster med Spark 2,1 och 2,2 på HDInsight 3,6. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att gå vidare till Spark 2,3 i HDInsight 3,6 och 30 2020 för att undvika potentiell system/support-avbrott.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Utfasning av Spark 2.3 i HDInsight 4.0 Spark-kluster
Från och med den 1 2020 juli kan kunder inte skapa nya Spark-kluster med Spark 2,3 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till Spark 2.4 på HDInsight 4.0 den 30 juni 2020 för att undvika potentiella system-/supportavbrott.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Utfasning av Kafka 1.1 i HDInsight 4.0 Kafka-kluster
Från och med juli 1 2020 kommer kunderna inte att kunna skapa nya Kafka-kluster med Kafka 1,1 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till Kafka 2.1 på HDInsight 4.0 den 30 juni 2020 för att undvika potentiella system-/supportavbrott.
 
## <a name="behavior-changes"></a>Beteende ändringar
### <a name="esp-spark-cluster-head-node-size-change"></a>Ändra storlek på ESP Spark-klusterresurs 
Den minsta tillåtna huvudnodens storlek för ESP Spark-kluster ändras till Standard_D13_V2. Virtuella datorer med låga kärnor och minne som Head-nod kan orsaka problem med ESP-kluster på grund av relativt låg processor-och minnes kapacitet. Från och med version använder du SKU: er som är högre än Standard_D13_V2 och Standard_E16_V3 som Head-nod för ESP Spark-kluster.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>En minsta 4-kärn virtuell dator krävs för Head-noden 
En minsta 4-kärn virtuell dator krävs för Head-noden för att säkerställa hög tillgänglighet och tillförlitlighet för HDInsight-kluster. Från och med april 6 2020 kan kunder bara välja 4 kärnor eller över VM som Head-nod för de nya HDInsight-klustren. Befintliga kluster kommer att fortsätta köras som förväntat. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Etablerings ändring i kluster arbets nod
När 80% av arbetsnoderna är klara, går klustret in i **drifts** skede. I det här skedet kan kunderna utföra alla data Plans åtgärder som att köra skript och jobb. Men kunder kan inte utföra några kontroll Plans åtgärder som att skala upp/ned. Endast borttagning stöds.
 
Efter **drifts** fasen väntar klustret ytterligare 60 minuter för de återstående 20% arbetsnoderna. I slutet av 60 minuter flyttas klustret till **körnings** fasen, även om alla arbetsnoder fortfarande inte är tillgängliga. När ett kluster går in i **körnings** fasen kan du använda det som normalt. Både kontroll Plans åtgärder som skalning upp/ned och data Plans åtgärder som att köra skript och jobb godkänns. Om några av de begärda arbetsnoderna inte är tillgängliga markeras klustret som delvis lyckades. Du debiteras för de noder som har distribuerats. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Skapa nytt huvud namn för tjänsten via HDInsight
Tidigare, med skapande av kluster, kan kunder skapa ett nytt huvud namn för tjänsten för att få åtkomst till det anslutna ADLS gen 1-kontot i Azure Portal. Från och med juni 15 2020 kan kunder inte skapa ett nytt huvud namn för tjänsten i arbets flödet för skapande av HDInsight. Se [skapa tjänstens huvud namn och certifikat med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Timeout för skript åtgärder med skapande av kluster
HDInsight stöder körning av skript åtgärder med skapande av kluster. I den här versionen måste alla skript åtgärder med skapande av kluster sluta inom **60 minuter**, eller tids gränsen uppnåddes. Skript åtgärder som skickas till kluster som körs påverkas inte. Läs mer information [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process).
 
## <a name="upcoming-changes"></a>Kommande ändringar
Inga kommande överändrade ändringar som du behöver betala.
 
## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 
 
## <a name="component-version-change"></a>Komponent versions ändring
### <a name="hbase-20-to-216"></a>HBase 2,0 till 2.1.6
HBase-versionen uppgraderas från version 2,0 till 2.1.6.
 
### <a name="spark-240-to-244"></a>Spark-2.4.0 till 2.4.4
Spark-versionen uppgraderas från version 2.4.0 till 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 till 2.1.1
Kafka-versionen uppgraderas från version 2.1.0 till 2.1.1.
 
Du hittar de aktuella komponent versionerna för HDInsight 4,0 AD HDInsight 3,6 i [det här dokumentet](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="known-issues"></a>Kända problem

### <a name="hive-warehouse-connector-issue"></a>Problem med Hive-dist. lager koppling
Det finns ett problem med Hive Warehouse Connector i den här versionen. Korrigeringen kommer att ingå i nästa version. Befintliga kluster som skapats före den här versionen påverkas inte. Undvik att släppa och återskapa klustret om det är möjligt. Öppna support ärende om du behöver mer hjälp.
