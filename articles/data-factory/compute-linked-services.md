---
title: Beräkningsmiljöer som stöds av Azure Data Factory
description: Lär dig mer om beräkningsmiljöer som du kan använda i Azure Data Factory-pipelines (till exempel Azure HDInsight) för att omvandla eller bearbeta data.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: 63843230b3d4a521df858b00c8e5c887e8f53a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415576"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Beräkningsmiljöer som stöds av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs olika beräkningsmiljöer som du kan använda för att bearbeta eller omvandla data. Den innehåller också information om olika konfigurationer (på begäran kontra ta med egna) som stöds av Data Factory när du konfigurerar länkade tjänster som länkar dessa beräkningsmiljöer till en Azure-datafabrik.

Följande tabell innehåller en lista över beräkningsmiljöer som stöds av Data Factory och de aktiviteter som kan köras på dem. 

| Compute-miljö                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HDInsight-kluster](#azure-hdinsight-on-demand-linked-service) på begäran eller [ditt eget HDInsight-kluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Gris](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Anpassad](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning-aktiviteter: batchkörning och resursuppdatering](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning kör pipeline](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning kör pipeline](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Lagrad procedur](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Anteckningsbok](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure-funktion](#azure-function-linked-service)         | [Azure-funktionsaktivitet](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>Beräkningsmiljö för HDInsight på begäran

I den här typen av konfiguration hanteras datormiljön helt av Azure Data Factory-tjänsten. Den skapas automatiskt av datafabrikstjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet är slutfört. Du kan skapa en länkad tjänst för beräkningsmiljön på begäran, konfigurera den och styra detaljerade inställningar för jobbkörning, klusterhantering och bootstrapping-åtgärder.

> [!NOTE]
> Konfigurationen på begäran stöds för närvarande endast för Azure HDInsight-kluster. Azure Databricks stöder också jobb på begäran med jobbkluster, se [Azure databricks länkad tjänst](#azure-databricks-linked-service) för mer information.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Länkad tjänst för Azure HDInsight på begäran

Azure Data Factory-tjänsten kan automatiskt skapa ett HDInsight-kluster på begäran för att bearbeta data. Klustret skapas i samma region som lagringskontot (egenskapen linkedServiceName i JSON) som är associerat med klustret. Lagringskontot måste vara ett allmänt azure storage-konto. 

Observera följande **viktiga** punkter om hdinsight-länkad tjänst på begäran:

* HDInsight-klustret på begäran skapas under din Azure-prenumeration. Du kan se klustret i din Azure-portal när klustret är igång. 
* Loggarna för jobb som körs på ett HDInsight-kluster på begäran kopieras till lagringskontot som är associerat med HDInsight-klustret. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword som definieras i den länkade tjänstdefinitionen används för att logga in i klustret för djupgående felsökning under klustrets livscykel. 
* Du debiteras endast för den tid då HDInsight-klustret är igång jobb.
* Du kan använda en **skriptåtgärd** med azure HDInsight on-demand-länkade tjänsten.  

> [!IMPORTANT]
> Det tar vanligtvis **20 minuter** eller mer att etablera ett Azure HDInsight-kluster på begäran.

### <a name="example"></a>Exempel

Följande JSON definierar en Linux-baserad HDInsight-länkad tjänst på begäran. Data Factory-tjänsten skapar automatiskt ett **Linux-baserat HDInsight-kluster** för att bearbeta den aktivitet som krävs. 

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
> HDInsight-klustret skapar en **standardbehållare** i blob-lagring som du angav i JSON **(linkedServiceName**). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor behöver bearbetas, såvida det inte finns ett befintligt livekluster (**timeToLive**). Det raderas när bearbetningen är klar. 
>
> När fler aktiviteter körs visas många behållare i azure-bloblagringen. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Lagringsutforskaren](https://storageexplorer.com/) till att ta bort containrar i din Azure bloblagring.

### <a name="properties"></a>Egenskaper

| Egenskap                     | Beskrivning                              | Krävs |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Egenskapen Type ska anges till **HDInsightOnDemand**. | Ja      |
| clusterSize                  | Antal arbets-/datanoder i klustret. HDInsight-klustret skapas med två huvudnoder tillsammans med antalet arbetsnoder som du anger för den här egenskapen. Noderna är av storlek Standard_D3 som har 4 kärnor, så ett 4-arbetsnodkluster tar 24 kärnor (4\*\*4 = 16 kärnor för arbetsnoder plus 2 4 = 8 kärnor för huvudnoder). Mer information [finns i Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera.](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) | Ja      |
| linkedServiceName            | Azure Storage-länkad tjänst som ska användas av klustret på begäran för lagring och bearbetning av data. HDInsight-klustret skapas i samma region som det här Azure Storage-kontot. Azure HDInsight har en begränsning för hur många kärnor du kan använda i varje Azure-region som stöds. Se till att du har tillräckligt med kärnkvoter i den Azure-regionen för att uppfylla de nödvändiga clusterSize. Mer information finns [i Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder ett Azure Data Lake Storage (Gen 2) som lagring. Om du vill lagra resultatdata från HDInsight-bearbetning i en Azure Data Lake Storage (Gen 2) använder du en kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure Data Lake Storage (Gen 2). </p> | Ja      |
| clusterResourceGroup         | HDInsight-klustret skapas i den här resursgruppen. | Ja      |
| tidtolering                   | Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir vid liv efter slutförandet av en aktivitetskörning om det inte finns några andra aktiva jobb i klustret. Det minsta tillåtna värdet är 5 minuter (00:05:00).<br/><br/>Om en aktivitetskörning till exempel tar 6 minuter och tidstolk är inställd på 5 minuter, förblir klustret vid liv i 5 minuter efter 6 minuters bearbetning av aktivitetskörningen. Om en annan aktivitetskörning körs med 6-minutersfönstret bearbetas den av samma kluster.<br/><br/>Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (kan ta ett tag), så använd den här inställningen efter behov för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger tidstolkivvärde till 0 tas klustret bort så fort aktivitetskörningen är klar. Om du anger ett högt värde kan klustret vara inaktivt för att du ska kunna logga in för vissa felsökningsändamål, men det kan leda till höga kostnader. Därför är det viktigt att du anger lämpligt värde baserat på dina behov.<br/><br/>Om egenskapsvärdet för timetolive är korrekt inställt kan flera pipelines dela instansen av HDInsight-klustret på begäran. | Ja      |
| clusterType (klustertyp)                  | Den typ av HDInsight-kluster som ska skapas. Tillåtna värden är "hadoop" och "spark". Om inget anges är standardvärdet hadoop. Enterprise Security Package-aktiverat kluster kan inte skapas på begäran, i stället använda ett [befintligt kluster/ ta med din egen beräkning](#azure-hdinsight-linked-service). | Inga       |
| version                      | Version av HDInsight-klustret. Om det inte anges används den aktuella HDInsight-definierade standardversionen. | Inga       |
| hostSubscriptionId           | Azure-prenumerations-ID som används för att skapa HDInsight-kluster. Om det inte anges används prenumerations-ID:t för din Azure-inloggningskontext. | Inga       |
| clusterNamePrefix           | Prefixet för HDI-klusternamn, en tidsstämpel lägger automatiskt till i slutet av klusternamnet| Inga       |
| sparkVersion (gnistVersion)                 | Versionen av gnistan om klustertypen är "Spark" | Inga       |
| ytterligareLänkade tjänstnamn | Anger ytterligare lagringskonton för den HDInsight-länkade tjänsten så att datafabrikstjänsten kan registrera dem för din räkning. Dessa lagringskonton måste finnas i samma region som HDInsight-klustret, som skapas i samma region som lagringskontot som anges av linkedServiceName. | Inga       |
| osType (olika)                       | Typ av operativsystem. Tillåtna värden är: Linux och Windows (endast för HDInsight 3.3). Standard är Linux. | Inga       |
| hcatalogLinkedServiceName    | Namnet på Azure SQL-länkade tjänst som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas med hjälp av Azure SQL Database som metabutik. | Inga       |
| connectVia (på)                   | Den integrationskörning som ska användas för att skicka aktiviteterna till den här HDInsight-länkade tjänsten. För hdinsight-länkad tjänst på begäran stöder den bara Azure Integration Runtime. Om det inte anges används standardkörningen för Azure Integration. | Inga       |
| clusterUserName                   | Användarnamnet för att komma åt klustret. | Inga       |
| klusterPassord                   | Lösenordet i typ av säker sträng för att komma åt klustret. | Inga       |
| klusterSshUserName         | Användarnamnet till SSH ansluter fjärranslutning till klustrets nod (för Linux). | Inga       |
| klusterSshPassword         | Lösenordet i typ av säker sträng till SSH fjärransluter klustrets nod (för Linux). | Inga       |
| scriptActions | Ange skript för [HDInsight-klusteranpassningar](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) när kluster skapas på begäran. <br />Azure Data Factorys användargränssnittsförfattningsverktyg stöder för närvarande att ange endast en skriptåtgärd, men du kan komma igenom den här begränsningen i JSON (ange flera skriptåtgärder i JSON). | Inga |


> [!IMPORTANT]
> HDInsight stöder flera Hadoop-klusterversioner som kan distribueras. Varje versionsval skapar en specifik version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter som finns i distributionen. Listan över HDInsight-versioner som stöds uppdateras för att tillhandahålla de senaste Hadoop-ekosystemkomponenterna och korrigeringarna. Se till att du alltid hänvisar till den senaste informationen [i HDInsight-versionen som stöds och OS-typ](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) för att säkerställa att du använder version av HDInsight som stöds. 
>
> [!IMPORTANT]
> För närvarande stöder HDInsight-länkade tjänster inte HBase, Interactive Query (Hive LLAP), Storm. 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON-exempel

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Den hdinsight-länkade tjänsten on-demand kräver en tjänsthuvudbemanning för att skapa HDInsight-kluster för din räkning. Om du vill använda autentisering av tjänstens huvudnamn registrerar du en programentitet i Azure Active Directory (Azure AD) och ger den **deltagarrollen** för prenumerationen eller resursgruppen där HDInsight-klustret skapas. Detaljerade steg finns i [Använda portal för att skapa ett Azure Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel 
- Klient-ID:t

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **Hyresgästen**              | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja      |

### <a name="advanced-properties"></a>Avancerade egenskaper

Du kan också ange följande egenskaper för detaljerad konfiguration av HDInsight-klustret på begäran.

| Egenskap               | Beskrivning                              | Krävs |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Anger de grundläggande konfigurationsparametrarna (som i core-site.xml) för HDInsight-klustret som ska skapas. | Inga       |
| hBaseKonfigurering     | Anger HBase-konfigurationsparametrarna (hbase-site.xml) för HDInsight-klustret. | Inga       |
| hdfsKonfigurering      | Anger HDFS-konfigurationsparametrarna (hdfs-site.xml) för HDInsight-klustret. | Inga       |
| hiveKonfigurering      | Anger konfigurationsparametrarna för registreringsdatafilen (hive-site.xml) för HDInsight-klustret. | Inga       |
| mapReduceKonfigurering | Anger mapReduce-konfigurationsparametrarna (mapred-site.xml) för HDInsight-klustret. | Inga       |
| oozieKonfigurering     | Anger Oozie-konfigurationsparametrarna (oozie-site.xml) för HDInsight-klustret. | Inga       |
| stormKonfigurering     | Anger parametrarna stormkonfiguration (storm-site.xml) för HDInsight-klustret. | Inga       |
| garnKonfigurering      | Anger konfigurationsparametrarna för garn (yarn-site.xml) för HDInsight-klustret. | Inga       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exempel – HDInsight-klusterkonfiguration på begäran med avancerade egenskaper

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
Du kan ange storleken på noderna head, data och zookeeper med hjälp av följande egenskaper: 

| Egenskap          | Beskrivning                              | Krävs |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Anger storleken på huvudnoden. Standardvärdet är: Standard_D3. Mer information finns i avsnittet **Ange nodstorlekar.** | Inga       |
| dataNodeSize      | Anger storleken på datanoden. Standardvärdet är: Standard_D3. | Inga       |
| zookeeperNodeSize | Anger storleken på noden Zoo Keeper. Standardvärdet är: Standard_D3. | Inga       |

#### <a name="specifying-node-sizes"></a>Ange nodstorlekar
Se artikeln [Storlekar på virtuella datorer](../virtual-machines/linux/sizes.md) för strängvärden som du måste ange för de egenskaper som nämns i föregående avsnitt. Värdena måste överensstämma med **CMDLETs & APIS** som refereras i artikeln. Som du kan se i artikeln har datanoden för stor (standard) storlek 7 GB minne, vilket kanske inte är tillräckligt bra för ditt scenario. 

Om du vill skapa huvudnoder och arbetsnoder av D4-storlek anger du **Standard_D4** som värde för egenskaper för headNodeSize och dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Om du anger fel värde för dessa egenskaper kan följande **felmeddelande visas:** Det gick inte att skapa klustret. Undantag: Unable to complete the cluster create operation. (Det går inte att slutföra åtgärden att skapa ett kluster.) Operation failed with code '400'. (Åtgärden misslyckades med koden 400). Cluster left behind state: 'Error'. (Klustret efterlämnade status: Fel.) Meddelande: "PreClusterCreationValidationFailure". När du får det här felet kontrollerar du att du använder **CMDLET-& APIS-namnet** från tabellen i artikeln [Storlekar på virtuella datorer.](../virtual-machines/linux/sizes.md)        

## <a name="bring-your-own-compute-environment"></a>Ta med din egen beräkningsmiljö
I den här typen av konfiguration kan användare registrera en redan befintlig datormiljö som en länkad tjänst i Data Factory. Datormiljön hanteras av användaren och Data Factory-tjänsten använder den för att utföra aktiviteterna.

Den här typen av konfiguration stöds för följande beräkningsmiljöer:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight länkad tjänst
Du kan skapa en Azure HDInsight-länkad tjänst för att registrera ditt eget HDInsight-kluster med Data Factory.

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
| Egenskap          | Beskrivning                                                  | Krävs |
| ----------------- | ------------------------------------------------------------ | -------- |
| typ              | Egenskapen Type ska ställas in på **HDInsight**.            | Ja      |
| clusterUri (klusterUri)        | Uri i HDInsight-klustret.                            | Ja      |
| användarnamn          | Ange namnet på den användare som ska användas för att ansluta till ett befintligt HDInsight-kluster. | Ja      |
| password          | Ange lösenord för användarkontot.                       | Ja      |
| linkedServiceName | Namnet på den Azure Storage-länkade tjänst som refererar till Azure-bloblagringslagringen som används av HDInsight-klustret. <p>För närvarande kan du inte ange en Azure Data Lake Storage (Gen 2) länkad tjänst för den här egenskapen. Om HDInsight-klustret har åtkomst till DataSjöarkivet kan du komma åt data i Azure Data Lake Storage (Gen 2) från Hive/Pig-skript. </p> | Ja      |
| isEspEnabled      | Ange '*sant*' om HDInsight-klustret är [Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) aktiverat. Standard är '*false*'. | Inga       |
| connectVia (på)        | Den integrationskörning som ska användas för att skicka aktiviteterna till den länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime. Om det inte anges används standardkörningen för Azure Integration. <br />För ESP-kluster (Enterprise Security Package) använder du en självvärd integreringskörning, som har en siktlinje till klustret eller att det bör distribueras i samma virtuella nätverk som ESP HDInsight-klustret. | Inga       |

> [!IMPORTANT]
> HDInsight stöder flera Hadoop-klusterversioner som kan distribueras. Varje versionsval skapar en specifik version av HDP-distributionen (Hortonworks Data Platform) och en uppsättning komponenter som finns i distributionen. Listan över HDInsight-versioner som stöds uppdateras för att tillhandahålla de senaste Hadoop-ekosystemkomponenterna och korrigeringarna. Se till att du alltid hänvisar till den senaste informationen [i HDInsight-versionen som stöds och OS-typ](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) för att säkerställa att du använder version av HDInsight som stöds. 
>
> [!IMPORTANT]
> För närvarande stöder HDInsight-länkade tjänster inte HBase, Interactive Query (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch-länkad tjänst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en Azure Batch-länkad tjänst för att registrera en batchpool med virtuella datorer (VMs) till en datafabrik. Du kan köra anpassad aktivitet med Azure Batch.

Se följande artiklar om du inte har tidigare i Azure Batch-tjänsten:

* [Grunderna i Azure Batch](../batch/batch-technical-overview.md) för en översikt över Azure Batch-tjänsten.
* [Cmdlet New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) för att skapa ett Azure Batch-konto (eller) [Azure-portal](../batch/batch-account-create-portal.md) för att skapa Azure Batch-kontot med Azure-portalen. Se Använda PowerShell för att hantera Azure [Batch-konto](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artikel för detaljerade instruktioner om hur du använder cmdlet.
* [Cmdlet new-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) för att skapa en Azure Batch-pool.

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
| typ              | Typegenskapen ska anges till **AzureBatch**. | Ja      |
| accountName       | Namn på Azure Batch-kontot.         | Ja      |
| Accesskey         | Åtkomstnyckel för Azure Batch-kontot.  | Ja      |
| batchUri (olikartade)          | URL till ditt Azure Batch-konto, i formatet https://*batchaccountname.region*.batch.azure.com. | Ja      |
| poolNamn          | Namn på poolen med virtuella datorer.    | Ja      |
| linkedServiceName | Namnet på den Azure Storage-länkade tjänst som är associerad med den här Azure Batch-länkade tjänsten. Den här länkade tjänsten används för mellanlagringsfiler som krävs för att köra aktiviteten. | Ja      |
| connectVia (på)        | Den integrationskörning som ska användas för att skicka aktiviteterna till den länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime. Om det inte anges används standardkörningen för Azure Integration. | Inga       |

## <a name="azure-machine-learning-studio-linked-service"></a>Azure Machine Learning Studio länkad tjänst
Du skapar en Azure Machine Learning Studio-länkad tjänst för att registrera en slutpunkt för machine learning-batchbedömning till en datafabrik.

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
| Typ                   | Typegenskapen ska anges till: **AzureML**. | Ja                                      |
| mlEndpoint (mlEndpoint)             | Url:en för batchbedömning.                   | Ja                                      |
| apiKey (apiKey)                 | Den publicerade arbetsytemodellens API.     | Ja                                      |
| updateResourceEndpoint | Uppdateringsresurs-URL:en för en Azure Machine Learning Web Service-slutpunkt som används för att uppdatera den förutsägande webbtjänsten med tränad modellfil | Inga                                       |
| servicePrincipalId     | Ange programmets klient-ID.     | Krävs om updateResourceEndpoint har angetts |
| servicePrincipalKey    | Ange programmets nyckel.           | Krävs om updateResourceEndpoint har angetts |
| tenant                 | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Krävs om updateResourceEndpoint har angetts |
| connectVia (på)             | Den integrationskörning som ska användas för att skicka aktiviteterna till den länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime. Om det inte anges används standardkörningen för Azure Integration. | Inga                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst
Du skapar en Azure Machine Learning-länkad tjänst för att ansluta en Azure Machine Learning-arbetsyta till en datafabrik.

> [!NOTE]
> För närvarande stöds endast tjänsthuvudbeklädnad autentisering för azure machine learning-tjänsten.

### <a name="example"></a>Exempel

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Egenskaper
| Egenskap               | Beskrivning                              | Krävs                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Typegenskapen ska anges till: **AzureMLService**. | Ja                                      |
| subscriptionId         | Azure-prenumerations-ID              | Ja                                      |
| resourceGroupName      | namn | Ja                                      |
| mlWorkspaceName        | Namn på Azure Machine Learning-arbetsyta | Ja  |
| servicePrincipalId     | Ange programmets klient-ID.     | Inga |
| servicePrincipalKey    | Ange programmets nyckel.           | Inga |
| tenant                 | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Krävs om updateResourceEndpoint har angetts | Inga |
| connectVia (på)             | Den integrationskörning som ska användas för att skicka aktiviteterna till den länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime. Om det inte anges används standardkörningen för Azure Integration. | Inga |    

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics-länkad** tjänst för att länka en Azure Data Lake Analytics-beräkningstjänst till en Azure-datafabrik. Data Lake Analytics U-SQL-aktivitet på pipelinen refererar till den länkade tjänsten. 

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
            "subscriptionId": "<optional, subscription ID of ADLA>",
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
| typ                 | Typegenskapen ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| accountName          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Inga                                       |
| subscriptionId       | Azure-prenumerations-ID                    | Inga                                       |
| resourceGroupName    | Azure-resursgruppsnamn                | Inga                                       |
| servicePrincipalId   | Ange programmets klient-ID.     | Ja                                      |
| servicePrincipalKey  | Ange programmets nyckel.           | Ja                                      |
| tenant               | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja                                      |
| connectVia (på)           | Den integrationskörning som ska användas för att skicka aktiviteterna till den länkade tjänsten. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime. Om det inte anges används standardkörningen för Azure Integration. | Inga                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks länkad tjänst
Du kan skapa **Azure Databricks-länkad tjänst** för att registrera Databricks-arbetsyta som du använder för att köra Databricks-arbetsbelastningarna(anteckningsbok, burk, python). 
> [!IMPORTANT]
> Databricks länkade tjänster stöder [instanspooler](https://aka.ms/instance-pools). 

### <a name="example---using-new-job-cluster-in-databricks"></a>Exempel - Använda nytt jobbkluster i Databricks

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exempel - Använda befintligt interaktivt kluster i Databricks

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
| typ                 | Egenskapen Type ska anges till: **Azure Databricks**. | Ja                                      |
| domän               | Ange Azure-regionen i enlighet med regionen för Databricks-arbetsytan. Exempel: https://eastus.azuredatabricks.net | Ja                                 |
| tillgångToken          | Åtkomsttoken krävs för att Data Factory ska autentisera till Azure Databricks. Åtkomsttoken måste genereras från databricks-arbetsytan. Mer detaljerade steg för att hitta åtkomsttoken hittar du [här](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Ja                                       |
| befintligaClusterId    | Kluster-ID för ett befintligt kluster för att köra alla jobb på detta. Detta bör vara ett redan skapat interaktivt kluster. Du kan behöva starta om klustret manuellt om det slutar svara. Databricks föreslår att du kör jobb i nya kluster för ökad tillförlitlighet. Du hittar kluster-ID för ett interaktivt kluster på Databricks arbetsyta -> kluster -> interaktivt klusternamn -> konfiguration -> taggar. [Mer information](https://docs.databricks.com/user-guide/clusters/tags.html) | Inga 
| instansPoolId    | Instanspool-ID för en befintlig pool i databricks arbetsyta.  | Inga  |
| nyaClusterVersion    | Spark-versionen av klustret. Det skapar ett jobbkluster i databricks. | Inga  |
| nyaClusterNumOfWorker| Antal arbetsnoder som det här klustret ska ha. Ett kluster har en Spark Driver och num_workers Executors för totalt num_workers + 1 Spark-noder. En sträng formaterad Int32, som "1" betyder numOfWorker är 1 eller "1:10" betyder automatisk skalning från 1 som min och 10 som max.  | Inga                |
| nyaKsterNodeTyp   | Det här fältet kodar, genom ett enda värde, de resurser som är tillgängliga för var och en av Spark-noderna i det här klustret. Spark-noder kan till exempel etableras och optimeras för minnes- eller beräkningsintensiva arbetsbelastningar. Det här fältet krävs för nytt kluster                | Inga               |
| nyaClusterSparkConf  | en uppsättning valfria, användarspecificerade Spark-konfigurationsnyckel-värdepar. Användare kan också passera i en rad extra JVM alternativ till föraren och executors via spark.driver.extraJavaOptions och spark.executor.extraJavaOptions respektive. | Inga  |
| nyaClusterInitScripts| En uppsättning valfria, användardefinierade initieringsskript för det nya klustret. Ange DBFS-sökvägen till init-skripten. | Inga  |


## <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst

Du skapar en Azure SQL-länkad tjänst och använder den med [den lagrade proceduraktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory-pipeline. Mer information om den här länkade tjänsten finns i azure [SQL Connector-artikeln.](connector-azure-sql-database.md#linked-service-properties)

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse länkad tjänst

Du skapar en Azure SQL Data Warehouse-länkad tjänst och använder den med [den lagrade proceduraktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory-pipeline. Mer information om den här länkade tjänsten finns i artikeln [Azure SQL Data Warehouse Connector.](connector-azure-sql-data-warehouse.md#linked-service-properties)

## <a name="sql-server-linked-service"></a>SQL Server-länkad tjänst

Du skapar en SQL Server-länkad tjänst och använder den med [den lagrade proceduraktiviteten](transform-data-using-stored-procedure.md) för att anropa en lagrad procedur från en Data Factory-pipeline. Mer information om den här länkade tjänsten finns i artikeln [SQL Server-anslutning.](connector-sql-server.md#linked-service-properties)

## <a name="azure-function-linked-service"></a>Azure-funktionslänkade tjänst

Du skapar en Azure Function-länkad tjänst och använder den med [Azure-funktionsaktiviteten](control-flow-azure-function-activity.md) för att köra Azure-funktioner i en Data Factory-pipeline. Returtypen för Azure-funktionen måste `JObject`vara en giltig . (Tänk på att [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) `JObject` *inte* är en .) Alla returtyper `JObject` som inte fungerar som den misslyckas och höjer användarfelet *Svarsinnehåll är inte ett giltigt JObject*.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| typ   | Typegenskapen måste anges till: **AzureFunction** | ja |
| url till funktionsapp | URL för Azure Function App. Formatet `https://<accountname>.azurewebsites.net`är . Den här URL:en är värdet under **URL-avsnittet** när du visar din funktionsapp i Azure-portalen  | ja |
| funktionsnyckel | Åtkomstnyckel för Azure-funktionen. Klicka på avsnittet **Hantera** för respektive funktion och kopiera antingen **funktionsnyckeln** eller **värdnyckeln**. Läs mer här: [Azure Functions HTTP-utlösare och bindningar](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="next-steps"></a>Nästa steg

En lista över de omvandlingsaktiviteter som stöds av Azure Data Factory finns i [Omforma data](transform-data.md).
