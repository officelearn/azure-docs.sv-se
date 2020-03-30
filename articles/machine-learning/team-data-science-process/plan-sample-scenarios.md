---
title: Identifiera scenarier för Azure Machine Learning - Team Data Science Process
description: Välj lämpliga scenarier för avancerad prediktiv analys med Team Data Science Process.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251627"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenarier för avancerade analyser i Azure Machine Learning
I den här artikeln beskrivs de olika exempeldatakällor och målscenarier som kan hanteras av [Team Data Science Process (TDSP).](overview.md) TDSP ger ett systematiskt tillvägagångssätt för team att samarbeta för att bygga intelligenta applikationer. Scenarierna som presenteras här illustrerar alternativ som är tillgängliga i arbetsflödet för databearbetning som är beroende av dataegenskaper, källplatser och måldatabaser i Azure.

**Beslutsträdet** för att välja de exempelscenarier som är lämpliga för dina data och mål visas i det sista avsnittet.

Vart och ett av följande avsnitt innehåller ett exempelscenario. För varje scenario visas ett möjligt datavetenskap eller avancerat analysflöde och stöd för Azure-resurser.

> [!NOTE]
> **För alla följande scenarier måste du:**
> <br/>
> 
> * [skapar ett lagringskonto](../../storage/common/storage-account-create.md)
>   <br/>
> * [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scenario \#1: Små till medelstora tabelldatauppsättning i lokala filer
![Små till medelstora lokala filer][1]

#### <a name="additional-azure-resources-none"></a>Ytterligare Azure-resurser: Inga
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Ladda upp en datauppsättning.
1. Skapa ett Azure Machine Learning-experimentflöde som börjar med uppladdade datauppsättningar.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scenario \#2: Små till medelstora datauppsättning av lokala filer som kräver bearbetning
![Små till medelstora lokala filer med bearbetning][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure Virtual Machine (IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör IPython Notebook.
1. Ladda upp data till en Azure Storage-behållare.
1. Förbehandla och rensa data i IPython Notebook och komma åt data från Azure Storage-behållaren.
1. Omvandla data till ett rensat tabellform.
1. Spara transformerade data i Azure-blobbar.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data från Azure-blobbar med modulen [Importera data.][import-data]
1. Skapa ett Azure Machine Learning-experimentflöde som börjar med intämda datauppsättningar.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scenario \#3: Stor datauppsättning med lokala filer, med inriktning på Azure Blobbar
![Stora lokala filer][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure Virtual Machine (IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör IPython Notebook.
1. Ladda upp data till en Azure Storage-behållare.
1. Förbehandla och rensa data i IPython Notebook, åtkomst till data från Azure-blobbar.
1. Omvandla data till ett rensat tabellformulär om det behövs.
1. Utforska data och skapa funktioner efter behov.
1. Extrahera ett litet till medelstort dataprov.
1. Spara de samplade data i Azure-blobbar.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data från Azure-blobbar med modulen [Importera data.][import-data]
1. Skapa Experimentflöde för Azure Machine Learning som börjar med intämda datauppsättningar.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scenario \#4: Små till medelstora datauppsättning med lokala filer, med inriktning på SQL Server i en virtuell Azure-dator
![Små till medelstora lokala filer till SQL DB i Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure Virtual Machine (SQL Server / IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör SQL Server + IPython Notebook.
1. Ladda upp data till en Azure Storage-behållare.
1. Förbehandla och rensa data i Azure Storage-behållare med IPython Notebook.
1. Omvandla data till ett rensat tabellformulär om det behövs.
1. Spara data till VM-lokala filer (IPython Notebook körs på vm, lokala enheter refererar till VM-enheter).
1. Läsa in data till SQL Server-databas som körs på en Virtuell Azure.Load data to SQL Server database running on an Azure VM.
   
   Alternativ \#1: Använda SQL Server Management Studio.
   
   * Logga in på VIRTUELL SQL Server
   * Kör SQL Server Management Studio.
   * Skapa databas- och måltabeller.
   * Använd en av massimportmetoderna för att läsa in data från VM-lokala filer.
   
   Alternativ \#2: Använda IPython Notebook - inte tillrådligt för medelstora och större datamängder
   
   <!-- -->    
   * Använd ODBC-anslutningssträng för att komma åt SQL Server på den virtuella datorn.
   * Skapa databas- och måltabeller.
   * Använd en av massimportmetoderna för att läsa in data från VM-lokala filer.
1. Utforska data, skapa funktioner efter behov. Funktionerna behöver inte materialiseras i databastabellerna. Notera endast den nödvändiga frågan för att skapa dem.
1. Bestäm en dataprovstorlek, om det behövs och/eller önskas.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data.][import-data] Klistra in den nödvändiga frågan som extraherar fält, skapar funktioner och exempeldata om det behövs direkt i frågan [Importera data.][import-data]
1. Skapa Experimentflöde för Azure Machine Learning som börjar med intämda datauppsättningar.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scenario \#5: Stor datauppsättning i lokala filer, mål SQL Server i Azure VM
![Stora lokala filer till SQL DB i Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure Virtual Machine (SQL Server / IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör SQL Server- och IPython Notebook-server.
1. Ladda upp data till en Azure Storage-behållare.
1. (Valfritt) Förbehandla och rensa data.
   
    a.  Förbehandla och rensa data i IPython Notebook, åtkomst till data från Azure-blobbar.
   
    b.  Omvandla data till ett rensat tabellformulär om det behövs.
   
    c.  Spara data till VM-lokala filer (IPython Notebook körs på vm, lokala enheter refererar till VM-enheter).
1. Läsa in data till SQL Server-databas som körs på en Virtuell Azure.Load data to SQL Server database running on an Azure VM.
   
    a.  Logga in på VIRTUELL SQL Server.
   
    b.  Om data inte redan har sparats hämtar du datafiler från Azure-lagringsbehållaren till mappen lokal virtuell dator.
   
    c.  Kör SQL Server Management Studio.
   
    d.  Skapa databas- och måltabeller.
   
    e.  Använd en av massimportmetoderna för att läsa in data.
   
    f.  Om tabellkopplingar krävs skapar du index för att påskynda kopplingar.
   
   > [!NOTE]
   > För snabbare inläsning av stora datastorlekar rekommenderar vi att du skapar partitionerade tabeller och massimporterar data parallellt. Mer information finns i [Parallell dataimport till SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Utforska data, skapa funktioner efter behov. Funktionerna behöver inte materialiseras i databastabellerna. Notera endast den nödvändiga frågan för att skapa dem.
1. Bestäm en dataprovstorlek, om det behövs och/eller önskas.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data.][import-data] Klistra in den nödvändiga frågan som extraherar fält, skapar funktioner och exempeldata om det behövs direkt i frågan [Importera data.][import-data]
1. Enkelt Experimentflöde för Azure Machine Learning som börjar med uppladdad datauppsättning

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scenario \#6: Stor datauppsättning i en SQL Server-databas lokalt och som riktar in sig på SQL Server i en virtuell Azure-dator
![Stor SQL DB on-prem till SQL DB i Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure Virtual Machine (SQL Server / IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör SQL Server- och IPython Notebook-server.
1. Använd en av dataexportmetoderna för att exportera data från SQL Server för att dumpa filer.
   
   > [!NOTE]
   > Om du bestämmer dig för att flytta alla data från den lokala databasen kan du använda en alternativ (snabbare) metod för att flytta hela databasen till SQL Server-instansen i Azure. Hoppa över stegen för att exportera data, skapa databas och läsa in/importera data till måldatabasen och följ den alternativa metoden.
   > 
   > 
1. Ladda upp dumpfiler till Azure Storage-behållaren.
1. Läsa in data till en SQL Server-databas som körs på en virtuell Azure-dator.
   
   a.  Logga in på den virtuella datorn för SQL Server.
   
   b.  Hämta datafiler från en Azure Storage-behållare till mappen lokal-VM.
   
   c.  Kör SQL Server Management Studio.
   
   d.  Skapa databas- och måltabeller.
   
   e.  Använd en av massimportmetoderna för att läsa in data.
   
   f.  Om tabellkopplingar krävs skapar du index för att påskynda kopplingar.
   
   > [!NOTE]
   > För snabbare inläsning av stora datastorlekar skapar du partitionerade tabeller och massimporterar data parallellt. Mer information finns i [Parallell dataimport till SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Utforska data, skapa funktioner efter behov. Funktionerna behöver inte materialiseras i databastabellerna. Notera endast den nödvändiga frågan för att skapa dem.
1. Bestäm en dataprovstorlek, om det behövs och/eller önskas.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data.][import-data] Klistra in den nödvändiga frågan som extraherar fält, skapar funktioner och exempeldata om det behövs direkt i frågan [Importera data.][import-data]
1. Enkelt Azure Machine Learning-experimentflöde som börjar med överförd datauppsättning.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativ metod för att kopiera en fullständig databas från en lokal SQL Server till Azure SQL Database
![Koppla från lokal DB och koppla till SQL DB i Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure Virtual Machine (SQL Server / IPython Notebook Server)
Om du vill replikera hela SQL Server-databasen i den virtuella datorn för SQL Server bör du kopiera en databas från en plats/server till en annan, förutsatt att databasen kan tas tillfälligt offline. Du kan använda SQL Server Management Studio Object Explorer eller använda motsvarande Transact-SQL-kommandon.

1. Koppla från databasen på källplatsen. Mer information finns i [Koppla från en databas](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. I fönstret Windows Explorer eller Windows Kommandotolken kopierar du den fristående databasfilen eller filerna och loggar filen eller filerna till målplatsen på DEN virtuella datorn för SQL Server i Azure.
1. Bifoga de kopierade filerna till sql Server-instansen för målet. Mer information finns i [Bifoga en databas](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Flytta en databas med Koppla från och bifoga (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scenario \#7: Stordata i lokala filer, målinsekedatabas i Azure HDInsight Hadoop-kluster
![Stordata i lokala mål Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure HDInsight Hadoop Cluster och Azure Virtual Machine (IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör IPython Notebook-server.
1. Skapa ett Azure HDInsight Hadoop-kluster.
1. (Valfritt) Förbehandla och rensa data.
   
   a.  Förbehandla och rensa data i IPython Notebook, komma åt data från Azure
   
       blobs.
   
   b.  Omvandla data till ett rensat tabellformulär om det behövs.
   
   c.  Spara data till VM-lokala filer (IPython Notebook körs på vm, lokala enheter refererar till VM-enheter).
1. Överför data till standardbehållaren för Hadoop-klustret som valts i steg 2.
1. Läs in data till Hive-databasen i Azure HDInsight Hadoop-klustret.
   
   a.  Logga in på huvudnoden för Hadoop-klustret
   
   b.  Öppna Hadoop-kommandoraden.
   
   c.  Ange Hive-rotkatalogen `cd %hive_home%\bin` efter kommando i Hadoop-kommandoraden.
   
   d.  Kör Hive-frågorna för att skapa databas och tabeller och läs in data från blob-lagring till Hive-tabeller.
   
   > [!NOTE]
   > Om data är stora kan användare skapa hive-tabellen med partitioner. Sedan kan användare `for` använda en loop i Hadoop-kommandoraden på huvudnoden för att läsa in data i Hive-tabellen som partitioneras efter partition.
   > 
   > 
1. Utforska data och skapa funktioner efter behov i Hadoop Command Line. Funktionerna behöver inte materialiseras i databastabellerna. Notera endast den nödvändiga frågan för att skapa dem.
   
   a.  Logga in på huvudnoden för Hadoop-klustret
   
   b.  Öppna Hadoop-kommandoraden.
   
   c.  Ange Hive-rotkatalogen `cd %hive_home%\bin` efter kommando i Hadoop-kommandoraden.
   
   d.  Kör Hive-frågorna i Hadoop-kommandoraden på huvudnoden i Hadoop-klustret för att utforska data och skapa funktioner efter behov.
1. Om det behövs och/eller önskas kan du prova data som får plats i Azure Machine Learning Studio.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt `Hive Queries` från modulen [importera data.][import-data] Klistra in den nödvändiga frågan som extraherar fält, skapar funktioner och exempeldata om det behövs direkt i frågan [Importera data.][import-data]
1. Enkelt Azure Machine Learning-experimentflöde som börjar med överförd datauppsättning.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Beslutsträd för scenarioval
---
I följande diagram sammanfattas de scenarier som beskrivs ovan och de avancerade analysprocess- och teknikval som görs som tar dig till vart och ett av de specificerade scenarierna. Databehandling, utforskning, funktionsteknik och sampling kan ske i en eller flera metod/miljöer – vid käll-, mellan- och/eller målmiljöer – och kan fortsätta iterativt efter behov. Diagrammet fungerar endast som en illustration av några av möjliga flöden och ger inte en uttömmande uppräkning.

![Exempel på DS-processgenomgångsscenarier][8]

### <a name="advanced-analytics-in-action-examples"></a>Exempel på avancerad analys i aktion
Genomgångar av Azure Machine Learning från slutna till slutna Azure Machine Learning som använder avancerad analysprocess och teknik med hjälp av offentliga datauppsättningar finns i:

* [Team Data Science Process i aktion: använda SQL Server](sql-walkthrough.md).
* [Team Data Science Process i aktion: använda HDInsight Hadoop kluster](hive-walkthrough.md).

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
