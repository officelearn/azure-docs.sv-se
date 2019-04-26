---
title: Dataplattformar för den virtuella datorn för datavetenskap – Azure | Microsoft Docs
description: Läs mer om vilka Dataplattformar och verktyg som stöds på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 27e0deae9c35ad8fa00659e3e3e505cace6e9014
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516505"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Dataplattformar som stöds på den virtuella datorn för datavetenskap

Den virtuella datorn på datavetenskap (DSVM) kan du skapa dina analyser mot en mängd olika Dataplattformar. Förutom gränssnitt till fjärrdata plattformar tillhandahåller DSVM en lokal instans för snabb utveckling och prototyper. 

Här följer dataplattformsverktyg som stöds på DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Vad är det?   | En lokal relationsdatabasinstansen      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Snabb utveckling lokalt med mindre datauppsättning <br/> Kör R i databasen   |
| Innehåller länkar till exempel      |    Ett litet antal New York City datauppsättning läses in i SQL-databasen `nyctaxi`. <br/> Jupyter-exempel som visar Microsoft R och databasanalys finns på:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Relaterade verktyg på DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-drivrutiner<br/> pyodbc RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 developer edition kan endast användas för utveckling och testning. Du behöver en licens eller en av SQL Server-datorer att köras i produktion. 


### <a name="setup"></a>Konfiguration

Databasservern är redan förkonfigurerad och Windows-tjänster som är relaterade till SQL Server (t.ex. `SQL Server (MSSQLSERVER)`) är inställda på att köras automatiskt. Endast manuella steg som ska köras är att aktivera databasanalys med Microsoft R. Du kan göra detta genom att köra följande kommando som en åtgärd i SQL Server Management Studio (SSMS) när du har loggat i som administratören på datorn öppnar du en ”ny fråga” i SSMS, se till att den valda databasen är `master` och kör sedan: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
För att köra SQL Server Management Studio kan du söker du efter ”SQL Server Management Studio” i programlistan eller använda Windows Search för att hitta och köra den. När du tillfrågas om autentiseringsuppgifter, Välj ”Windows-autentisering” och använda namnet på datorn eller ```localhost``` i SQL-servernamnet. 

### <a name="how-to-use--run-it"></a>Hur du använder / köra den?  

Databasserver med databasinstansen av standardtyp körs automatiskt som standard. Du kan använda verktyg som SQL Server Management Studio på den virtuella datorn för att få åtkomst till lokal SQL Server-databas. Lokala administratörskonto ha administratörsåtkomst till databasen. 

DSVM levereras också med ODBC-drivrutiner och JDBC-drivrutiner för att kommunicera med SQL Server, Azure SQL-databaser och Azure SQL Data Warehouse från program som är skrivna på flera språk, inklusive Python och R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hur är det konfigurerade / installerad på DSVM? 

SQL Server är installerad på vanligt sätt. Det finns på `C:\Program Files\Microsoft SQL Server`. In-database-R-instansen påträffades vid `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM har också en separat fristående R Server-instans som installerats på `C:\Program Files\Microsoft\R Server\R_SERVER`. Dessa två-R-instanser delar inte biblioteken.


## <a name="apache-spark-2x-standalone"></a>Apache Spark-2.x (fristående)

| | |
| ------------- | ------------- |
| Vad är det?   | En fristående (en nod i processen)-instans för populära Apache Spark-plattformen, ett system för snabb storskalig databearbetning och maskininlärning     |
| Stöds DSVM-versioner      | Linux <br /> Windows (experimentell)      |
| Vanliga användningsområden      | * Snabb utveckling av Spark/PySpark-program lokalt med mindre datauppsättning och senare distribuerar den i stora Spark-kluster, till exempel Azure HDInsight<br/> * Testa Microsoft R Server Spark-kontexten <br />* Använda SparkML eller Microsofts med öppen källkod [MMLSpark](https://github.com/Azure/mmlspark) -biblioteket för att skapa ML-program  |
| Innehåller länkar till exempel      |    Jupyter-exempel: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R-Server (Spark-kontext): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Relaterade verktyg på DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark-kärnor)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Hur du använder den
Du kan köra Spark genom att skicka in Spark-jobb på kommandoraden med `spark-submit` eller `pyspark` kommandon. Du kan också skapa en Jupyter-anteckningsbok genom att skapa en ny anteckningsbok med Spark-kernel. 

Du kan använda Spark från R med bibliotek som SparkR, Sparklyr eller Microsoft R Server som är tillgängliga på DSVM. Se pekare till exempel i tabellen ovan. 

> [!NOTE]
> Med Microsoft R Server i Spark-kontexten av DSVM stöds bara på Ubuntu Linux DSVM-version. 



### <a name="setup"></a>Konfiguration
Innan du kör i Spark-kontexten i Microsoft R Server på Ubuntu Linux DSVM-version som du behöver göra en en gång installationsprogrammet steg för att aktivera en lokal nod HDFS Hadoop och Yarn-instans. Som standard är Hadoop-tjänster installerat men inaktiverat på DSVM. För att kunna aktivera den måste du köra följande kommandon som rot första gången:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Du kan stoppa Hadoop relaterade tjänster när du inte behöver dem genom att köra ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` ett exempel som visar hur du utvecklar och testar FRU i remote Spark-kontexten (som är fristående Spark-instansen på DSVM) har angetts och är tillgängliga i den `/dsvm/samples/MRS` katalogen. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hur är det konfigurerade / installerad på DSVM? 
|Plattform|Installationsplats ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotek för åtkomst till data från Azure Blob eller Azure Data Lake-lagring (ADLS) och använder maskininlärning för MMLSpark-bibliotek för Microsofts är förinstallerade i $SPARK_HOME/JAR-filer. De här JAR-filer läses in automatiskt när Spark startas. Som standard använder Spark data på den lokala disken. Du behöver skapa/konfigurera för Spark-instansen på DSVM att komma åt data som lagras på Azure blob eller ADLS den `core-site.xml` fil baserat på mallen finns i $SPARK_HOME/conf/core-site.xml.template (där det finns platshållare för Blob- och ADLS konfigurationer) med rätt autentiseringsuppgifter till Azure blob- och Azure Data Lake Storage. Du hittar mer detaljerade anvisningar om hur du skapar autentiseringsuppgifter för ADLS-tjänstens [här](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). När autentiseringsuppgifter för Azure blob eller ADLS har angetts i filen core-site.xml, du kan referera till de data som lagras i de källorna med URI-prefixet för wasb: / / eller adl: / /. 

