---
title: Plattformar och verktyg för dataforskningsprojekt - Team Data Science Process
description: Specificeras och beskriver de data- och resurserna som är tillgängliga för företag standardiserat Team Data Science Process.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3297319c67ad2b7c94371356cde49113c7ef737
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718896"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plattformar och verktyg för dataforskningsprojekt

Microsoft tillhandahåller ett fullständigt spektrum av analys resurser för både molnbaserade eller lokala plattformar. De kan distribueras för att göra körningen av dina data science-projekt, effektiv och skalbar. Vägledning för team som implementerar data vetenskaps projekt i en spårnings bara, versions kontroll och samarbets väg tillhandahålls av TDSP ( [team data science process](overview.md) ).  En översikt över personal rollerna och deras associerade uppgifter som hanteras av ett data vetenskaps team som är standardiserade för den här processen finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md).

De analys resurser som är tillgängliga för data vetenskaps team som använder TDSP omfattar:

- Virtuella datorer för datavetenskap (både Windows och Linux CentOS)
- HDInsight Spark-kluster
- Synapse Analytics
- Azure Data Lake
- HDInsight Hive-kluster
- Azure File Storage
- SQL Server 2019 R-och python-tjänster
- Azure Databricks

I det här dokumentet har vi kortfattat beskriver resurserna och innehåller länkar till självstudier och genomgångar TDSP-team har publicerat. De kan hjälpa dig att lära dig hur du använder dem steg för steg och börja använda dem för att skapa intelligenta program. Mer information om dessa resurser är tillgängliga på sina produktsidor. 

## <a name="data-science-virtual-machine-dsvm"></a>Virtuell dator för datavetenskap (DSVM)

Den virtuella datorn för datavetenskap erbjuds på både Windows och Linux av Microsoft, som innehåller populära verktyg för data science modellering och utveckling. Det innehåller verktyg som:

- Microsoft R Server Developer Edition 
- Anaconda Python-distribution
- Jupyter-anteckningsböcker för Python- och R 
- Visual Studio Community Edition med Python och R-verktyg i Windows / Eclipse på Linux
- Power BI desktop för Windows
- SQL Server 2016 Developer Edition på Windows / Postgres på Linux

Den innehåller också **ml-och AI-verktyg** som xgboost, Mxnet och Vowpal Wabbit.

För närvarande finns DSVM i operativ systemen **Windows** och **Linux CentOS** . Välj storleken på din DSVM (antal processorkärnor) och mängden minne baserat på dina behov för data science-projekt som du planerar att köra på den. 

