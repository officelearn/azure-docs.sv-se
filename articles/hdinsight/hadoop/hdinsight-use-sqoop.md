---
title: Kör Apache Sqoop-jobb med Azure HDInsight (Apache Hadoop)
description: Lär dig hur du använder Azure PowerShell från en arbetsstation för att köra Sqoop-import och export mellan ett Hadoop-kluster och en Azure SQL-databas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951861"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Använda Apache Sqoop med Hadoop i HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop i HDInsight för att importera och exportera data mellan ett HDInsight-kluster och en Azure SQL-databas.

Även om Apache Hadoop är ett naturligt val för bearbetning av ostrukturerade och halvstrukturerade data, till exempel loggar och filer, kan det också finnas ett behov av att bearbeta strukturerade data som lagras i relationsdatabaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) är ett verktyg som är utformat för att överföra data mellan Hadoop-kluster och relationsdatabaser. Du kan använda den för att importera data från ett relationsdatabashanteringssystem (RDBMS) som SQL Server, MySQL eller Oracle till det Hadoop-distribuerade filsystemet (HDFS), omvandla data i Hadoop med MapReduce eller Apache Hive och sedan exportera tillbaka data till ett RDBMS . I den här artikeln använder du en SQL Server-databas för relationsdatabasen.

> [!IMPORTANT]  
> I den här artikeln konfigureras en testmiljö för att utföra dataöverföringen. Du väljer sedan en dataöverföringsmetod för den här miljön från en av metoderna i avsnittet [Kör Sqoop-jobb](#run-sqoop-jobs), längre under.

För Sqoop-versioner som stöds på HDInsight-kluster, se [Vad är nytt i klusterversionerna från HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Förstå scenariot

HDInsight-klustret levereras med vissa exempeldata. Du använder följande två exempel:

* En Apache Log4j-loggfil, `/example/data/sample.log`som finns på . Följande loggar extraheras från filen:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* En Hive-tabell med namnet `hivesampletable`, som `/hive/warehouse/hivesampletable`refererar till datafilen som finns på . Tabellen innehåller vissa data från mobila enheter.
  
  | Field | Datatyp |
  | --- | --- |
  | clientid |sträng |
  | frågetid |sträng |
  | Marknaden |sträng |
  | enhetplattform |sträng |
  | devicemake |sträng |
  | enhetsmodell |sträng |
  | state |sträng |
  | land |sträng |
  | frågatid |double |
  | Sessionid |bigint |
  | sessionpagevieworder |bigint |

I den här artikeln använder du dessa två datauppsättningar för att testa Sqoop import och export.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Konfigurera testmiljö

Klustret, SQL-databasen och andra objekt skapas via Azure-portalen med hjälp av en Azure Resource Manager-mall. Mallen finns i [Snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)för Azure . Resource Manager-mallen anropar ett bacpac-paket för att distribuera tabellschemana till en SQL-databas.  Bacpac-paketet finns i en offentlig https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpacblob-behållare. Om du vill använda en privat behållare för bacpac-filerna använder du följande värden i mallen:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importera med hjälp av en mall eller Azure-portalen stöder endast import av en BACPAC-fil från Azure blob storage.

1. Välj följande avbildning för att öppna Resource Manager-mallen i Azure-portalen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Ange följande egenskaper:

    |Field |Värde |
    |---|---|
    |Prenumeration |Välj din Azure-prenumeration i listrutan.|
    |Resursgrupp |Välj resursgrupp i listrutan eller skapa en ny|
    |Location |Välj en region i listrutan.|
    |Klusternamn |Ange ett namn för Hadoop-klustret. Använd endast gemener.|
    |Användarnamn för klusterinloggning |Behåll det förifyllda värdet `admin`.|
    |Lösenord för klusterinloggning |Ange ett lösenord.|
    |Ssh-användarnamn |Behåll det förifyllda värdet `sshuser`.|
    |Ssh lösenord |Ange ett lösenord.|
    |Sql-administratörsinloggning |Behåll det förifyllda värdet `sqluser`.|
    |Lösenord för Sql-administratör |Ange ett lösenord.|
    |_artifacts plats | Använd standardvärdet om du inte vill använda din egen bacpac-fil på en annan plats.|
    |_artifacts plats sas-token |Lämna tomt.|
    |Filnamn för Bacpac |Använd standardvärdet om du inte vill använda din egen bacpac-fil.|
    |Location |Använd standardvärdet.|

    Azure SQL Server-namnet `<ClusterName>dbserver`kommer att vara . Databasnamnet kommer `<ClusterName>db`att vara . Standardnamn för lagringskontot kommer att vara `e6qhezrh2pdqu`.

3. Välj **Jag godkänner de villkor som anges ovan**.

4. Välj **Köp**. Du ser en ny panel med namnet Skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret och SQL Database.

## <a name="run-sqoop-jobs"></a>Kör Sqoop-jobb

HDInsight kan köra Sqoop-jobb med hjälp av en mängd olika metoder. Använd följande tabell för att bestämma vilken metod som är rätt för dig och följ sedan länken för en genomgång.

| **Använd den här** om du vill... | ... ett **interaktivt** skal | ... **batchbearbetning** | ... från det här **klientoperativsystemet** |
|:--- |:---:|:---:|:--- |:--- |
| [Ssh](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (för tillfället) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Begränsningar

* Massexport – Med Linux-baserad HDInsight stöder Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database för närvarande inte massinfogningar.
* Batching - Med Linux-baserade HDInsight, När du använder växeln `-batch` när du utför skär, utför Sqoop flera skär i stället för batchning av skäråtgärder.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig att använda Sqoop. Du kan läsa mer här:

* [Använda Apache Hive med HDInsight](../hdinsight-use-hive.md)
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att överföra data till HDInsight/Azure Blob-lagring.
* [Använda Apache Sqoop för att importera och exportera data mellan Apache Hadoop på HDInsight och SQL Database](./apache-hadoop-use-sqoop-mac-linux.md)