---
title: Plattformar och verktyg för datavetenskap team projekt - Azure | Microsoft Docs
description: Specificerar samt information om data och analyser resurser att standardisera Team datavetenskap processen för företag.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 404e3dd106edf82f4f22e4c6a17987bd0bc51f65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Verktyg för datavetenskap grupprojekt och plattformar

Microsoft tillhandahåller en fullständig spektrum av data och analytics-tjänster och resurser för både molnet eller lokala plattformar. De kan distribueras för att körningen av dina datavetenskap projekt effektivt och skalbara. Vägledning för team implementera datavetenskap projekt i en trackable version kontrolleras och samarbetsfunktioner sätt tillhandahålls av den [Team datavetenskap Process](overview.md) (TDSP).  En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av en datavetenskap team standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md).

Data och analyser tjänster som är tillgängliga att datavetenskap grupper med hjälp av TDSP omfattar:

- Datavetenskap virtuella datorer (både Windows och Linux-CentOS)
- HDInsight Spark-kluster
- SQL Data Warehouse
- Azure Data Lake
- HDInsight Hive-kluster
- Azure File Storage
- SQL Server 2016 R Services

I det här dokumentet vi kort beskriver resurserna och innehåller länkar till självstudier och genomgång TDSP team har publicerat. De kan hjälpa dig att lära dig hur du använder dem steg för steg och börja använda dem för att skapa intelligent program. Mer information om dessa resurser är tillgänglig på sidorna produkten. 

## <a name="data-science-virtual-machine-dsvm"></a>Datavetenskap virtuell dator (DSVM)

Datavetenskap virtuella datorn finns på både Windows- och Linux av Microsoft, innehåller populära verktyg för datavetenskap modellering aktiviteter och utveckling. Det innehåller verktyg som:

- Microsoft R Server Developer Edition 
- Anaconda Python distribution
- Jupyter-anteckningsböcker för Python och R 
- Visual Studio Community Edition med Python och R verktyg i Windows / Linux-solförmörkelse
- Power BI desktop för Windows
- SQL Server 2016 Developer Edition på Windows / Postgres på Linux

Den omfattar också **ML och AI verktyg** som CNTK (en öppen källa djup Learning toolkit från Microsoft), xgboost, mxnet och Vowpal Wabbit.

DSVM är för närvarande tillgänglig i **Windows** och **CentOS Linux** operativsystem. Välj storleken på din DSVM (antal processorkärnor) och mängden minne baserat på dina behov av datavetenskap projekt som du planerar att köra på den. 

