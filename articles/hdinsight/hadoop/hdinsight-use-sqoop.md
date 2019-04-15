---
title: Kör Apache Sqoop jobb med Azure HDInsight (Apache Hadoop)
description: Lär dig hur du använder Azure PowerShell på en arbetsstation för att köra Sqoop-import och export mellan ett Hadoop-kluster och en Azure SQL database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565860"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Använd Apache Sqoop med Hadoop i HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop i HDInsight för att importera och exportera data mellan ett HDInsight-kluster och en Azure SQL database.

Även om Apache Hadoop är det naturliga valet för att bearbeta Ostrukturerade och delvis strukturerade data, till exempel loggar och filer, kan det också vara nödvändigt att bearbeta strukturerade data som lagras i relationsdatabaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) är ett verktyg som utformats för att överföra data mellan Hadoop-kluster och relationsdatabaser. Du kan använda den för att importera data från ett hanteringssystem för relationsdatabaser (RDBMS) som SQL Server, MySQL eller Oracle i Hadoop distributed file system (HDFS), transformera data i Hadoop MapReduce eller Apache Hive och sedan exportera data tillbaka till en RDBMS . I den här artikeln får använder du en SQL Server-databas för din relationsdatabas.

> [!IMPORTANT]  
> Den här artikeln ställer in en testmiljö att utföra dataöverföringen. Du sedan välja en metod för överföring av data för den här miljön från någon av metoderna i avsnittet [köra Sqoop jobb](#run-sqoop-jobs), vilket ytterligare nedan.

Sqoop-versioner som stöds i HDInsight-kluster, se [vad är nytt i klusterversionerna från HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Förstå scenariot

HDInsight-kluster levereras med exempeldata. Du kan använda följande två exempel:

* En Apache Log4j-loggfil som finns i `/example/data/sample.log`. Följande loggar extraheras från filen:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* En Hive-tabell med namnet `hivesampletable`, som hänvisar till datafilen i `/hive/warehouse/hivesampletable`. Tabellen innehåller några data för mobila enheter.
  
  | Fält | Datatyp |
  | --- | --- |
  | clientid |sträng |
  | querytime |sträng |
  | marknaden |sträng |
  | deviceplatform |sträng |
  | devicemake |sträng |
  | devicemodel |sträng |
  | state |sträng |
  | Land |sträng |
  | querydwelltime |double |
  | sessions-ID |bigint |
  | sessionpagevieworder |bigint |

I den här artikeln använder du dessa två datauppsättningar för att testa Sqoop-import och export.

## <a name="create-cluster-and-sql-database"></a>Skapa testmiljö
Klustret, SQL-databas och andra objekt skapas via Azure-portalen med en Azure Resource Manager-mall. Mallen finns i [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Resource Manager-mallen anropar en bacpac-paketet för att distribuera tabellscheman till en SQL-databas.  Bacpac-paketet finns i en offentlig blobbehållare https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Om du vill använda en privat behållare för bacpac-filer, använder du följande värden i mallen:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importera med hjälp av en mall eller Azure-portalen stöder bara importera en BACPAC-fil från Azure blob storage.

1. Välj följande bild för att öppna Resource Manager-mall i Azure-portalen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange följande egenskaper:

    |Fält |Värde |
    |---|---|
    |Prenumeration |Välj din Azure-prenumeration från den nedrullningsbara listan.|
    |Resursgrupp |Välj din resursgrupp i listrutan eller skapa en ny|
    |Plats |Välj en region från den nedrullningsbara listan.|
    |Klusternamn |Ange ett namn för Hadoop-klustret. Använd endast gemena bokstäver.|
    |Användarnamn för klusterinloggning |Behåll värdet i förväg `admin`.|
    |Lösenord för klusterinloggning |Ange ett lösenord.|
    |SSH-användarnamn |Behåll värdet i förväg `sshuser`.|
    |SSH lösenord |Ange ett lösenord.|
    |SQL-administratörsinloggning |Behåll värdet i förväg `sqluser`.|
    |SQL-administratörslösenord |Ange ett lösenord.|
    |_artifacts plats | Använd standardvärdet om du inte vill använda din egen bacpac-fil på en annan plats.|
    |_artifacts plats Sas-Token |Lämna tomt.|
    |Namn på Bacpac-fil |Använd standardvärdet om du inte vill använda din egen bacpac-fil.|
    |Plats |Använd standardvärdet.|

    Azure SQL Server-namnet blir `<ClusterName>dbserver`. Namnet på databasen kommer att `<ClusterName>db`. Standard lagringskontonamn kommer att `e6qhezrh2pdqu`.

3. Välj **jag godkänner villkoren som anges ovan**.

4. Välj **Köp**. Du ser en ny panel visas med rubriken skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret och SQL Database.

## <a name="run-sqoop-jobs"></a>Köra Sqoop-jobb

HDInsight kan köra Sqoop-jobb med hjälp av olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig och klicka sedan på länken för en genomgång.

| **Använd den här** om du vill... | ...an **interaktiva** shell | ...**batch** bearbetning | ...from detta **klientoperativsystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (för tillfället) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Begränsningar

* Massinläsning exportera – med Linux-baserade HDInsight, Sqoop anslutningen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.
* Batchbearbetning - med Linux-baserade HDInsight när du använder den `-batch` växlar när du utför infogningar och Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Hive med HDInsight](../hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](../hdinsight-use-pig.md)
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att ladda upp data till HDInsight/Azure Blob storage.
