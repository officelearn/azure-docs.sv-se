---
title: Identifiera avancerade analyser scenarier för Azure Machine Learning | Microsoft Docs
description: Välj lämplig scenarier för detta avancerad förutsägelseanalys med Team av vetenskapliga data.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 53aecc1e-5089-42cf-8d44-77678653f92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 7b1b8f00e109776a3a5d3cc8c2224e8160b85704
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenarier för avancerade analyser i Azure Machine Learning
Den här artikeln beskrivs olika exempel datakällor och målscenarier som kan hanteras av den [Team Data vetenskap processen (TDSP)](overview.md). TDSP ger systematiskt för grupper samarbeta i att skapa intelligent program. Scenarier som presenteras här visar alternativen i arbetsflödet databearbetning som beror på dataegenskaper, källplatser och mål-databaser i Azure.

Den **beslutsträdet** för att välja exempelscenarier som passar för dina data och målet visas i det sista avsnittet.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Följande avsnitt innehåller ett exempelscenario. För varje scenario ett möjligt datavetenskap eller avancerade analyser flödet och ge support för Azure-resurser visas.

> [!NOTE]
> **För alla följande scenarier måste du:**
> <br/>
> 
> * [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md)
>   <br/>
> * [Skapa en arbetsyta för Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scenariot \#1: små till medelstora tabular datauppsättning i lokala filer
![Små till medelstora lokala filer][1]

#### <a name="additional-azure-resources-none"></a>Ytterligare Azure-resurser: ingen
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
2. Överför en datamängd.
3. Skapa ett flöde för Azure Machine Learning-experiment som börjar med överförda datauppsättning/ar.

## <a name="smalllocalprocess"></a>Scenariot \#2: små till medelstora dataset för lokala filer som krävs
![Små till medelstora lokala filer med bearbetning][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (IPython anteckningsboken server)
1. Skapa en Azure-dator som kör IPython bärbar dator.
2. Överföra data till en Azure storage-behållare.
3. Förbearbeta och rensa data i IPython anteckningsbok, kommer åt data från Azure storage-behållare.
4. Transformera data till rensad tabellform.
5. Spara transformerade data i Azure BLOB.
6. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
7. Läsa data från Azure blobar med hjälp av den [importera Data] [ import-data] modul.
8. Skapa ett flöde för Azure Machine Learning-experiment som börjar med infogade datauppsättning/ar.

## <a name="largelocal"></a>Scenariot \#3: stora dataset av lokala filer, riktad på Azure-BLOB
![Stora lokala filer][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (IPython anteckningsboken server)
1. Skapa en Azure-dator som kör IPython bärbar dator.
2. Överföra data till en Azure storage-behållare.
3. Förbearbeta och rensa data i IPython anteckningsbok, kommer åt data från Azure BLOB.
4. Transformera data till rensad tabellform, om det behövs.
5. Utforska data och skapa funktioner efter behov.
6. Extrahera ett datasampel för små till medelstora.
7. Spara samplade data i Azure BLOB.
8. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Läsa data från Azure blobar med hjälp av den [importera Data] [ import-data] modul.
10. Skapa flödet för Azure Machine Learning-experiment från och med infogade datauppsättning/ar.

## <a name="smalllocaltodb"></a>Scenariot \#4: liten medelhög DataSet av lokala filer, SQL Server i en virtuell dator i Azure som mål
![Små till medelstora lokala filer till SQL DB i Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython anteckningsboken server)
1. Skapa en Azure-dator som kör SQL Server + IPython bärbar dator.
2. Överföra data till en Azure storage-behållare.
3. Förbearbeta och rensa data i Azure storage-behållare med IPython bärbar dator.
4. Transformera data till rensad tabellform, om det behövs.
5. Sparar data till VM-lokala filer (IPython bärbar dator körs på den virtuella datorn finns i lokala enheter för VM-enheter).
6. Läsa in data till SQL Server-databas som körs på en virtuell dator i Azure.
   
   Alternativet \#1: använder SQL Server Management Studio.
   
   * Logga in på SQLServer-dator
   * Kör SQL Server Management Studio.
   * Skapa tabeller i databasen och målet.
   * Använd en av flesta importera metoder för att läsa in data från VM-lokala filer.
   
   Alternativet \#2: med IPython anteckningsboken – inte lämpligt för medelstora och större datauppsättningar
   
   <!-- -->    
   * Använd ODBC-anslutningssträng för att få åtkomst till SQL Server på den virtuella datorn.
   * Skapa tabeller i databasen och målet.
   * Använd en av flesta importera metoder för att läsa in data från VM-lokala filer.
7. Utforska data, skapa funktioner efter behov. Observera att funktionerna för inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
8. Besluta om exempel datastorleken, om det behövs och/eller önskas.
9. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
10. Läsa data direkt från en SQL Server med hjälp av den [importera Data] [ import-data] modul. Klistra in den nödvändiga fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i den [importera Data] [ import-data] frågan.
11. Skapa flödet för Azure Machine Learning-experiment från och med infogade datauppsättning/ar.

## <a name="largelocaltodb"></a>Scenariot \#5: stora datauppsättning i lokala filer, mål SQL Server i Azure VM
![Stora lokala filer till SQL DB i Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython anteckningsboken server)
1. Skapa en Azure-dator som kör SQL Server och IPython anteckningsboken server.
2. Överföra data till en Azure storage-behållare.
3. (Valfritt) Förbearbeta och rensa data.
   
   a.  Förbearbeta och rensa data i IPython anteckningsbok, kommer åt data från Azure
   
       blobs.
   
   b.  Transformera data till rensad tabellform, om det behövs.
   
   c.  Sparar data till VM-lokala filer (IPython bärbar dator körs på den virtuella datorn finns i lokala enheter för VM-enheter).
4. Läsa in data till SQL Server-databas som körs på en virtuell dator i Azure.
   
   a.  Logga in på SQLServer-dator.
   
   b.  Om data inte sparats redan hämta datafiler från Azure
   
       storage container to local-VM folder.
   
   c.  Kör SQL Server Management Studio.
   
   d.  Skapa tabeller i databasen och målet.
   
   e.  Använd en av flesta importera metoder för att läsa in data.
   
   f.  Om tabellkopplingar krävs, skapa index för att påskynda kopplingar.
   
   > [!NOTE]
   > Det rekommenderas för snabbare överföring av stora datamängder som du skapar partitionerade tabeller och massimportera data parallellt. Mer information finns i [parallella Import av Data till SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Utforska data, skapa funktioner efter behov. Observera att funktionerna för inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
6. Besluta om exempel datastorleken, om det behövs och/eller önskas.
7. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Läsa data direkt från en SQL Server med hjälp av den [importera Data] [ import-data] modul. Klistra in den nödvändiga fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i den [importera Data] [ import-data] frågan.
9. Enkel Azure Machine Learning experiment flödet startar med överförda datamängd

## <a name="largedbtodb"></a>Scenariot \#6: stora dataset i en SQL Server-databas lokalt, SQL Server i en virtuell dator i Azure som mål
![Stora SQL DB lokalt till SQL DB i Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython anteckningsboken server)
1. Skapa en Azure-dator som kör SQL Server och IPython anteckningsboken server.
2. Använd en av data exportera metoder för att exportera data från SQL Server till dumpfiler.
   
   > [!NOTE]
   > Om du vill flytta alla data från lokala databas, en alternativ (snabbare) metod att flytta hela databasen till SQL Server-instansen i Azure. Hoppa över stegen för att exportera data, skapa databas, och Läs in/importera data till måldatabasen och följer alternativ metod.
   > 
   > 
3. Överför filer med felsökningsdumpar till Azure storage-behållare.
4. Läsa in data till en SQL Server-databas som körs på en virtuell dator i Azure.
   
   a.  Logga in på SQLServer-dator.
   
   b.  Hämta data från en Azure storage-behållare till den lokala VM-mappen.
   
   c.  Kör SQL Server Management Studio.
   
   d.  Skapa tabeller i databasen och målet.
   
   e.  Använd en av flesta importera metoder för att läsa in data.
   
   f.  Om tabellkopplingar krävs, skapa index för att påskynda kopplingar.
   
   > [!NOTE]
   > Importera data parallellt för snabbare överföring av stora datamängder, skapa partitionerade tabeller och omfång. Mer information finns i [parallella Import av Data till SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Utforska data, skapa funktioner efter behov. Observera att funktionerna för inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
6. Besluta om exempel datastorleken, om det behövs och/eller önskas.
7. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Läsa data direkt från en SQL Server med hjälp av den [importera Data] [ import-data] modul. Klistra in den nödvändiga fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i den [importera Data] [ import-data] frågan.
9. Enkel Azure Machine Learning experimentera flödet som börjar med överförda dataset.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativ metod för att kopiera en fullständig databas från en lokal SQL Server till Azure SQL Database
![Koppla från lokala DB och ansluta till SQL-databas i Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython anteckningsboken server)
Om du vill replikera hela SQL Server-databasen i SQL Server-VM, bör du kopiera en databas från en plats-server till en annan, förutsatt att databasen kan tas offline. Detta gör du i SQL Server Management Studio Object Explorer eller med hjälp av motsvarande Transact-SQL-kommandon.

1. Koppla från databasen på källplatsen. Mer information finns i [koppla från en databas](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
2. Kopiera frånkopplad databasfilen eller filerna och loggfilen eller filer i Utforskaren i Windows eller Windows kommandotolk-fönster till platsen på SQL Server-VM i Azure.
3. Bifoga filerna till mål SQL Server-instansen. Mer information finns i [ansluta en databas](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Flytta en databas med hjälp av koppla från och ansluta (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scenariot \#7: stordata i lokala filer mål Hive-databas i Azure HDInsight Hadoop-kluster
![Stordata i lokala mål Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure HDInsight Hadoop-kluster och Azure-dator (IPython anteckningsboken server)
1. Skapa en Azure-dator som kör IPython anteckningsboken server.
2. Skapa ett Azure HDInsight Hadoop-kluster.
3. (Valfritt) Förbearbeta och rensa data.
   
   a.  Förbearbeta och rensa data i IPython anteckningsbok, kommer åt data från Azure
   
       blobs.
   
   b.  Transformera data till rensad tabellform, om det behövs.
   
   c.  Sparar data till VM-lokala filer (IPython bärbar dator körs på den virtuella datorn finns i lokala enheter för VM-enheter).
4. Överföra data till standardbehållaren för det Hadoop-kluster som är markerade i steg 2.
5. Läsa in data till Hive-databas i Azure HDInsight Hadoop-kluster.
   
   a.  Logga in till huvudnod i Hadoop-kluster
   
   b.  Öppna Hadoop-kommandoraden.
   
   c.  Ange Hive rotkatalogen av kommandot `cd %hive_home%\bin` i Hadoop-kommandoraden.
   
   d.  Köra Hive-frågor för att skapa databasen och tabeller och läsa data från blob storage till Hive-tabeller.
   
   > [!NOTE]
   > Om data är stor, kan användare skapa Hive-tabell med partitioner. Användare kan sedan använda en `for` loop i Hadoop kommandoraden på huvudnoden att läsa in data i Hive-tabell som har partitionerats med partitionen.
   > 
   > 
6. Utforska data och skapa funktioner som behövs i Hadoop-kommandoraden. Observera att funktionerna för inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
   
   a.  Logga in till huvudnod i Hadoop-kluster
   
   b.  Öppna Hadoop-kommandoraden.
   
   c.  Ange Hive rotkatalogen av kommandot `cd %hive_home%\bin` i Hadoop-kommandoraden.
   
   d.  Köra Hive-frågor i Hadoop kommandoraden i huvudnod i Hadoop-kluster och utforska data och skapa funktioner efter behov.
7. Om det behövs och/eller önskas, exempeldata att få plats i Azure Machine Learning Studio.
8. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Läsa data direkt från den `Hive Queries` med hjälp av den [importera Data] [ import-data] modul. Klistra in den nödvändiga fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i den [importera Data] [ import-data] frågan.
10. Enkel Azure Machine Learning experimentera flödet som börjar med överförda dataset.

## <a name="decisiontree"></a>Beslutsträd för val av scenariot
- - -
Följande diagram sammanfattar de scenarier som beskrivs ovan och Advanced Analytics processen och Teknikval som görs som tar dig vidare till var och en av de specificerade scenarierna. Observera att databearbetning, undersökning, funktionen tekniker och sampling kan ta i metoden/miljön--på källan, mellanliggande, eller mål-miljöer, och kan fortsätta upprepade gånger efter behov. Diagrammet bara fungerar som en bild på några möjliga flöden och ger inte en uttömmande förteckning.

![Genomgång exempelscenarier DS process][8]

### <a name="advanced-analytics-in-action-examples"></a>Avancerade analyser i praktiken exempel
För slutpunkt till slutpunkt Azure Machine Learning genomgångar som använder den Advanced Analytics processen och teknik som använder offentliga datauppsättningar, se:

* [Teamet vetenskap av data i praktiken: använder SQL Server](sql-walkthrough.md).
* [Teamet vetenskap av data i praktiken: med HDInsight Hadoop-kluster](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