Läs mer på Windows-version av DSVM [Microsoft datavetenskap Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) på Azure marketplace. Linux-versionen av DSVM finns [Linux datavetenskap virtuella](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Information om hur du utför några av de vanliga datavetenskap aktiviteterna på DSVM effektivt finns [tio saker du kan göra på datavetenskap virtuell dator](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark-kluster

Apache Spark är en öppen källkod parallell bearbetning ramverk som stöder minnesintern bearbetning för att höja prestandan hos ett stort program för stordataanalys. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks funktioner för beräkning i minnet gör det ett bra alternativ för iterativa algoritmer i machine learning och diagram beräkningar. Spark är kompatibelt med Azure Blob storage (WASB), så att dina befintliga data som lagras i Azure enkelt kan bearbetas med Spark.

När du skapar ett Spark-kluster i HDInsight skapas Azure-beräkningsresurser med Spark installerat och konfigurerat. Det tar cirka 10 minuter för att skapa ett Spark-kluster i HDInsight. Lagra data som ska bearbetas i Azure Blob storage. Information om hur du använder Azure Blob Storage med ett kluster finns i [använda HDFS-kompatibla Azure Blob storage med Hadoop i HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP team från Microsoft har publicerat två slutpunkt till slutpunkt genomgång om hur du använder Azure HDInsight Spark-kluster för att skapa lösningar för vetenskap, en med hjälp av Python och andra Scala. Mer information om Azure HDInsight **Spark-kluster**, se [översikt: Apache Spark i HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Mer information om hur du skapar en datavetenskap lösning med hjälp av **Python** på ett Azure HDInsight Spark-kluster, se [översikt av datavetenskap med Spark på Azure HDInsight](spark-overview.md). Mer information om hur du skapar en datavetenskap lösning med hjälp av **Scala** på ett Azure HDInsight Spark-kluster, se [datavetenskap med hjälp av Scala och Spark på Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse kan du skala beräkningsresurser enkelt och i sekunder utan överbelasta eller över betalar. Den erbjuder också unika alternativet för att pausa användning av beräkningsresurser, vilket gör att du bättre hantera moln-kostnader. Möjligheten att distribuera skalbara beräkningsresurser gör det möjligt att överföra alla data till Azure SQL Data Warehouse. Lagringskostnader är minimala och du kan köra beräkning endast på delarna av datauppsättningar som du vill analysera. 

Mer information om Azure SQL Data Warehouse finns i [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) webbplats. Information om hur du skapar slutpunkt till slutpunkt avancerade Analyslösningar med SQL Data Warehouse finns [Team datavetenskap Process i praktiken: använda SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure data lake är som en företagsomfattande lagringsplats för varje typ av data som samlas in på en enda plats innan några formella krav eller schemat infördes. Den här flexibiliteten gör det möjligt för varje typ av data som ska behållas i en datasjö, oavsett dess storlek eller struktur eller hur snabbt inhämtas. Organisationer kan sedan använda Hadoop eller avancerade analyser för att hitta mönster i dessa data sjöar. Data sjöar kan också fungera som en lagringsplats för förberedelse av lägre kostnad data innan organisation, data och flytta den till ett datalager.

Mer information om Azure Data Lake finns [introduktion till Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Information om hur du skapar en skalbar slutpunkt till slutpunkt vetenskap lösning med Azure Data Lake finns [skalbara datavetenskap i Azure Data Lake: en genomgång för slutpunkt till slutpunkt](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop)-kluster

Apache Hive är ett data warehouse system för Hadoop som gör att sammanfatta data, frågor och analys av data med hjälp av HiveQL, ett frågespråk som liknar SQL. Hive kan användas för att interaktivt Utforska dina data eller skapa återanvändbara batch bearbetar jobb.

Du kan projektstrukturen på i stort sett Ostrukturerade data i hive. Du kan använda Hive för att fråga data i ett Hadoop-kluster utan att behöva använda eller ens känner, Java eller MapReduce när du har definierat strukturen. HiveQL (Hive query language) kan du skriva frågor med instruktioner som liknar T-SQL.

För data forskare kör Hive Python User-Defined funktioner (UDF) i Hive-frågor för att bearbeta poster. Den här möjligheten utökar möjligheterna för Hive-frågor i dataanalys avsevärt. Kan data forskare att genomföra skalbara funktionen tekniker i de flesta är bekanta med språk: SQL-liknande HiveQL och Python. 

Läs mer på Azure HDInsight Hive-kluster, [använda Hive och HiveQL med Hadoop i HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Information om hur du skapar en skalbar slutpunkt till slutpunkt vetenskap lösning med Azure HDInsight Hive-kluster finns [Team datavetenskap Process i praktiken: med HDInsight Hadoop-kluster](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage är en tjänst som erbjuder filresurser i molnet med hjälp av standardprotokoll för Server Message Block (SMB). Både SMB 2.1 och SMB 3.0 stöds. Med Azure File Storage kan du snabbt och utan kostsamma omskrivningar migrera äldre program som är beroende av filresurser till Azure. Program som körs på virtuella Azure-datorer eller molntjänster eller från lokala klienter kan montera en filresurs i molnet, precis som ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Särskilt användbart för datavetenskap projekt är möjligheten att skapa en Azure-lagring som plats för att dela data från project med gruppmedlemmarna projektet. Var och en av dem sedan har åtkomst till samma kopia av data i Azure file storage. De kan också använda den här fillagring för att dela funktionsuppsättningar som genererats under körningen av projektet. Om projektet är en klient engagement, kan klienterna skapa ett Azure file storage under sin egen Azure-prenumeration dela data från project och funktioner med dig. På så sätt kan har klienten fullständig kontroll över projekt datatillgångar. Mer information om Azure File Storage finns [Kom igång med Azure File storage i Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) och [använda Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R tillhandahåller (i databasen) en plattform för att utveckla och distribuera intelligent program som kan få nya insikter. Du kan använda omfattande och kraftfulla R språk, inklusive många paket som tillhandahålls av R-gemenskapen för att skapa modeller och generera förutsägelser från SQL Server-data. Eftersom R-tjänster (i databasen) integrerar R-språk med SQL Server, hålls analytics nära data, vilket eliminerar kostnader och säkerhetsriskerna med att flytta data.

R-tjänster (i databasen) stöder öppen källkod R språk med en omfattande uppsättning SQL Server-verktyg och tekniker. De erbjuda överlägsen prestanda, säkerhet, tillförlitlighet och hanterbarhet. Du kan distribuera R lösningar med hjälp av praktiskt och välbekanta verktyg. Ditt program i produktion kan anropa R-runtime och hämta förutsägelser och grafer med Transact-SQL. Du kan också använda ScaleR-bibliotek för att förbättra skalning och prestanda för R lösningar. Mer information finns i [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx)

TDSP team från Microsoft har publicerat två slutpunkt till slutpunkt-genomgång som visar hur du skapar vetenskap lösningar i SQL Server 2016 R Services: en för R-programmerare och en för SQL-utvecklare. För **R programmerare**, se [datavetenskap slutpunkt till slutpunkt genomgången](https://msdn.microsoft.com/library/mt612857.aspx). För **SQL utvecklare**, se [i databasen Advanced Analytics för SQL-utvecklare (självstudier)](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>Bilaga: Verktyg för att ställa in datavetenskap projekt

### <a name="install-git-credential-manager-on-windows"></a>Installera Git Autentiseringshanteraren i Windows

Om du följer TDSP **Windows**, måste du installera den **Git autentiseringsuppgifter Manager (GCM)** att kommunicera med Git-databaser. Om du vill installera GCM måste du först installera **Chocolaty**. Om du vill installera Chocolaty och GCM, kör du följande kommandon i Windows PowerShell som en **administratören**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Installera Git på datorer med Linux (CentOS)

Kör följande kommando, bash, för att installera Git på datorer med Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generera offentlig SSH-nyckel på datorer med Linux (CentOS)

Om du använder datorer med Linux (CentOS) för att köra git-kommandon måste du lägga till den offentliga SSH-nyckeln för datorn till VSTS-servern så att den här datorn kan identifieras av VSTS-servern. Först måste du generera en offentlig SSH-nyckel och lägga till för offentliga SSH-nycklar i VSTS säkerhet inställningen sidan. 

- Om du vill generera SSH-nyckeln, kör du följande två kommandon: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Kopiera hela ssh key inklusive *ssh-rsa*. 
- Logga in på servern VSTS. 
- Klicka på **< ditt namn\>**  i det övre högra hörnet av sidan och klicka på **säkerhet**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Klicka på **offentliga SSH-nycklar**, och klicka på **+ Lägg till**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Klistra in den ssh nyckeln precis har kopierats i textrutan och spara.


## <a name="next-steps"></a>Nästa steg

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.