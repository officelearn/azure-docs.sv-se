---
title: Data plattformar för den datavetenskap virtuella - Azure | Microsoft Docs
description: Data plattformar för datavetenskap virtuell dator.
keywords: datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: b3f340006801287383c2afb2924706affbd77a51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="data-platforms"></a>Dataplattformar

Den virtuella datorn på vetenskap (DSVM) kan du skapa din analytics mot en mängd olika data plattformar. Förutom gränssnitt fjärrdata plattformar tillhandahåller DSVM en lokal instans för snabb utveckling och prototyper. 

Följande är data platform verktyg som stöds på DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Vad är det?   | En lokal relationsdatabasinstansen      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Snabb utveckling lokalt med mindre datamängd <br/> Kör i databasen R   |
| Länkar till exempel      |    Ett litet antal New York City Dataset läses in i SQL-databasen `nyctaxi`. <br/> Jupyter-exempel som visar Microsoft R och analyser i databasen finns på:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Relaterade verktyg på DSVM       | SQL Server Management Studio <br/> ODBC/JDBC-drivrutiner<br/> pyodbc RODBC<br />Apache ökad      |

> [!NOTE]
> SQL Server 2016 developer edition kan endast användas för utveckling och testning. Du behöver en licens eller en av de virtuella datorerna SQL Server att köra den i produktion. 


### <a name="setup"></a>Konfiguration

Databasservern redan förkonfigurerad och Windows-tjänster som är relaterade till SQL Server (t.ex. `SQL Server (MSSQLSERVER)`) är inställd på att köras automatiskt. Endast manuella steg ska köras är att aktivera i databasen analytics med hjälp av Microsoft R. Du kan göra detta genom att köra följande kommando en gång åtgärden i SQL Server Management Studio (SSMS) när du loggade in som datoradministratör öppnar en ”ny fråga” i SSMS, se till att den valda databasen är `master` och kör sedan: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Du kan söka efter ”SQL Server Management Studio” i programlistan eller använda Windows Search hitta och kör den för att köra SQL Server Management Studio. När du tillfrågas om autentiseringsuppgifter, Välj ”Windows-autentisering” och använd datornamnet eller ```localhost``` i SQL Server-namnet. 

### <a name="how-to-use--run-it"></a>Hur du använder / kör den?  

Databasserver med standard-databasinstans körs automatiskt som standard. Du kan använda verktyg som SQL Server Management Studio på den virtuella datorn för att få åtkomst till SQL Server-databas lokalt. Lokala administratörskonto ha administratörsåtkomst till databasen. 

DSVM levereras också med ODBC-drivrutiner och JDBC-drivrutiner tala med SQL Server, Azure SQL-databaser och Azure SQL Data Warehouse från program på flera språk, inklusive Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hur är det konfigurerade / installerad på DSVM? 

SQL Server är installerat på vanligt sätt. Det finns på `C:\Program Files\Microsoft SQL Server`. Instansen i databasen R påträffades vid `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM har också en separat fristående R Server-instans som installerats på `C:\Program Files\Microsoft\R Server\R_SERVER`. Dessa två-R-instanser delar inte biblioteken.


## <a name="apache-spark-2x-standalone"></a>Apache Väck 2.x (fristående)

| | |
| ------------- | ------------- |
| Vad är det?   | En fristående (nod i processen) instans av populära Apache Spark-plattform, ett system för snabb storskalig databearbetning och maskininlärning     |
| Stöds DSVM versioner      | Linux <br /> Windows (försök)      |
| Vanliga användningsområden      | * Snabb utveckling av Spark/PySpark program lokalt med mindre datamängd och senare distribuerar den stora Spark-kluster, till exempel Azure HDInsight<br/> * Testa Microsoft R Server Spark-kontext <br />* Använder SparkML eller Microsofts öppen källkod [MMLSpark](https://github.com/Azure/mmlspark) bibliotek för att skapa ML-program  |
| Länkar till exempel      |    Jupyter-exempel: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (Spark sammanhang): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Relaterade verktyg på DSVM       | PySpark Scala<br/>Jupyter (Spark/PySpark kärnor)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache ökad      |

### <a name="how-to-use-it"></a>Hur du använder det.
Du kan köra Spark genom att skicka Spark jobb på kommandoraden med `spark-submit` eller `pyspark` kommandon. Du kan också skapa en Jupyter-anteckningsbok genom att skapa en ny anteckningsbok med Spark-kernel. 

Du kan använda Spark från R bibliotek som SparkR, Sparklyr eller Microsoft R Server som är tillgängliga på DSVM. Se pekare till exempel i tabellen ovan. 

> [!NOTE]
> Kör Microsoft R Server Spark gäller DSVM stöds bara på Ubuntu Linux DSVM edition. 



### <a name="setup"></a>Konfiguration
Innan du kör i Spark-kontexten i Microsoft R Server på Ubuntu Linux DSVM edition måste du göra en engångsinställning som installationsprogrammet steg för att aktivera en lokal nod Hadoop HDFS och Yarn-instans. Som standard installeras men inaktiveras på DSVM Hadoop-tjänster. För att aktivera den, måste du köra följande kommandon som rot för första gången:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Du kan stoppa Hadoop-relaterade tjänster när du inte behöver dem genom att köra ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` ett exempel som visar hur du utvecklar och testar FRU i fjärranslutna Spark-kontexten (som är fristående Spark-instans på DSVM) har angetts och är tillgängliga i den `/dsvm/samples/MRS` katalog. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hur är det konfigurerade / installerad på DSVM? 
|Plattform|Installationsplats ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Bibliotek för att komma åt data från Azure Blob- eller Azure Data Lake-lagring (ADLS) och använda Microsofts MMLSpark maskininlärning bibliotek är förinstallerade i $SPARK_HOME/burkar. Dessa burkar läses in automatiskt när Spark startas. Som standard använder Spark data på den lokala disken. För att Spark-instans på DSVM att komma åt data som lagras på Azure blob- eller ADLS måste du skapa/konfigurera den `core-site.xml` filen utifrån mallen finns i $SPARK_HOME/conf/core-site.xml.template (där det finns platshållare för Blob- och ADLS konfigurationer) med korrekta autentiseringsuppgifter till Azure blob och Azure Data Lake lagring. Du hittar mer detaljerade anvisningar om hur du skapar Tjänstereferenser ADLS [här](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). När autentiseringsuppgifterna för Azure blob eller ADLS registreras i filen core-site.XML, du kan referera till data som lagras i dessa källor med URI-prefix för wasb: / / eller adl: / /. 

