---
title: Plattformar och verktyg för datavetenskapsprojekt - Team Data Science Process
description: Specificerar och diskuterar de data- och analysresurser som är tillgängliga för företag som standardiserar i teamdatavetenskapsprocessen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251614"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plattformar och verktyg för datavetenskapliga projekt

Microsoft tillhandahåller ett komplett spektrum av analysresurser för både molnplattformar eller lokala plattformar. De kan distribueras för att göra genomförandet av dina datavetenskapsprojekt effektiva och skalbara. Vägledning för team som implementerar datavetenskapliga projekt på ett spårbart, versionskontrollerat och samarbetsinriktat sätt tillhandahålls av [Team Data Science Process](overview.md) (TDSP).  En översikt över de personalroller och deras associerade uppgifter som hanteras av ett data science-team som standardiserar den här processen finns i [Roller och uppgifter för teamdatavetenskap.](roles-tasks.md)

De analysresurser som är tillgängliga för datavetenskapslag med hjälp av TDSP är:

- Virtuella datavetenskapsmaskiner (både Windows och Linux CentOS)
- HDInsight Spark-kluster
- Synapse Analytics
- Azure Data Lake
- HDInsight Hive-kluster
- Azure File Storage
- SQL Server 2019 R- och Python-tjänster
- Azure Databricks

I det här dokumentet beskriver vi kortfattat resurserna och tillhandahåller länkar till självstudier och genomgångar som TDSP-teamen har publicerat. De kan hjälpa dig att lära dig hur du använder dem steg för steg och börja använda dem för att bygga dina intelligenta program. Mer information om dessa resurser finns på deras produktsidor. 

## <a name="data-science-virtual-machine-dsvm"></a>Virtuell dator för datavetenskap (DSVM)

Den virtuella datavetenskapsdatorn som erbjuds på både Windows och Linux av Microsoft innehåller populära verktyg för modellering och utveckling av datavetenskap. Den innehåller verktyg som:

- Microsoft R Server Developer Edition 
- Anaconda Python-distribution
- Jupyter bärbara datorer för Python och R 
- Visual Studio Community Edition med Python och R-verktyg på Windows / Eclipse på Linux
- Power BI-skrivbordet för Windows
- SQL Server 2016 Developer Edition på Windows / Postgres på Linux

Den innehåller också **ML och AI-verktyg** som xgboost, mxnet och Vowpal Wabbit.

För närvarande är DSVM tillgängligt i **Windows-** och **Linux CentOS-operativsystem.** Välj storleken på din DSVM (antal CPU-kärnor och mängden minne) baserat på behoven hos de datavetenskapsprojekt som du planerar att köra på den. 

