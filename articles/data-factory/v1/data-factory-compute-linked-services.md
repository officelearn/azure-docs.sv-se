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
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1547b5c3a5c629b85ff5fa9de6b39b25531d9ec9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-miljöer som stöds av Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Compute länkade tjänster i V2](../compute-linked-services.md).

Den här artikeln beskrivs olika beräknings-miljöer som du kan använda för att bearbeta eller Transformera data. Det ger också information om olika konfigurationer (på begäran eller ta med din egen) som stöds av Data Factory när du konfigurerar länkade tjänster länka dessa compute miljöer till ett Azure data factory.

Följande tabell innehåller en lista över compute-miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                      | activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [egna HDInsight-kluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop-strömning](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-aktiviteter: batchkörning och resursuppdatering](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQLServer](#sql-server-linked-service) | [Lagrad procedur](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versioner som stöds i Azure Data Factory
Azure HDInsight har stöd för flera Hadoop-klusterversioner som kan distribueras när som helst. Varje version alternativ skapas en viss version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter som ingår i distributionen. Microsoft håller uppdaterar listan över versioner som stöds av HDInsight för att ange senaste Hadoop-ekosystemet komponenterna och korrigeringar. Detaljerad information finns i [HDInsight-versioner som stöds](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-baserat HDInsight version 3.3 har avslutats på 31 juli 2017. Data Factory v1 på begäran HDInsight länkade tjänster kunder gavs förrän den 15 December 2017 för att testa och uppgradera till en senare version av HDInsight. Windows-baserade HDInsight kommer tas bort på 31 juli 2018.
>
> 

**Vad händer när datumet för tillbakadragandet** 

Efter 15 December 2017:

- Du kommer inte längre att kunna skapa Linux-baserat HDInsight version 3.3 (eller tidigare versioner) kluster med länkad på begäran HDInsight-tjänst i Azure Data Factory v1. 

- Om den [osType och/eller versionsegenskapen](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) inte uttryckligen anges i befintliga Azure Data Factory v1 på begäran HDInsight länkade tjänsten JSON definitioner, standardvärdet kommer att ändras från **Version = 3.1 osType = Windows** till **Version = 3,6 osType = Linux**.

Efter den 31 juli 2018:

- Du kommer inte längre att kunna skapa någon version av Windows-baserade HDInsight-kluster med länkad på begäran HDInsight-tjänst i Azure Data Factory v1. 

 **Rekommenderade åtgärder** 

- Uppdatering av [osType och/eller versionsegenskapen](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) påverkas Azure Data Factory v1 på begäran HDInsight länkade tjänsten definitionerna nyare Linux-baserat HDInsight versioner (HDInsight 3,6) se till att du kan använda den senaste Hadoop ekosystemet komponenter och korrigeringar. 
- Innan den 15 December 2017 testa Azure Data Factory V1 Hive, Pig, MapReduce och Hadoop streaming aktiviteter som refererar till den berörda länkade tjänsten och kontrollera de är kompatibla med den nya *osType* och/eller  *Version* standardvärde (Version = 3,6 osType = Linux) eller explicit HDInsight-version och osType som du uppgraderar till. Mer information om kompatibilitet, granska den [migrera från ett Windows-baserade HDInsight-kluster till ett Linux-baserade kluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) och [vad är Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) dokumentationen webbsidor. 
- Explicit inställd på osType på Windows innan den 15 December 2017 om du vill fortsätta använda Azure Data Factory v1On begäran HDInsight länkad tjänst för att skapa Windows-baserade HDInsight-kluster. Men rekommenderar vi ändå migrera till Linux-baserade HDInsight-kluster innan den 31 juli 2018. 
- Uppdatera DotNet anpassad aktivitet JSON-definitionen om du vill använda en länkad tjänst för Azure-Batch i stället, om du använder på begäran HDInsight länkad tjänst för att köra Azure Data Factory v1DotNet anpassad aktivitet. Läs mer på den [använda anpassade aktiviteter i en Azure DataFactory pipeline](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-use-custom-activities) dokumentationen webbsidan. 

>[!Note]
>För kunder som använder din befintliga sätta ditt eget kluster (BYOC) länkad HDInsight-tjänst i Azure Data Factory v1- eller personer använder BYOC och på begäran HDInsightLinked tjänst i Azure Data Factory v2, den senaste versionen stöder princip ofAzure HDInsight kluster tillämpas redan, därför ingen åtgärd krävs. 
>
> 


## <a name="on-demand-compute-environment"></a>På begäran beräknings-miljö
I den här typen av konfiguration hanteras datormiljön fullständigt av Azure Data Factory-tjänsten. Den skapas automatiskt av tjänsten Data Factory innan ett jobb har skickats för bearbetning av data och tas bort när jobbet har slutförts. Du kan skapa en länkad tjänst för beräkning på begäran-miljön, konfigurera den och styra detaljerade inställningar för jobbkörningen klusterhantering och startprogram åtgärder.

> [!NOTE]
> På begäran-konfiguration stöds för närvarande endast för Azure HDInsight-kluster.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight på begäran länkade tjänsten
Azure Data Factory-tjänsten kan automatiskt skapa ett Windows/Linux-baserade på begäran HDInsight-kluster för bearbetning av data. Klustret skapas i samma region som lagringskontot (linkedServiceName-egenskapen i JSON) som är associerade med klustret.

Observera följande **viktiga** punkter om på begäran HDInsight länkade tjänsten:

* Du ser inte på begäran HDInsight-klustret skapas i din Azure-prenumeration. Azure Data Factory-tjänsten hanterar HDInsight-kluster på begäran för din räkning.
* Jobb som körs på ett HDInsight-kluster på begäran finns i loggarna kopieras till storage-konto som är associerade med HDInsight-klustret. Du kan komma åt dessa loggar från Azure-portalen i den **kör aktivitetsinformation** bladet. Se [övervaka och hantera Pipelines](data-factory-monitor-manage-pipelines.md) artikeln för information.
* Du debiteras endast för tiden när HDInsight-klustret är igång och jobb som körs.

> [!IMPORTANT]
> Det tar vanligtvis **20 minuter** eller mer att etablera ett Azure HDInsight-kluster på begäran.
>
> 

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight länkad tjänst. Data Factory-tjänsten skapar automatiskt en **Linux-baserade** vid bearbetning av en datasektorn HDInsight-kluster. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Om du vill använda ett Windows-baserade HDInsight-kluster **osType** till **windows** eller Använd inte egenskapen som standard är: windows.  

> [!IMPORTANT]
> HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar. 
>
> Allteftersom fler sektorer bearbetas kan du se många behållare i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure Blob-lagring.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Typegenskapen bör anges till **HDInsightOnDemand**. | Ja      |
| ClusterSize                  | Antal worker/data noder i klustret. HDInsight-kluster skapas med 2 huvudnoderna tillsammans med antalet arbetarnoder som du anger för den här egenskapen. Noder har storlek Standard_D3 med 4 kärnor, så ett kluster med noder 4 worker tar 24 kärnor (4\*4 = 16 kärnor för arbetarnoder plus 2\*4 = 8 kärnor för huvudnoderna). Se [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) för ytterligare information om Standard_D3-nivå. | Ja      |
| TimeToLive                   | Tillåten inaktivitetstid för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir aktiva efter slutförande av en aktivitet som kör om det finns inga aktiva jobb i klustret.<br/><br/>Till exempel om en aktivitet kör tar 6 minuter och timetolive är inställd på 5 minuter, förblir klustret aktiv på 5 minuter efter 6 minuter för aktiviteten kör. Om en annan aktivitet kör körs med fönstret 6 minuter, bearbetas men det av samma kluster.<br/><br/>Skapar ett HDInsight-kluster på begäran är en kostsam åtgärd (kan ta en stund), så Använd den här inställningen som krävs för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger timetolive-värdet till 0 bort klustret som kör aktiviteten har slutförts. Men om du anger ett högt värde kan klustret förblir inaktiva i onödan ledde höga kostnader. Det är därför viktigt att du har angett rätt värde baserat på dina behov.<br/><br/>Om egenskapen timetolive-värdet är korrekt, delar flera pipelines instans av HDInsight-kluster på begäran. | Ja      |
| Version                      | Version av HDInsight-klustret. Standardvärdet är 3.1 för Windows-kluster och 3.2 för Linux-kluster. | Nej       |
| linkedServiceName            | Azure Storage länkade tjänsten som ska användas av klustret på begäran för lagring och bearbetning av data. HDInsight-klustret skapas i samma region som Azure Storage-konto.<p>För närvarande kan du skapa ett HDInsight-kluster med på begäran som använder ett Azure Data Lake Store som lagring. Om du vill lagra Resultatdata från HDInsight som bearbetas i en Azure Data Lake Store kan du använda en Kopieringsaktiviteten för att kopiera data från Azure Blobblagring till Azure Data Lake Store. </p> | Ja      |
| additionalLinkedServiceNames | Anger ytterligare lagringskonton för till HDInsight länkade tjänsten så att Data Factory-tjänsten kan registrera dem å dina vägnar. Dessa storage-konton måste vara i samma region som HDInsight-kluster som skapas i samma region som lagringskontot som anges av linkedServiceName. | Nej       |
| osType                       | Typ av operativsystem. Tillåtna värden är: (standard) för Windows och Linux | Nej       |
| hcatalogLinkedServiceName    | Namnet på Azure SQL länkade tjänst som pekar på HCatalog-databasen. HDInsight-kluster på begäran har skapats med Azure SQL-databas som metastore. | Nej       |

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON-exempel

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Avancerade egenskaper
Du kan också ange följande egenskaper för den detaljerade konfigurationen av HDInsight-kluster på begäran.

| Egenskap               | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger konfigurationsparametrar kärnor (som core-site.xml) för HDInsight-kluster som ska skapas. | Nej       |
| hBaseConfiguration     | Anger HBase konfigurationsparametrar (hbase-site.xml) för HDInsight-klustret. | Nej       |
| hdfsConfiguration      | Anger konfigurationsparametrar för HDFS (hdfs-site.xml) för HDInsight-klustret. | Nej       |
| hiveConfiguration      | Anger hive konfigurationsparametrar (hive-site.xml) för HDInsight-klustret. | Nej       |
| mapReduceConfiguration | Anger konfigurationsparametrar för MapReduce (mapred site.xml) för HDInsight-klustret. | Nej       |
| oozieConfiguration     | Anger konfigurationsparametrar för Oozie (oozie-site.xml) för HDInsight-klustret. | Nej       |
| stormConfiguration     | Anger Storm konfigurationsparametrar (storm-site.xml) för HDInsight-klustret. | Nej       |
| yarnConfiguration      | Anger Yarn konfigurationsparametrar (yarn-site.xml) för HDInsight-klustret. | Nej       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel – på begäran HDInsight klusterkonfigurationen med avancerade egenskaper

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
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
Du kan ange storleken på head, data och zookeeper-noder med följande egenskaper: 

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger storleken på huvudnoden. Standardvärdet är: Standard_D3. Finns det **ange nod storlekar** information. | Nej       |
| dataNodeSize      | Anger storleken på datanoden. Standardvärdet är: Standard_D3. | Nej       |
| zookeeperNodeSize | Anger storleken på noden djurskötare. Standardvärdet är: Standard_D3. | Nej       |

#### <a name="specifying-node-sizes"></a>Anger att noden storlekar
Finns det [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md) artikel strängvärde måste du ange för egenskaper som beskrivs i föregående avsnitt. Värden måste överensstämma med den **CMDLETs & API: er** refereras i artikeln. Som du ser i artikeln har datanoden för stor (standard) storlek 7 GB minne, vilket inte kanske är bra för ditt scenario. 

Om du vill skapa D4 storlek huvudnoderna och arbetsnoder ange **Standard_D4** som värdet för egenskaperna headNodeSize och dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper visas följande **fel:** gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: 'PreClusterCreationValidationFailure'. När du får det här felet kan du se till att du använder den **CMDLET & API: er** namn från tabellen i den [storlekar för virtuella datorer](../../virtual-machines/linux/sizes.md) artikel.  

> [!NOTE]
> Azure Data Factory stöder för närvarande inte HDInsight-kluster med Azure Data Lake Store som primär store. Använd Azure Storage som primär lagringsplats för HDInsight-kluster. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Ta med din egen beräknings-miljö
I den här typen av konfiguration kan användare registrera en redan befintlig datormiljö som en länkad tjänst i Data Factory. Datormiljön hanteras av användaren och Data Factory-tjänsten används för att utföra aktiviteter.

Den här typen av konfiguration stöds för följande beräknings-miljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQLServer

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight länkade tjänsten
Du kan skapa en Azure HDInsight länkad tjänst för att registrera ditt eget kluster i HDInsight med Data Factory.

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
| typ              | Typegenskapen bör anges till **HDInsight**. | Ja      |
| clusterUri        | URI för HDInsight-klustret.        | Ja      |
| användarnamn          | Ange namnet på användaren som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| lösenord          | Ange lösenordet för användarkontot.   | Ja      |
| linkedServiceName | Namn på länkad Azure Storage-tjänst som refererar till Azure blob storage som används av HDInsight-klustret. <p>För närvarande kan du ange ett Azure Data Lake Store länkade tjänsten för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Azure Data Lake Store från Hive/Pig-skript. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Azure Batch länkade tjänsten
Du kan skapa en Azure Batch länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) till en data factory. Du kan köra .NET anpassade aktiviteter med hjälp av Azure Batch eller Azure HDInsight.

Se följande avsnitt om du har använt Azure Batch-tjänsten:

* [Grunderna i Azure Batch](../../batch/batch-technical-overview.md) en översikt över Azure Batch-tjänsten.
* [Nya AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) för att skapa ett Azure Batch-konto (eller) [Azure-portalen](../../batch/batch-account-create-portal.md) skapa Azure Batch-kontot med hjälp av Azure portal. Se [använda PowerShell för att hantera Azure Batch-kontot](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) avsnittet detaljerade anvisningar om hur du använder cmdlet.
* [Nya AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) för att skapa en Azure Batch-pool.

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

Lägg till ”**.\< regionsnamnet\>**”till namnet på batch-kontot för den **accountName** egenskapen. Exempel:

```json
"accountName": "mybatchaccount.eastus"
```

Ett annat alternativ är att tillhandahålla batchUri slutpunkten som visas i följande exempel:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Typegenskapen bör anges till **AzureBatch**. | Ja      |
| Kontonamn       | Namnet på Azure Batch-kontot.         | Ja      |
| accessKey         | Åtkomstnyckeln för Azure Batch-kontot.  | Ja      |
| Poolnamn          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på Azure Storage länkade tjänst som är associerad med den här Azure Batch länkade tjänsten. Den här länkade tjänsten används för tillfälliga filer som krävs för att köra aktiviteten och lagrar körningsloggar aktivitet. | Ja      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkade tjänsten
Du kan skapa en Azure Machine Learning länkad tjänst för att registrera en Machine Learning-batch bedömningsslutpunkten till en data factory.

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
| Typ       | Typegenskapen bör anges till: **AzureML**. | Ja      |
| mlEndpoint | URL för batchbedömningsjobbet.                   | Ja      |
| apiKey     | Den publicerade arbetsytemodellens API.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkade tjänsten
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka ett Azure Data Lake Analytics compute-tjänst till ett Azure data factory. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar för allmänna egenskaper som används i JSON-definitionen. Du kan ytterligare välja mellan tjänstens huvudnamn och autentiseringsuppgifter för användarautentisering.

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **typ**                 | Typegenskapen bör anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| **Kontonamn**          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics-URI.           | Nej                                       |
| **prenumerations-ID**       | Azure prenumerations-id                    | Nej (om den inte anges data factory-prenumeration används). |
| **resourceGroupName**    | Azure resursgruppens namn                | Nej (om inget annat anges, resursgruppen av datafabriken används). |

### <a name="service-principal-authentication-recommended"></a>Tjänstens huvudnamn autentisering (rekommenderas)
Om du vill använda huvudnamn autentiseringen av tjänsten registrera en entitet för program i Azure Active Directory (AD Azure) och bevilja åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Nyckeln för programmet 
* Klient-ID:t

Använd service principal autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckeln.           | Ja      |
| **klient**              | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja      |

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

### <a name="user-credential-authentication"></a>Användarautentisering för autentiseringsuppgifter
Alternativt kan du använda användarautentisering för autentiseringsuppgifter för Data Lake Analytics genom att ange följande egenskaper:

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| **auktorisering** | Klicka på den **auktorisera** i den Data Factory-redigeraren och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL till den här egenskapen. | Ja      |
| **sessions-ID**     | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja      |

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
Auktoriseringskoden du genererade med hjälp av den **auktorisera** knappen upphör att gälla efter en stund. Förfallodatum tidpunkter för olika typer av användarkonton finns i följande tabell. Du kan se följande fel visas vid autentiseringen **token upphör att gälla**: autentiseringsuppgifter fel: invalid_grant - AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Bevilja beviljade åtkomsten har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

| Användartyp                                | Upphör att gälla efter                            |
| :--------------------------------------- | :--------------------------------------- |
| Användarkonton som inte hanteras av Azure Active Directory (@hotmail.com, @live.comosv.) | 12 timmar                                 |
| Användarkonton som hanteras av Azure Active Directory (AAD) | 14 dagar efter det sista segmentet kör. <br/><br/>90 dagar, om ett segment baserat på OAuth-baserad länkade tjänst körs på minst en gång var fjortonde dag. |

Om du vill undvika/åtgärda felet, omauktorisera med hjälp av den **auktorisera** knappen när den **token upphör att gälla** och distribuera den länkade tjänsten. Du kan också generera värdena för **sessionId** och **auktorisering** egenskaper programmässigt med hjälp av koden på följande sätt:

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

Se [AzureDataLakeStoreLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), och [AuthorizationSessionGetResponse klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) avsnitt hittar du information om Data Factory-klasser som används i koden. Lägg till en referens till: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för WindowsFormsWebAuthenticationDialog-klassen. 

## <a name="azure-sql-linked-service"></a>Azure SQL länkade tjänsten
Du skapar en Azure SQL-länkade tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. Se [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du skapar en länkad Azure SQL Data Warehouse-tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. Se [Azure SQL Data Warehouse-Anslutningsapp](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

## <a name="sql-server-linked-service"></a>SQLServer länkade tjänsten
Du skapar en SQL Server som är länkad tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. Se [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

