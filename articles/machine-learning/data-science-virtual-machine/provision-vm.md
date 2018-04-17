---
title: Etablera Windows datavetenskap virtuell dator i Azure | Microsoft Docs
description: Konfigurera och skapa en datavetenskap virtuell dator i Azure för analys och maskininlärning.
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
ms.openlocfilehash: 34bdbf6e98d5232bf924f3f32a2acb3460a7737f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Etablera Windows datavetenskap virtuell dator på Azure
Microsoft datavetenskap virtuella datorn är en avbildning på virtuell dator (VM) för Windows Azure före installeras och konfigureras med flera populära verktyg som används för dataanalys och maskininlärning. Verktygen är:

* [Azure Machine Learning](../preview/index.yml) arbetsstationen
* [Microsoft Server för Maskininlärning](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python-distribution
* Jupyter-anteckningsbok (med R, Python, PySpark kärnor)
* Visual Studio Community Edition
* Power BI desktop
* SQL Server 2017 Developer Edition
* Standalone Spark-instans för lokal utveckling och testning
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning och dataanalys verktyg
  * Djupgående Learning ramverk: en omfattande uppsättning AI ramverk, till exempel [Microsoft kognitiva Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [klientprocess](https://chainer.org/), mxNet Keras ingår i den virtuella datorn.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): snabb machine learning-system som har stöd för tekniker som online, hash, allreduce, minskning, learning2search, aktiv, och interaktiva learning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): ett verktyg som ger snabb och exakt ökat trädet implementering.
  * [Rattle](http://rattle.togaware.com/) (den R analytiska verktyget att lära dig enkelt): ett verktyg som gör att komma igång med dataanalys och maskininlärning i R enkelt. Det innehåller GUI-baserade datagranskning och modellering med automatisk kodgenerering för R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : ett visual datautvinning och maskininlärning programvara i Java.
  * [Apache ökad](https://drill.apache.org/): en schemafria SQL frågemotor för Hadoop, NoSQL och lagringsutrymmet i molnet.  Stöder ODBC och JDBC-gränssnitt för att aktivera frågor NoSQL och filer från standard BI-verktyg som PowerBI, Excel, Tableau.
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster
* Git inklusive Git Bash att arbeta med källkodslager inklusive GitHub, Visual Studio Team Services och ger flera populära Linux kommandoradsverktyg (inklusive awk, sed, perl, grep, hitta, wget, curl, etc.) tillgänglig både git bash och kommandot fråga. 

Gör datavetenskap omfattar iteration av en serie uppgifter:

1. Hitta, läsa in och förbearbetning av data
2. Skapa och testa modeller
3. Distribuera modeller för användning i intelligent program

Datavetare utföra dessa uppgifter med hjälp av en mängd olika verktyg. Det kan vara ganska lång tid att hitta rätt versioner av programvaran, och sedan ladda ned och installera dem. Microsoft datavetenskap Virtual Machine kan minska det här problemet genom att ge en klar att använda avbildning som kan etableras på Azure alla flera populära verktyg före installeras och konfigureras. 

Microsoft datavetenskap Virtual Machine jump-starts projektet analytics. På så sätt kan du arbeta med aktiviteter på olika språk, inklusive R, Python, SQL och C#. Visual Studio har ett IDE för att utveckla och testa din kod som är lätt att använda. Azure SDK om du ingår i den virtuella datorn kan du skapa program med hjälp av olika tjänster på Microsofts molnplattform. 

Det finns inga avgifter för programvara för den här datavetenskap VM-avbildning. Du betalar bara för avgifter för användning av Azure vilka beroende på storleken på den virtuella datorn som du etablerar. Mer information om beräkning avgifter finns i avsnittet priser information på den [datavetenskap virtuella](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) sidan. 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Andra versioner av datavetenskap virtuell dator
En [Ubuntu](dsvm-ubuntu-intro.md) bilden är tillgänglig, med många liknande verktyg plus några ytterligare djup learning ramverk. En [CentOS](linux-dsvm-intro.md) bilden är också tillgänglig. Vi erbjuder även en [Windows Server 2012 edition](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) för den virtuella datorn för vetenskap av data om några verktyg är bara tillgängliga på Windows Server 2016-versionen.  I annat fall gäller den här artikeln även för Windows Server 2012-versionen.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en Microsoft datavetenskap virtuell dator, måste du ha följande:

* **En Azure-prenumeration**: Om du vill skaffa en finns [hämta kostnadsfri utvärderingsversion av Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Skapa din Microsoft Data vetenskap virtuell dator
Följ dessa steg om du vill skapa en instans av Microsoft datavetenskap Virtual Machine:

1. Navigera till den virtuella datorn visas i en lista på [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Välj den **skapa** längst ned till tas med i en guide.![ Konfigurera-data-vetenskap-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. Guiden som används för att skapa Microsoft datavetenskap virtuell dator kräver **indata** för var och en av de **fyra steg** räknas upp till höger på denna bild. Här följer de indata som behövs för att konfigurera var och en av de här stegen:
   
   1. **Grundläggande inställningar**
      
      1. **Namnet**: namnet på din server för vetenskap av data som du skapar.
      2. **VM disktyp**: Välj mellan SSD och Hårddisk. För NC_v1 GPU instansen (NVidia Tesla K80 baserat), Välj **Hårddisk** som typ av disk. 
      3. **Användarnamnet**: Admin inloggnings-id för kontot.
      4. **Lösenordet**: Admin kontolösenord.
      5. **Prenumerationen**: Om du har mer än en prenumeration väljer du en som datorn ska skapas och debiteras.
      6. **Resursgruppen**: du kan skapa en ny eller Använd en befintlig grupp.
      7. **Plats**: Välj datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data eller som är närmast den fysiska platsen för snabbaste nätverksåtkomst.
   2. **Storlek**: Välj något av de servertyper som uppfyller dina krav på funktionsnivå och kostnaden begränsningar. Du får fler alternativ för VM-storlekar genom att välja ”Visa alla”.
   3. **Inställningar för**:
      
      1. **Använda hanterade diskar**: Välj hanteras om du vill att Azure för att hantera diskar för den virtuella datorn.  Du måste annars ange ett nytt eller befintligt lagringskonto. 
      2. **Andra parametrar**: Använd vanligtvis bara standardvärdena. Om du vill överväga att använda icke-standardvärden hovra över informationsmeddelande länken Hjälp om specifika fält.
    a. **Sammanfattning**: Kontrollera att all information du angett är korrekt och klicka på **skapa**. **Obs**: den virtuella datorns har inte några ytterligare kostnader utöver beräkning för server-storlek som du har valt i den **storlek** steg. 

> [!NOTE]
> Etableringen bör ta ungefär 10 20 minuter. Status för etablering av visas på Azure-portalen.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Hur du kommer åt Microsoft datavetenskap virtuell dator
När den virtuella datorn har skapats kan du fjärrskrivbord till den med administratörsautentiseringsuppgifter för kontot som du konfigurerade i föregående **grunderna** avsnitt. 

När den virtuella datorn skapas och etableras, är du redo att börja använda verktygen som installeras och konfigureras på den. Det finns start-menyn paneler och ikoner på skrivbordet för många av verktyg. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Verktygen som installeras på Microsoft datavetenskap Virtual Machine



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Om du vill använda Microsoft enterprise-bibliotek för skalbara R eller Python för din analys av har den virtuella datorn Microsoft ML Server Developer edition (tidigare känt som Microsoft R Server) installerat. Microsoft ML-servern är en brett distribuerbara företagsklass analytics platform för både R och Python och skalbar, kommersiellt stöds och säkra. Stöder en mängd olika stordata statistik, förutsägelsemodellering och maskininlärning funktioner ML-Server har stöd för en fullständig uppsättning analytics – undersökning, analys, visualisering och modellering. Genom att använda och utöka öppen källkod R och Python, Microsoft ML Server är helt kompatibel med R / Python-skript, funktioner och CRAN / pip / skala Conda paket att analysera data på företaget. Minnesbegränsningarna för öppen källa R åtgärdas även genom att lägga till parallella och chunked bearbetning av data. På så sätt kan du göra analyser på data som är mycket större än vad som passar i huvudminnet.  Visual Studio Community Edition tas med på den virtuella datorn innehåller R-verktyg för Visual Studio och Python tools för Visual Studio-tillägg som ger en fullständig IDE för att arbeta med R eller Python. Vi erbjuder även andra IDEs samt som [RStudio](http://www.rstudio.com) och [PyCharm Community edition](https://www.jetbrains.com/pycharm/) på den virtuella datorn. 

### <a name="python"></a>Python
För utveckling med hjälp av Python, har distribution av Anaconda Python 2.7 och 3,6 installerats. Den här distributionen innehåller grundläggande Python tillsammans med ungefär 300 populäraste matematiska, ingenjörer och data analytics paket. Du kan använda Python Tools för Visual Studio (PTVS) som är installerad i Visual Studio 2017 Community edition eller någon av IDEs levereras tillsammans med Anaconda som INAKTIV eller Spyder. Du kan starta en av dessa genom att söka i sökfältet (**Win** + **S** nyckel).

> [!NOTE]
> Om du vill peka Python Tools för Visual Studio på Anaconda Python 2.7, måste du skapa anpassade miljöer för varje version. Om du vill ange sökvägarna miljö i Visual Studio 2017 Community Edition, gå till **verktyg** -> **Python Tools** -> **Python-miljöer**och klicka sedan på **+ anpassad**. 
> 
> 

Anaconda Python 3,6 installeras under C:\Anaconda och Anaconda Python 2.7 installeras under c:\Anaconda\envs\python2. Se [dokumentationen till PTVS](/visualstudio/python/python-environments.md#selecting-and-installing-python-interpreters) detaljerade anvisningar. 

### <a name="jupyter-notebook"></a>Jupyter Notebook
Anaconda distribution innehåller också en Jupyter-anteckningsbok en miljö att dela kod och analys. En Jupyter-anteckningsbok server har redan konfigurerats med Python 2.7, Python 3.x, PySpark, Julia och R kärnor. Det finns en skrivbordsikon med namnet ”Jupyter Notebook” för att starta Jupyter-servern och starta om webbläsaren för att komma åt servern bärbar dator. 

Vi har paketerat flera bärbara datorer av exemplet i Python och R. Jupyter-anteckningsböcker visar hur du arbetar med Microsoft ML Server, SQL Server ML Services (i databasen analytics), Python, Microsoft kognitiva ToolKit, Tensorflow och andra tekniker för Azure när du har åtkomst till Jupyter. Du kan se länken prov på startsidan för bärbar dator när du autentiserar dig till Jupyter-anteckningsbok med lösenordet du skapade tidigare. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community edition
Visual Studio Community edition är installerade på den virtuella datorn. Det är en kostnadsfri version av populära IDE från Microsoft som du kan använda i utvärderingssyfte och för små team. Du kan checka ut licensvillkoren [här](https://www.visualstudio.com/support/legal/mt171547).  Öppna Visual Studio genom att dubbelklicka på skrivbordet eller **starta** menyn. Du kan också söka efter program med **Win** + **S** och ange ”Visual Studio”. När det kan du skapa projekt i språk som C#, Python, R, node.js. Plugin-program installeras även som gör det enkelt att arbeta med Azure-tjänster som Azure Data Catalog, Azure HDInsight (Hadoop, Spark) och Azure Data Lake. Nu finns även ett plugin-program som kallas ```Visual Studio Tools for AI``` som sömlöst integrerar till Azure Machine Learning och hjälper dig att snabbt skapa AI-program. 

> [!NOTE]
> Du får ett meddelande om att utvärderingsperioden har upphört att gälla. Ange autentiseringsuppgifterna för ditt Microsoft-konto eller skapa ett kostnadsfritt konto för att få åtkomst till Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition
En utvecklare version av SQL Server-2017 med ML Services körs i databasen analytics finns på den virtuella datorn i R eller Python. Ml – tillhandahåller en plattform för att utveckla och distribuera intelligent program. Du kan använda de omfattande och kraftfulla dessa språk och många paket från gemenskapen att skapa modeller och generera förutsägelser för SQL Server-data. Du kan behålla analytics nära data eftersom ML-tjänster (i databasen) integrerar både R och Python språk i SQL Server. Detta eliminerar kostnader och säkerhetsriskerna med dataflyttning.

> [!NOTE]
> SQL Server developer edition kan endast användas för utveckling och testning. Du måste ha en licens för att köra den i produktion. 
> 
> 

Du kan komma åt SQLServer genom att starta **SQL Server Management Studio**. VM-namn fylls som servernamn. Använd Windows-autentisering när du har loggat in som administratör på Windows. När du är i SQL Server Management Studio kan du skapa andra användare, skapa databaser, importera data och köra SQL-frågor. 

Kör följande kommando om du vill aktivera i databasen analytics med hjälp av SQL ML Services som en tid åtgärden i SQL Server management studio när du loggar in som serveradministratör. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Flera Azure tools är installerat på den virtuella datorn:

* Det finns en genväg på skrivbordet till Azure SDK-dokumentationen. 
* **AzCopy**: används för att flytta data till och från Microsoft Azure Storage-konto. Om du vill se användning, Skriv **Azcopy** i Kommandotolken för att se användningen. 
* **Microsoft Azure Lagringsutforskaren**: används för att bläddra igenom objekten som du har lagrat i ditt Azure Storage-konto och överför data till och från Azure storage. Du kan skriva **Lagringsutforskaren** i Sök eller hitta på Start-menyn för att komma åt det här verktyget. 
* **Adlcopy**: används för att flytta data till Azure Data Lake. Om du vill se användning, Skriv **adlcopy** i en kommandotolk. 
* **dtui**: används för att flytta data till och från Azure Cosmos DB, en NoSQL-databas i molnet. Typen **dtui** i Kommandotolken. 
* **Azure Data Factory Integration Runtime**: gör det möjligt för flytt av data mellan lokala datakällor och molnet. Den används i verktyg som Azure Data Factory. 
* **Microsoft Azure Powershell**: ett verktyg som används för att administrera dina Azure-resurser i Powershell skriptspråk som också är installerad på den virtuella datorn. 

### <a name="power-bi"></a>Power BI
Som hjälper dig att skapa instrumentpaneler och bra visualiseringar i **Power BI Desktop** har installerats. Använda verktyget som hämtar data från olika källor för att skapa dina instrumentpaneler och rapporter och publicera dem till molnet. Mer information finns i [Power BI](http://powerbi.microsoft.com) plats. Du hittar Power BI desktop på Start-menyn. 

> [!NOTE]
> Du behöver ett Office 365-konto till Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning arbetsstationen är ett skrivbordsprogram och kommandoradsgränssnitt. Arbetsstationen har förberedelse av inbyggda data som lär sig förberedelsesteg dina data som du utför dem. Det ger också projekthantering kör historik och anteckningsboken integrering med bättra på produktiviteten. Du kan dra nytta av de bästa ramverk för öppen källkod, inklusive TensorFlow, kognitiva Toolkit, Spark ML och scikit – Lär dig att utveckla modeller. På DSVM ger vi en skrivbordsikon för att installera Azure Machine Learning-arbetsstation till katalogen för den enskilda användaren % LOCALAPPDATA %. Varje användare som behöver använda arbetsstationen måste göra en tid åtgärden Dubbelklicka på den ```AzureML Workbench Setup``` skrivbordsikon att installera deras instansen i arbetsstationen. Azure Machine Learning också skapar och använder en användarspecifik Python-miljö som ska extraheras i % LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Ytterligare Microsoft-utvecklingsverktyg
Den [ **Microsoft Web Platform Installer** ](https://www.microsoft.com/web/downloads/platform.aspx) kan användas för att identifiera och ladda ned andra Microsoft-utvecklingsverktyg. Det finns också en genväg till de verktyg som finns på Microsoft datavetenskap virtuella skrivbordet.  

## <a name="important-directories-on-the-vm"></a>Viktiga kataloger på den virtuella datorn
| Objekt | Katalog |
| --- | --- |
| Serverkonfigurationer för Jupyter-anteckningsbok |C:\ProgramData\jupyter |
| Arbetskatalog för Jupyter Notebook-exempel |c:\dsvm\notebooks och c:\users\<användarnamn > \notebooks|
| Andra exempel |c:\dsvm\samples |
| Anaconda (standard: Python 3,6) |c:\Anaconda |
| Anaconda Python 2.7 miljö |c:\Anaconda\envs\python2 |
| Microsoft ML Server fristående Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| R standardinstansen (ML Server fristående) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL ML Services i databasen instans directory |C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Azure Machine Learning arbetsstationen (per användare) | %localappdata%\amlworkbench | 
| Diverse verktyg |c:\dsvm\tools |

> [!NOTE]
> På Windows Server 2012-version av DSVM och Windows Server 2016 version före mars 2018 är standard Anaconda miljön Python 2.7. Den sekundära miljön är Python 3.5 på c:\Anaconda\envs\py35. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Här följer några nästa steg för att fortsätta din inlärning och undersökning. 

* Utforska datavetenskap verktyg på datavetenskap VM genom att klicka på start-menyn och checka ut verktyg som visas på menyn.
* Lär dig mer om Azure Machine Learning-tjänster och arbetsstationen genom att besöka produkten [sidan Snabbstart och självstudier](../preview/index.yml). 
* Gå till **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** för exempel som använder RevoScaleR biblioteket i R som har stöd för dataanalys på företagsnivå.  
* Läs artikeln: [10 saker du kan göra på datavetenskap virtuell dator](http://aka.ms/dsvmtenthings)
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team datavetenskap Process](../team-data-science-process/index.yml).
* Besök den [Azure AI-galleriet](http://gallery.cortanaintelligence.com) tjänster i Azure machine learning och analytics exempel som använder Azure Machine learning och relaterade data. Vi har också tillhandahålls en ikon på den **starta** menyn och på skrivbordet för den virtuella datorn till det här galleriet.

