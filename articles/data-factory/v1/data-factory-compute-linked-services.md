---
title: Compute-miljöer som stöds av Azure Data Factory | Microsoft Docs
description: Läs mer om beräkningsmiljöer som du kan använda i Azure Data Factory pipelines (till exempel Azure HDInsight) för att transformera eller bearbeta data.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565726"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-miljöer som stöds av Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [beräkna länkade tjänster i](../compute-linked-services.md).

Den här artikeln förklarar beräkningsmiljöer som du kan använda för att bearbeta och omvandla data. Den innehåller också information om olika konfigurationer (på begäran jämfört med bring your own) att Data Factory stöder när du konfigurerar länkade tjänster som länkar dessa compute-miljöer i en Azure-datafabrik.

Följande tabell innehåller en lista över beräkningsmiljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                      | Aktiviteter                               |
| ---------------------------------------- | ---------------------------------------- |
| [Azure HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [ett eget HDInsight-kluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-aktiviteter: Batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versioner som stöds i Data Factory
Azure HDInsight har stöd för flera Hadoop-klusterversioner som du kan distribuera när som helst. Varje version som stöds skapar en specifik version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter i distributionen. 

Microsoft uppdaterar listan över HDInsight-versioner som stöds med de senaste Hadoop-ekosystemet komponenter och korrigeringar. Detaljerad information finns i [stöds HDInsight versioner](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-baserade HDInsight version 3.3 drogs tillbaka den 31 juli 2017. Data Factory version 1 på begäran HDInsight länkade tjänster som kunder har beviljats fram till 15 December 2017, testa och uppgradera till en senare version av HDInsight. Windows-baserade HDInsight kommer att dras tillbaka den 31 juli 2018.
>
> 

### <a name="after-the-retirement-date"></a>Efter datumet för tillbakadragandet 

Efter den 15 December 2017:

- Du kan inte längre skapa Linux-baserade HDInsight version 3.3 (eller tidigare versioner) kluster med hjälp av en på begäran HDInsight-länkad tjänst i Data Factory version 1. 
- Om den [ **osType** och **Version** egenskaper](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) inte explicit i JSON-definition för en befintlig Data Factory version 1 på begäran HDInsight-länkad tjänst , ändras standardvärdet från **Version = 3.1 osType = Windows** till **Version =\<senaste HDI-standardversion\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType = Linux**.

Efter den 31 juli 2018:

- Du kan inte längre skapa någon version av Windows-baserade HDInsight-kluster med hjälp av en på begäran länkad HDInsight-tjänst i Data Factory version 1. 

### <a name="recommended-actions"></a>Rekommenderade åtgärder 

- För att säkerställa att du kan använda de senaste Hadoop-ekosystemet-komponenter och korrigeringar, uppdatera den [ **osType** och **Version** egenskaper](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) av berörda Data Factory version 1 på begäran HDInsight länkade tjänster till nyare versioner av Linux-baserat HDInsight (HDInsight 3.6). 
- Testa Data Factory version 1 Hive, Pig, MapReduce och Hadoop strömmande aktiviteter som refererar till den berörda länkade tjänsten före den 15 December 2017. Se till att de är kompatibla med de nya **osType** och **Version** standardvärden (**Version = 3.6**, **osType = Linux**) eller explicita HDInsight version och operativsystem kan du ange att du uppgraderar till. 
  Mer information om kompatibilitet finns [migrera från ett Windows-baserade HDInsight-kluster till ett Linux-baserade kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) och [vad är Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Om du vill fortsätta att använda en Data Factory version 1 på begäran länkad HDInsight-tjänsten för att skapa Windows-baserade HDInsight-kluster måste uttryckligen ange **osType** till **Windows** före den 15 December 2017. Vi rekommenderar att du migrerar till Linux-baserade HDInsight-kluster innan 31 juli 2018. 
- Om du använder en på begäran länkad HDInsight-tjänst för att köra Data Factory version 1 DotNet anpassad aktivitet, uppdatera DotNet anpassade aktivitets-JSON-definitionen för att i stället använda en Azure-Batch-länkade tjänsten. Mer information finns i [Använd anpassade aktiviteter i Data Factory-pipeline](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Om du använder dina befintliga bring-your-own-kluster HDInsight länkad enhet i Data Factory version 1 eller en bring-your-own och på begäran länkad HDInsight-tjänst i Azure Data Factory, ingen åtgärd krävs. I dessa scenarier kan tillämpas redan den senaste principen för versionen av HDInsight-kluster. 
>
> 


## <a name="on-demand-compute-environment"></a>På begäran beräkningsmiljö
På begäran-konfiguration hanterar Data Factory fullständigt beräkningsmiljön. Data Factory skapar automatiskt beräkningsmiljön innan ett jobb skickas för bearbetning av data. När jobbet har slutförts tas Data Factory beräkningsmiljön. 

Du kan skapa en länkad tjänst för en beräkningsmiljö för på begäran. Använd den länkade tjänsten att konfigurera compute-miljö och för att kontrollera detaljerade inställningar för jobbkörning, klusterhantering och start av åtgärder.

> [!NOTE]
> På begäran-konfiguration stöds för närvarande endast för HDInsight-kluster.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight på begäran länkad tjänst
Data Factory kan automatiskt skapa en Windows- eller Linux-baserade HDInsight-kluster på begäran för bearbetning av data. Klustret skapas i samma region som lagringskontot som är associerat med klustret. Använd JSON **linkedServiceName** egenskapen att skapa klustret.

Observera följande *nyckel* punkter om på begäran HDInsight-länkad tjänst:

* HDInsight-kluster på begäran visas inte i Azure-prenumerationen. Data Factory-tjänsten hanterar HDInsight-kluster på begäran för din räkning.
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till storage-kontot som är associerat med HDInsight-klustret. För att komma åt de här loggarna i Azure-portalen går du till den **Aktivitetskörningsinformation** fönstret. Mer information finns i [övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md).
* Du debiteras endast för den tid som HDInsight-klustret är igång och jobb som körs.

> [!IMPORTANT]
> Det tar vanligtvis *20 minuter* eller mer att etablera ett HDInsight-kluster på begäran.
>
> 

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight-länkad tjänst. Data Factory skapar automatiskt en *Linux-baserade* HDInsight-klustret när den bearbetar en datasektor. 

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
> HDInsight-klustret skapar en *standardbehållaren* i Azure Blob storage som du anger i JSON **linkedServiceName** egenskapen. Enligt design, HDInsight tar inte bort den här behållaren när klustret tas bort. I ett HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Klustret tas bort när bearbetningen är klar. 
>
> Allteftersom fler sektorer bearbetas kan se du många behållare i Blob storage. Om du inte behöver behållarna för att felsöka jobb, kanske du vill ta bort behållare för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf<your Data Factory name>-<linked service name>-<date and time>`. Du kan använda ett verktyg som [Microsoft Lagringsutforskaren](https://storageexplorer.com/) att ta bort behållare i Blob storage.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                      | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Ange typegenskapen som **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antal arbetare och datanoder i klustret. HDInsight-klustret har skapats med 2 huvudnoder, förutom antalet arbetsnoder som du anger för den här egenskapen. Noderna är storleken Standard_D3 som har 4 kärnor. En 4-worker nodkluster tar 24 kärnor (4\*4 = 16 kärnor för arbetsnoder plus 2\*4 = 8 kärnor för huvudnoder). Mer information om den Standard_D3 nivån finns [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ja      |
| timeToLive                   | Tillåtna inaktivitetstiden för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir aktiv när en aktivitet körningen har slutförts, om det finns inga aktiva jobb i klustret.<br /><br />Till exempel om en aktivitet kör tar 6 minuter och **timeToLive** är inställd på 5 minuter, kluster är kvar alive i 5 minuter efter 6 minuter bearbeta den aktivitet som körs. Om en annan aktivitet som kör körs i fönstret 6 minuter, bearbetas den av samma kluster.<br /><br />Det är en kostsam åtgärd (det kan ta ett tag) för att skapa ett HDInsight-kluster på begäran. Använd den här inställningen som behövs för att förbättra prestanda för en data factory genom att återanvända ett HDInsight-kluster på begäran.<br /><br />Om du ställer in den **timeToLive** värde att **0**, klustret tas bort när aktiviteten körs har slutförts. Men om du ställer in ett högt värde för kan klustret Behåll inaktiv, onödigt lett till höga kostnader. Det är viktigt att ange ett lämpligt värde utifrån dina behov.<br /><br />Om den **timeToLive** har angetts på rätt sätt måste flera pipelines kan dela instansen av HDInsight-kluster på begäran. | Ja      |
| version                      | Versionen av HDInsight-klustret. Tillåtna HDInsight-versioner, se [stöds HDInsight versioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Om värdet inte anges, den [senaste HDI-standardversion](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) används. | Nej       |
| linkedServiceName            | Länkad Azure Storage-tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. HDInsight-klustret har skapats i samma region som det här lagringskontot.<p>För närvarande kan skapa du inte en på begäran HDInsight-kluster som använder Azure Data Lake Store som lagring. Om du vill spara Resultatdata från HDInsight i Data Lake Store kan du använda Kopieringsaktivitet för att kopiera data från Blob storage till Data Lake Store. </p> | Ja      |
| additionalLinkedServiceNames | Anger ytterligare lagringskonton för länkad HDInsight-tjänsten. Data Factory registrerar storage-konton för din räkning. Dessa konton måste vara i samma region som HDInsight-kluster. HDInsight-klustret har skapats i samma region som lagringskontot som anges av den **linkedServiceName** egenskapen. | Nej       |
| osType                       | Typ av operativsystem. Tillåtna värden är **Linux** och **Windows**. Om värdet inte anges **Linux** används.  <br /><br />Vi rekommenderar starkt med Linux-baserade HDInsight-kluster. Slutdatum för HDInsight på Windows är den 31 juli 2018. | Nej       |
| hcatalogLinkedServiceName    | Namnet på den länkade Azure SQL-tjänst som pekar på HCatalog-databasen. HDInsight-kluster på begäran skapas med hjälp av SQL-databas som metaarkiv. | Nej       |

#### <a name="example-linkedservicenames-json"></a>Exempel: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Avancerade egenskaper
Detaljerad konfiguration av HDInsight-kluster på begäran, kan du ange följande egenskaper:

| Egenskap                | Beskrivning                              | Obligatoriskt |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger konfigurationsparametrar core (core-site.xml) för att skapa HDInsight-klustret. | Nej       |
| hBaseConfiguration     | Anger HBase konfigurationsparametrar (hbase-site.xml) för HDInsight-klustret. | Nej       |
| hdfsConfiguration      | Anger konfigurationsparametrar för HDFS (hdfs-site.xml) för HDInsight-klustret. | Nej       |
| hiveConfiguration      | Anger konfigurationsparametrar för Hive (hive-site.xml) för HDInsight-klustret. | Nej       |
| mapReduceConfiguration | Anger konfigurationsparametrar för MapReduce (mapred-site.xml) för HDInsight-klustret. | Nej       |
| oozieConfiguration     | Anger Oozie konfigurationsparametrar (oozie-site.xml) för HDInsight-klustret. | Nej       |
| stormConfiguration     | Anger de Storm konfigurationsparametrarna (storm-site.xml) för HDInsight-klustret. | Nej       |
| yarnConfiguration      | Anger YARN konfigurationsparametrar (yarn-site.xml) för HDInsight-klustret. | Nej       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel: På begäran HDInsight-klusterkonfiguration med avancerade egenskaper

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
Om du vill ange storleken på huvudnoder, datanoder och ZooKeeper-noder, använder du följande egenskaper: 

| Egenskap           | Beskrivning                              | Obligatoriskt |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger storleken på huvudnoden. Standardvärdet är **Standard_D3**. Mer information finns i [ange nodstorlekar](#specify-node-sizes). | Nej       |
| dataNodeSize      | Anger storleken på datanod. Standardvärdet är **Standard_D3**. | Nej       |
| zookeeperNodeSize | Anger storleken på ZooKeeper-nod. Standardvärdet är **Standard_D3**. | Nej       |

#### <a name="specify-node-sizes"></a>Ange storleken på
Strängvärden som du måste ange för de egenskaper som beskrivs i föregående avsnitt, se [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md). Värdena måste uppfylla cmdletarna och API: er som refereras till i [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md). Nodstorlek för stor (standard) data har 7 GB minne. Detta kanske inte är tillräcklig för ditt scenario. 

Om du vill skapa D4 storlek huvudnoderna och arbetsnoder ange **Standard_D4** som värde för den **egenskaper för headNodeSize** och **dataNodeSize** egenskaper: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper visas följande meddelande:

  Det gick inte att skapa kluster. Undantag: Det går inte att slutföra klustret för att skapa. Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Klustret sörjande tillstånd: ”Fel”. Meddelande: 'PreClusterCreationValidationFailure'. 
  
Om du ser det här meddelandet, kontrollera att du använder du cmdlet och API-namn från tabellen i [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Data Factory stöder för närvarande inte HDInsight-kluster som använder Data Lake Store som primär lagring. Använda Azure Storage som det primära lagret för HDInsight-kluster. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Bring-your-own beräkningsmiljö
Du kan registrera en befintlig beräkningsmiljö som en länkad tjänst i Datafabriken. Du hanterar beräkningsmiljön. Data Factory-tjänsten använder compute-miljö för att köra aktiviteter.

Den här typen av konfiguration stöds för följande beräkningsmiljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight-länkad tjänst
Du kan skapa en länkad HDInsight-tjänst för att registrera ett eget HDInsight-kluster med Data Factory.

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
| Egenskap           | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange typegenskapen som **HDInsight**. | Ja      |
| clusterUri        | URI för HDInsight-klustret.        | Ja      |
| användarnamn          | Namnet på användarkontot du använder för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| password          | Lösenordet för användarkontot.   | Ja      |
| linkedServiceName | Namnet på den länkade storage-tjänst som refererar till Blob-lagring som används av HDInsight-klustret. <p>För närvarande kan ange du inte ett Data Lake Store-länkad tjänst för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Data Lake Store från Hive och Pig-skript. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Tjänsten Azure Batch länkad
Du kan skapa en Batch länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) till en data factory. Du kan köra Microsoft .NET anpassade aktiviteter med hjälp av Batch eller HDInsight.

Om du är van vid att använda Batch-tjänsten:

* Lär dig mer om [grunderna om Azure Batch](../../batch/batch-technical-overview.md).
* Lär dig mer om den [New AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet. Använd denna cmdlet för att skapa ett Batch-konto. Du kan också skapa Batch-konto med hjälp av den [Azure-portalen](../../batch/batch-account-create-portal.md). Detaljerad information om hur du använder cmdleten finns i [använda PowerShell för att hantera ett Batch-konto](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Lär dig mer om den [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet. Använd denna cmdlet för att skapa en Batch-pool.

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

För den **accountName** egenskapen, lägga till **.\< Regionsnamn\>**  till namnet på ditt batch-konto. Exempel:

```json
"accountName": "mybatchaccount.eastus"
```

Ett annat alternativ är att tillhandahålla den **batchUri** slutpunkt. Exempel:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Egenskaper
| Egenskap           | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ange typegenskapen som **AzureBatch**. | Ja      |
| accountName       | Namnet på Batch-kontot.         | Ja      |
| accessKey         | Åtkomstnyckeln för Batch-kontot.  | Ja      |
| Poolnamn          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den länkade storage-tjänst som är associerat med den här batchen länkad tjänst. Den här länkade tjänsten används för tillfälliga filer som krävs för att köra aktiviteten och för att lagra aktivitetsloggar för körning. | Ja      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du kan skapa en Machine Learning länkad tjänst för att registrera en Machine Learning batch bedömningsslutpunkten till en data factory.

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
| Egenskap    | Beskrivning                              | Obligatoriskt |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Ange typegenskapen som **AzureML**. | Ja      |
| mlEndpoint | Batchbedömnings-URL: en.                   | Ja      |
| ApiKey     | Den publicerade arbetsytemodellens API.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du kan skapa en Data Lake Analytics-länkad tjänst för att länka en Data Lake Analytics-databearbetningstjänst till en Azure-datafabrik. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

I följande tabell beskrivs de allmänna egenskaper som används i JSON-definition:

| Egenskap                  | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| typ                 | Ange typegenskapen som **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Namnet på Data Lake Analytics-kontot.  | Ja                                      |
| dataLakeAnalyticsUri | Data Lake Analytics-URI.           | Nej                                       |
| subscriptionId       | Azure-prenumerations-ID.                    | Nej<br /><br />(Om inte anges används prenumerationen för data factory.) |
| resourceGroupName    | Azure resursgruppens namn.                | Nej<br /><br /> (Om inte anges används resursgruppen för data factory.) |

### <a name="authentication-options"></a>Autentiseringsalternativ
Du kan välja mellan autentisering med hjälp av ett huvudnamn för tjänsten eller användarens autentiseringsuppgifter för din Data Lake Analytics-länkad tjänst.

#### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)
Registrera en program-entitet för att använda autentisering av tjänstens huvudnamn i Azure Active Directory (AD Azure). Bevilja sedan Azure AD-åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Programnyckel 
* Klient-ID:t

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap                 | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Programmets klient-ID.     | Ja      |
| servicePrincipalKey | Programnyckel.           | Ja      |
| tenant              | Klientorganisationens information (domain name eller klient-ID) där programmet finns. För att få den här informationen kan håller du muspekaren i det övre högra hörnet i Azure Portal. | Ja      |

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

#### <a name="user-credential-authentication"></a>Användarautentisering för autentiseringsuppgifter
För autentisering av användare autentiseringsuppgifter för Data Lake Analytics, anger du följande egenskaper:

| Egenskap           | Beskrivning                              | Obligatoriskt |
| :---------------- | :--------------------------------------- | :------- |
| Auktorisering | I Data Factory Editor väljer den **auktorisera** knappen. Ange den autentiseringsuppgift som tilldelar automatiskt genererade auktorisering URL: en till den här egenskapen. | Ja      |
| sessions-ID     | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unik och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja      |

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

#### <a name="token-expiration"></a>Giltighetstid för token
Auktoriseringskod som du skapade genom att välja den **auktorisera** knappen upphör att gälla efter ett angivet tidsintervall. 

Du kan se ett felmeddelande när autentiseringstoken upphör att gälla: 

  Åtgärdsfel för autentiseringsuppgift: invalid_grant - AADSTS70002: Fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Det tillhandahållna åtkomst beviljandet har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

I följande tabell visas kontolösenordet av typ av användarkonto: 

| Användartyp                                | Upphör att gälla efter                            |
| :--------------------------------------- | :--------------------------------------- |
| Användarkonton som är *inte* hanteras av Azure AD (Hotmail, Live och så vidare) | 12 timmar.                                 |
| Användarkonton som *är* hanteras av Azure AD | Kör 14 dagar efter den sista sektorn. <br /><br />90 dagar, om en sektor som baseras på körs en OAuth-baserad länkad tjänst minst en gång var fjortonde dag. |

För att undvika eller lösa det här felet, omauktorisera genom att välja den **auktorisera** knappen när token upphör att gälla. Distribuera sedan om den länkade tjänsten. Du kan också skapa värden för den **sessionId** och **auktorisering** egenskaper via programmering med hjälp av följande kod:

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

Mer information om Data Factory-klasser som används i det här kodexemplet finns i:
* [AzureDataLakeStoreLinkedService-klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService-klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse-klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Lägg till en referens till Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för den **WindowsFormsWebAuthenticationDialog** klass. 

## <a name="azure-sql-linked-service"></a>Azure SQL-länkad tjänst
Du kan skapa en länkad SQL-tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipeline. Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du kan skapa en SQL Data Warehouse länkad tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipeline. Mer information finns i [Azure SQL Data Warehouse-anslutningsappen](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>SQL Server-länkade tjänst
Du kan skapa en länkad SQL Server-tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipeline. Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties).

