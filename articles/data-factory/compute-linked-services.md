---
title: Beräknings miljöer som stöds av Azure Data Factory | Microsoft Docs
description: Lär dig mer om beräknings miljöer som du kan använda i Azure Data Factory pipelines (till exempel Azure HDInsight) för att transformera eller bearbeta data.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d0fd26da81c4f59f16b5f0364cf165ec36a6ea39
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516334"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Beräknings miljöer som stöds av Azure Data Factory
I den här artikeln beskrivs olika beräknings miljöer som du kan använda för att bearbeta eller transformera data. Den innehåller också information om olika konfigurationer (på begäran eller ta med din egen) som stöds av Data Factory när du konfigurerar länkade tjänster som länkar dessa beräknings miljöer till en Azure Data Factory.

Följande tabell innehåller en lista över beräknings miljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HDInsight-kluster på begäran](#azure-hdinsight-on-demand-linked-service) eller [ditt eget HDInsight-kluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Anpassad](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning aktiviteter: Batch-körning och uppdaterings resurs](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service) [SQL Server](#sql-server-linked-service) | [Lagrad procedur](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>Beräknings miljö för HDInsight på begäran
I den här typen av konfiguration hanteras dator miljön fullständigt av Azure Data Factory tjänsten. Den skapas automatiskt av Data Factory tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan skapa en länkad tjänst för beräknings miljön på begäran, konfigurera den och kontrol lera detaljerade inställningar för jobb körning, kluster hantering och start åtgärder.

> [!NOTE]
> Konfigurationen på begäran stöds för närvarande endast för Azure HDInsight-kluster. Azure Databricks stöder även jobb på begäran med hjälp av jobb kluster, se [Azure Databricks-länkade tjänster](#azure-databricks-linked-service) för mer information.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Länkad Azure HDInsight-tjänst på begäran
Azure Data Factorys tjänsten kan automatiskt skapa ett HDInsight-kluster på begäran för att bearbeta data. Klustret skapas i samma region som lagrings kontot (linkedServiceName-egenskapen i JSON) som är associerad med klustret. Lagrings kontot måste vara ett allmänt standard Azure Storage-konto. 

Observera följande **viktiga** punkter om den länkade HDInsight-tjänsten på begäran:

* HDInsight-klustret på begäran skapas i din Azure-prenumeration. Du kan se klustret i Azure Portal när klustret är igång. 
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till det lagrings konto som är associerat med HDInsight-klustret. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword som definieras i den länkade tjänst definitionen används för att logga in på klustret för djupgående fel sökning under klustrets livs cykel. 
* Du debiteras bara för den tid då HDInsight-klustret är igång.
* Du kan använda en **skript åtgärd** med en länkad Azure HDInsight-tjänst på begäran.  

> [!IMPORTANT]
> Det tar vanligt vis **20 minuter** eller mer att etablera ett Azure HDInsight-kluster på begäran.

### <a name="example"></a>Exempel
Följande JSON definierar en Linux-baserad länkad HDInsight-tjänst på begäran. Tjänsten Data Factory skapar automatiskt ett **Linux-baserat HDInsight-** kluster för att bearbeta den begärda aktiviteten. 

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
> När fler aktiviteter körs ser du många behållare i Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Lagringsutforskaren](https://storageexplorer.com/) till att ta bort containrar i din Azure bloblagring.
>
> 

### <a name="properties"></a>properties
| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Egenskapen Type ska anges till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antalet arbets uppgifter/datanoder i klustret. HDInsight-klustret skapas med två head-noder och antalet arbetsnoder som du anger för den här egenskapen. Noderna har storleks Standard_D3 som har 4 kärnor, så ett kluster med fyra arbets noder tar 24 kärnor (4\*4 = 16 kärnor för arbetsnoder, plus 2\*4 = 8 kärnor för huvudnoder). Se [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka och mycket mer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) för mer information. | Ja      |
| linkedServiceName            | Azure Storage länkad tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. HDInsight-klustret skapas i samma region som det här Azure Storage kontot. Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. Se till att du har tillräckligt många kärn kvoter i Azure-regionen för att uppfylla de nödvändiga clusterSize. Mer information finns i [Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka och mycket mer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder en Azure Data Lake Store som lagrings plats. Om du vill lagra resultat data från HDInsight-bearbetning i en Azure Data Lake Store använder du en kopierings aktivitet för att kopiera data från Azure-Blob Storage till Azure Data Lake Store. </p> | Ja      |
| clusterResourceGroup         | HDInsight-klustret skapas i den här resurs gruppen. | Ja      |
| TimeToLive                   | Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir aktivt efter att en aktivitets körning slutförts om det inte finns några andra aktiva jobb i klustret. Det minsta tillåtna värdet är 5 minuter (00:05:00).<br/><br/>Om en aktivitets körning till exempel tar 6 minuter och TimeToLive har angetts till 5 minuter förblir klustret aktiv i 5 minuter efter 6 minuters bearbetning av aktivitets körningen. Om en annan aktivitets körning körs med fönstret 6 minuter, bearbetas den av samma kluster.<br/><br/>Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (kan ta en stund), så Använd den här inställningen vid behov för att förbättra prestandan hos en data fabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger TimeToLive-värdet till 0, tas klustret bort så snart aktiviteten körs klart. Om du anger ett högt värde kan klustret vara inaktivt så att du kan logga in för vissa fel söknings syfte men det kan resultera i höga kostnader. Därför är det viktigt att du anger rätt värde utifrån dina behov.<br/><br/>Om värdet för egenskapen TimeToLive har angetts korrekt kan flera pipelines dela instansen av HDInsight-klustret på begäran. | Ja      |
| clusterType                  | Typ av HDInsight-kluster som ska skapas. Tillåtna värden är "Hadoop" och "Spark". Om inget värde anges är standardvärdet Hadoop. Det går inte att skapa Enterprise Security Package aktiverat kluster på begäran, i stället använda ett [befintligt kluster/ta med din egen beräkning](#azure-hdinsight-linked-service). | Nej       |
| version                      | Version av HDInsight-klustret. Om det inte anges används den aktuella HDInsight-definierade standard versionen. | Nej       |
| hostSubscriptionId           | ID för Azure-prenumerationen som används för att skapa HDInsight-kluster. Om inget anges används prenumerations-ID: t för din Azure-inloggnings kontext. | Nej       |
| clusterNamePrefix           | Prefixet för HDI-klustrets namn, en tidstämpel läggs automatiskt till i slutet av kluster namnet| Nej       |
| sparkVersion                 | Versionen av Spark om kluster typen är "Spark" | Nej       |
| additionalLinkedServiceNames | Anger ytterligare lagrings konton för den länkade HDInsight-tjänsten så att tjänsten Data Factory kan registrera dem åt dig. Dessa lagrings konton måste finnas i samma region som HDInsight-klustret, som skapas i samma region som det lagrings konto som anges av linkedServiceName. | Nej       |
| osType                       | Typ av operativ system. Tillåtna värden är: Linux och Windows (endast för HDInsight 3,3). Standardvärdet är Linux. | Nej       |
| hcatalogLinkedServiceName    | Namnet på den länkade Azure SQL-tjänsten som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas med hjälp av Azure SQL Database som metaarkiv. | Nej       |
| connectVia                   | Integration Runtime som ska användas för att skicka aktiviteter till den länkade HDInsight-tjänsten. För den länkade HDInsight-tjänsten på begäran stöder den bara Azure Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej       |
| clusterUserName                   | Användar namnet för att få åtkomst till klustret. | Nej       |
| clusterPassword                   | Lösen ordet i typ av säker sträng för att komma åt klustret. | Nej       |
| clusterSshUserName         | Användar namnet för SSH fjärr anslutning till klustrets nod (för Linux). | Nej       |
| clusterSshPassword         | Lösen ordet i typ av säker sträng till SSH fjärran slutet av klustrets nod (för Linux). | Nej       |
| scriptActions | Ange skript för [anpassningar av HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) när du skapar kluster på begäran. <br />För närvarande stöder Azure Data Factory användar gränssnitts redigerings verktyget bara en skript åtgärd, men du kan få igenom denna begränsning i JSON (ange flera skript åtgärder i JSON). | Nej |


> [!IMPORTANT]
> HDInsight stöder flera Hadoop-kluster versioner som kan distribueras. Varje versions val skapar en speciell version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter som ingår i distributionen. Listan över de HDInsight-versioner som stöds fortsätter att uppdateras för att tillhandahålla de senaste komponenterna och korrigeringarna för Hadoop-eko systemet. Se till att du alltid refererar till den senaste informationen om [vilka HDInsight-versioner som stöds och vilken typ av operativ system](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) som stöds. 
>
> [!IMPORTANT]
> För närvarande stöder inte HDInsight-länkade tjänster HBase, interaktiv fråga (Hive LLAP), storm. 
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

Den länkade HDInsight-tjänsten på begäran kräver en tjänstens huvud namns autentisering för att skapa HDInsight-kluster för din räkning. Om du vill använda tjänstens huvud namns autentisering registrerar du en programentitet i Azure Active Directory (Azure AD) och tilldelar den rollen **deltagare** i prenumerationen eller resurs gruppen där HDInsight-klustret skapas. Detaljerade anvisningar finns i [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel 
- Klient-ID:t

Använd tjänstens huvud namns autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Obligatorisk |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **innehav**              | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja      |

### <a name="advanced-properties"></a>Avancerade egenskaper

Du kan också ange följande egenskaper för den detaljerade konfigurationen av HDInsight-klustret på begäran.

| Egenskap               | Beskrivning                              | Obligatorisk |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger kärn konfigurations parametrar (som i site. xml) för att skapa HDInsight-klustret. | Nej       |
| hBaseConfiguration     | Anger konfigurations parametrar för HBase (HBase-site. xml) för HDInsight-klustret. | Nej       |
| hdfsConfiguration      | Anger HDFS-konfigurations parametrar (HDFS-site. xml) för HDInsight-klustret. | Nej       |
| hiveConfiguration      | Anger Hive-konfigurations parametrar (Hive-site. xml) för HDInsight-klustret. | Nej       |
| mapReduceConfiguration | Anger konfigurations parametrar för MapReduce (mapred-site. xml) för HDInsight-klustret. | Nej       |
| oozieConfiguration     | Anger konfigurations parametrar för Oozie (Oozie-site. xml) för HDInsight-klustret. | Nej       |
| stormConfiguration     | Anger Storm-konfigurationsparametrar (Storm-site. xml) för HDInsight-klustret. | Nej       |
| yarnConfiguration      | Anger konfigurations parametrar för garn (yarn-site. xml) för HDInsight-klustret. | Nej       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel – konfiguration av HDInsight-kluster på begäran med avancerade egenskaper

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

### <a name="node-sizes"></a>Node-storlekar
Du kan ange storlekarna på huvud-, data-och Zookeeper-noderna med följande egenskaper: 

| Egenskap          | Beskrivning                              | Obligatorisk |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger Head-nodens storlek. Standardvärdet är: Standard_D3. Mer information finns i avsnittet **Ange Node-storlekar** . | Nej       |
| dataNodeSize      | Anger data nodens storlek. Standardvärdet är: Standard_D3. | Nej       |
| zookeeperNodeSize | Anger storleken på den Zoo Keeper-noden. Standardvärdet är: Standard_D3. | Nej       |

#### <a name="specifying-node-sizes"></a>Ange Node-storlekar
Se [storlekarna för Virtual Machines](../virtual-machines/linux/sizes.md) artikel för sträng värden som du måste ange för egenskaperna som nämns i föregående avsnitt. Värdena måste följa cmdletarna &- **API: er** som refereras till i artikeln. Som du kan se i artikeln har datanoden för stor (standard) storlek 7 GB minne, vilket kanske inte är tillräckligt för ditt scenario. 

Om du vill skapa Standard_D4 för D4 size-Head och Worker, anger du som värde för egenskaperna HeadNodeSize och dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger ett felaktigt värde för dessa egenskaper kan du få följande **fel meddelande:** Det gick inte att skapa klustret. Undantag: Det gick inte att slutföra åtgärden för att skapa klustret. Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Klustrets vänsterkant efter tillstånd: "Error". Meddelande: 'PreClusterCreationValidationFailure'. När du får det här felet måste du kontrol lera att du använder **cmdleten & API: er** namn i tabellen i Virtual Machiness artikel [storlek](../virtual-machines/linux/sizes.md) .        

## <a name="bring-your-own-compute-environment"></a>Ta med din egen beräknings miljö
I den här typen av konfiguration kan användarna registrera en redan befintlig dator miljö som en länkad tjänst i Data Factory. Dator miljön hanteras av användaren och den Data Factory tjänsten använder den för att köra aktiviteterna.

Den här typen av konfiguration stöds för följande beräknings miljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Länkad Azure HDInsight-tjänst
Du kan skapa en länkad Azure HDInsight-tjänst för att registrera ditt eget HDInsight-kluster med Data Factory.

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

### <a name="properties"></a>properties
| Egenskap          | Beskrivning                                                  | Krävs |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | Egenskapen Type ska anges till **HDInsight**.            | Ja      |
| clusterUri        | URI för HDInsight-klustret.                            | Ja      |
| username          | Ange namnet på den användare som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| password          | Ange lösen ordet för användar kontot.                       | Ja      |
| linkedServiceName | Namnet på den länkade tjänsten Azure Storage som refererar till Azure Blob Storage som används av HDInsight-klustret. <p>För närvarande kan du inte ange en Azure Data Lake Store länkad tjänst för den här egenskapen. Om HDInsight-klustret har åtkomst till Data Lake Store, kan du komma åt data i Azure Data Lake Store från Hive/gris-skript. </p> | Ja      |
| isEspEnabled      | Ange*True*om HDInsight-klustret är [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) aktiverat. Standardvärdetär false. | Nej       |
| connectVia        | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om den inte anges används standard Azure Integration Runtime. <br />För Enterprise Security Package (ESP) aktiverat HDInsight-kluster använder du en lokal integration runtime som har en detaljerad linje för klustret, eller så bör det distribueras inuti samma Virtual Network som ESP HDInsight-klustret. | Nej       |

> [!IMPORTANT]
> HDInsight stöder flera Hadoop-kluster versioner som kan distribueras. Varje versions val skapar en speciell version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter som ingår i distributionen. Listan över de HDInsight-versioner som stöds fortsätter att uppdateras för att tillhandahålla de senaste komponenterna och korrigeringarna för Hadoop-eko systemet. Se till att du alltid refererar till den senaste informationen om [vilka HDInsight-versioner som stöds och vilken typ av operativ system](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) som stöds. 
>
> [!IMPORTANT]
> För närvarande stöder inte HDInsight-länkade tjänster HBase, interaktiv fråga (Hive LLAP), storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en Azure Batch länkad tjänst för att registrera en batch-pool med virtuella datorer (VM) till en data fabrik. Du kan köra anpassad aktivitet med Azure Batch.

Se följande avsnitt om du är nybörjare på Azure Batch-tjänsten:

* [Azure Batch grunderna](../batch/batch-technical-overview.md) för en översikt över Azure Batchs tjänsten.
* [New-AzBatchAccount-](/powershell/module/az.batch/New-azBatchAccount) cmdlet för att skapa ett Azure Batch konto (eller) [Azure Portal](../batch/batch-account-create-portal.md) för att skapa Azure Batch-kontot med hjälp av Azure Portal. Detaljerade anvisningar om hur du använder cmdleten finns i avsnittet [använda PowerShell för att hantera Azure Batchs konto](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New-AzBatchPool-](/powershell/module/az.batch/New-AzBatchPool) cmdlet för att skapa en Azure Batch-pool.

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


### <a name="properties"></a>properties
| Egenskap          | Beskrivning                              | Krävs |
| ----------------- | ---------------------------------------- | -------- |
| type              | Egenskapen Type ska anges till **AzureBatch**. | Ja      |
| accountName       | Namnet på Azure Batch kontot.         | Ja      |
| accessKey         | Åtkomst nyckel för Azure Batch kontot.  | Ja      |
| batchUri          | URL till ditt Azure Batch-konto, i formatet https://*batchaccountname. region*. batch.Azure.com. | Ja      |
| poolName          | Namn på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den länkade tjänsten Azure Storage som är kopplad till den här Azure Batch länkade tjänsten. Den här länkade tjänsten används för uppsamlingsfiler som krävs för att köra aktiviteten. | Ja      |
| connectVia        | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du skapar en Azure Machine Learning länkad tjänst för att registrera en Machine Learning slut punkt för batch-poäng till en data fabrik.

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

### <a name="properties"></a>properties
| Egenskap               | Beskrivning                              | Obligatorisk                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | Egenskapen Type ska anges till: **AzureML**. | Ja                                      |
| mlEndpoint             | URL för batch-poäng.                   | Ja                                      |
| apiKey                 | Den publicerade arbets ytans modells API.     | Ja                                      |
| updateResourceEndpoint | Uppdaterings resursens URL för en Azure ML-webbtjänsts slut punkt som används för att uppdatera den förutsägbara webb tjänsten med utbildad modell fil | Nej                                       |
| servicePrincipalId     | Ange programmets klient-ID.     | Krävs om updateResourceEndpoint har angetts |
| servicePrincipalKey    | Ange programmets nyckel.           | Krävs om updateResourceEndpoint har angetts |
| tenant                 | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Krävs om updateResourceEndpoint har angetts |
| connectVia             | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka en Azure Data Lake Analytics Compute-tjänst till en Azure Data Factory. Data Lake Analytics U-SQL-aktiviteten i pipelinen refererar till den här länkade tjänsten. 

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

### <a name="properties"></a>properties

| Egenskap             | Beskrivning                              | Krävs                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | Egenskapen Type ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Azure Data Lake Analytics konto namn.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics-URI.           | Nej                                       |
| subscriptionId       | ID för Azure-prenumeration                    | Nej                                       |
| resourceGroupName    | Azure-resursgruppsnamn                | Nej                                       |
| servicePrincipalId   | Ange programmets klient-ID.     | Ja                                      |
| servicePrincipalKey  | Ange programmets nyckel.           | Ja                                      |
| tenant               | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja                                      |
| connectVia           | Integration Runtime som ska användas för att skicka aktiviteter till den här länkade tjänsten. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime. Om den inte anges används standard Azure Integration Runtime. | Nej                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks länkad tjänst
Du kan skapa **Azure Databricks länkade tjänsten** för att registrera Databricks-arbetsyta som du kommer att använda för att köra Databricks-arbetsbelastningar (Notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Exempel – använda nytt jobb kluster i Databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exempel – använda befintligt interaktivt kluster i Databricks

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

### <a name="properties"></a>properties

| Egenskap             | Beskrivning                              | Obligatorisk                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Namnet på den länkade tjänsten               | Ja   |
| type                 | Egenskapen Type ska anges till: **AzureDatabricks**. | Ja                                      |
| domän               | Ange Azure-regionen baserat på regionen för Databricks-arbetsytan. Exempel: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Åtkomsttoken krävs för att Data Factory ska kunna autentisera till Azure Databricks. Åtkomsttoken måste genereras från arbets ytan databricks. Mer detaljerade anvisningar för att hitta åtkomsttoken finns [här](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ja                                       |
| existingClusterId    | Kluster-ID för ett befintligt kluster att köra alla jobb på detta. Detta bör vara ett interaktivt kluster som redan skapats. Du kan behöva starta om klustret manuellt om det slutar svara. Databricks föreslå jobb som körs på nya kluster för bättre tillförlitlighet. Du hittar kluster-ID: t för ett interaktivt kluster på Databricks-arbetsytan – > kluster – > interaktivt kluster namn-> konfigurations > taggar. [Mer information](https://docs.databricks.com/user-guide/clusters/tags.html) | Nej 
| newClusterVersion    | Spark-versionen av klustret. Ett jobb kluster skapas i databricks. | Nej  |
| newClusterNumOfWorker| Antal arbetsnoder som det här klustret ska ha. Ett kluster har en spark-drivrutin och num_workers-körningar för totalt antal num_workers + 1 Spark-noder. En sträng formaterad Int32, t. ex. "1" betyder att numOfWorker är 1 eller "1:10" betyder automatisk skalning från 1 som min och 10 som Max.  | Nej                |
| newClusterNodeType   | Det här fältet kodas med hjälp av ett enda värde och resurserna som är tillgängliga för var och en av Spark-noderna i det här klustret. Till exempel kan Spark-noderna tillhandahållas och optimeras för minnes-eller beräknings intensiva arbets belastningar det här fältet krävs för det nya klustret                | Nej               |
| newClusterSparkConf  | en uppsättning valfria, användardefinierade konfigurations nyckel/värde-par för Spark-konfiguration. Användarna kan också skicka en sträng med extra JVM-alternativ till driv rutinen och körningarna via Spark. driver. extraJavaOptions och Spark. utförar. extraJavaOptions. | Nej  |
| newClusterInitScripts| en uppsättning valfria, användardefinierade initierings skript för det nya klustret. Ange DBFS-sökvägen till init-skripten. | Nej  |


## <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst
Du skapar en länkad Azure SQL-tjänst och använder den med den [lagrade procedur aktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory pipeline. Se artikeln om [Azure SQL Connector](connector-azure-sql-database.md#linked-service-properties) för information om den här länkade tjänsten.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst
Du skapar en Azure SQL Data Warehouse länkad tjänst och använder den med den [lagrade procedur aktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory pipeline. Se [Azure SQL Data Warehouse Connector](connector-azure-sql-data-warehouse.md#linked-service-properties) -artikeln för information om den här länkade tjänsten.

## <a name="sql-server-linked-service"></a>SQL Server länkad tjänst
Du skapar en SQL Server länkad tjänst och använder den med den [lagrade procedur aktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory pipeline. Se [SQL Server Connector](connector-sql-server.md#linked-service-properties) -artikeln för information om den här länkade tjänsten.

## <a name="next-steps"></a>Nästa steg
En lista över de omvandlings aktiviteter som stöds av Azure Data Factory finns i [transformera data](transform-data.md).
