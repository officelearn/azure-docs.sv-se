---
title: Compute-miljöer som stöds av Azure Data Factory | Microsoft Docs
description: Läs mer om beräkningsmiljöer som du kan använda i Azure Data Factory pipelines (till exempel Azure HDInsight) för att transformera eller bearbeta data.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: douglasl
ms.openlocfilehash: 110005469d5ff42af10b29fcee97c2f130ecdc2d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873839"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Compute-miljöer som stöds av Azure Data Factory
Den här artikeln beskrivs olika beräkningsmiljöer som du kan använda för att bearbeta och omvandla data. Den innehåller också information om olika konfigurationer (på begäran och ta med din egen) som stöds av Data Factory när du konfigurerar länkade tjänster länkar dessa compute-miljöer i en Azure-datafabrik.

Följande tabell innehåller en lista över de beräkningsmiljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [ett eget HDInsight-kluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Anpassad](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-aktiviteter: batchkörning och resursuppdatering](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQLServer](#sql-server-linked-service) | [Lagrad procedur](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Anteckningsboken](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-compute-environment"></a>På begäran beräkningsmiljö
I den här typen av konfiguration kan hanteras helt datormiljön av Azure Data Factory-tjänsten. Den skapas automatiskt av Data Factory-tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan skapa en länkad tjänst för beräkningsmiljö för på begäran, konfigurera den och styra detaljerade inställningar för jobbkörning, klusterhantering och start av åtgärder.

> [!NOTE]
> På begäran-konfiguration stöds för närvarande endast för Azure HDInsight-kluster.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight på begäran länkad tjänst
Azure Data Factory-tjänsten kan automatiskt skapa ett kluster för HDInsight på begäran för att bearbeta data. Klustret skapas i samma region som lagringskontot (linkedServiceName-egenskapen i JSON) som är associerade med klustret. Lagringskontot måste vara ett allmänt Azure storage-standardkonto. 

Observera följande **viktiga** punkter om på begäran HDInsight-länkad tjänst:

* HDInsight-kluster på begäran skapas under din Azure-prenumeration. Är du kunna se kluster i Azure portal när klustret är igång och körs. 
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till lagringskontot som associerats med HDInsight-kluster. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword som definierats i din länkade tjänstdefinitionen används för att logga in i kluster för djupgående felsökning under livscykeln för klustret. 
* Du debiteras endast för den tid när HDInsight-klustret är igång och jobb som körs.
* Skriptåtgärd stöds nu med Azure HDInsight på begäran länkad tjänst.  

> [!IMPORTANT]
> Det tar vanligtvis **20 minuter** eller mer att etablera ett Azure HDInsight-kluster på begäran.

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight-länkad tjänst. Tjänsten Data Factory skapar automatiskt en **Linux-baserade** HDInsight-kluster för att bearbeta aktiviteten. 

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
> HDInsight-klustret skapar en **standardcontainer** i den bloblagring som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar. 
>
> Allteftersom mer aktivitet körs kan se du många behållare i Azure blob storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Lagringsutforskaren](http://storageexplorer.com/) till att ta bort containrar i din Azure bloblagring.
>
> 

### <a name="properties"></a>Egenskaper
| Egenskap                      | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Type-egenskapen ska anges till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antal worker/data noder i klustret. HDInsight-klustret har skapats med 2 huvudnoder tillsammans med antalet arbetsnoder som du anger för den här egenskapen. Noderna är storlek Standard_D3 med 4 kärnor, så att ett kluster med noder 4 worker tar 24 kärnor (4\*4 = 16 kärnor för arbetsnoder plus 2\*4 = 8 kärnor för huvudnoder). Se [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) mer information. | Ja      |
| linkedServiceName            | Länkad Azure Storage-tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. HDInsight-klustret har skapats i samma region som Azure Storage-kontot. Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. Kontrollera att du har tillräckligt med kärnkvoter i den Azure-regionen som uppfyller de nödvändiga clusterSize. Mer information finns att [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>För närvarande kan skapa du inte en på begäran HDInsight-kluster som använder en Azure Data Lake Store som lagring. Om du vill lagra Resultatdata från HDInsight i ett Azure Data Lake Store, kan du använda en Kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure Data Lake Store. </p> | Ja      |
| clusterResourceGroup         | HDInsight-klustret har skapats i den här resursgruppen. | Ja      |
| TimeToLive                   | Tillåtna inaktivitetstiden för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir aktiv efter det att en aktivitet som körs om det finns inga aktiva jobb i klustret. Den minsta tillåtna värdet är 5 minuter (00: 05:00).<br/><br/>Till exempel om en aktivitet kör tar 6 minuter och timetolive är inställd på 5 minuter, kvar klustret alive under 5 minuter efter 6 minuter bearbeta aktiviteten körs. Om en annan aktivitet som kör körs med fönstret 6 minuter, bearbetas den av samma kluster.<br/><br/>Det är en kostsam åtgärd (kan ta en stund), så Använd den här inställningen som krävs för att förbättra prestanda för en data factory genom att återanvända ett HDInsight-kluster på begäran att skapa ett HDInsight-kluster på begäran.<br/><br/>Om du ställer in timetolive-värdet till 0 tas klustret bort när aktiviteten körningen har slutförts. Å andra sidan om du ställer in ett högt värde för klustret kan vara inaktiv för inloggning för felsökning av vissa syfte, men det kan leda till höga kostnader. Det är därför viktigt att du ställer in lämpligt värde utifrån dina behov.<br/><br/>Om timetolive egenskapens värde är korrekt inställt, kan flera pipelines delar instans av HDInsight-kluster på begäran. | Ja      |
| clusterType                  | Typ av HDInsight-klustret skapas. Tillåtna värden är ”hadoop” och ”spark”. Om inte anges är standardvärdet hadoop. Enterprise Security Package aktiverat kluster stöds inte för tillfället | Nej       |
| version                      | Versionen av HDInsight-klustret. Om den inte anges används den aktuella HDInsight definierade standardversionen. | Nej       |
| hostSubscriptionId           | Azure-prenumerations-ID som används för att skapa HDInsight-kluster. Om inte anges använder prenumerations-ID för din Azure-inloggningskontext. | Nej       |
| clusterNamePrefix           | Prefixet för HDI-klustrets namn, en tidsstämpel läggs automatiskt i slutet av klustrets namn| Nej       |
| sparkVersion                 | Version av Apache spark om typ av kluster är ”Spark” | Nej       |
| additionalLinkedServiceNames | Anger ytterligare lagringskonton för HDInsight-länkad tjänst så att Data Factory-tjänsten kan registrera dem för din räkning. Dessa konton måste vara i samma region som HDInsight-kluster som skapas i samma region som lagringskontot som anges av linkedServiceName. | Nej       |
| osType                       | Typ av operativsystem. Tillåtna värden är: Linux och Windows (för HDInsight-3.3). Standardvärdet är Linux. | Nej       |
| hcatalogLinkedServiceName    | Namnet på Azure SQL länkade tjänst som pekar på HCatalog-databasen. HDInsight-kluster på begäran skapas med hjälp av Azure SQL-databas som metaarkiv. | Nej       |
| connectVia                   | Integration Runtime som ska användas för att skicka ut aktiviteter till den här länkade HDInsight-tjänsten. För på begäran länkad HDInsight-tjänsten stöder bara Azure Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej       |
| clusterUserName                   | Användarnamnet för att få åtkomst till klustret. | Nej       |
| clusterPassword                   | Lösenordet i typen av säker sträng för åtkomst till klustret. | Nej       |
| clusterSshUserName         | Användarnamnet för SSH fjärransluta till klustrets nod (för Linux). | Nej       |
| clusterSshPassword         | Lösenordet i typ av säker sträng SSH fjärransluta klustrets nod (för Linux). | Nej       |


> [!IMPORTANT]
> HDInsight har stöd för flera Hadoop-klusterversioner som kan distribueras. Varje version alternativ skapar en specifik version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter som ingår i distributionen. Listan över HDInsight-versioner som stöds och ser till att uppdateras för att ge senaste Hadoop-ekosystemet-komponenter och korrigeringar. Kontrollera att du alltid gå till senaste information för [stöds HDInsight version och OS-typ](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) att se till att du använder version som stöds av HDInsight. 
>
> 
> [!IMPORTANT]
> För närvarande HDInsight länkade tjänster inte stöder HBase, Interactive Query (LLAP Hive), Storm och Enterprise Security aktiverat (domänansluten) kluster. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionallinkedservicenames, JSON-exempel

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

På begäran HDInsight-länkad tjänst kräver en autentisering av tjänstens huvudnamn att skapa HDInsight-kluster för din räkning. Att använda autentisering av tjänstens huvudnamn, registrera ett program-entiteten i Azure Active Directory (Azure AD) och bevilja den **deltagare** rollen för prenumerationen eller resursgruppen som HDInsight-klustret har skapats. Detaljerade anvisningar finns i [Använd portalen för att skapa en Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel 
- Klient-ID:t

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap                 | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **klient**              | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja      |

### <a name="advanced-properties"></a>Avancerade egenskaper

Du kan också ange följande egenskaper för detaljerad konfiguration av HDInsight-kluster på begäran.

| Egenskap                | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger konfigurationsparametrar core (som i core-site.xml) för att skapa HDInsight-klustret. | Nej       |
| hBaseConfiguration     | Anger HBase konfigurationsparametrar (hbase-site.xml) för HDInsight-klustret. | Nej       |
| hdfsConfiguration      | Anger konfigurationsparametrar för HDFS (hdfs-site.xml) för HDInsight-klustret. | Nej       |
| hiveConfiguration      | Anger konfigurationsparametrar för hive (hive-site.xml) för HDInsight-klustret. | Nej       |
| mapReduceConfiguration | Anger konfigurationsparametrar för MapReduce (mapred-site.xml) för HDInsight-klustret. | Nej       |
| oozieConfiguration     | Anger Oozie konfigurationsparametrar (oozie-site.xml) för HDInsight-klustret. | Nej       |
| stormConfiguration     | Anger de Storm konfigurationsparametrarna (storm-site.xml) för HDInsight-klustret. | Nej       |
| yarnConfiguration      | Anger Yarn konfigurationsparametrar (yarn-site.xml) för HDInsight-klustret. | Nej       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel – på begäran HDInsight-kluster-konfiguration med avancerade egenskaper

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

### <a name="node-sizes"></a>Nodstorlekar
Du kan ange storleken på huvudnoder, datanoder och zookeeper-noder med följande egenskaper: 

| Egenskap           | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| Egenskaper för headNodeSize      | Anger storleken på huvudnoden. Standardvärdet är: Standard_D3. Se den **att ange storleken på** information. | Nej       |
| dataNodeSize      | Anger storleken på datanod. Standardvärdet är: Standard_D3. | Nej       |
| zookeeperNodeSize | Anger storleken på noden Zoo sköter. Standardvärdet är: Standard_D3. | Nej       |

#### <a name="specifying-node-sizes"></a>Anger storleken på
Se den [storlekar för virtuella datorer](../virtual-machines/linux/sizes.md) artikeln strängvärde måste du ange för de egenskaper som nämns i föregående avsnitt. Värdena måste överensstämma med den **cmdletar och API: er** som nämns i artikeln. Som du ser i artikeln har datanoden i stor (standard) 7 GB minne, vilket inte kanske är tillräckligt för ditt scenario. 

Om du vill skapa D4 storlek huvudnoderna och arbetsnoder ange **Standard_D4** som värde för egenskaper för headNodeSize och dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för de här egenskaperna, kan du få följande **fel:** gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: 'PreClusterCreationValidationFailure'. När du får det här felet kan du se till att du använder den **cmdlet: en och API: er** namn från tabellen i den [storlekar för virtuella datorer](../virtual-machines/linux/sizes.md) artikeln.        

## <a name="bring-your-own-compute-environment"></a>Ta med din egen beräkningsmiljö
Användare kan registrera ett befintligt datormiljö som en länkad tjänst i Data Factory i den här typen av konfiguration. Datormiljön hanteras av användaren och Data Factory-tjänsten används för att köra aktiviteterna.

Den här typen av konfiguration stöds för följande beräkningsmiljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQLServer

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight-länkad tjänst
Du kan skapa en Azure HDInsight-länkad tjänst för att registrera ett eget HDInsight-kluster med Data Factory.

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
| Egenskap           | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Type-egenskapen ska anges till **HDInsight**. | Ja      |
| clusterUri        | URI för HDInsight-klustret.        | Ja      |
| användarnamn          | Ange namnet på användaren som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| lösenord          | Ange lösenordet för användarkontot.   | Ja      |
| linkedServiceName | Namnet på den länkade Azure Storage-tjänst som refererar till Azure blob-lagring som används av HDInsight-klustret. <p>För närvarande kan ange du inte en Azure Data Lake Store-länkad tjänst för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Azure Data Lake Store från Hive/Pig-skript. </p> | Ja      |
| connectVia        | Integration Runtime som ska användas för att skicka ut aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller lokal Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej       |

> [!IMPORTANT]
> HDInsight har stöd för flera Hadoop-klusterversioner som kan distribueras. Varje version alternativ skapar en specifik version av Hortonworks Data Platform (HDP)-distribution och en uppsättning komponenter som ingår i distributionen. Listan över HDInsight-versioner som stöds och ser till att uppdateras för att ge senaste Hadoop-ekosystemet-komponenter och korrigeringar. Kontrollera att du alltid gå till senaste information för [stöds HDInsight version och OS-typ](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) att se till att du använder version som stöds av HDInsight. 
>
> [!IMPORTANT]
> För närvarande HDInsight länkade tjänster inte stöder HBase, Interactive Query (LLAP Hive), Storm och Enterprise Security aktiverat (domänansluten) kluster. 
>
> 

## <a name="azure-batch-linked-service"></a>Tjänsten Azure Batch länkad

Du kan skapa en Azure Batch-länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) till en data factory. Du kan köra anpassade aktiviteten med Azure Batch.

Se följande avsnitt om du är nybörjare på Azure Batch-tjänsten:

* [Azure Batch-grunder](../batch/batch-technical-overview.md) en översikt över Azure Batch-tjänsten.
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet för att skapa ett Batch-konto (eller) [Azure-portalen](../batch/batch-account-create-portal.md) att skapa Azure Batch-konto med hjälp av Azure portal. Se [använda PowerShell för att hantera Azure Batch-konto](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) avsnittet detaljerade anvisningar om hur du använder cmdlet: en.
* [Ny-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet för att skapa en Azure Batch-pool.

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
| Egenskap           | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Type-egenskapen ska anges till **AzureBatch**. | Ja      |
| Kontonamn       | Namnet på Azure Batch-kontot.         | Ja      |
| accessKey         | Åtkomstnyckel för Azure Batch-kontot.  | Ja      |
| batchUri          | URL: en till din Azure Batch-konto i formatet https://*batchaccountname.region*. batch.azure.com. | Ja      |
| Poolnamn          | Namnet på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på Azure Storage länkade tjänsten som är associerad med den här Azure Batch-länkade tjänsten. Den här länkade tjänsten används för mellanlagring av filer som krävs för att köra aktiviteten. | Ja      |
| connectVia        | Integration Runtime som ska användas för att skicka ut aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller lokal Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Skapar du en Azure Machine Learning-länkad tjänst för att registrera en Machine Learning batch bedömningsslutpunkten till en data factory.

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
| Egenskap                | Beskrivning                              | Krävs                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Type-egenskapen ska anges till: **AzureML**. | Ja                                      |
| mlEndpoint             | Batchbedömnings-URL: en.                   | Ja                                      |
| ApiKey                 | Den publicerade arbetsytemodellens API.     | Ja                                      |
| updateResourceEndpoint | Uppdatera resurs-URL för en Azure ML Web Service-slutpunkt som används för att uppdatera förutsägande webbtjänsten med tränade modellfilen | Nej                                       |
| servicePrincipalId     | Ange programmets klient-ID.     | Krävs om updateResourceEndpoint har angetts |
| servicePrincipalKey    | Ange programmets nyckel.           | Krävs om updateResourceEndpoint har angetts |
| klient                 | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Krävs om updateResourceEndpoint har angetts |
| connectVia             | Integration Runtime som ska användas för att skicka ut aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller lokal Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** compute service till en Azure data factory-länkade tjänst som länkar ett Azure Data Lake Analytics. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

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

| Egenskap              | Beskrivning                              | Krävs                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| typ                 | Type-egenskapen ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| Kontonamn          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics-URI.           | Nej                                       |
| subscriptionId       | Azure-prenumerations-id                    | Nej                                       |
| resourceGroupName    | Azure-resursgruppsnamn                | Nej                                       |
| servicePrincipalId   | Ange programmets klient-ID.     | Ja                                      |
| servicePrincipalKey  | Ange programmets nyckel.           | Ja                                      |
| klient               | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja                                      |
| connectVia           | Integration Runtime som ska användas för att skicka ut aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller lokal Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej                                       |



## <a name="azure-databricks-linked-service"></a>Azure länkad Databricks-tjänst
Du kan skapa **Azure Databricks-länkad tjänst** att registrera Databricks-arbetsyta som du ska använda för att köra Databricks workloads(notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Exempel – med den nya jobb kluster i Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exempel – med befintliga interaktiva kluster i Databricks

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

| Egenskap              | Beskrivning                              | Krävs                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| namn                 | Namnet på den länkade tjänsten               | Ja   |
| typ                 | Type-egenskapen ska anges till: **AzureDatabricks**. | Ja                                      |
| domän               | Ange den Azure-Region som därefter baserat på regionen i Databricks-arbetsytan. Exempel: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Åtkomst-token krävs för Data Factory för att autentisera till Azure Databricks. Åtkomsttoken behöver skapas databricks-arbetsytan. Mer detaljerade steg för att hitta den åtkomst-token kan hittas [här](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ja                                       |
| existingClusterId    | Kluster-ID för ett befintligt kluster att köra alla jobb på detta. Det bör vara ett redan skapat interaktiva kluster. Du kan behöva starta om klustret manuellt om den slutar svara. Databricks föreslår jobb som körs på nya kluster för större tillförlitlighet. Du hittar ID för kluster av en interaktiv kluster i Databricks-Arbetsyta -> kluster -> interaktiva klustrets namn -> konfigurationsserver -> taggar. [Mer information](https://docs.databricks.com/user-guide/clusters/tags.html) | Nej 
| newClusterVersion    | Spark-versionen av klustret. Det skapar ett jobb-kluster i databricks. | Nej  |
| newClusterNumOfWorker| Antalet arbetsnoder som det här klustret ska ha. Ett kluster har en Spark-drivrutinen och num_workers Executors totalt num_workers + 1 Spark-noder. En sträng formaterad Int32, som ”1” innebär att numOfWorker är 1 eller ”1:10” innebär att automatisk skalning från 1 som min och 10 som max.  | Nej                |
| newClusterNodeType   | Det här fältet kodar via ett enda värde, resurserna som är tillgängliga för var och en av Spark-noder i det här klustret. Till exempel Spark noder kan etableras och optimerade för beräkningsintensiva arbetsbelastningar för minne eller beräkning det här fältet är obligatoriskt för nytt kluster                | Nej               |
| newClusterSparkConf  | en uppsättning valfria, användardefinierade Spark configuration nyckel / värde-par. Användare kan även skicka i en sträng med extra JVM-alternativ till drivrutinen och executors via spark.driver.extraJavaOptions och spark.executor.extraJavaOptions respektive. | Nej  |


## <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst
Du skapar en länkad Azure SQL-tjänst och använda det med den [lagrade Proceduraktiviteten](transform-data-using-stored-procedure.md) att anropa en lagrad procedur från Data Factory-pipeline. Se [Azure SQL-anslutningen](connector-azure-sql-database.md#linked-service-properties) nedan för information om den här länkade tjänsten.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du skapar en länkad Azure SQL Data Warehouse-tjänst och använda det med den [lagrade Proceduraktiviteten](transform-data-using-stored-procedure.md) att anropa en lagrad procedur från Data Factory-pipeline. Se [Azure SQL Data Warehouse-Anslutningsappen](connector-azure-sql-data-warehouse.md#linked-service-properties) nedan för information om den här länkade tjänsten.

## <a name="sql-server-linked-service"></a>SQL Server-länkade tjänst
Du skapar en länkad SQL Server-tjänst och använda det med den [lagrade Proceduraktiviteten](transform-data-using-stored-procedure.md) att anropa en lagrad procedur från Data Factory-pipeline. Se [SQL Server-anslutningen](connector-sql-server.md#linked-service-properties) nedan för information om den här länkade tjänsten.

## <a name="next-steps"></a>Nästa steg
En lista över transformeringsaktiviteter som stöds av Azure Data Factory finns i [Transformera data](transform-data.md).
