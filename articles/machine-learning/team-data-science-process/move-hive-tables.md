---
title: "Skapa Hive-tabeller och Läs in data från Azure Blob Storage | Microsoft Docs"
description: "Skapa Hive-tabeller och läsa in data i blob till hive tabeller"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: c90c3d3c0effd68a4a5962d4d097fccbdc3fee56
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Skapa Hive-tabeller och Läs in data från Azure Blob Storage
Det här avsnittet innehåller allmänna Hive-frågor som skapar Hive-tabeller och läsa in data från Azure blob storage. Vägledning finns även på partitionering Hive-tabeller och använda den optimerade raden kolumner (ORC) formatering för att förbättra frågeprestanda.

Detta **menyn** länkar till avsnitt som beskriver hur du mata in data i mål-miljöer där data kan lagras och behandlas under Team Data vetenskap processen (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver mer information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).
* Etablera ett anpassat Hadoop-kluster med HDInsight-tjänst.  Om du behöver mer information, se [anpassa Azure HDInsight Hadoop-kluster för avancerade analyser](customize-hadoop-cluster.md).
* Aktiverade fjärråtkomst till klustret, inloggad och öppnas Hadoop kommandoradskonsol. Om du behöver mer information, se [komma åt det Head nod för Hadoop-kluster](customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure blob storage
Om du har skapat en virtuell Azure-dator genom att följa instruktionerna i [ställa in en virtuell Azure-dator för avancerade analyser](../data-science-virtual-machine/setup-virtual-machine.md), den här skriptfilen bör har hämtats till den *C:\\användare \\ \<användarnamn\>\\dokument\\datavetenskap skript* katalog på den virtuella datorn. Dessa Hive-frågor kräver bara att du ansluter dina egna dataschemat och Azure blob storage-konfigurationen i relevanta fält ska bli klar för överföring.

Vi förutsätter att data för Hive-tabeller är i ett **okomprimerad** tabellformat och att data har överförts till standardvärdet (eller en extra) behållare för storage-konto som används av Hadoop-kluster.

Om du vill praxis på den **NYC Taxi resa Data**, måste du:

* **Hämta** 24 [NYC Taxi resa Data](http://www.andresmh.com/nyctaxitrips) filer (12 resa filer och 12 avgiften-filer)
* **Packa upp** alla filer i CSV-filer, och sedan
* **Överför** dem till standard (eller lämplig behållare) för Azure storage-konto som har skapats med proceduren som beskrivs i den [anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics processen och tekniken](customize-hadoop-cluster.md)avsnittet. Processen för att ladda upp CSV-filer till standardbehållaren för storage-konto kan hittas på den här [sidan](hive-walkthrough.md#upload).

## <a name="submit"></a>Hur du skickar in Hive-frågor
Du kan skicka hive-frågor med hjälp av:

1. [Skicka Hive-frågor via Hadoop kommandoraden i headnode av Hadoop-kluster](#headnode)
2. [Skicka Hive-frågor med Hive-redigeraren](#hive-editor)
3. [Skicka Hive-frågor med Azure PowerShell-kommandon](#ps)

Hive-frågor är SQL-liknande. Om du är bekant med SQL kan vara den [Hive för SQL användare Cheat blad](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) användbart.

När en Hive-fråga, kan du också styra målet utdata från Hive-frågor, vare sig det är på skärmen eller till en lokal fil på huvudnoden eller till en Azure-blob.

### <a name="headnode"></a> 1. Skicka Hive-frågor via Hadoop kommandoraden i headnode av Hadoop-kluster
Om Hive-frågan är komplex leder att skicka den direkt i Hadoop huvudnod klustret normalt till att vända snabbare än att skicka den med en Hive-redigeraren eller Azure PowerShell-skript.

Logga in till huvudnod i Hadoop-kluster, öppna kommandoraden Hadoop på skrivbordet för huvudnoden och anger kommandot `cd %hive_home%\bin`.

Det finns tre sätt att skicka Hive-frågor i Hadoop kommandoraden:

* direkt
* med hjälp av .hql filer
* med Hive kommandokonsolen

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Skicka Hive-frågor direkt i Hadoop kommandoraden.
Du kan köra kommandot som `hive -e "<your hive query>;` att skicka enkla Hive-frågor direkt i Hadoop kommandoraden. Här är ett exempel där den röda rutan beskrivs det kommando som skickar Hive-fråga och den gröna rutan beskrivs utdata från Hive-fråga.

![Skapa arbetsyta](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Skicka Hive-frågor i .hql filer
När Hive-frågan är mer komplicerad och har flera rader, är redigerar frågor i kommandoraden eller Hive kommandokonsolen inte praktiskt. Ett alternativ är att använda en textredigerare i huvudnod Hadoop-kluster och spara Hive-frågor i en .hql-fil i en lokal katalog på huvudnoden. Sedan kan skicka Hive-fråga i filen .hql med hjälp av den `-f` argumentet på följande sätt:

    hive -f "<path to the .hql file>"

![Skapa arbetsyta](./media/move-hive-tables/run-hive-queries-3.png)

**Ignorera förlopp status skärmen utskrift av Hive-frågor**

Som standard när Hive-frågan har skickats i Hadoop kommandoraden skrivs förloppet för jobbet kartan/minska ut på skärmen. Du kan använda ett argument för att ignorera skärmen utskrift av jobbförloppet kartan/minska `-S` (”S” i versaler) i kommandot rad på följande sätt:

    hive -S -f "<path to the .hql file>"
.    hive -S -e ”<Hive queries>”

#### <a name="submit-hive-queries-in-hive-command-console"></a>Skicka Hive-frågor i Hive-Kommandotolken.
Du kan också ange Hive kommandokonsolen genom att köra kommandot `hive` i Hadoop kommandoraden och skicka Hive-frågor i Hive-Kommandotolken. Här är ett exempel. I det här exemplet markerar två röda rutor de kommandon som används för att ange Hive kommandokonsolen och Hive-fråga som skickade i Hive kommandokonsolen respektive. Den gröna rutan visar utdata från Hive-fråga.

![Skapa arbetsyta](./media/move-hive-tables/run-hive-queries-2.png)

I föregående exempel utdata direkt Hive frågeresultaten på skärmen. Du kan också skriva utdata till en lokal fil på huvudnoden eller till en Azure-blob. Du kan sedan använda andra verktyg för att ytterligare analysera utdata från Hive-frågor.

**Utdata Hive frågeresultaten till en lokal fil.**
Om du vill spara resultatet i Hive-frågan till en lokal katalog på huvudnoden har att skicka Hive-fråga i Hadoop kommandoraden på följande sätt:

    hive -e "<hive query>" > <local path in the head node>

I följande exempel skrivs utdata för Hive-fråga till en fil `hivequeryoutput.txt` i katalogen `C:\apps\temp`.

![Skapa arbetsyta](./media/move-hive-tables/output-hive-results-1.png)

**Utdata Hive frågeresultaten till en Azure-blob**

Du kan också spara resultatet av Hive-frågan till en Azure blob i standardbehållaren för Hadoop-kluster. Hive-fråga för det här är följande:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

I följande exempel skrivs utdata för Hive-fråga till en blob-katalog `queryoutputdir` i standardbehållaren för Hadoop-kluster. Här, behöver du bara ange katalognamnet utan blob-namn. Ett fel returneras om du anger både katalog och blob namn, exempelvis `wasb:///queryoutputdir/queryoutput.txt`.

![Skapa arbetsyta](./media/move-hive-tables/output-hive-results-2.png)

Om du öppnar standardbehållaren för Hadoop-kluster med Azure Lagringsutforskaren kan du se utdata för Hive-fråga som visas i följande bild. Du kan använda filter (visas med röd ruta) för att endast hämta blobben med angivna bokstäverna i namn.

![Skapa arbetsyta](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Skicka Hive-frågor med Hive-redigeraren
Du kan också använda konsolen fråga (Hive-redigeraren) genom att ange en URL i formatet *https://&#60; Hadoop-klusternamn >.azurehdinsight.net/Home/HiveEditor* i en webbläsare. Du måste vara inloggad i ser den här konsolen och du måste ha autentiseringsuppgifter här din Hadoop-kluster.

### <a name="ps"></a> 3. Skicka Hive-frågor med Azure PowerShell-kommandon
Du kan också använda PowerShell för att skicka Hive-frågor. Instruktioner finns i [skicka Hive-jobb med hjälp av PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Skapa Hive-databasen och tabeller
Hive-frågor delas i den [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) och kan hämtas därifrån.

Här är Hive-frågan som skapar en Hive-tabell.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Nedan följer beskrivningar av de fält som du ska ansluta och andra konfigurationer:

* **&#60; databasnamn >**: namnet på databasen som du vill skapa. Om du vill använda standarddatabasen frågan *Skapa databas...*  kan utelämnas.
* **&#60; tabellnamn >**: namnet på den tabell som du vill skapa inom den angivna databasen. Om du vill använda standarddatabasen tabellen kan refereras direkt av *&#60; tabellnamn >* utan &#60; databasnamn >.
* **&#60; fältavgränsaren >**: avgränsare som avgränsar fälten i filen ska överföras till Hive-tabell.
* **&#60; radbrytningstecken >**: avgränsare som avgränsar rader i datafilen.
* **&#60; lagringsplats >**: Azure storage-plats att spara data för Hive-tabeller. Om du inte anger *plats &#60; lagringsplats >*, lagras i databasen och tabeller i *hive/datalager/* katalogen i standardbehållaren för Hive-klustret som standard. Om du vill ange lagringsplatsen måste lagringsplatsen vara i standardbehållaren för databasen och tabeller. Den här platsen måste vara kallas klustrets i formatet plats i förhållande till standardbehållaren *' wasb: / / / &#60; katalogen 1 > / ”* eller *' wasb: / / / &#60; katalogen 1 > / &#60; katalogen 2 > /'*osv. När frågan körs, skapas de relativa katalogerna i standardbehållaren.
* **TBLPROPERTIES("Skip.Header.Line.Count"="1")**: om filen har en rubrikrad, måste du lägga till egenskapen **slutet** av den *Skapa tabell* frågan. Annars rubrikraden har lästs in som en post i tabellen. Den här konfigurationen kan utelämnas i frågan om filen inte har en rubrikrad.

## <a name="load-data"></a>Läs in data till Hive-tabeller
Här är Hive-frågan som läser in data i en Hive-tabell.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **&#60; sökväg till blob-data >**: om blob-filen ska överföras till Hive-tabell är i standardbehållaren för HDInsight Hadoop-kluster i *&#60; sökväg till blob-data >* ska vara i formatet *' wasb: / / / &#60; katalogen i den här behållaren > / &#60; blob filnamn >'*. Blob-fil kan också vara i ytterligare en behållare för HDInsight Hadoop-kluster. I det här fallet *&#60; sökväg till blob-data >* ska vara i formatet *' wasb: / / &#60; behållarens namn > @&#60; lagringskontonamnet >.blob.core.windows.net/ &#60; blob filnamn >'*.

  > [!NOTE]
  > Blob-data ska överföras till Hive-tabell måste vara i standard eller ytterligare en behållare på lagringskontot för Hadoop-kluster. I annat fall den *Läs in DATA* frågan inte kunde köras klagande att det går inte att komma åt data.
  >
  >

## <a name="partition-orc"></a>Avancerade alternativ: partitionerad tabell och lagra Hive-data i ORC-format
Om data är stor, är partitionera tabellen bra för frågor som behöver bara söka igenom några partitioner i tabellen. Exempelvis är det rimligt att partitionera loggdata för en webbplats efter datum.

Förutom partitionering Hive-tabeller, är det också bra att lagra Hive-data i formatet optimerade raden kolumner (ORC). Mer information om ORC formatering finns <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">med ORC-filer som förbättrar prestanda när Hive läsning, skrivning och bearbetning av data</a>.

### <a name="partitioned-table"></a>Partitionerad tabell
Här är Hive-frågan som skapar en partitionerad tabell och läser in data i den.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

När du frågar partitionerade tabeller, rekommenderar vi att lägga till villkor för partitionen i den **början** av den `where` satsen som detta förbättrar effektivitet söker avsevärt.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Hive data lagras i ORC-format
Du kan inte direkt läsa in data från blob storage i Hive-tabeller som är lagrad i ORC-format. Här följer de steg som du måste vidta för att läsa in data från Azure BLOB-objekt till Hive-tabeller som är lagrad i ORC-format.

Skapa en extern tabell **LAGRAS AS TEXTFILE** och läsa in data från blob storage i tabellen.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Skapa en intern tabell med samma schemat som den externa tabellen i steg 1, med samma fältavgränsaren och lagra Hive-data i formatet ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Välj data från den externa tabellen i steg 1 och infoga i tabellen ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Om tabellen TEXTFILE *&#60; databasnamn >. &#60; externa textfile tabellnamn >* har partitioner i steg3 i `SELECT * FROM <database name>.<external textfile table name>` kommandot väljer variabeln partition som ett fält i datamängden som returnerades. Lägga till den *&#60; databasnamn >. &#60; ORC tabellnamn >* misslyckas eftersom *&#60; databasens namn >. &#60; ORC tabellnamn >* inte partition variabeln som ett fält i tabellens schema. I detta fall du måste välja de fält som ska läggas till specifikt *&#60; databasnamn >. &#60; ORC-tabellnamn >* på följande sätt:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Det är säkert att släppa den *&#60; externa textfile tabellnamn >* när med följande fråga efter alla data som har infogats i *&#60; databasnamn >. &#60; ORC-tabellnamn >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

När den här proceduren bör du ha en tabell med data i formatet ORC redo att användas.  
