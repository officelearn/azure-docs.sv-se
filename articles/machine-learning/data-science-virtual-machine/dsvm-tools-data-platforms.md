---
title: Data plattformar som stöds
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de data plattformar och verktyg som stöds för Azure Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bfae8147c348c76fa0e406fec283144ebc26e86b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270113"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Dataplattformar som stöds på den virtuella datorn för datavetenskap

Med en Data Science Virtual Machine (DSVM) kan du bygga upp din analys mot en mängd olika data plattformar. Förutom gränssnitt till fjärrdata plattformar tillhandahåller DSVM en lokal instans för snabb utveckling och prototyper.

Följande data plattforms verktyg stöds på DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developers utgåva

| | |
| ------------- | ------------- |
| Vad är det?   | En lokal relationsdatabasinstansen      |
| DSVM-versioner som stöds      | Windows: SQL Server 2017, Windows 2019 (för hands version): SQL Server 2019      |
| Vanliga användningsområden      | Snabb utveckling lokalt med mindre datauppsättning <br/> Kör R i databasen   |
| Innehåller länkar till exempel      |    Ett litet exempel på en ny data uppsättning i Göteborg i Göteborg läses in i SQL-databasen:<br/>  `nyctaxi` <br/> Jupyter-exempel som visar Microsoft Machine Learning Server och databas analys finns på:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Relaterade verktyg på DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-drivrutiner<br/> pyodbc RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer Edition kan endast användas i utvecklings-och test syfte. Du behöver en licens eller en av SQL Server-datorer att köras i produktion.


### <a name="setup"></a>Konfiguration

Databas servern är redan förkonfigurerad och Windows-tjänster som är relaterade till SQL Server (som `SQL Server (MSSQLSERVER)`) är inställda på att köras automatiskt. Det enda manuella steget innebär att du aktiverar databas analyser med hjälp av Microsoft Machine Learning Server. Du kan aktivera analys genom att köra följande kommando som en engångs åtgärd i SQL Server Management Studio (SSMS). Kör det här kommandot när du har loggat in som dator administratör, öppna en ny fråga i SSMS och kontrol lera att den valda databasen är `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Om du vill köra SQL Server Management Studio kan du söka efter "SQL Server Management Studio" i program listan eller använda Windows Search för att hitta och köra den. När du uppmanas att ange autentiseringsuppgifter väljer du **Windows-autentisering** och använder dator namnet eller ```localhost``` i fältet **SQL Server namn** .

### <a name="how-to-use-and-run-it"></a>Använda och köra den

Som standard körs databas servern med standard databas instansen automatiskt. Du kan använda verktyg som SQL Server Management Studio på den virtuella datorn för att få åtkomst till lokal SQL Server-databas. Lokala administratörs konton har administratörs behörighet för databasen.

Dessutom levereras DSVM med ODBC-och JDBC-drivrutiner för att prata med SQL Server, Azure SQL-databaser och Azure SQL Data Warehouse från program som har skrivits på flera språk, inklusive python och Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hur konfigureras den och installeras på DSVM? 

 SQL Server installeras på standard sätt. Du hittar den på `C:\Program Files\Microsoft SQL Server`. Machine Learning Server-instansen i databasen finns på `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM har också en separat fristående Machine Learning Server instans, som installeras på `C:\Program Files\Microsoft\R Server\R_SERVER`. Dessa två Machine Learning Server-instanser delar inte bibliotek.


## <a name="apache-spark-2x-standalone"></a>Apache Spark-2.x (fristående)

| | |
| ------------- | ------------- |
| Vad är det?   | En fristående instans (enkel nod i processen) för den populära Apache Spark plattformen. ett system för snabb, storskalig data bearbetning och maskin inlärning     |
| DSVM-versioner som stöds      | Linux     |
| Vanliga användningsområden      | * Snabb utveckling av Spark/PySpark-program lokalt med en mindre data uppsättning och senare distribution på stora Spark-kluster som Azure HDInsight<br/> * Test Microsoft Machine Learning Server Spark-kontext <br />* Använd SparkML eller Microsofts [MMLSpark](https://github.com/Azure/mmlspark) -bibliotek med öppen källkod för att skapa ml-program |
| Innehåller länkar till exempel      |    Jupyter-exempel: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (Spark-kontext):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Relaterade verktyg på DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark-kärnor)<br/>Microsoft Machine Learning Server, sparker, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Hur du använder den
Du kan skicka Spark-jobb på kommando raden genom att köra kommandot `spark-submit` eller `pyspark`. Du kan också skapa en Jupyter-anteckningsbok genom att skapa en ny anteckningsbok med Spark-kernel.

Du kan använda Spark från R genom att använda bibliotek som sparker, Sparklyr och Microsoft Machine Learning Server, som är tillgängliga på DSVM. Se pekare till exempel i tabellen ovan.

### <a name="setup"></a>Konfiguration
Innan du kör i en spark-kontext i Microsoft Machine Learning Server på Ubuntu Linux DSVM-versionen måste du slutföra ett engångs inställnings steg för att aktivera en lokal enskild nod Hadoop HDFS-och garn-instans. Som standard är Hadoop-tjänster installerat men inaktiverat på DSVM. Om du vill aktivera dem kör du följande kommandon som rot första gången:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Du kan stoppa de Hadoop-relaterade tjänsterna när du inte längre behöver dem genom att köra ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Ett exempel som visar hur du utvecklar och testar fru i en fjärran sluten Spark-kontext (som är den fristående Spark-instansen på DSVM) tillhandahålls och är tillgänglig i katalogen `/dsvm/samples/MRS`.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hur konfigureras den och installeras på DSVM? 
|Plattform|Installationsplats ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotek för att komma åt data från Azure Blob Storage eller Azure Data Lake Storage, som använder Microsoft MMLSpark Machine-Learning-bibliotek, förinstalleras i $SPARK _HOME/Jars. De här JAR-filer läses in automatiskt när Spark startas. Som standard använder Spark data på den lokala disken. 

För Spark-instansen på DSVM för att komma åt data som lagras i Blob Storage eller Azure Data Lake Storage, måste du skapa och konfigurera `core-site.xml`-filen baserat på mallen som finns i $SPARK _HOME/conf/Core-site.xml.template. Du måste också ha rätt autentiseringsuppgifter för att få åtkomst till Blob Storage och Azure Data Lake Storage. (Observera att mallfilerna använder plats hållare för Blob Storage och Azure Data Lake Storage konfigurationer.)

Mer detaljerad information om hur du skapar Azure Data Lake Storage autentiseringsuppgifter för tjänsten finns i [autentisering med Azure Data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). När autentiseringsuppgifterna för Blob Storage eller Azure Data Lake Storage har angetts i site. XML-filen kan du referera till de data som lagras i dessa källor via URI-prefixet wasb://eller adl://.