Mer information om Windows-versionen av DSVM finns i [Microsoft data science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) på Azure Marketplace. Linux-versionen av DSVM finns i [linux data science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Information om hur du kör några vanliga data vetenskaps uppgifter på DSVM effektivt finns i [10 saker du kan göra på den virtuella datorn för data vetenskap](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-kluster

Apache Spark är en öppen källkod parallell bearbetning av ramverk som stöder intern bearbetning för att höja prestandan hos program för stordataanalys av stordata. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks minnesintern beräkning funktioner blir det ett bra alternativ för iterativa algoritmer i machine learning och för graph-beräkningar. Spark är kompatibelt med Azure Blob storage (WASB), så att dina befintliga data som lagras i Azure enkelt kan bearbetas med Spark.

När du skapar ett Spark-kluster i HDInsight skapas Azure-beräkningsresurser med Spark installerat och konfigurerat. Det tar cirka 10 minuter för att skapa ett Spark-kluster i HDInsight. Store data som ska bearbetas i Azure Blob storage. Information om hur du använder Azure Blob Storage med ett kluster finns i [använda HDFS-kompatibel Azure Blob Storage med Hadoop i HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP-teamet från Microsoft har publicerat två slutpunkt till slutpunkt genomgång om hur du använder Azure HDInsight Spark-kluster för att skapa lösningar för vetenskap, en med Python och andra Scala. Mer information om Azure HDInsight **Spark-kluster**finns i [Översikt: Apache Spark på HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Information om hur du skapar en data vetenskaps lösning med **python** i ett Azure HDInsight Spark kluster finns i [Översikt över data vetenskap med Spark på Azure HDInsight](spark-overview.md). Information om hur du skapar en data vetenskaps lösning med **Scala** på ett Azure HDInsight Spark-kluster finns i [data vetenskap med Scala och Spark på Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse kan du skala beräkningsresurser och enkelt på några sekunder, inte behöver etablera eller betala för mycket. Den erbjuder även den unika möjligheten att pausa användning av beräkningsresurser, vilket ger dig frihet i hanteringen av dina molnkostnader. Möjligheten att distribuera skalbara beräkningsresurser gör det möjligt att ta med alla dina data i Azure SQL Data Warehouse. Kostnader för lagring är minimala och du kan sedan endast delarna av datauppsättningar som du vill analysera. 

Mer information om Azure SQL Data Warehouse finns på [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webbplats. Information om hur du skapar avancerade analys lösningar från slut punkt till slut punkt med SQL Data Warehouse finns [i processen team data science i praktiken: använda SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake är en företagsomfattande lagrings plats för alla typer av data som samlas in på en och samma plats, före eventuella formella krav eller schema som införs. Den här flexibiliteten gör att alla typer av data som ska lagras i en datasjö, oavsett dess storlek eller struktur eller hur snabbt det matas in. Organisationer kan sedan använda Hadoop eller avancerad analys för att hitta mönster i dessa data Lake-sjöar. Data Lake-sjöar kan också fungera som en lagringsplats för förberedelse av data för lägre kostnad innan curating data och flytta den till ett datalager.

Mer information om Azure Data Lake finns i [Introduktion av Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Information om hur du skapar en skalbar data vetenskaps lösning från slut punkt till slut punkt med Azure Data Lake finns i [scalable data science i Azure Data Lake: en slut punkt till slut punkts genom gång](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop)-kluster

Apache Hive är ett data warehouse-system för Hadoop, vilket gör att datasammanfattning, frågor och analys av data med hjälp av HiveQL, ett frågespråk som liknar SQL. Hive kan användas för att interaktivt Utforska dina data eller för att skapa återanvändbara batch bearbetar jobb.

Du kan projektstruktur på i stort sett Ostrukturerade data i hive. När du har definierat strukturen kan du använda Hive för att fråga efter dessa data i ett Hadoop-kluster utan att behöva använda, eller ens veta Java- eller MapReduce. HiveQL (frågespråk för Hive) kan du skriva frågor med instruktioner som liknar T-SQL.

För dataexperter kör Hive Python User-Defined funktioner (UDF) i Hive-frågor för att bearbeta poster. Den här möjligheten utökar möjligheterna för Hive-frågor i dataanalys avsevärt. Kan dataforskare att utföra skalbar funktionsframställning på språk som de huvudsakligen är vana vid: SQL-liknande HiveQL och Python. 

Mer information om Azure HDInsight Hive-kluster finns i [använda Hive och HiveQL med Hadoop i HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Information om hur du skapar en skalbar lösning för data vetenskap från slut punkt till slut punkt med Azure HDInsight Hive-kluster finns [i processen team data science i praktiken: använda HDInsight Hadoop-kluster](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage är en tjänst som erbjuder filresurser i molnet med standardprotokoll för Server Message Block (SMB). Både SMB 2.1 och SMB 3.0 stöds. Med Azure File Storage kan du snabbt och utan kostsamma omskrivningar migrera äldre program som är beroende av filresurser till Azure. Program som körs på virtuella Azure-datorer eller molntjänster eller från lokala klienter kan montera en filresurs i molnet, precis som ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Särskilt användbart för dataforskningsprojekt är möjligheten att skapa en Azure-lagring som plats för att dela projektdata med dina gruppmedlemmar för projektet. Var och en av dem sedan har åtkomst till samma kopia av data i Azure file storage. De kan också använda den här fillagring för att dela funktionsuppsättningar som genereras under körningen av projektet. Om projektet är en kontakt i klienten, kan klienterna skapa en Azure file storage sin egen Azure-prenumeration att dela projektdata och funktioner med dig. På så sätt kan har klienten fullständig kontroll över dataresurser som projektet. Mer information om Azure File Storage finns i [komma igång med Azure File Storage i Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) och [hur du använder Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R-och python-tjänster

R Services (in-Database) tillhandahåller en plattform för utveckling och distribution av intelligenta program som kan få nya insikter. Du kan använda omfattande och kraftfulla R-språket, inklusive de många paket som tillhandahålls av R-communityn för att skapa modeller och genererar förutsägelser från dina SQL Server-data. Eftersom R-tjänster (i databasen) integrerar R-språket med SQL Server, kommer Analytics att stå nära data, vilket eliminerar de kostnader och säkerhets risker som är kopplade till att flytta data.

R Services (in-Database) stöder R-språket med öppen källkod med en omfattande uppsättning SQL Server verktyg och tekniker. De ger överlägsna prestanda, säkerhet, tillförlitlighet och hanterbarhet. Du kan distribuera R-lösningar med hjälp av enkla och bekanta verktyg. Dina produktionsprogram kan anropa körningsmiljön för r. och hämta förutsägelser och visuella objekt med hjälp av Transact-SQL. Du kan också använda ScaleR-bibliotek för att förbättra skalbarhet och prestanda för dina R-lösningar. Mer information finns i [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

TDSP-teamet från Microsoft har publicerat två slutpunkt till slutpunkt genomgång som visar hur du skapar datavetenskapliga lösningar i SQL Server 2016 R Services: en för R-programmerare och en för SQL-utvecklare. För **R-programmerare**, se [genom gång av data vetenskap från slut punkt till slut punkt](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). För **SQL-utvecklare**, se [Avancerad analys i databasen för SQL-utvecklare (självstudie)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Bilaga: verktyg för att konfigurera data vetenskaps projekt

### <a name="install-git-credential-manager-on-windows"></a>Installera Git Credential Manager på Windows

Om du följer TDSP i **Windows**måste du installera **git CREDENTIAL Manager (GCM)** för att kommunicera med git-databaserna. Om du vill installera GCM måste du först installera **Chocolaty**. Om du vill installera Chocolaty och GCM kör du följande kommandon i Windows PowerShell som **administratör**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Installera Git på datorer för Linux (CentOS)

Kör följande bash-kommando för att installera Git på datorer för Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generera offentlig SSH-nyckel på datorer för Linux (CentOS)

Om du använder Linux (CentOS) datorer för att köra git-kommandon måste du lägga till den offentliga SSH-nyckeln för din dator till din Azure DevOps-tjänster, så att den här datorn kan identifieras av Azure DevOps-tjänsterna. Först måste du generera en offentlig SSH-nyckel och lägga till nyckeln i offentliga SSH-nycklar i inställningen för Azure DevOps-tjänsterna säkerhetssidan. 

1. Om du vill skapa SSH-nyckeln, kör du följande två kommandon: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Kommandon för att skapa SSH-nyckeln](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopiera hela SSH-nyckeln inklusive *SSH-RSA*. 
1. Logga in på Azure DevOps-tjänster. 
1. Klicka på **< ditt namn\>** i det övre högra hörnet på sidan och klicka på **säkerhet**. 
    
   ![Klicka på ditt namn och klicka sedan på säkerhet](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klicka på **offentliga SSH-nycklar**och klicka på **+ Lägg till**. 

   ![Klicka på offentliga SSH-nycklar och sedan klicka på + Lägg till](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Klistra in SSH-nyckeln som kopierats i text rutan och spara.


## <a name="next-steps"></a>Nästa steg

Fullständiga genom gångar från slut punkt till slut punkt som visar alla steg i processen för **särskilda scenarier** tillhandahålls också. De visas och länkas med miniatyr beskrivningar i [exempel avsnittet genom gångar](walkthroughs.md) . De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel som visar hur du utför stegen i team data science-processen genom att använda Azure Machine Learning Studio (klassisk) finns i [Azure ml](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) Learning-sökvägen.
