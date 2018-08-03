---
title: Etablera Windows Data Science-dator på Azure | Microsoft Docs
description: Konfigurera och skapa en virtuell dator för datavetenskap på Azure för analys och maskininlärning.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: b749d8a904bc40eba3346cc03d9274236380c80d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449759"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Etablera Windows Data Science-dator på Azure
Microsoft Data Science Virtual Machine är en Windows Azure-dator (VM) avbildning som redan är installerat och konfigurerat med flera populära verktyg som vanligen används för dataanalys och maskininlärning. Verktygen finns:

* [Azure Machine Learning](../service/index.yml) Workbench
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python-distribution
* Jupyter-anteckningsbok (med R, Python, PySpark-kärnor)
* Visual Studio Community Edition
* Power BI desktop
* SQL Server 2017 Developer Edition
* Standalone Spark-instans för lokal utveckling och testning
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning och Data Analytics-verktyg
  * Deep Learning-ramverk: ett stort utbud av AI-ramverk, däribland [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras ingår i den virtuella datorn.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): snabb machine learning-system som har stöd för tekniker som online, hash, allreduce, sänkt, learning2search, aktiv, och interaktiva utbildningar.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): ett verktyg för att tillhandahålla snabba och exakta bättre trädet implementering.
  * [Rattle](http://rattle.togaware.com/) (den R analytiska Verktyg att lära dig enkelt): ett verktyg som gör att komma igång med dataanalys och maskininlärning i R enkelt. Det omfattar GUI-baserade datagranskning och modellering med automatisk generering av klientkod R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : en visual datautvinning och machine learning-programvara i Java.
  * [Apache Drill](https://drill.apache.org/): en schemafria SQL-frågemotor för Hadoop-, NoSQL- och Molnlagring.  Har stöd för ODBC- och JDBC-gränssnitt för att aktivera förfrågningar till NoSQL- och filer från standard BI-verktyg som Power BI, Excel, bland annat Tableau.
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster
* Git inklusive Git Bash att arbeta med lagringsutrymmen för källkod inklusive GitHub, Visual Studio Team Services och ger flera populära Linux-kommandoradsverktyg (inklusive awk, sed, perl, grep, hitta, wget, curl, etc.) tillgänglig både på git bash och kommandot fråga. 

Datavetenskap omfattar iteration av en serie uppgifter:

1. Sökning, läser in och bearbeta data data i förväg
1. Skapa och testa modeller
1. Distribuera modeller för användning i intelligenta program

Dataexperter använda en mängd olika verktyg för att utföra dessa uppgifter. Det kan vara ganska tidskrävande att hitta rätt versioner av programvaran och sedan ladda ned och installera den. Microsoft Data Science Virtual Machine kan minska det här problemet genom att tillhandahålla en färdiga att använda-avbildning som kan etableras på Azure med alla flera populära verktyg förinstallerat och konfigurerats. 

Microsoft Data Science Virtual Machine jump-starts ditt analysprojekt. Det kan du arbeta på uppgifter på olika språk, inklusive R, Python, SQL och C#. Visual Studio har en IDE för att utveckla och testa din kod som är lätt att använda. Azure-SDK som ingår i den virtuella datorn kan du skapa program med olika tjänster på Microsofts molnplattform. 

Det finns inga programvaruavgifter för den här data science VM-avbildning. Du betalar bara för användning av Azure-avgifter vilka beroende på storleken på den virtuella datorn som du etablerar. Mer information om beräkning avgifter kan hittas i informationsavsnittet priser på den [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) sidan. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Andra versioner av den virtuella datorn för datavetenskap
En [Ubuntu](dsvm-ubuntu-intro.md) bilden är tillgänglig, med många liknande verktyg plus några ytterligare deep learning-ramverk. En [CentOS](linux-dsvm-intro.md) bilden är också tillgänglig. Vi erbjuder också en [Windows Server 2012-versionen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) av den virtuella datorn för datavetenskap dock några verktyg är bara tillgängliga på Windows Server 2016-versionen.  Annars kan gäller den här artikeln även för Windows Server 2012-versionen.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en Microsoft Data Science Virtual Machine, måste du ha följande:

* **En Azure-prenumeration**: Om du vill skaffa en Se [skaffa Azure kostnadsfria utvärderingsversionen](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Skapa din Microsoft Data Science-dator
Följ dessa steg om du vill skapa en instans av Microsoft Data Science Virtual Machine:

1. Navigera till den virtuella datorn på [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
1. Välj den **skapa** längst ned för att gå till en guide.![ Konfigurera –--virtuell dator för datavetenskap](./media/provision-vm/configure-data-science-virtual-machine.png)
1. I guiden som används för att skapa Microsoft Data Science Virtual Machine kräver **indata** för var och en av de **fyra steg** räknas upp till höger i den här bilden. Här följer de indata som behövs för att konfigurera var och en av de här stegen:
   
   1. **Grundläggande inställningar**
      
      1. **Namn på**: namnet på din data science-server som du skapar.
      1. **Typ av virtuell Datordisk**: välja mellan SSD och HDD. För NC_v1 GPU instans (NVidia Tesla K80 baserat), Välj **HDD** som typ av disk. 
      1. **Användarnamnet**: inloggnings-id för Admin-konto.
      1. **Lösenord**: lösenord för administratörskonto.
      1. **Prenumeration**: Om du har mer än en prenumeration kan du välja den som som datorn är skapas och faktureras.
      1. **Resursgrupp**: du kan skapa en ny eller Använd en befintlig grupp.
      1. **Plats**: Välj det datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data eller som är närmast dina fysiska platsen för snabbaste nätverksåtkomst.
   1. **Storlek**: Välj något av de servertyper som uppfyller dina funktionella krav och begränsningar för kostnad. Du kan få fler alternativ med VM-storlekar genom att välja ”Visa alla”.
   1. **Inställningar för**:
      
      1. **Använda Managed Disks**: Välj hanterade om du vill att Azure för att hantera diskar för den virtuella datorn.  Annars måste du ange ett nytt eller befintligt lagringskonto. 
      1. **Andra parametrar**: Använd vanligtvis bara standardvärdena. Om du vill att överväga användning av icke-standardvärden, hovrar du över informationsmeddelande länken Hjälp om specifika fält.
    a. **Sammanfattning**: Kontrollera att all information du angett är korrekt och klicka på **skapa**. **Obs**: den virtuella datorn har inte några ytterligare avgifter utöver beräkning för serverstorlek som du valde i den **storlek** steg. 

> [!NOTE]
> Etableringen tar cirka 10-20 minuter. Status för etableringen visas på Azure portal.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Hur du kommer åt Microsoft Data Science Virtual Machine
När den virtuella datorn har skapats kan du fjärrskrivbord till den med hjälp av autentiseringsuppgifter som administratör-konto som du konfigurerade i föregående **grunderna** avsnittet. 

När den virtuella datorn skapas och etablerats, är du redo att börja använda de verktyg som är installerade och konfigurerade på den. Det finns paneler för start-menyn och ikoner på skrivbordet för många av verktygen. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Verktygen som installeras på Microsoft Data Science Virtual Machine



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Om du vill använda Microsoft enterprise-bibliotek för skalbar R eller Python för analys, har den virtuella datorn Microsoft ML Server Developer edition (kallades tidigare Microsoft R Server) installerat. Microsoft ML Server är en analysplattform för brett distribuerbar företagsklass för både R och Python och är skalbar, kommersiellt stöds och säker. Stöd för olika stordatastatistik, förutsägande modellering och funktioner för maskininlärning, ML Server har stöd för en fullständig uppsättning analytics – utforskning, analys, visualisering och modellering. Genom att använda och utökar R med öppen källkod och Python, Microsoft ML Server är helt kompatibel med R / Python-skript, funktioner och CRAN / pip / Conda paket att analysera data på enterprise-skala. Det tar också ITU minnesbegränsningarna för Open Source R genom att lägga till parallella program och segmenterat bearbetning av data. Detta gör att du kan köra analyser på data som är mycket större än vad som passar i huvudminnet.  Visual Studio Community Edition ingår på den virtuella datorn innehåller R Tools för Visual Studio och Python tools för Visual Studio-tillägg som ger en fullständig IDE för att arbeta med R eller Python. Erbjuder vi även andra IDE: er samt som [RStudio](http://www.rstudio.com) och [PyCharm Community edition](https://www.jetbrains.com/pycharm/) på den virtuella datorn. 

### <a name="python"></a>Python
För utveckling med hjälp av Python har 2.7 och 3.6 Anaconda Python-distribution installerats. Den här distributionen innehåller grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data. Du kan använda Python Tools för Visual Studio (PTVS) som är installerad i Visual Studio 2017 Community edition eller någon av de IDE: er levereras tillsammans med Anaconda som INAKTIV eller Spyder. Du kan starta en av dessa genom att söka i sökfältet (**vinna** + **S** nyckel).

> [!NOTE]
> Om du vill peka Python-verktygen för Visual Studio på Anaconda Python 2.7, måste du skapa anpassade miljöer för varje version. Ange sökvägarna miljö i Visual Studio 2017 Community Edition och navigera till **verktyg** -> **Python Tools** -> **Python-miljöer**och klicka sedan på **+ anpassad**. 
> 
> 

Anaconda Python 3.6 installeras under C:\Anaconda och Anaconda Python 2.7 är installerat under c:\Anaconda\envs\python2. Se [dokumentationen till PTVS](/visualstudio/python/installing-python-interpreters.md) detaljerade anvisningar. 

### <a name="jupyter-notebook"></a>Jupyter Notebook
Anaconda distribution levereras också med en Jupyter-anteckningsbok, en miljö för att dela kod och analys. En Jupyter notebook-server har förkonfigurerats med Python 2.7, Python 3.x, PySpark, Julia och R-kärnor. Det finns en skrivbordsikonen med namnet ”Jupyter Notebook” för att starta en Jupyter-server och starta om webbläsaren för att komma åt Notebook-server. 

Vi har förpackat flera exempelanteckningsböcker i Python och R. Jupyter notebooks visar hur du arbetar med Microsoft ML Server, SQL Server ML Services (databasintern analys), Python, Microsoft Cognitive ToolKit, Tensorflow och andra Azure-tekniker när du har åtkomst till Jupyter. Du kan se länken till exemplen på startsidan för bärbar dator när du autentiserar till Jupyter-anteckningsboken med hjälp av lösenordet som du skapade i ett tidigare steg. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community edition
Visual Studio Community edition är installerade på den virtuella datorn. Det är en kostnadsfri version av populära IDE från Microsoft som du kan använda i utvärderingssyfte med små team. Du kan ta en titt licensvillkoren [här](https://www.visualstudio.com/support/legal/mt171547).  Öppna Visual Studio genom att dubbelklicka på skrivbordsikonen eller **starta** menyn. Du kan också söka efter program med **vinna** + **S** och ange ”Visual Studio”. När det kan du skapa projekt på språk som C#, Python, R, node.js. Plugin-program installeras också som gör det enkelt att arbeta med Azure-tjänster som Azure Data Catalog, Azure HDInsight (Hadoop, Spark) och Azure Data Lake. Nu finns även ett plugin-program som kallas ```Visual Studio Tools for AI``` som smidigt kan integreras till Azure Machine Learning och hjälper dig att snabbt skapa AI-program. 

> [!NOTE]
> Du kan få ett meddelande om att din utvärderingsperiod har gått ut. Ange autentiseringsuppgifterna för ditt Microsoft-konto eller skapa ett nytt kostnadsfritt konto för att få åtkomst till Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
En utvecklare version av SQL Server 2017 med ML-tjänsterna och köra analyser i databasen finns på den virtuella datorn i R eller Python. ML-tjänster tillhandahåller en plattform för utveckling och distribution intelligenta program. Du kan använda omfattande och kraftfulla dessa språk och många paket från communityn att skapa modeller och genererar förutsägelser för SQL Server-data. Du kan behålla analytics nära data eftersom ML Services (databasintern) integrerar både R och Python språk i SQL Server. Detta eliminerar kostnaderna och säkerhetsrisker som förknippas med dataförflyttning.

> [!NOTE]
> SQL Server developer-utgåvan kan endast användas för utveckling och testning. Du behöver en licens för att köra den i produktion. 
> 
> 

Du kan komma åt SQLServer genom att starta **SQL Server Management Studio**. Namnet på din VM fylls som servernamn. Använd Windows-autentisering när du loggat in som administratör på Windows. När du är i SQL Server Management Studio kan du skapa andra användare, skapa databaser, importera data och köra SQL-frågor. 

Kör följande kommando för att aktivera databasanalys med SQL ML-tjänster, som en engångsåtgärd i SQL Server management studio när du loggar in som serveradministratör. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Flera Azure-verktygen är installerade på den virtuella datorn:

* Det finns en genväg på skrivbordet till i Azure SDK-dokumentationen. 
* **AzCopy**: används för att flytta data till och från din Microsoft Azure Storage-konto. Om du vill se användning, Skriv **Azcopy** i Kommandotolken för att se användningen. 
* **Microsoft Azure Storage Explorer**: används för att bläddra igenom objekten som du har lagrat i ditt Azure Storage-konto och överför data till och från Azure storage. Du kan skriva **Lagringsutforskaren** i Sök eller hitta på Windows Start-menyn för att få åtkomst till det här verktyget. 
* **Adlcopy**: används för att flytta data till Azure Data Lake. Om du vill se användning, Skriv **adlcopy** i en kommandotolk. 
* **dtui**: används för att flytta data till och från Azure Cosmos DB, en NoSQL-databas i molnet. Typ **dtui** på Kommandotolken. 
* **Azure Data Factory Integration Runtime**: aktiverar dataförflyttning mellan molnet och lokala datakällor. Den används i verktyg som Azure Data Factory. 
* **Microsoft Azure Powershell**: ett verktyg som används för att administrera dina Azure-resurser i Powershell skriptspråk som också är installerad på den virtuella datorn. 

### <a name="power-bi"></a>Power BI
Att hjälpa dig att skapa instrumentpaneler och fantastiska visualiseringar i **Power BI Desktop** har installerats. Använda verktyget för att hämta data från olika källor, skapa dina instrumentpaneler och rapporter och publicera dem till molnet. Mer information finns i den [Power BI](http://powerbi.microsoft.com) plats. Du hittar Power BI desktop på Start-menyn. 

> [!NOTE]
> Du behöver en Office 365-konto till Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench är ett skrivbordsprogram och kommandoradsgränssnitt. Workbench har inbyggda funktioner för dataförberedelse som lär sig dina som du utför dem. Det ger också projekthantering, körningshistorik och integrering av anteckningsböcker du stärker din produktivitet. Du kan dra nytta av de bästa ramverken med öppen källkod, inklusive TensorFlow, Cognitive Toolkit, Spark ML och scikit-Lär dig att utveckla dina modeller. På DSVM tillhandahåller vi en skrivbordsikonen för att installera Azure Machine Learning workbench till katalogen för den enskilda användaren % LOCALAPPDATA %. Varje användare som behöver använda arbetsstationen måste göra en engångsåtgärd för Dubbelklicka på den ```AzureML Workbench Setup``` skrivbordsikonen för att installera sin instans av Workbench. Azure Machine Learning kan du även skapar och använder en per användare Python-miljö som ska extraheras i % LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Ytterligare Microsoft-utvecklingsverktyg
Den [ **Microsoft Web Platform Installer** ](https://www.microsoft.com/web/downloads/platform.aspx) kan användas för att identifiera och hämta andra Microsoft-utvecklingsverktyg. Det finns också en genväg till de verktyg som finns på Microsoft Data Science Virtual Machine-skrivbordet.  

## <a name="important-directories-on-the-vm"></a>Viktiga kataloger på den virtuella datorn
| Objekt | Katalog |
| --- | --- |
| Jupyter notebook server-konfigurationer |C:\ProgramData\jupyter |
| Arbetskatalog för Jupyter Notebook-exempel |c:\dsvm\notebooks och c:\users\<användarnamn > \notebooks|
| Andra exempel |c:\dsvm\samples |
| Anaconda (standard: Python 3.6) |c:\Anaconda |
| Anaconda Python 2.7 miljö |c:\Anaconda\envs\python2 |
| Microsoft ML Server fristående Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| R standardinstansen (ML Server fristående) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL ML Services i databasen instans-katalog |C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Azure Machine Learning Workbench (per användare) | %localappdata%\amlworkbench | 
| Diverse verktyg |c:\dsvm\tools |

> [!NOTE]
> På Windows Server 2012-versionen av DSVM och Windows Server 2016-versionen före mars 2018 är standardmiljön för Anaconda Python 2.7. Den sekundära miljön är Python 3.5 på c:\Anaconda\envs\py35. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Här följer några nästa steg för att fortsätta din inlärning och undersökning. 

* Utforska de olika verktyg för datavetenskap på den virtuella datorn för datavetenskap genom att klicka på start-menyn och checka ut de verktyg som visas på menyn.
* Läs mer om Azure Machine Learning Services och Workbench genom att gå till produkten [sidan Snabbstart och självstudier](../service/index.yml). 
* Gå till **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** för exempel med hjälp av RevoScaleR-biblioteket i R som har stöd för dataanalys på företagsnivå.  
* Läs artikeln: [10 saker som du kan göra med datavetenskap, virtuell dator](http://aka.ms/dsvmtenthings)
* Lär dig att skapa heltäckande Analyslösningar systematiskt med hjälp av den [Team Data Science Process](../team-data-science-process/index.yml).
* Gå till den [Azure AI-galleriet](http://gallery.cortanaintelligence.com) för machine learning och data analytics-exempel som använder Azure Machine learning och relaterade data services på Azure. Vi har också lagt en ikon på den **starta** menyn och på skrivbordet för den virtuella datorn till det här galleriet.

