---
title: Köra apache Sqoop-jobb med Azure HDInsight (Apache Hadoop)
description: Lär dig hur du använder Azure PowerShell från en arbets station för att köra Sqoop import och export mellan ett Hadoop-kluster och en Azure SQL-databas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 564bced9ae71213cb534393a7dcc45c929df3794
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917374"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Använda Apache Sqoop med Hadoop i HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop i HDInsight för att importera och exportera data mellan ett HDInsight-kluster och en Azure SQL-databas.

Även om Apache Hadoop är ett naturligt val för bearbetning av ostrukturerade och delvis strukturerade data, t. ex. loggar och filer, kan det också finnas behov av att bearbeta strukturerade data som lagras i Relations databaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) är ett verktyg som har utformats för att överföra data mellan Hadoop-kluster och Relations databaser. Du kan använda den för att importera data från ett relationellt databas hanterings system (RDBMS) som SQL Server, MySQL eller Oracle till Hadoop Distributed File System (HDFS), transformera data i Hadoop med MapReduce eller Apache Hive och sedan exportera data tillbaka till en RDBMS . I den här artikeln använder du en SQL Server databas för Relations databasen.

> [!IMPORTANT]  
> Den här artikeln skapar en test miljö för att utföra data överföringen. Sedan väljer du en data överförings metod för den här miljön från någon av metoderna i avsnittet [Kör Sqoop-jobb](#run-sqoop-jobs), nedan.

För Sqoop-versioner som stöds i HDInsight-kluster, se [Vad är nytt i de kluster versioner som tillhandahålls av HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Förstå scenariot

HDInsight-kluster innehåller vissa exempel data. Du använder följande två exempel:

* En Apache log4j-loggfil som finns på `/example/data/sample.log`. Följande loggar extraheras från filen:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* En Hive-tabell `hivesampletable`med namnet som refererar till den data fil `/hive/warehouse/hivesampletable`som finns på. Tabellen innehåller vissa mobila enhets data.
  
  | Fält | Datatyp |
  | --- | --- |
  | clientid |sträng |
  | querytime |sträng |
  | Telefonförsäljning |sträng |
  | deviceplatform |sträng |
  | devicemake |sträng |
  | devicemodel |sträng |
  | state |sträng |
  | Ursprungslandet |sträng |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

I den här artikeln använder du dessa två data uppsättningar för att testa Sqoop import och export.

## <a name="create-cluster-and-sql-database"></a>Konfigurera test miljö
Klustret, SQL-databasen och andra objekt skapas via Azure Portal med hjälp av en Azure Resource Manager mall. Mallen finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Resource Manager-mallen anropar ett BACPAC-paket för att distribuera tabell scheman till en SQL-databas.  BACPAC-paketet finns i en offentlig BLOB- https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac behållare. Om du vill använda en privat behållare för BACPAC-filerna använder du följande värden i mallen:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importera med hjälp av en mall eller Azure Portal har endast stöd för att importera en BACPAC-fil från Azure Blob Storage.

1. Välj följande bild för att öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>

2. Ange följande egenskaper:

    |Fält |Value |
    |---|---|
    |Subscription |Välj din Azure-prenumeration i list rutan.|
    |Resource group |Välj din resurs grupp i den nedrullningsbara listan eller skapa en ny|
    |Location |Välj en region i den nedrullningsbara listan.|
    |Klusternamn |Ange ett namn för Hadoop-klustret. Använd bara gemena bokstäver.|
    |Användarnamn för klusterinloggning |Behåll värdet `admin`i förväg.|
    |Lösenord för klusterinloggning |Ange ett lösen ord.|
    |Användar namn för SSH |Behåll värdet `sshuser`i förväg.|
    |SSH-lösenord |Ange ett lösen ord.|
    |SQL admin-inloggning |Behåll värdet `sqluser`i förväg.|
    |SQL admin-lösenord |Ange ett lösen ord.|
    |_artifacts-plats | Använd standardvärdet om du inte vill använda din egen BACPAC-fil på en annan plats.|
    |SAS-token för _artifacts-plats |Lämna tomt.|
    |BACPAC fil namn |Använd standardvärdet om du inte vill använda din egen BACPAC-fil.|
    |Location |Använd standardvärdet.|

    Namnet på Azure-SQL Server kommer `<ClusterName>dbserver`att vara. Databas namnet kommer att vara `<ClusterName>db`. Standard namnet för lagrings kontot är `e6qhezrh2pdqu`.

3. Välj **Jag accepterar de villkor som anges ovan**.

4. Välj **Köp**. Du ser en ny panel med rubriken skicka distribution för Malldistribution. Det tar cirka 20 minuter att skapa klustret och SQL Database.

## <a name="run-sqoop-jobs"></a>Kör Sqoop-jobb

HDInsight kan köra Sqoop-jobb genom att använda en mängd olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig bäst. Följ sedan länken för en genom gång.

| **Använd den här** om du vill... | ...an **interaktiva** shell | ...**batch** bearbetning | ...from detta **klientoperativsystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (för tillfället) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Begränsningar

* Mass export – med Linux-baserat HDInsight har Sqoop-anslutningen som används för att exportera data till Microsoft SQL Server eller Azure SQL Database för närvarande inte stöd för Mass infogningar.
* Batchering – med Linux-baserat HDInsight, när du använder `-batch` växeln när du utför infogningar, utför Sqoop flera infogningar i stället för att batch-sätta in åtgärderna.

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Hive med HDInsight](../hdinsight-use-hive.md)
* [Använda Apache gris med HDInsight](../hdinsight-use-pig.md)
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att ladda upp data till HDInsight/Azure Blob Storage.