Mer information om Windows-utgåvan av DSVM finns i [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) på Azure Marketplace. För Linux-utgåvan av DSVM, se [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Mer information om hur du utför några av de vanliga datavetenskapsuppgifterna på DSVM effektivt finns i [10 saker du kan göra på Virtual Machine för datavetenskap](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-kluster

Apache Spark är ett ramverk för parallellbearbetning med öppen källkod som stöder intern bearbetning för att höja prestandan hos program för stordataanalys. Spark-bearbetningsmotorn är byggd för hastighet, användarvänlighet och sofistikerad analys. Sparks beräkningskapacitet i minnet gör det till ett bra val för iterativa algoritmer inom maskininlärning och för grafberäkningar. Spark är också kompatibel med Azure Blob storage (WASB), så dina befintliga data som lagras i Azure kan enkelt bearbetas med Spark.

När du skapar ett Spark-kluster i HDInsight skapas Azure-beräkningsresurser med Spark installerat och konfigurerat. Det tar cirka 10 minuter att skapa ett Spark-kluster i HDInsight. Lagra data som ska bearbetas i Azure Blob-lagring. Information om hur du använder Azure Blob Storage med ett kluster finns i [Använda HDFS-kompatibel Azure Blob-lagring med Hadoop i HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP-teamet från Microsoft har publicerat två heltäckande genomgångar om hur du använder Azure HDInsight Spark Clusters för att skapa datavetenskapslösningar, en med Python och den andra Scala. Mer information om Azure HDInsight **Spark-kluster**finns i [Översikt: Apache Spark på HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Mer information om hur du skapar en data science-lösning med **Python** i ett Azure HDInsight Spark-kluster finns i [Översikt över datavetenskap med Spark på Azure HDInsight](spark-overview.md). Mer information om hur du skapar en data science-lösning med **Scala** i ett Azure HDInsight Spark-kluster finns i [Data science med Scala och Spark på Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Med Azure SQL Data Warehouse kan du skala beräkningsresurser enkelt och på några sekunder, utan överetablering eller överbetalande. Det erbjuder också det unika alternativet för att pausa användningen av beräkningsresurser, vilket ger dig friheten att bättre hantera dina molnkostnader. Möjligheten att distribuera skalbara beräkningsresurser gör det möjligt att föra in alla dina data i Azure SQL Data Warehouse. Lagringskostnaderna är minimala och du kan bara köra beräkning på de delar av datauppsättningar som du vill analysera. 

Mer information om Azure SQL Data Warehouse finns på [WEBBPLATSEN FÖR SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse) Mer information om hur du skapar avancerade analyslösningar från på nytt med SQL Data Warehouse [finns i Åtgärden Team Data Science Process: använda SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake är som en företagsomfattande databas över alla typer av data som samlas in på en enda plats, innan formella krav eller schema införs. Denna flexibilitet gör att alla typer av data kan lagras i en datasjö, oavsett storlek eller struktur eller hur snabbt den förtärs. Organisationer kan sedan använda Hadoop eller avancerad analys för att hitta mönster i dessa datasjöar. Datasjöar kan också fungera som en lagringsplats för förberedelse av billigare data innan data curerades och flyttar dem till ett informationslager.

Mer information om Azure Data Lake finns i [Introduktion till Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Mer information om hur du skapar en skalbar heltäckande data science-lösning med Azure Data Lake finns [i Skalbar datavetenskap i Azure Data Lake: En genomgång från slutna till slutna dagar](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive-kluster (Hadoop)

Apache Hive är ett informationslagersystem för Hadoop, som möjliggör datasumrisering, frågor och analys av data med HiveQL, ett frågespråk som liknar SQL. Hive kan användas för att interaktivt utforska dina data eller för att skapa återanvändbara batchbearbetningsjobb.

Hive kan du projektstruktur på i stort sett ostrukturerade data. När du har definierat strukturen kan du använda Hive för att fråga dessa data i ett Hadoop-kluster utan att behöva använda, eller ens veta, Java eller MapReduce. HiveQL (Hive-frågespråket) gör att du kan skriva frågor med satser som liknar T-SQL.

För dataexperter kan Hive köra uddf-funktioner (Python User-Defined Functions) i Hive-frågor för att bearbeta poster. Den här möjligheten utökar möjligheten för Hive-frågor i dataanalys avsevärt. Specifikt gör det möjligt för dataforskare att utföra skalbar funktionsteknik på språk som de oftast är bekanta med: SQL-liknande HiveQL och Python. 

Mer information om Azure HDInsight Hive-kluster finns i [Använda Hive och HiveQL med Hadoop i HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Mer information om hur du skapar en skalbar heltäckande datavetenskapslösning med Azure HDInsight Hive-kluster finns [i Åtgärden Team Data Science Process: använda HDInsight Hadoop-kluster](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage är en tjänst som erbjuder filresurser i molnet med hjälp av standardfilmeddelandeblock (SMB) protokoll. Både SMB 2.1 och SMB 3.0 stöds. Med Azure File Storage kan du snabbt och utan kostsamma omskrivningar migrera äldre program som är beroende av filresurser till Azure. Program som körs på virtuella Azure-datorer eller molntjänster eller från lokala klienter kan montera en filresurs i molnet, precis som ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Särskilt användbart för datascience-projekt är möjligheten att skapa ett Azure-filarkiv som plats för att dela projektdata med dina projektgruppsmedlemmar. Var och en av dem har sedan åtkomst till samma kopia av data i Azure-fillagringen. De kan också använda den här fillagringen för att dela funktionsuppsättningar som genereras under projektets genomförande. Om projektet är en klientåtagande kan dina klienter skapa en Azure-fillagring under sin egen Azure-prenumeration för att dela projektdata och funktioner med dig. På så sätt har klienten full kontroll över projektdatatillgångarna. Mer information om Azure File Storage finns i [Komma igång med Azure File Storage på Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) och Hur du använder Azure File Storage med [Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R- och Python-tjänster

R Services (In-database) tillhandahåller en plattform för att utveckla och distribuera intelligenta program som kan upptäcka nya insikter. Du kan använda det rika och kraftfulla R-språket, inklusive de många paket som tillhandahålls av R-communityn, för att skapa modeller och generera förutsägelser från dina SQL Server-data. Eftersom R Services (I-databasen) integrerar R-språket med SQL Server hålls analyser nära data, vilket eliminerar kostnader och säkerhetsrisker i samband med rörliga data.

R Services (In-database) stöder R-språket med öppen källkod med en omfattande uppsättning SQL Server-verktyg och -tekniker. De erbjuder överlägsen prestanda, säkerhet, tillförlitlighet och hanterbarhet. Du kan distribuera R-lösningar med hjälp av praktiska och välbekanta verktyg. Dina produktionsprogram kan anropa R-körningen och hämta förutsägelser och visuella objekt med Transact-SQL. Du kan också använda ScaleR-biblioteken för att förbättra omfattningen och prestandan för dina R-lösningar. Mer information finns i [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

TDSP-teamet från Microsoft har publicerat två heltäckande genomgångar som visar hur du skapar datavetenskapslösningar i SQL Server 2016 R Services: en för R-programmerare och en för SQL-utvecklare. För **R-programmerare**finns i [Data Science End-to-End Genomgång](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). För **SQL-utvecklare**finns [i Avancerad analys i databasen för SQL-utvecklare (självstudiekurs).](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Bilaga: Verktyg för att inrätta datavetenskapliga projekt

### <a name="install-git-credential-manager-on-windows"></a>Installera Git-autentiseringshanteraren i Windows

Om du följer TDSP i **Windows**måste du installera **GCM (Git-autentiseringshanteraren)** för att kunna kommunicera med Git-databaserna. För att installera GCM måste du först installera **Chocolaty**. Om du vill installera Chocolaty och GCM kör du följande kommandon i Windows PowerShell som **administratör:**  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Installera Git på Linux-maskiner (CentOS)

Kör följande bash-kommando för att installera Git på Linux-datorer (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generera offentliga SSH-nyckel på Linux-maskiner (CentOS)

Om du använder Linux-datorer (CentOS) för att köra git-kommandona måste du lägga till den offentliga SSH-nyckeln för din dator i dina Azure DevOps-tjänster, så att den här datorn känns igen av Azure DevOps Services. Först måste du generera en offentlig SSH-nyckel och lägga till nyckeln till SSH-offentliga nycklar på säkerhetsinställningssidan för Azure DevOps Services. 

1. Om du vill generera SSH-tangenten kör du följande två kommandon: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Kommandon för att generera SSH-tangenten](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopiera hela ssh-tangenten inklusive *ssh-rsa*. 
1. Logga in på dina Azure DevOps-tjänster. 
1. Klicka **<Ditt namn\> ** längst upp till höger på sidan och klicka på **säkerhet**. 
    
   ![Klicka på ditt namn och klicka sedan på säkerhet](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klicka på **SSH-allmänna nycklar**och klicka på **+Lägg till**. 

   ![Klicka på SSH-offentliga nycklar och klicka sedan på +Lägg till](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Klistra in ssh-tangenten som kopieras till textrutan och spara.


## <a name="next-steps"></a>Nästa steg

Fullständiga genomgångar från slutpunkt till slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och länkas med miniatyrbeskrivningar i avsnittet [Exempelgenomgångar.](walkthroughs.md) De illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 

Exempel som visar hur du kör steg i Team Data Science Process med hjälp av Azure Machine Learning Studio (klassiskt) finns i utbildningsväg [med Azure ML.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
