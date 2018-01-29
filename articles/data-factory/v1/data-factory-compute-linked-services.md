---
title: "Compute-miljöer som stöds av Azure Data Factory | Microsoft Docs"
description: "Mer information om beräkning miljöer där du kan använda i Azure Data Factory pipelines (till exempel Azure HDInsight) till omvandlings- eller data."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 410fb74d8f8ec6196bbd4cc19cc97704649b75c9
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-miljöer som stöds av Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Compute länkade tjänster i version 2](../compute-linked-services.md).

Den här artikeln förklaras i beräknings-miljöer där du kan använda för att bearbeta eller Transformera data. Det ger också information om olika konfigurationer (på begäran och sätta egna) att beräkna Data Factory-stöd när du konfigurerar länkade tjänster som länkar dessa miljöer till ett Azure data factory.

Följande tabell innehåller en lista över compute-miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                      | Aktiviteter                               |
| ---------------------------------------- | ---------------------------------------- |
| [Azure HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [egna HDInsight-kluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop-strömning](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versioner som stöds i Data Factory
Azure HDInsight har stöd för flera Hadoop-klusterversioner som du kan distribuera när som helst. Varje version som stöds skapar en viss version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter i distributionen. 

Microsoft uppdaterar listan över versioner som stöds HDInsight med senaste Hadoop-ekosystemet komponenterna och korrigeringar. Detaljerad information finns i [stöds HDInsight-versioner](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-baserat HDInsight version 3.3 har dragits tillbaka den 31 juli 2017. Data Factory version 1 på begäran HDInsight länkade tjänster kunder gavs förrän den 15 December 2017 för att testa och uppgradera till en senare version av HDInsight. Windows-baserade HDInsight kommer tas bort 31 juli 2018.
>
> 

### <a name="after-the-retirement-date"></a>Efter datumet för tillbakadragandet 

Efter 15 December 2017:

- Du kan inte längre skapa Linux-baserat HDInsight version 3.3 (eller tidigare versioner) kluster med hjälp av en HDInsight på begäran länkade tjänsten i Data Factory version 1. 
- Om den [ **osType** och **Version** egenskaper](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) inte uttryckligen anges i JSON-definitionen för en befintlig Data Factory version 1 på begäran länkad HDInsight-tjänst , ändras från standardvärdet **Version = 3.1 osType = Windows** till **Version =\<senaste HDI-standardversionen\>(https://docs.microsoft.com/azure/hdinsight/ hdinsight-Component-Versioning#hadoop-Components-Available-with-different-hdinsight-versions), osType = Linux**.

Efter den 31 juli 2018:

- Du kan inte längre skapa någon version av Windows-baserade HDInsight-kluster med hjälp av en tjänst för länkad HDInsight på begäran i Data Factory version 1. 

### <a name="recommended-actions"></a>Rekommenderade åtgärder 

- För att säkerställa att du kan använda de senaste komponenterna i Hadoop-ekosystemet och korrigeringar, uppdatera det [ **osType** och **Version** egenskaper](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) av berörda Data Factory version 1 på begäran HDInsight länkade tjänster till nyare versioner av Linux-baserat HDInsight (HDInsight 3,6). 
- Testa Data Factory version 1 Hive, Pig, MapReduce och Hadoop streaming aktiviteter som refererar till den berörda länkade tjänsten innan den 15 December 2017. Se till att de är kompatibla med den nya **osType** och **Version** standardvärdena (**Version = 3,6**, **osType = Linux**) eller explicit HDInsight-version och operativsystem kan du ange att du uppgraderar till. 
  Mer information om kompatibilitet finns [migrera från ett Windows-baserade HDInsight-kluster till ett Linux-baserade kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) och [vad är Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Om du vill fortsätta använda en länkad HDInsight-tjänsten Data Factory version 1 på begäran för att skapa Windows-baserade HDInsight-kluster måste uttryckligen ange **osType** till **Windows** innan den 15 December 2017. Vi rekommenderar att du migrerar till Linux-baserade HDInsight-kluster innan den 31 juli 2018. 
- Om du använder en länkad HDInsight-tjänst för på begäran för att köra Data Factory version 1 DotNet anpassad aktivitet, uppdatering DotNet anpassad aktivitet JSON-definition i stället använda en Azure Batch länkade tjänsten. Mer information finns i [använda anpassade aktiviteter i en Data Factory-pipelinen](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Om du använder din befintliga bring your own klustret HDInsight länkad enhet i Data Factory version 1 eller en bring your own och på begäran länkad HDInsight-tjänst i Azure Data Factory version 2, ingen åtgärd krävs. I dessa fall tillämpas redan den senaste principen för versionen av HDInsight-kluster. 
>
> 


## <a name="on-demand-compute-environment"></a>På begäran beräknings-miljö
På begäran-konfiguration hanterar Data Factory fullständigt beräknings-miljö. Data Factory skapar automatiskt compute-miljön innan ett jobb som har skickats för bearbetning av data. När jobbet har slutförts tas Data Factory beräknings-miljö. 

Du kan skapa en länkad tjänst för en beräkning på begäran-miljö. Använd den länkade tjänsten att konfigurera beräkning och för att kontrollera detaljerade inställningar för jobbkörningen klusterhantering och startprogram åtgärder.

> [!NOTE]
> På begäran-konfiguration stöds för närvarande endast för HDInsight-kluster.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure på begäran länkad HDInsight-tjänst
Data Factory kan automatiskt skapa ett Windows- eller Linux-baserade på begäran HDInsight-kluster för databearbetning. Klustret skapas i samma region som lagringskontot som är kopplad till klustret. Använda JSON **linkedServiceName** egenskap för att skapa klustret.

Observera följande *nyckeln* punkter om på begäran HDInsight länkade tjänsten:

* HDInsight-kluster på begäran visas inte i din Azure-prenumeration. Data Factory-tjänsten hanterar HDInsight-kluster på begäran för din räkning.
* Jobb som körs på ett HDInsight-kluster på begäran finns i loggarna kopieras till storage-konto som är kopplad till HDInsight-klustret. För att komma åt dessa loggar i Azure-portalen går du till den **kör aktivitetsinformation** fönstret. Mer information finns i [övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md).
* Du debiteras endast för tiden som HDInsight-klustret är igång och jobb som körs.

> [!IMPORTANT]
> Det tar vanligtvis *20 minuter* eller mer att etablera ett HDInsight-kluster på begäran.
>
> 

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight länkad tjänst. Data Factory skapar automatiskt en *Linux-baserade* HDInsight-klustret när den bearbetar en datasektorn. 

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
> HDInsight-kluster skapas en *standardbehållaren* i Azure Blob storage som du anger i JSON **linkedServiceName** egenskapen. Avsiktligt HDInsight tas inte bort den här behållaren när klustret har tagits bort. I en länkad HDInsight-tjänst för på begäran, ett HDInsight-kluster skapas varje gång ett segment måste bearbetas, såvida det inte finns ett befintligt live kluster (**timeToLive**). Klustret tas bort när bearbetningen är klar. 
>
> När flera segment bearbetas, kan du se många behållare i Blob storage. Om du inte behöver behållarna för att felsöka jobb, kanske du vill ta bort behållare för att minska lagringskostnaden för. Namnen på de här behållarna följer ett mönster: `adf<your Data Factory name>-<linked service name>-<date and time>`. Du kan använda ett verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) att ta bort behållare i Blob storage.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Ange typegenskapen till **HDInsightOnDemand**. | Ja      |
| ClusterSize                  | Antal arbetare och data som noder i klustret. HDInsight-kluster skapas med 2 huvudnoderna förutom antalet arbetarnoder som du anger för den här egenskapen. Noder har storlek Standard_D3 med 4 kärnor. Ett 4-arbetsnoden klustret tar 24 kärnor (4\*4 = 16 kärnor för arbetarnoder plus 2\*4 = 8 kärnor för huvudnoderna). Mer information om Standard_D3 nivån finns [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ja      |
| timeToLive                   | Tillåten inaktivitetstid för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir alive när en aktivitet körs är klar, om det finns inga aktiva jobb i klustret.<br /><br />Till exempel om en aktivitet kör tar 6 minuter och **timeToLive** är inställd på 5 minuter, av klustret förblir alive för 5 minuter efter 6 minuter för aktiviteten kör. Om en annan aktivitet kör körs i fönstret 6 minuter, bearbetas men det av samma kluster.<br /><br />Skapar ett HDInsight-kluster på begäran är en kostsam åtgärd (det kan ta en stund). Använd den här inställningen som behövs för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br /><br />Om du ställer in den **timeToLive** värde till **0**, klustret tas bort när aktiviteten körs har avslutats. Men om du anger ett högt värde för kan klustret förblir inaktiva i onödan ledde höga kostnader. Det är viktigt att ange rätt värde baserat på dina behov.<br /><br />Om den **timeToLive** värdet anges korrekt kan flera pipelines dela instansen av HDInsight-kluster på begäran. | Ja      |
| version                      | Versionen av HDInsight-klustret. Tillåtna HDInsight versioner finns [stöds HDInsight-versioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Om värdet inte anges i [senaste HDI-standardversionen](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions) används. | Nej       |
| linkedServiceName            | Länkad Azure Storage-tjänst som ska användas av klustret på begäran för lagring och bearbetning av data. HDInsight-klustret skapas i samma region som det här lagringskontot.<p>För närvarande kan du skapa ett HDInsight-kluster med på begäran som använder Azure Data Lake Store som lagring. Om du vill lagra Resultatdata från HDInsight bearbetning i Data Lake Store kan du använda Kopieringsaktiviteten för att kopiera data från Blob storage till Data Lake Store. </p> | Ja      |
| additionalLinkedServiceNames | Anger ytterligare lagringskonton för länkad HDInsight-tjänsten. Data Factory registrerar storage-konton för din räkning. Dessa storage-konton måste vara i samma region som HDInsight-klustret. HDInsight-klustret skapas i samma region som lagringskontot som anges av den **linkedServiceName** egenskapen. | Nej       |
| osType                       | Typ av operativsystem. Tillåtna värden är **Linux** och **Windows**. Om värdet inte anges **Linux** används.  <br /><br />Vi rekommenderar starkt med Linux-baserat HDInsight-kluster. Datumet för tillbakadragandet för HDInsight på Windows är 31 juli 2018. | Nej       |
| hcatalogLinkedServiceName    | Namnet på SQL Azure länkade tjänst som pekar på HCatalog-databas. HDInsight-kluster på begäran har skapats genom att använda SQL-databas som metastore. | Nej       |

#### <a name="example-linkedservicenames-json"></a>Exempel: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Avancerade egenskaper
Detaljerade konfiguration av HDInsight-kluster på begäran, kan du ange följande egenskaper:

| Egenskap               | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger konfigurationsparametrar kärnor (core-site.xml) för HDInsight-kluster som ska skapas. | Nej       |
| hBaseConfiguration     | Anger HBase konfigurationsparametrar (hbase-site.xml) för HDInsight-klustret. | Nej       |
| hdfsConfiguration      | Anger konfigurationsparametrar för HDFS (hdfs-site.xml) för HDInsight-klustret. | Nej       |
| hiveConfiguration      | Anger Hive konfigurationsparametrar (hive-site.xml) för HDInsight-klustret. | Nej       |
| mapReduceConfiguration | Anger konfigurationsparametrar för MapReduce (mapred site.xml) för HDInsight-klustret. | Nej       |
| oozieConfiguration     | Anger konfigurationsparametrar för Oozie (oozie-site.xml) för HDInsight-klustret. | Nej       |
| stormConfiguration     | Anger Storm konfigurationsparametrar (storm-site.xml) för HDInsight-klustret. | Nej       |
| yarnConfiguration      | Anger YARN konfigurationsparametrar (yarn-site.xml) för HDInsight-klustret. | Nej       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel: Klusterkonfiguration för HDInsight på begäran med avancerade egenskaper

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

### <a name="node-sizes"></a>Noden storlekar
Om du vill ange storleken på head, data och ZooKeeper-noder, använder du följande egenskaper: 

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger storleken på huvudnoden. Standardvärdet är **Standard_D3**. Mer information finns i [ange nod storlekar](#specify-node-sizes). | Nej       |
| dataNodeSize      | Anger storleken på datanoden. Standardvärdet är **Standard_D3**. | Nej       |
| zookeeperNodeSize | Anger storleken på ZooKeeper-nod. Standardvärdet är **Standard_D3**. | Nej       |

#### <a name="specify-node-sizes"></a>Ange nod storlekar
Strängvärden som du måste ange egenskaperna som beskrivs i föregående avsnitt, se [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md). Värden måste överensstämma med cmdlets och API: er som refereras i [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md). Nod för stor (standard) datastorleken har 7 GB minne. Detta kanske inte är tillräckligt för ditt scenario. 

Om du vill skapa D4 storlek huvudnoderna och arbetsnoder ange **Standard_D4** som värde för den **headNodeSize** och **dataNodeSize** egenskaper: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper, kan du se följande meddelande:

  Det gick inte att skapa kluster. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Message: 'PreClusterCreationValidationFailure'. 
  
Om du ser det här meddelandet, kontrollera att du använder cmdlet och API-namn från tabellen i [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Data Factory stöder för närvarande inte HDInsight-kluster som använder Data Lake Store som primär Arkiv. Använd Azure Storage som primär Arkiv för HDInsight-kluster. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Ta med egna compute-miljö
Du kan registrera en befintlig beräknings-miljö som en länkad tjänst i Data Factory. Du kan hantera beräknings-miljö. Data Factory-tjänsten använder beräknings-miljön för att köra aktiviteter.

Den här typen av konfiguration stöds för följande beräknings-miljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Azure SQL Data Warehouse, SQLServer

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight länkad tjänst
Du kan skapa en länkad HDInsight-tjänst för att registrera ditt eget kluster i HDInsight med Data Factory.

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
| typ              | Ange typegenskapen till **HDInsight**. | Ja      |
| clusterUri        | URI för HDInsight-klustret.        | Ja      |
| användarnamn          | Namnet på användarkontot som ska använda för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| lösenord          | Lösenordet för användarkontot.   | Ja      |
| linkedServiceName | Namnet på den länkade lagringstjänsten som refererar till Blob-lagring som används av HDInsight-klustret. <p>För närvarande kan du ange ett Data Lake Store länkade tjänsten för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Data Lake Store från Hive och Pig-skript. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst
Du kan skapa en Batch länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) till en data factory. Du kan köra Microsoft .NET anpassade aktiviteter med hjälp av Batch- eller HDInsight.

Om du har använt Batch-tjänsten:

* Lär dig mer om [grunderna i Azure Batch](../../batch/batch-technical-overview.md).
* Lär dig mer om den [ny AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet. Använd denna cmdlet för att skapa ett Batch-konto. Du kan också skapa Batch-kontot med hjälp av den [Azure-portalen](../../batch/batch-account-create-portal.md). Detaljerad information om hur du använder cmdleten finns [använda PowerShell för att hantera ett batchkonto](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Lär dig mer om den [ny AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet. Använd denna cmdlet för att skapa en Batch-pool.

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

För den **accountName** egenskapen bifoga **.\< regionsnamnet\>**  till namnet på batch-kontot. Exempel:

```json
"accountName": "mybatchaccount.eastus"
```

Ett annat alternativ är att tillhandahålla den **batchUri** slutpunkt. Exempel:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange typegenskapen till **AzureBatch**. | Ja      |
| Kontonamn       | Namnet på Batch-kontot.         | Ja      |
| accessKey         | Åtkomstnyckeln för Batch-kontot.  | Ja      |
| Poolnamn          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den länkade lagringstjänsten som är kopplad till den här batchen länkade tjänsten. Den här länkade tjänsten används för tillfälliga filer som krävs för att köra aktiviteten och för att lagra körningsloggar för aktiviteten. | Ja      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du kan skapa en Machine Learning länkad tjänst för att registrera en Machine Learning-batch bedömningsslutpunkten till en data factory.

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
| mlEndpoint | URL för batchbedömningsjobbet.                   | Ja      |
| apiKey     | Den publicerade arbetsytemodellens API.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du kan skapa en Data Lake Analytics länkad tjänst om du vill länka en beräkning Data Lake Analytics-tjänst till ett Azure data factory. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

I följande tabell beskrivs de allmänna egenskaper som används i JSON-definitionen:

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| typ                 | Ange typegenskapen till **AzureDataLakeAnalytics**. | Ja                                      |
| Kontonamn          | Namnet på Data Lake Analytics-kontot.  | Ja                                      |
| dataLakeAnalyticsUri | The Data Lake Analytics URI.           | Nej                                       |
| subscriptionId       | Azure prenumerations-ID.                    | Nej<br /><br />(Om den inte anges används data factory-prenumeration.) |
| resourceGroupName    | Azure resursgruppens namn.                | Nej<br /><br /> (Om den inte anges används resursgruppen data factory.) |

### <a name="authentication-options"></a>Autentiseringsalternativ
Du kan välja mellan autentisering med hjälp av ett huvudnamn för tjänsten eller användarens autentiseringsuppgifter för din Data Lake Analytics länkad tjänst.

#### <a name="service-principal-authentication-recommended"></a>Tjänstens huvudnamn autentisering (rekommenderas)
Registrera en entitet för program i Azure Active Directory (AD Azure) om du vill använda huvudnamn autentiseringen av tjänsten. Sedan kan ge åtkomst till Azure AD till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Nyckeln för programmet 
* Klient-ID:t

Använd service principal autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Programmets klient-ID.     | Ja      |
| servicePrincipalKey | Programmets nyckel.           | Ja      |
| klient              | Klient information (domain name eller klient ID) där programmet finns. För att få den här informationen, håller du muspekaren i det övre högra hörnet i Azure-portalen. | Ja      |

**Exempel: Service principal autentisering**
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

#### <a name="user-credential-authentication"></a>Användarautentisering för autentiseringsuppgifter
För autentisering av användare autentiseringsuppgifter för Data Lake Analytics, anger du följande egenskaper:

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| Auktorisering | I Data Factory-redigeraren, Välj den **auktorisera** knappen. Ange autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL till den här egenskapen. | Ja      |
| sessionId     | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja      |

**Exempel: Användarautentisering för autentiseringsuppgifter**
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

#### <a name="token-expiration"></a>Token upphör att gälla
Auktoriseringskoden som du skapade genom att välja den **auktorisera** knappen upphör att gälla efter ett angivet tidsintervall. 

Du kan se följande felmeddelande när autentiseringstoken upphör att gälla: 

  Autentiseringsuppgifter fel: invalid_grant - AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Bevilja beviljade åtkomsten har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

I följande tabell visas överskridna av användarkontotyp: 

| Användartyp                                | Upphör att gälla efter                            |
| :--------------------------------------- | :--------------------------------------- |
| Användarkonton som är *inte* hanteras av Azure AD (Hotmail Live och så vidare) | 12 timmar.                                 |
| Användarkonton som *är* hanteras av Azure AD | 14 dagar efter det sista segmentet kör. <br /><br />90 dagar, om en sektor som baseras på körs en länkad OAuth-tjänst minst en gång var fjortonde dag. |

För att undvika eller lösa problemet, omauktorisera genom att välja den **auktorisera** knappen när token upphör att gälla. Distribuera sedan om den länkade tjänsten. Du kan också generera värden för den **sessionId** och **auktorisering** egenskaper programmatiskt genom att använda följande kod:

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

Mer information om Data Factory-klasser som används i det här kodexemplet finns:
* [AzureDataLakeStoreLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse-klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Lägg till en referens till Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för den **WindowsFormsWebAuthenticationDialog** klass. 

## <a name="azure-sql-linked-service"></a>Azure SQL-länkade tjänst
Du kan skapa en länkad SQL-tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du kan skapa en SQL Data Warehouse länkad tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>SQL Server som är länkad tjänst
Du kan skapa en SQL Server som är länkad tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties).

