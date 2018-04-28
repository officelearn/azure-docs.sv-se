---
title: Compute-miljöer som stöds av Azure Data Factory | Microsoft Docs
description: Mer information om beräkning miljöer där du kan använda i Azure Data Factory pipelines (till exempel Azure HDInsight) till omvandlings- eller data.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: 6f9f0f9a9bab7e6865ae5a48552ac702ae2bf6fb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-miljöer som stöds av Azure Data Factory
Den här artikeln beskrivs olika beräknings-miljöer som du kan använda för att bearbeta eller Transformera data. Det ger också information om olika konfigurationer (på begäran eller ta med din egen) som stöds av Data Factory när du konfigurerar länkade tjänster länka dessa compute miljöer till ett Azure data factory.

Följande tabell innehåller en lista över compute-miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                      | activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [egna HDInsight-kluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop-strömning](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [Anpassad](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-aktiviteter: batchkörning och resursuppdatering](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQLServer](#sql-server-linked-service) | [Lagrad procedur](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>På begäran beräknings-miljö
I den här typen av konfiguration hanteras datormiljön fullständigt av Azure Data Factory-tjänsten. Den skapas automatiskt av tjänsten Data Factory innan ett jobb har skickats för bearbetning av data och tas bort när jobbet har slutförts. Du kan skapa en länkad tjänst för beräkning på begäran-miljön, konfigurera den och styra detaljerade inställningar för jobbkörningen klusterhantering och startprogram åtgärder.

> [!NOTE]
> På begäran-konfiguration stöds för närvarande endast för Azure HDInsight-kluster.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure på begäran länkad HDInsight-tjänst
Azure Data Factory-tjänsten kan automatiskt skapa ett på begäran HDInsight-kluster för bearbetning av data. Klustret skapas i samma region som lagringskontot (linkedServiceName-egenskapen i JSON) som är associerade med klustret. Lagringskontot måste vara en generell Azure storage-standardkonto. 

Observera följande **viktiga** punkter om på begäran HDInsight länkade tjänsten:

* HDInsight-kluster på begäran skapas under din Azure-prenumeration. Du kan se i klustret i Azure-portalen när klustret är igång och körs. 
* Jobb som körs på ett HDInsight-kluster på begäran finns i loggarna kopieras till storage-konto som är associerade med HDInsight-klustret. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword som definierats i din länkade tjänstdefinitionen används för att logga in i klustret för avancerad felsökning under livscykeln för klustret. 
* Du debiteras endast för tiden när HDInsight-klustret är igång och jobb som körs.

> [!IMPORTANT]
> Det tar vanligtvis **20 minuter** eller mer att etablera ett Azure HDInsight-kluster på begäran.
>
> 

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight länkad tjänst. Data Factory-tjänsten skapar automatiskt en **Linux-baserade** HDInsight-kluster att bearbeta aktiviteten. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> HDInsight-klustret skapar en **standardbehållare** i den blobblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här behållaren när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar. 
>
> När fler aktiviteter körs, kan du se många behållare i din Azure blob storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort behållare i din Azure Blob-lagring.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Typegenskapen bör anges till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antal worker/data noder i klustret. HDInsight-kluster skapas med 2 huvudnoderna tillsammans med antalet arbetarnoder som du anger för den här egenskapen. Noder har storlek Standard_D3 med 4 kärnor, så ett kluster med noder 4 worker tar 24 kärnor (4\*4 = 16 kärnor för arbetarnoder plus 2\*4 = 8 kärnor för huvudnoderna). Se [ställa in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) information. | Ja      |
| linkedServiceName            | Azure Storage länkade tjänsten som ska användas av klustret på begäran för lagring och bearbetning av data. HDInsight-klustret skapas i samma region som Azure Storage-konto. Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. Kontrollera att du har tillräckligt med core kvoter i den Azure-regionen som uppfyller de nödvändiga clusterSize. Mer information finns i [ställa in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>För närvarande kan du skapa ett HDInsight-kluster med på begäran som använder ett Azure Data Lake Store som lagring. Om du vill lagra Resultatdata från HDInsight som bearbetas i en Azure Data Lake Store kan du använda en Kopieringsaktiviteten för att kopiera data från Azure Blobblagring till Azure Data Lake Store. </p> | Ja      |
| clusterResourceGroup         | HDInsight-klustret har skapats i den här resursgruppen. | Ja      |
| TimeToLive                   | Tillåten inaktivitetstid för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir aktiva efter slutförande av en aktivitet som kör om det finns inga aktiva jobb i klustret. Den minsta tillåtna värdet är 5 minuter (00: 05:00).<br/><br/>Till exempel om en aktivitet kör tar 6 minuter och timetolive är inställd på 5 minuter, förblir klustret aktiv på 5 minuter efter 6 minuter för aktiviteten kör. Om en annan aktivitet kör körs med fönstret 6 minuter, bearbetas men det av samma kluster.<br/><br/>Skapar ett HDInsight-kluster på begäran är en kostsam åtgärd (kan ta en stund), så Använd den här inställningen som krävs för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger timetolive-värdet till 0 bort klustret som kör aktiviteten har slutförts. Men om du anger ett högt värde kan klustret kan vara inaktiv för inloggning för felsökning av vissa syfte, men det kan resultera i höga kostnader. Det är därför viktigt att du har angett rätt värde baserat på dina behov.<br/><br/>Om egenskapen timetolive-värdet är korrekt, delar flera pipelines instans av HDInsight-kluster på begäran. | Ja      |
| clusterType                  | Typ av HDInsight-klustret skapas. Tillåtna värden är ”hadoop” och ”spark”. Om inget anges är standardvärdet hadoop. | Nej       |
| version                      | Version av HDInsight-klustret. Om den inte anges används den aktuella HDInsight definierade standardversionen. | Nej       |
| hostSubscriptionId           | Azure prenumerations-ID som används för att skapa HDInsight-kluster. Om inget anges används prenumerations-ID för Azure-inloggning-kontexten. | Nej       |
| clusterNamePrefix           | Prefixet för HDI klustrets namn, en tidsstämpel läggs automatiskt i slutet av klustrets namn| Nej       |
| sparkVersion                 | Versionen av spark om typ av kluster är ”Spark” | Nej       |
| additionalLinkedServiceNames | Anger ytterligare lagringskonton för till HDInsight länkade tjänsten så att Data Factory-tjänsten kan registrera dem å dina vägnar. Dessa storage-konton måste vara i samma region som HDInsight-kluster som skapas i samma region som lagringskontot som anges av linkedServiceName. | Nej       |
| osType                       | Typ av operativsystem. Tillåtna värden är: Linux och Windows (för HDInsight-3.3). Standard är Linux. | Nej       |
| hcatalogLinkedServiceName    | Namnet på Azure SQL länkade tjänst som pekar på HCatalog-databasen. HDInsight-kluster på begäran har skapats med Azure SQL-databas som metastore. | Nej       |
| connectVia                   | Integration körningsmiljön som används för att skicka aktiviteter till den här HDInsight länkade tjänsten. För på-begäran HDInsight länkad tjänst stöder endast Azure Integration Runtime. Om inget anges används standard-Azure Integration Runtime. | Nej       |
| clusterUserName                   | Användarnamnet får åtkomst till klustret. | Nej       |
| clusterPassword                   | Lösenord i typen av säker sträng får åtkomst till klustret. | Nej       |
| clusterSshUserName         | Användarnamnet för SSH fjärransluta till nod i klustret (för Linux). | Nej       |
| clusterSshPassword         | Lösenord i typen av säker sträng till SSH fjärransluta klustrets nod (för Linux). | Nej       |


> [!IMPORTANT]
> HDInsight stöder flera Hadoop-klusterversioner som kan distribueras. Varje version alternativ skapas en viss version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter som ingår i distributionen. Listan över versioner som stöds HDInsight behåller uppdateras för att tillhandahålla senaste Hadoop-ekosystemet komponenterna och korrigeringar. Kontrollera att du alltid se senaste information av [stöds HDInsight-version och typ av operativsystem](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) att se till att du använder HDInsight version som stöds. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON-exempel

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

På begäran HDInsight länkade tjänsten kräver ett huvudnamn autentiseringen av tjänsten att skapa HDInsight-kluster för din räkning. Du använder service principal autentisering, registrera en Programenhet i Azure Active Directory (Azure AD) och bevilja den **deltagare** rollen för prenumerationen eller resursgrupp som HDInsight-klustret skapas. Detaljerade anvisningar finns i [använda portalen för att skapa ett Azure Active Directory applikationen eller tjänsten säkerhetsobjekt som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Nyckeln för programmet 
- Klient-ID:t

Använd service principal autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckeln.           | Ja      |
| **Klient**              | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja      |

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
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
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
Finns det [storlekar för virtuella datorer](../virtual-machines/linux/sizes.md) artikel strängvärde måste du ange för egenskaper som beskrivs i föregående avsnitt. Värden måste överensstämma med den **CMDLETs & API: er** refereras i artikeln. Som du ser i artikeln har datanoden för stor (standard) storlek 7 GB minne, vilket inte kanske är bra för ditt scenario. 

Om du vill skapa D4 storlek huvudnoderna och arbetsnoder ange **Standard_D4** som värdet för egenskaperna headNodeSize och dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper visas följande **fel:** gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: 'PreClusterCreationValidationFailure'. När du får det här felet kan du se till att du använder den **CMDLET & API: er** namn från tabellen i den [storlekar för virtuella datorer](../virtual-machines/linux/sizes.md) artikel.        

## <a name="bring-your-own-compute-environment"></a>Ta med din egen beräknings-miljö
I den här typen av konfiguration kan användare registrera en redan befintlig datormiljö som en länkad tjänst i Data Factory. Datormiljön hanteras av användaren och Data Factory-tjänsten används för att utföra aktiviteter.

Den här typen av konfiguration stöds för följande beräknings-miljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQLServer

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight länkad tjänst
Du kan skapa en Azure HDInsight länkad tjänst för att registrera ditt eget kluster i HDInsight med Data Factory.

### <a name="example"></a>Exempel

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
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
| connectVia        | Integration Runtime som används för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime. Om inget anges används standard-Azure Integration Runtime. | Nej       |

> [!IMPORTANT]
> HDInsight stöder flera Hadoop-klusterversioner som kan distribueras. Varje version alternativ skapas en viss version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter som ingår i distributionen. Listan över versioner som stöds HDInsight behåller uppdateras för att tillhandahålla senaste Hadoop-ekosystemet komponenterna och korrigeringar. Kontrollera att du alltid se senaste information av [stöds HDInsight-version och typ av operativsystem](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) att se till att du använder HDInsight version som stöds. 
>

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst

Du kan skapa en Azure Batch länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) till en data factory. Du kan köra anpassade aktiviteten med Azure Batch.

Se följande avsnitt om du har använt Azure Batch-tjänsten:

* [Grunderna i Azure Batch](../batch/batch-technical-overview.md) en översikt över Azure Batch-tjänsten.
* [Nya AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) för att skapa ett Azure Batch-konto (eller) [Azure-portalen](../batch/batch-account-create-portal.md) skapa Azure Batch-kontot med hjälp av Azure portal. Se [använda PowerShell för att hantera Azure Batch-kontot](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) avsnittet detaljerade anvisningar om hur du använder cmdlet.
* [Nya AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) för att skapa en Azure Batch-pool.

### <a name="example"></a>Exempel

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Egenskaper
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Typegenskapen bör anges till **AzureBatch**. | Ja      |
| Kontonamn       | Namnet på Azure Batch-kontot.         | Ja      |
| accessKey         | Åtkomstnyckeln för Azure Batch-kontot.  | Ja      |
| batchUri          | URL till Azure Batch-kontot, i formatet för https://*batchaccountname.region*. batch.azure.com. | Ja      |
| Poolnamn          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på Azure Storage länkade tjänst som är associerad med den här Azure Batch länkade tjänsten. Den här länkade tjänsten används för mellanlagring av filer som krävs för att köra aktiviteten. | Ja      |
| connectVia        | Integration Runtime som används för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime. Om inget anges används standard-Azure Integration Runtime. | Nej       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du kan skapa en Azure Machine Learning länkad tjänst för att registrera en Machine Learning-batch bedömningsslutpunkten till en data factory.

### <a name="example"></a>Exempel

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap               | Beskrivning                              | Krävs                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Typegenskapen bör anges till: **AzureML**. | Ja                                      |
| mlEndpoint             | URL för batchbedömningsjobbet.                   | Ja                                      |
| apiKey                 | Den publicerade arbetsytemodellens API.     | Ja                                      |
| updateResourceEndpoint | Uppdatera resurs-URL för en Azure ML Web Service-slutpunkt som används för att uppdatera förutsägande webbtjänsten med utbildade modellfil | Nej                                       |
| servicePrincipalId     | Ange programmets klient-ID.     | Krävs om updateResourceEndpoint har angetts |
| servicePrincipalKey    | Ange programmets nyckeln.           | Krävs om updateResourceEndpoint har angetts |
| klient                 | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Krävs om updateResourceEndpoint har angetts |
| connectVia             | Integration Runtime som används för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime. Om inget anges används standard-Azure Integration Runtime. | Nej                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka ett Azure Data Lake Analytics compute-tjänst till ett Azure data factory. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

### <a name="example"></a>Exempel

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Egenskaper

| Egenskap             | Beskrivning                              | Krävs                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| typ                 | Typegenskapen bör anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| Kontonamn          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics-URI.           | Nej                                       |
| subscriptionId       | Azure prenumerations-id                    | Nej (om den inte anges data factory-prenumeration används). |
| resourceGroupName    | Azure resursgruppens namn                | Nej (om inget annat anges, resursgruppen av datafabriken används). |
| servicePrincipalId   | Ange programmets klient-ID.     | Ja                                      |
| servicePrincipalKey  | Ange programmets nyckeln.           | Ja                                      |
| klient               | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja                                      |
| connectVia           | Integration Runtime som används för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime. Om inget anges används standard-Azure Integration Runtime. | Nej                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks länkad tjänst
Du kan skapa **Azure Databricks länkade tjänsten** att registrera Databricks arbetsytan som du ska använda för att köra Databricks workloads(notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Exempel: med den nya jobb klustret i Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exempel – med befintliga interaktiva klustret i Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Egenskaper

| Egenskap             | Beskrivning                              | Krävs                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| namn                 | Namnet på den länkade tjänsten               | Ja   |
| typ                 | Typegenskapen bör anges till: **AzureDatabricks**. | Ja                                      |
| domän               | Ange Azure-Region därefter baserat på regionen i arbetsytan Databricks. Exempel: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Åtkomsttoken krävs för Data Factory för att autentisera till Azure Databricks. Åtkomsttoken behöver genereras från arbetsytan databricks. Mer detaljerade steg för att hitta den åtkomst-token kan hittas [här](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ja                                       |
| existingClusterId    | Kluster-ID för ett befintligt kluster som kör alla jobb på den här. Detta bör vara ett redan har skapat interaktiva kluster. Du kan behöva starta om klustret slutar svara. Databricks föreslår jobb som körs på nya kluster för högre tillförlitlighet. Du hittar ID för kluster i en interaktiv kluster på arbetsytan -> Databricks-kluster > interaktiv klustrets namn -> Configuration->-taggar. [Mer information](https://docs.databricks.com/user-guide/clusters/tags.html) | Nej 
| newClusterVersion    | Spark-version av klustret. Den skapar ett kluster som jobbet i databricks. | Nej  |
| newClusterNumOfWorker| Antalet arbetarnoder som det här klustret ska ha. Ett kluster har en drivrutin som Spark och num_workers Utförarna för totalt num_workers + 1 Spark-noder. En sträng formaterad Int32, som ”1” betyder numOfWorker är 1 eller ”1:10” innebär Autoskala från 1 som min och 10 som max.  | Nej                |
| newClusterNodeType   | Det här fältet kodar via ett enskilt värde resurser tillgängliga för varje Spark-nod i det här klustret. Till exempel Spark noder kan etableras och optimeras för minne eller beräkning arbetsbelastningar i det här fältet krävs för nytt kluster                | Nej               |
| newClusterSparkConf  | en uppsättning valfria, användardefinierade Spark configuration nyckel-värdepar. Användare kan också skicka i en sträng med extra JVM alternativ till drivrutinen och executors via spark.driver.extraJavaOptions och spark.executor.extraJavaOptions respektive. | Nej  |


## <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst
Du skapar en Azure SQL-länkade tjänst och använda den med den [lagrade Proceduraktiviteten](transform-data-using-stored-procedure.md) att anropa en lagrad procedur från Data Factory-pipelinen. Se [Azure SQL Connector](connector-azure-sql-database.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du skapar en länkad Azure SQL Data Warehouse-tjänst och använda den med den [lagrade Proceduraktiviteten](transform-data-using-stored-procedure.md) att anropa en lagrad procedur från Data Factory-pipelinen. Se [Azure SQL Data Warehouse-Anslutningsapp](connector-azure-sql-data-warehouse.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

## <a name="sql-server-linked-service"></a>SQL Server som är länkad tjänst
Du skapar en SQL Server som är länkad tjänst och använda den med den [lagrade Proceduraktiviteten](transform-data-using-stored-procedure.md) att anropa en lagrad procedur från Data Factory-pipelinen. Se [SQL Server-anslutningen](connector-sql-server.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

## <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - namngivningsregler
Följande tabell innehåller namngivningsregler för Data Factory-artefakter.

| Namn                             | Unika namn                          | Kontroller                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Unikt över Microsoft Azure. Namn är inte skiftlägeskänsliga, det vill säga `MyDF` och `mydf` referera till samma data factory. | <ul><li>Varje datafabriken är kopplad till en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller en siffra och får innehålla endast bokstäver, siffror och streck (-).</li><li>Varje streck (-) måste föregås och följas av en bokstav eller en siffra. Streck i följd är inte tillåtna i behållarnamn.</li><li>Namnet kan innehålla 3-63 tecken.</li></ul> |
| Länkade tjänster/tabeller/pipelines | Unikt med i en data factory. Namn är inte skiftlägeskänsliga. | <ul><li>Maximalt antal tecken i ett tabellnamn: 260.</li><li>Objektnamn måste börja med en bokstav, siffra eller ett understreck (_).</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/” ”, <” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |
| Resursgrupp                   | Unikt över Microsoft Azure. Namn är inte skiftlägeskänsliga. | <ul><li>Maximalt antal tecken: 1000.</li><li>Namnet får innehålla bokstäver, siffror och följande tecken ”:-” ”, _” ”,,” och ””.</li></ul> |

## <a name="next-steps"></a>Nästa steg
En lista över omvandling av aktiviteter som stöds av Azure Data Factory finns [Transformera data](transform-data.md).
