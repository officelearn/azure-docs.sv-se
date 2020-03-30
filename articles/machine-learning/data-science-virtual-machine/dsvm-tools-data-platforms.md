---
title: Dataplattformar som stöds
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de dataplattformar och verktyg som stöds för Den virtuella Azure Data Science-datorn.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282332"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Dataplattformar som stöds på Virtual Machine för datavetenskap

Med en virtuell dator för datavetenskap (DSVM) kan du skapa din analys mot ett brett spektrum av dataplattformar. Förutom gränssnitt till fjärrdataplattformar tillhandahåller DSVM en lokal instans för snabb utveckling och prototyper.

Följande dataplattformsverktyg stöds på DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Vad är detta?   | En lokal relationsdatabasinstans      |
| DSVM-utgåvor som stöds      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typiska användningsområden      | Snabb utveckling lokalt med mindre datauppsättning <br/> Kör R i databasen   |
| Länkar till exempel      |    Ett litet exempel på en New York City-datauppsättning läses in i SQL-databasen:<br/>  `nyctaxi` <br/> Jupyter-exempel som visar Microsoft Machine Learning Server och analys i databasen finns på:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Relaterade verktyg på DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-drivrutiner<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer Edition kan endast användas för utvecklings- och teständamål. Du behöver en licens eller en av virtuella SQL Server-datorer för att köra den i produktion.


### <a name="setup"></a>Installation

Databasservern är redan förkonfigurerad och Windows-tjänsterna `SQL Server (MSSQLSERVER)`relaterade till SQL Server (som) är inställda på att köras automatiskt. Det enda manuella steget innebär att aktivera analys i databasen med hjälp av Microsoft Machine Learning Server. Du kan aktivera analyser genom att köra följande kommando som en engångsåtgärd i SQL Server Management Studio (SSMS). Kör det här kommandot när du har loggat in som maskinadministratör, öppnar en `master`ny fråga i SSMS och kontrollerar att den valda databasen är:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Om du vill köra SQL Server Management Studio kan du söka efter "SQL Server Management Studio" i programlistan eller använda Windows Search för att hitta och köra den. När du uppmanas att ange autentiseringsuppgifter ```localhost``` väljer du **Windows-autentisering** och använder datornamnet eller i fältet SQL **Server-namn.**

### <a name="how-to-use-and-run-it"></a>Så här använder och kör du den

Som standard körs databasservern med standarddatabasinstansen automatiskt. Du kan använda verktyg som SQL Server Management Studio på den virtuella datorn för att komma åt SQL Server-databasen lokalt. Lokala administratörskonton har administratörsbehörighet i databasen.

DSVM levereras också med ODBC- och JDBC-drivrutiner för att prata med SQL Server, Azure SQL-databaser och Azure SQL Data Warehouse från program skrivna på flera språk, inklusive Python och Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hur är det konfigurerat och installerat på DSVM? 

 SQL Server installeras på standardsättet. Den finns på `C:\Program Files\Microsoft SQL Server`. Machine Learning Server-instansen i `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`databasen finns på . DSVM har också en separat fristående Machine Learning Server-instans, som är installerad på `C:\Program Files\Microsoft\R Server\R_SERVER`. Dessa två Machine Learning Server-instanser delar inte bibliotek.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (fristående)

| | |
| ------------- | ------------- |
| Vad är detta?   | En fristående (ennod i processen) instans av den populära Apache Spark-plattformen. ett system för snabb, storskalig databehandling och maskininlärning     |
| DSVM-utgåvor som stöds      | Linux     |
| Typiska användningsområden      | * Snabb utveckling av Spark/PySpark-program lokalt med en mindre datauppsättning och senare distribution på stora Spark-kluster som Azure HDInsight<br/> * Testa Microsoft Machine Learning Server Spark-kontext <br />* Använd SparkML eller Microsofts [open-source MMLSpark](https://github.com/Azure/mmlspark) bibliotek för att bygga ML-program |
| Länkar till exempel      |    Jupyter prov: <br />&nbsp;&nbsp;* ~/bärbara datorer/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/bärbara datorer/MMLSpark <br /> Microsoft Machine Learning Server (Spark-kontext): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Relaterade verktyg på DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark-kärnor)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Så här används det
Du kan skicka Spark-jobb på `spark-submit` kommandoraden genom att köra kommandot eller. `pyspark` Du kan också skapa en Jupyter-anteckningsbok genom att skapa en ny anteckningsbok med Spark-kärnan.

Du kan använda Spark från R med hjälp av bibliotek som SparkR, Sparklyr och Microsoft Machine Learning Server, som är tillgängliga på DSVM. Visa pekare till exempel i tabellen ovan.

### <a name="setup"></a>Installation
Innan du kör i en Spark-kontext i Microsoft Machine Learning Server på Ubuntu Linux DSVM-utgåva måste du slutföra ett engångsinstallationssteg för att aktivera en lokal ennod Hadoop HDFS- och Yarn-instans. Som standard installeras Hadoop-tjänster men inaktiveras på DSVM. Om du vill aktivera dem kör du följande kommandon som root första gången:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Du kan stoppa Hadoop-relaterade tjänster när du ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```inte längre behöver dem genom att köra .

Ett exempel som visar hur du utvecklar och testar MRS i en fjärr spark-kontext (som är den `/dsvm/samples/MRS` fristående Spark-instansen på DSVM) tillhandahålls och är tillgänglig i katalogen.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hur är det konfigurerat och installerat på DSVM? 
|Plattform|Installera plats ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotek för att komma åt data från Azure Blob storage eller Azure Data Lake Storage, med hjälp av Microsoft MMLSpark maskininlärningsbibliotek, förinstalleras i $SPARK_HOME/jars. Dessa JARs laddas automatiskt när Spark startar. Som standard använder Spark data på den lokala disken. 

För att Spark-instansen på DSVM ska komma åt data som lagras i `core-site.xml` Blob-lagring eller Azure Data Lake Storage måste du skapa och konfigurera filen baserat på mallen som finns i $SPARK_HOME/conf/core-site.xml.template. Du måste också ha rätt autentiseringsuppgifter för att komma åt Blob-lagring och Azure Data Lake Storage. (Observera att mallfilerna använder platshållare för Blob-lagring och Azure Data Lake Storage-konfigurationer.)

Mer detaljerad information om hur du skapar Azure Data Lake Storage-tjänstautentiseringsuppgifter finns i [Autentisering med Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). När autentiseringsuppgifterna för Blob-lagring eller Azure Data Lake Storage har angetts i filen core-site.xml kan du referera till de data som lagras i dessa källor via URI-prefixet wasb:// eller adl://.

