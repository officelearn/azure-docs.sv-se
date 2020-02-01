---
title: Viktig information för Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 091ca4d632d89405d85c66e264aff9867979fcd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905225"
---
# <a name="release-notes"></a>Viktig information

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

## <a name="release-date-01092020"></a>Utgivnings datum: 01/09/2020

Den här versionen gäller både för HDInsight 3,6 och 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

> [!IMPORTANT]  
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [artikeln om versions hantering i HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nya funktioner
### <a name="tls-12-enforcement"></a>Tvingande TLS 1.2
Transport Layer Security (TLS) och Secure Sockets Layer (SSL) är krypterings protokoll som ger kommunikations säkerhet i ett dator nätverk. Läs mer om [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight använder TLS 1,2 på offentliga HTTPs-slutpunkter, men TLS 1,1 stöds fortfarande för bakåtkompatibilitet. 

I den här versionen kan kunder bara välja TLS 1,2 för alla anslutningar via den offentliga kluster slut punkten. För att stödja detta introduceras den nya egenskapen **minSupportedTlsVersion** och kan anges när klustret skapas. Om egenskapen inte har angetts har klustret fortfarande stöd för TLS 1,0, 1,1 och 1,2, som är samma som dagens beteende. Kunder kan ange värdet för den här egenskapen till "1,2", vilket innebär att klustret bara har stöd för TLS 1,2 och senare. Mer information finns i [planera ett virtuellt nätverk – Transport Layer Security](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Ta med din egen nyckel för disk kryptering
Alla hanterade diskar i HDInsight skyddas med Azure Storage Service Encryption (SSE). Data på diskarna krypteras av Microsoft-hanterade nycklar som standard. Från och med den här versionen kan du Bring Your Own Key (BYOK) för disk kryptering och hantera den med hjälp av Azure Key Vault. BYOK-kryptering är en konfiguration i ett steg när klustret skapas utan extra kostnad. Registrera bara HDInsight som en hanterad identitet med Azure Key Vault och Lägg till krypterings nyckeln när du skapar klustret. Mer information finns i [kund hanterad nyckel disk kryptering](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Utfasning
Inga utfasningar för den här versionen. För att komma igång med kommande inaktuella ändringar, se [kommande ändringar](#upcoming-changes).

## <a name="behavior-changes"></a>Beteende ändringar
Inga beteende ändringar för den här versionen. För att komma igång med kommande ändringar, se [kommande ändringar](#upcoming-changes).

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>En minsta 4-kärn virtuell dator krävs för Head-noden 
En minsta 4-kärn virtuell dator krävs för Head-noden för att säkerställa hög tillgänglighet och tillförlitlighet för HDInsight-kluster. Från och med den 6 april 2020 kan kunder bara välja 4 kärnor eller över VM som Head-nod för de nya HDInsight-klustren. Befintliga kluster kommer att fortsätta köras som förväntat. 

### <a name="esp-spark-cluster-node-size-change"></a>Ändra storlek på ESP Spark-klusternod 
I den kommande versionen kommer den minsta tillåtna nodtypen för ESP Spark-kluster att ändras till Standard_D13_V2. Virtuella datorer i A-serien kan orsaka problem med ESP-kluster på grund av relativt låg processor-och minnes kapacitet. Virtuella datorer i A-serien är inaktuella för att skapa nya ESP-kluster.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. I den kommande versionen används skalnings uppsättningar för virtuella Azure-datorer istället i HDInsight. Läs mer om skalnings uppsättningar för virtuella Azure-datorer.

### <a name="hbase-20-to-21"></a>HBase 2,0 till 2,1
I den kommande HDInsight 4,0-versionen kommer HBase-versionen att uppgraderas från version 2,0 till 2,1.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 AD HDInsight 3,6 här.

## <a name="known-issues"></a>Kända problem

Från och med den 29 januari 2020 uppstår ett aktivt problem där du kan få ett fel meddelande när du försöker använda en Jupyter Notebook. Använd stegen nedan för att åtgärda problemet. Du kan också se det här [MSDN-inlägget](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight) eller det här [StackOverflow-inlägget](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103) för uppdaterad information eller ställa frågor till fler frågor. Den här sidan kommer att uppdateras när problemet åtgärdas.

**Kompileringsfel**

* ValueError: kan inte konvertera antecknings boken till v5 eftersom den versionen inte finns
* Ett fel uppstod vid inläsning av antecknings boken. Med den här versionen kan du läsa in Notebook format v4 eller tidigare

**Orsak** 

Filen _version. py på klustret har uppdaterats till 5. x. x i stället för 4.4. x. # # eller Ambari måste startas om.

**Lösning**

Om du skapar en ny Jupyter-anteckningsbok och får ett av de fel som anges ovan utför du följande steg för att åtgärda problemet.

1. Öppna Ambari i en webbläsare genom att gå till `https://CLUSTERNAME.azurehdinsight.net`, där kluster namn är namnet på klustret.
1. I Ambari klickar du på **Jupyter**på den vänstra menyn och sedan på **tjänst åtgärder**klickar du på **stoppa**.
1. SSH till klustrets huvudnoden där Jupyter-tjänsten körs.
1. Öppna följande fil/usr/bin/Anaconda/lib/python2.7/site-packages/nbformat/_version. py i sudo-läge.
1. Kontrol lera värdet för version_info.
1. Om värdet för version_info är inställt på: 

    version_info = (5, 0, 3)

    Ändra sedan posten till: 
    
    version_info = (4, 4, 0)

    Och spara filen. 

    Om version_info redan har angetts till (4, 4, 0) fortsätter du till nästa steg eftersom endast Ambari måste startas om, inga ytterligare ändringar krävs.
1. Gå tillbaka till Ambari och klicka på **starta om alla**i **tjänst åtgärder**.
