---
title: Beräkningsmiljöer som stöds av Azure Data Factory
description: Lär dig mer om beräkningsmiljöer som du kan använda i Azure Data Factory-pipelines (till exempel Azure HDInsight) för att omvandla eller bearbeta data.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0cc7c3b7d8b364e0bcca671efaff2cf324695428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281553"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Beräkningsmiljöer som stöds av Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Beräkna länkade tjänster i](../compute-linked-services.md).

I den här artikeln beskrivs de beräkningsmiljöer som du kan använda för att bearbeta eller omvandla data. Den innehåller också information om olika konfigurationer (on-demand kontra bring-your-own) som Data Factory stöder när du konfigurerar länkade tjänster som länkar dessa beräkningsmiljöer till en Azure-datafabrik.

Följande tabell innehåller en lista över beräkningsmiljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                      | Aktiviteter                               |
| ---------------------------------------- | ---------------------------------------- |
| [Azure HDInsight-kluster](#azure-hdinsight-on-demand-linked-service) på begäran eller [ditt eget HDInsight-kluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versioner som stöds i Data Factory
Azure HDInsight stöder flera Hadoop-klusterversioner som du kan distribuera när som helst. Varje version som stöds skapar en specifik version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter i distributionen. 

Microsoft uppdaterar listan över HDInsight-versioner som stöds med de senaste Hadoop-ekosystemkomponenterna och korrigeringarna. Detaljerad information finns i [HDInsight-versioner som stöds](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-baserad HDInsight version 3.3 pensionerades 31 juli 2017. Data Factory version 1 on-demand HDInsight länkade tjänster kunder gavs fram till december 15, 2017, för att testa och uppgradera till en senare version av HDInsight. Windows-baserade HDInsight kommer att dras tillbaka 31 juli 2018.
>
> 

### <a name="after-the-retirement-date"></a>Efter pensioneringsdatumet 

Efter den 15 december 2017:

- Du kan inte längre skapa Linux-baserade HDInsight version 3.3-kluster (eller tidigare versioner) med hjälp av en HDInsight-länkad tjänst på begäran i Data Factory version 1. 
- Om [ **egenskaperna för osType** och **Version** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) inte uttryckligen anges i JSON-definitionen för en befintlig HDInsight-tjänst för datafabrik version 1 ändras standardvärdet från **Version=3.1, osType=Windows** till **Version=\<senaste HDI-standardversion\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**.

Efter den 31 juli 2018:

- Du kan inte längre skapa någon version av Windows-baserade HDInsight-kluster med hjälp av en HDInsight-länkad tjänst på begäran i Data Factory version 1. 

### <a name="recommended-actions"></a>Rekommenderade åtgärder 

- För att säkerställa att du kan använda de senaste Hadoop-ekosystemkomponenterna och korrigeringarna uppdaterar du [ **osType-** och **versionsegenskaperna** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) för berörda Data Factory version 1 på begäran HDInsight-länkade tjänstdefinitioner till nyare Linux-baserade HDInsight-versioner (HDInsight 3.6). 
- Före den 15 december 2017 testar du datafabrikens version 1 hive-, gris-, kartreeduce- och Hadoop-streamingaktiviteter som refererar till den berörda länkade tjänsten. Kontrollera att de är kompatibla med de nya **standardvärdena för osType** och **version** (**Version=3.6**, **osType=Linux**) eller den explicita HDInsight-versionen och OS-typen som du uppgraderar till. 
  Mer information om kompatibilitet finns i [Migrera från ett Windows-baserat HDInsight-kluster till ett Linux-baserat kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) och Vilka är [Hadoop-komponenterna och versionerna tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Om du vill fortsätta använda en HDInsight-tjänst för datafabrik version 1 på begäran för att skapa Windows-baserade HDInsight-kluster anger du uttryckligen **osType** till **Windows** före den 15 december 2017. Vi rekommenderar att du migrerar till Linux-baserade HDInsight-kluster före den 31 juli 2018. 
- Om du använder en HDInsight-länkad tjänst på begäran för att köra Data Factory version 1 DotNet Custom Activity uppdaterar du DotNet Custom Activity JSON-definitionen för att i stället använda en Azure Batch-länkad tjänst. Mer information finns [i Använda anpassade aktiviteter i en Data Factory-pipeline](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Om du använder din befintliga, bring-your-own kluster HDInsight-länkade enhet i Data Factory version 1 eller en bring-your-own och on-demand HDInsight-länkad tjänst i Azure Data Factory krävs ingen åtgärd. I dessa scenarier tillämpas redan den senaste princip för versionsstöd för HDInsight-kluster. 
>
> 


## <a name="on-demand-compute-environment"></a>Beräkningsmiljö på begäran
I en konfiguration på begäran hanterar Data Factory beräkningsmiljön. Data Factory skapar automatiskt beräkningsmiljön innan ett jobb skickas för bearbetning av data. När jobbet är klart tar Data Factory bort beräkningsmiljön. 

Du kan skapa en länkad tjänst för en beräkningsmiljö på begäran. Använd den länkade tjänsten för att konfigurera beräkningsmiljön och för att styra detaljerade inställningar för jobbkörning, klusterhantering och bootstrapping-åtgärder.

> [!NOTE]
> För närvarande stöds konfigurationen på begäran endast för HDInsight-kluster.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Länkad tjänst för Azure HDInsight på begäran
Data Factory kan automatiskt skapa ett Windows-baserat eller Linux-baserat HDInsight-kluster på begäran för bearbetning av data. Klustret skapas i samma region som lagringskontot som är associerat med klustret. Använd egenskapen JSON **linkedServiceName** för att skapa klustret.

Observera följande *viktiga* punkter om hdinsight-länkad tjänst på begäran:

* HDInsight-klustret på begäran visas inte i din Azure-prenumeration. Tjänsten Data Factory hanterar HDInsight-klustret på begäran för din räkning.
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till lagringskontot som är associerat med HDInsight-klustret. För att komma åt dessa loggar, i Azure-portalen, gå till fönstret **Information om aktivitetskörning.** Mer information finns i [Övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md).
* Du debiteras bara för den tid som HDInsight-klustret är igång jobb.

> [!IMPORTANT]
> Det tar vanligtvis *20 minuter* eller mer att etablera ett HDInsight-kluster på begäran.
>
> 

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserad HDInsight-länkad tjänst på begäran. Data Factory skapar automatiskt ett *Linux-baserat HDInsight-kluster* när det bearbetar ett datasegment. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> HDInsight-klustret skapar en *standardbehållare* i Azure Blob-lagring som du anger i egenskapen JSON **linkedServiceName.** HdInsight tar inte bort den här behållaren när klustret tas bort. I en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en segment behöver bearbetas, såvida det inte finns ett befintligt live-kluster **(timeToLive**). Klustret tas bort när bearbetningen är klar. 
>
> När fler segment bearbetas visas många behållare i blob-lagringen. Om du inte behöver behållarna för felsökning av jobb kanske du vill ta bort behållarna för att minska lagringskostnaden. Namnen på de här containrarna följer ett mönster: `adf<your Data Factory name>-<linked service name>-<date and time>`. Du kan använda ett verktyg som [Microsoft Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Blob-lagring.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Ange egenskapen type till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antalet arbets- och datanoder i klustret. HDInsight-klustret skapas med två huvudnoder, förutom antalet arbetsnoder som du anger för den här egenskapen. Noderna är av storlek Standard_D3, som har 4 kärnor. Ett 4-arbetsnodkluster tar 24\*kärnor (4 4 = 16\*kärnor för arbetsnoder, plus 2 4 = 8 kärnor för huvudnoder). Mer information om nivån Standard_D3 finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ja      |
| timeToLive                   | Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir vid liv när en aktivitetskörning är klar, om det inte finns några andra aktiva jobb i klustret.<br /><br />Om en aktivitetskörning till exempel tar 6 minuter och **timeToLive** är inställd på 5 minuter, förblir klustret vid liv i 5 minuter efter 6 minuters bearbetning av aktivitetskörningen. Om en annan aktivitetskörning körs i 6-minutersfönstret bearbetas den av samma kluster.<br /><br />Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (det kan ta ett tag). Använd den här inställningen efter behov för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br /><br />Om du ställer in **timeToLive-värdet** på **0**tas klustret bort så snart aktivitetskörningen är klar. Men om du anger ett högt värde kan klustret vara inaktivt, vilket i onödan resulterar i höga kostnader. Det är viktigt att ange lämpligt värde baserat på dina behov.<br /><br />Om **timeToLive-värdet** är korrekt inställt kan flera pipelines dela instansen av HDInsight-klustret på begäran. | Ja      |
| version                      | Versionen av HDInsight-klustret. För tillåtna HDInsight-versioner finns i [HDInsight-versioner som stöds](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Om det här värdet inte anges används den [senaste HDI-standardversionen.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) | Inga       |
| linkedServiceName            | Azure Storage-länkade tjänsten som ska användas av klustret på begäran för lagring och bearbetning av data. HDInsight-klustret skapas i samma region som det här lagringskontot.<p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder Azure Data Lake Store som lagring. Om du vill lagra resultatdata från HDInsight-bearbetning i DataSjölag använder du Kopiera aktivitet för att kopiera data från Blob-lagring till DataSjölagring. </p> | Ja      |
| ytterligareLänkade tjänstnamn | Anger ytterligare lagringskonton för den HDInsight-länkade tjänsten. Data Factory registrerar lagringskontona för din räkning. Dessa lagringskonton måste finnas i samma region som HDInsight-klustret. HDInsight-klustret skapas i samma region som lagringskontot som anges av egenskapen **linkedServiceName.** | Inga       |
| osType (olika)                       | Typ av operativsystem. Tillåtna värden är **Linux** och **Windows**. Om det här värdet inte anges används **Linux.**  <br /><br />Vi rekommenderar starkt att du använder Linux-baserade HDInsight-kluster. Pensionen för HDInsight på Windows är 31 juli 2018. | Inga       |
| hcatalogLinkedServiceName    | Namnet på azure SQL-länkade tjänsten som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas med hjälp av SQL-databasen som metabutik. | Inga       |

#### <a name="example-linkedservicenames-json"></a>Exempel: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Avancerade egenskaper
För detaljerad konfiguration av HDInsight-klustret på begäran kan du ange följande egenskaper:

| Egenskap               | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger de grundläggande konfigurationsparametrarna (core-site.xml) för HDInsight-klustret som ska skapas. | Inga       |
| hBaseKonfigurering     | Anger HBase-konfigurationsparametrarna (hbase-site.xml) för HDInsight-klustret. | Inga       |
| hdfsKonfigurering      | Anger HDFS-konfigurationsparametrarna (hdfs-site.xml) för HDInsight-klustret. | Inga       |
| hiveKonfigurering      | Anger hive-konfigurationsparametrarna (hive-site.xml) för HDInsight-klustret. | Inga       |
| mapReduceKonfigurering | Anger mapReduce-konfigurationsparametrarna (mapred-site.xml) för HDInsight-klustret. | Inga       |
| oozieKonfigurering     | Anger Oozie-konfigurationsparametrarna (oozie-site.xml) för HDInsight-klustret. | Inga       |
| stormKonfigurering     | Anger parametrarna stormkonfiguration (storm-site.xml) för HDInsight-klustret. | Inga       |
| garnKonfigurering      | Anger YARN-konfigurationsparametrarna (yarn-site.xml) för HDInsight-klustret. | Inga       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel: HDInsight-klusterkonfiguration på begäran med avancerade egenskaper

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Nodstorlekar
Om du vill ange storleken på noderna head, data och ZooKeeper använder du följande egenskaper: 

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger storleken på huvudnoden. Standardvärdet är **Standard_D3**. Mer information finns i [Ange nodstorlekar](#specify-node-sizes). | Inga       |
| dataNodeSize      | Anger storleken på datanoden. Standardvärdet är **Standard_D3**. | Inga       |
| zookeeperNodeSize | Anger storleken på zookeepernoden. Standardvärdet är **Standard_D3**. | Inga       |

#### <a name="specify-node-sizes"></a>Ange nodstorlekar
För strängvärden som du måste ange för de egenskaper som [beskrivs](../../virtual-machines/linux/sizes.md)i föregående avsnitt finns i Storlekar på virtuella datorer . Värdena måste överensstämma med de cmdlets och API:er som refereras i [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md). Datanodstorleken Stor (standard) har 7 GB minne. Detta kanske inte är tillräckligt för ditt scenario. 

Om du vill skapa D4-ämnad och arbetsnoder anger du **Standard_D4** som värde för egenskaperna **headNodeSize** och **dataNodeSize:** 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper kan följande meddelande visas:

  Det gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: "PreClusterCreationValidationFailure". 
  
Om det här meddelandet visas kontrollerar du att du använder cmdlet- och API-namnen från tabellen i [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> För närvarande stöder Data Factory inte HDInsight-kluster som använder Data Lake Store som primärt arkiv. Använd Azure Storage som primärt arkiv för HDInsight-kluster. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Bring-your-own beräkningsmiljö
Du kan registrera en befintlig beräkningsmiljö som en länkad tjänst i Data Factory. Du hanterar beräkningsmiljön. Tjänsten Data Factory använder beräkningsmiljön för att utföra aktiviteter.

Den här typen av konfiguration stöds för följande beräkningsmiljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL-databas, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight länkad tjänst
Du kan skapa en HDInsight-länkad tjänst för att registrera ditt eget HDInsight-kluster med Data Factory.

### <a name="example"></a>Exempel

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange egenskapen type till **HDInsight**. | Ja      |
| clusterUri (klusterUri)        | Uri i HDInsight-klustret.        | Ja      |
| användarnamn          | Namnet på användarkontot som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| password          | Lösenordet för användarkontot.   | Ja      |
| linkedServiceName | Namnet på den lagringslänkade tjänsten som refererar till Blob-lagringen som används av HDInsight-klustret. <p>För närvarande kan du inte ange en datasjölagringslänktjänst för den här egenskapen. Om HDInsight-klustret har åtkomst till DataSjöarkivet kan du komma åt data i DataSjöarkivet från Hive- eller Pig-skript. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Azure Batch-länkad tjänst
Du kan skapa en batchlänkd tjänst för att registrera en batchpool med virtuella datorer (VMs) till en datafabrik. Du kan köra anpassade aktiviteter i Microsoft .NET med hjälp av Batch eller HDInsight.

Om du inte har använt tjänsten Batch tidigare:

* Läs mer om [grunderna i Azure Batch](../../batch/batch-technical-overview.md).
* Läs mer om cmdleten [New-AzureBatchAccount.](https://msdn.microsoft.com/library/mt125880.aspx) Använd den här cmdleten för att skapa ett batchkonto. Du kan också skapa batchkontot med hjälp av [Azure-portalen](../../batch/batch-account-create-portal.md). Detaljerad information om hur du använder cmdleten finns i [Använda PowerShell för att hantera ett batchkonto](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Läs mer om cmdleten [New-AzureBatchPool.](https://msdn.microsoft.com/library/mt125936.aspx) Använd den här cmdleten för att skapa en batchpool.

### <a name="example"></a>Exempel

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Lägg till för egenskapen **accountName** **.\< regionnamnet\> ** till namnet på ditt batchkonto. Ett exempel:

```json
"accountName": "mybatchaccount.eastus"
```

Ett annat alternativ **batchUri** är att ange batchUri-slutpunkten. Ett exempel:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange typegenskapen till **AzureBatch**. | Ja      |
| accountName       | Namnet på batchkontot.         | Ja      |
| Accesskey         | Åtkomstnyckeln för batchkontot.  | Ja      |
| poolNamn          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den lagringslänkade tjänst som är associerad med den här batchlänkade tjänsten. Den här länkade tjänsten används för mellanlagringsfiler som krävs för att köra aktiviteten och för att lagra loggar för aktivitetskörning. | Ja      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du kan skapa en machine learning-länkad tjänst för att registrera en slutpunkt för machine learning-batchbedömning till en datafabrik.

### <a name="example"></a>Exempel

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap   | Beskrivning                              | Krävs |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Ange typegenskapen till **AzureML**. | Ja      |
| mlEndpoint (mlEndpoint) | Url:en för batchbedömning.                   | Ja      |
| apiKey (apiKey)     | Den publicerade arbetsytemodellens API.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du kan skapa en DataSjöAnalys-länkad tjänst för att länka en Data Lake Analytics-beräkningstjänst till en Azure-datafabrik. Data Lake Analytics U-SQL-aktivitet på pipelinen refererar till den länkade tjänsten. 

I följande tabell beskrivs de generiska egenskaper som används i JSON-definitionen:

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| typ                 | Ange typegenskapen till **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Kontonamnet för DataSjöanalys.  | Ja                                      |
| dataLakeAnalyticsUri | DataSjöanalys URI.           | Inga                                       |
| subscriptionId       | Azure-prenumerations-ID.                    | Inga<br /><br />(Om inget anges används datafabriksprenumerationen.) |
| resourceGroupName    | Namnet på Azure-resursgruppen.                | Inga<br /><br /> (Om inget anges används resursgruppen för datafabriken.) |

### <a name="authentication-options"></a>Autentiseringsalternativ
För den länkade tjänsten DataSjöanalys kan du välja mellan autentisering med hjälp av ett tjänsthuvudnamn eller en användarautentiseringsuppgifter.

#### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)
Om du vill använda autentisering av tjänstens huvudnamn registrerar du en programentitet i Azure Active Directory (Azure AD). Bevilja sedan Azure AD-åtkomst till DataSjö store. Detaljerade steg finns i [Autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)från tjänst till tjänst . Anteckna följande värden, som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Programnyckel 
* Klient-ID:t

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Programmets klient-ID.     | Ja      |
| servicePrincipalKey | Programmets nyckel.           | Ja      |
| tenant              | Klientinformationen (domännamn eller klient-ID) där ditt program finns. Om du vill hämta den här informationen håller du musen i det övre högra hörnet av Azure-portalen. | Ja      |

**Exempel: Autentisering av tjänstens huvudnamn**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Autentisering av användarautentiseringsuppgifter
För autentiseringsuppgifter för datasjöanalys för användare anger du följande egenskaper:

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| auktorisering | Välj knappen **Auktorisera** i Redigeraren för Data Factory. Ange den autentiseringspost som tilldelar den automatiskt skapade auktoriseringsadressen till den här egenskapen. | Ja      |
| Sessionid     | OAuth-sessions-ID:et från OAuth-auktoriseringssessionen. Varje sessions-ID är unikt och kan endast användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory Editor. | Ja      |

**Exempel: Autentisering av användarautentiseringsuppgifter**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token förfallodatum
Auktoriseringskoden som du genererade genom att välja knappen **Auktorisera** upphör att gälla efter ett inställt intervall. 

Följande felmeddelande kan visas när autentiseringstoken upphör att gälla: 

  Åtgärdsfel för autentiseringsuppgifter: invalid_grant - AADSTS70002: Fel uppstod när autentiseringsuppgifterna inte validerades. AADSTS70008: Förutsatt åtkomstbidrag har upphört att gälla eller återkallas. Spår-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timepstam: 2015-12-15 21:09:31Z

I följande tabell visas förfallodatum efter användarkontotyp: 

| Användartyp                                | Upphör att gälla efter                            |
| :--------------------------------------- | :--------------------------------------- |
| Användarkonton som *inte* hanteras av Azure AD (Hotmail, Live och så vidare) | 12 timmar.                                 |
| Användarkonton som *hanteras* av Azure AD | 14 dagar efter den sista segmentkörningen. <br /><br />90 dagar om ett segment som baseras på en OAuth-baserad länkad tjänst körs minst en gång var 14:e dag. |

Undvik eller lösa det här felet genom att välja knappen **Auktorisera** när token upphör att gälla. Distribuera sedan om den länkade tjänsten. Du kan också generera värden för **sessionsId-** och **auktoriseringsegenskaperna** programmässigt med hjälp av följande kod:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Mer information om de datafabriksklasser som används i det här kodexemplet finns i:
* [Klassen AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Klassen AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Klassen AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Lägg till en referens till klassen Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för klassen **WindowsFormsWebAuthenticationDialog.** 

## <a name="azure-sql-linked-service"></a>Länkad Azure SQL-tjänst
Du kan skapa en SQL-länkad tjänst och använda den med [den lagrade proceduraktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory-pipeline. Mer information finns i [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du kan skapa en SQL Data Warehouse-länkad tjänst och använda den med [den lagrade proceduraktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory-pipeline. Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>SQL Server-länkad tjänst
Du kan skapa en SQL Server-länkad tjänst och använda den med [den lagrade proceduraktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory-pipeline. Mer information finns i [SQL Server-anslutning](data-factory-sqlserver-connector.md#linked-service-properties).

