---
title: 'Snabbstart: Skapa ett Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurera och skapa en virtuell dator för datavetenskap på Azure för analys och maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: 6c0abc20775e604f9ea1c6ba882e5a9ff1d89e90
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195309"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>Snabbstart: Konfigurera en Windows Data Science Virtual Machine på Azure

Microsoft Windows Data Science Virtual Machine (DSVM) är en Windows Server 2016 Virtual Machine-avbildning (VM) i Azure. Den är förinstallerad och konfigurerad med verktyg för data analys och maskin inlärning.

## <a name="included-data-science-tools"></a>Inkluderade verktyg för data vetenskap

Följande verktyg ingår i en DSVM:

* Python SDK för [Azure Machine learnings tjänsten](../service/index.yml).
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Anaconda Python-distribution.
* Jupyter-anteckningsbok med R, Python och PySpark-kärnor.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 Developer edition.
* En Apache Spark-instans för fristående för lokal utveckling och testning.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning och data analytics-verktyg:
  * Ramverk för djup inlärning: [TensorFlow](https://www.tensorflow.org/)-, [kedje](https://chainer.org/)-, MXNET-och keras AI-ramverk ingår på den virtuella datorn.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): En snabb machine learning-system som har stöd för tekniker som hash-online, allreduce, sänkt, learning2search och aktiva och interaktiv utbildning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Ett verktyg som ger snabba och exakta bättre trädet implementering.
  * [Rattle](https://togaware.com/rattle/). Verktyget R-analys som hjälper dig att komma igång med data analys och maskin inlärning i R. Den innehåller GUI-baserad data utforskning och modellering med automatisk generering av R-kod.
  * [Wekas](https://www.cs.waikato.ac.nz/ml/weka/): Visual datautvinning och machine learning-programvara i Java.
  * [Apache-granskning](https://drill.apache.org/): En schemafria SQL frågemotor för Apache Hadoop-, NoSQL- och molnlagring. Det har stöd för ODBC- och JDBC-gränssnitt för frågor till NoSQL och filer från standard BI-verktyg som Power BI, Microsoft Excel och Tableau.
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster.
* Git, inklusive git bash, för att arbeta med käll kods databaser som GitHub och Azure DevOps. Git innehåller flera populära kommando rads verktyg för Linux som är tillgängliga både i git-bash och i en kommando tolk. Exempel är awk, sed, perl, grep, Sök, wget eller curl.
* Utvecklingsverktyg och redigerare (RStudio, pycharm med).

### <a name="about-data-science"></a>Om data vetenskap

Datavetenskap omfattar iteration av en serie uppgifter:

1. Hitta, läsa in och Förbearbeta data.
1. Skapa och testa modeller.
1. Distribuera modeller för användning i intelligenta program.

Dataexperter kan du använda flera verktyg för dessa uppgifter. Det kan ta lång tid att hitta rätt versioner av programvaran och sedan ladda ned och installera den. DSVM sparar tid genom att tillhandahålla en färdig avbildning som kan tillhandahållas på Azure, med flera populära verktyg förinstallerade och konfigurerade.

DSVM-hopp – startar ditt analys projekt. Du kan arbeta med uppgifter på olika språk, inklusive R, python, SQL och C#. Visual Studio tillhandahåller en enkel att använda integrerad utvecklingsmiljö (IDE) för att utveckla och testa din kod. Azure SDK ingår i den virtuella datorn så att du kan bygga dina program med hjälp av tjänster på Microsofts moln plattform.

Det finns inga program varu avgifter för den här DSVM-avbildningen. Du betalar bara användningen av Azure-avgifter. De är beroende av storleken på den virtuella dator som du etablerar. Mer information finns på [sidan data science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows).

### <a name="other-dsvm-versions"></a>Andra DSVM-versioner

* En [Ubuntu](dsvm-ubuntu-intro.md) bild. Den har verktyg som liknar DSVM, plus några djup inlärnings ramverk.
* En [Linux CentOS](linux-dsvm-intro.md) bild.
* Den [Windows Server 2012-versionen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) av den virtuella datorn för datavetenskap. Det finns några verktyg endast på Windows Server 2016-versionen. Annars kan gäller den här artikeln även för Windows Server 2012-versionen.

## <a name="prerequisite"></a>Krav

Om du vill skapa en Microsoft Data Science Virtual Machine, måste du ha en Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Skapa din DSVM

Så här skapar du en DSVM-instans:

1. Gå till listan med virtuella datorer på [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Du kan uppmanas att logga in på ditt Azure-konto om du inte redan har loggat in.
1. Välj knappen **skapa** längst ned.

   ![Lista över virtuella datorer på Azure Portal, med knappen Skapa](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Ange följande information för att konfigurera vart och ett av stegen som visas i den högra rutan i skärm bilden:

   1. **Grunderna**:
      * **Namn på**: Ange namnet på DSVM.
      * **Typ av virtuell dator disk**: Välj antingen **SSD** eller **HDD**. För en NC_v1 GPU-instans som NVIDIA Tesla K80-baserad väljer du **HDD** som disk typ.
      * **Användarnamn**: Ange ID för administratörs kontot.
      * **Lösenord**: Ange lösen ordet för administratörs kontot.
      * **Prenumeration**: Om du har mer än en prenumeration väljer du den dator där datorn ska skapas och faktureras.
      * **Resursgrupp**: Skapa en ny grupp eller Använd en befintlig.
      * **Plats**: Välj det data Center som är lämpligast. För snabbast nätverks åtkomst är det data Center som har de flesta data eller som är närmast din fysiska plats.
   1. **Storlek**: Välj den server typ som uppfyller dina funktions krav och kostnads begränsningar. Fler alternativ med VM-storlekar väljer **visa alla**.
   1. **Inställningar för**:  
      * **Använda hanterade diskar**. Välj **hanterade** om du vill att Azure för att hantera diskar för den virtuella datorn. Om inte, måste du ange ett nytt eller befintligt lagringskonto.  
      * **Andra parametrar**. Du kan använda standardvärdena. Om du vill använda värden som inte är standardvärden hovrar du över informations länken för att få hjälp med de angivna fälten.
   1. **Sammanfattning av**: Kontrollera att all information som du angett är korrekt. Välj **Skapa**.

> [!NOTE]
> * Den virtuella datorn debiteras inte ytterligare avgifter utöver beräknings kostnaden för den server storlek som du valde i steget **storlek** .
> * Etableringen tar 10 till 20 minuter. Du kan visa statusen för den virtuella datorn på Azure Portal.

## <a name="access-the-dsvm"></a>Åtkomst till DSVM

När den virtuella datorn har skapats och allokerats kan du komma åt den via en fjärr skrivbords anslutning. Använd de autentiseringsuppgifter för administratörs kontot som du konfigurerade i **grundläggande** steg för att skapa en virtuell dator. 

Du är redo att börja använda verktygen som installeras och konfigureras på den virtuella datorn. Många av verktygen kan nås via **Start** -menyns paneler och skriv bords ikoner.

Du kan också bifoga en DSVM till Azure Notebooks för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna i den kostnads fria tjänst nivån. Mer information finns i [Hantera och konfigurera antecknings projekt](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Verktyg som är installerade på DVSM

I följande avsnitt lär du dig mer om de verktyg som är installerade på Data Science Virtual Machine.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Du kan använda Microsoft Enterprise Library för din analys eftersom Machine Learning Server Developer Edition är installerat på den virtuella datorn. Machine Learning Server som tidigare kallades Microsoft R Server, är en mycket distributions bara analys plattform. Den är skalbar och stöds kommersiellt.

Machine Learning Server har stöd för olika stordatastatistik, förutsägande modellering och maskininlärning uppgifter. Det har stöd för en fullständig uppsättning analytics: utforskning, analys, visualisering och modellering. Genom att använda och utökar R och Python för öppen källkod, är Machine Learning Server kompatibel med R och Python-skript och funktioner. Det är också kompatibla med CRAN, pip och Conda-paket för att analysera data i enterprise-skala.

Machine Learning Server löser minnesbegränsningarna för R med öppen källkod genom att lägga till parallell och segmenterat bearbetning av data. Det innebär att du kan köra analyser på mycket större data än vad som passar i huvud minnet. 

Visual Studio Community ska tas med på den virtuella datorn. Den har R Tools för Visual Studio-och Python Tools for Visual Studio-tillägg (PTVS) som tillhandahåller en fullständig IDE för att arbeta med R eller python. Vi tillhandahåller även andra IDE: er som [RStudio](https://www.rstudio.com) och [pycharm med Community Edition](https://www.jetbrains.com/pycharm/) på den virtuella datorn.

### <a name="python"></a>Python

För utveckling med hjälp av python installeras Anaconda python-distributioner 2,7 och 3,6. Dessa distributioner har grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data. Du kan använda PTVS, som installeras i Visual Studio Community 2017. Eller så kan du använda en av IDE: er som är paketerad med Anaconda, t. ex. inaktiv eller Spyder. Sök efter och öppna ett av dessa paket (Windows-tangenten + S).

> [!NOTE]
> Om du vill peka Python-verktygen för Visual Studio på Anaconda Python 2.7, måste du skapa anpassade miljöer för varje version. Om du vill ange dessa miljö Sök vägar i Visual Studio 2017-communityn går du till **verktyg** > **python tools** > **python-miljöer**. Välj sedan **+ anpassad**.

Anaconda python 3,6 installeras under C:\Anaconda. Anaconda python 2,7 installeras under C:\Anaconda\envs\python2. Detaljerade anvisningar finns i PTVS- [dokumentationen](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Jupyter-anteckningsbok

Anaconda-distribution levereras också med Jupyter Notebook, en miljö för att dela kod och analys. Jupyter Notebook-server är förkonfigurerad med Python 2.7, Python 3.x, PySpark, Julia och R-kärnor. Starta Jupyter-servern och öppna webbläsaren för att få åtkomst till Notebook-servern med hjälp av ikonen **Jupyter Notebook** skriv bord.

Vi paketera flera exempelanteckningsböcker i Python och R. När du har åtkomst till Jupyter visar hur du arbetar med följande tekniker de bärbara datorerna:

* Machine Learning Server
* SQL Server Machine Learning Services, i databas analys
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Andra Azure-tekniker

Du hittar länken till exemplen på hem sidan för notebook när du har autentiserat dig för Jupyter Notebook med hjälp av lösen ordet som du skapade tidigare.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM innehåller Visual Studio Community. Det här är en kostnads fri version av den populära IDE-versionen från Microsoft som du kan använda för utvärderings syfte och små team. Se [licens villkoren för program vara från Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Öppna Visual Studio med hjälp av Skriv bords ikonen eller **Start** -menyn. Sök efter program (Windows-tangenten + S) följt av **Visual Studio**. Därifrån kan du skapa projekt på språk som C#, python, R och Node. js. Installerade plugin-program gör det enkelt att arbeta med följande Azure-tjänster:

* Azure Data Catalog
* Azure HDInsight för Hadoop och Spark
* Azure Data Lake

Ett plugin-program som kallas Azure Machine Learning för Visual Studio Code integreras också med Azure Machine Learning och hjälper dig att snabbt bygga AI-program.

> [!NOTE]
> Du kan få ett meddelande om att utvärderingsperioden har upphört att gälla. Ange autentiseringsuppgifterna för ditt Microsoft-konto. Eller skapa ett nytt kostnadsfritt konto för att få åtkomst till Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM levereras med en Developer-version av SQL Server 2017 med Machine Learning Services. Den här SQL Server versionen kommer antingen i R eller python och kan köras i databas analys. 

Machine Learning Services tillhandahåller en plattform för utveckling och distribution intelligenta program. Du kan använda dessa språk och många paket från communityn för att skapa modeller och genererar förutsägelser för SQL Server-data. Du kan behålla analytics nära data eftersom Machine Learning Services, i databasen, integrerar både R och Python språk i SQL Server. Den här integreringen slipper du kostnaderna och säkerhetsrisker som förknippas med dataförflyttning.

> [!NOTE]
> SQL Server Developer-utgåvan är endast för utveckling och testning. Du behöver en licens för att köra den i produktion.

Du kan komma åt SQL Server genom att öppna Microsoft SQL Server Management Studio. Namnet på den virtuella datorn är ifyllt som **Server namn**. Använd Windows-autentisering när du loggar in som administratör på Windows. När du använder SQL Server Management Studio kan du skapa andra användare, skapa databaser, importera data och köra SQL-frågor.

Om du vill aktivera databas analys med hjälp av SQL Server Machine Learning Services kör du följande kommando som en engångs åtgärd i SQL Server Management Studio när du har loggat in som Server administratör:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Ersätt `%COMPUTERNAME%` med namnet på den virtuella datorn.

### <a name="azure"></a>Azure

Flera Azure-verktygen är installerade på den virtuella datorn:

* En genväg på skrivbordet går till Azure SDK-dokumentationen.
* Använd AzCopy för att kopiera data in och ut ur ditt Azure Storage-konto. Om du vill se användning, ange **Azcopy** i en kommandotolk.
* Använd Azure Storage Explorer för att bläddra igenom de objekt som du lagrar på ditt Azure Storage-konto. Den kopierar även data till och från Azure Storage. För att komma åt det här verktyget anger du **Storage Explorer** i **Sök** fältet. Eller hitta på Windows **starta** menyn.
* AdlCopy kopierar data till Azure Data Lake. Om du vill se användning, ange **adlcopy** i en kommandotolk.
* Dtui-verktyget kopierar data till och från Azure Cosmos DB, en NoSQL-databas i molnet. Ange **dtui** i en kommandotolk.
* Integrerings körningen kopierar data mellan lokala data källor och molnet. Den används i verktyg som Azure Data Factory.
* Använd Azure PowerShell för att administrera dina Azure-resurser i PowerShell-skript språket. Det är också installeras på den virtuella datorn.

### <a name="power-bi"></a>Power BI

DSVM levereras med Power BI Desktop installerat för att hjälpa dig att bygga instrument paneler och visualiseringar. Använda verktyget för att hämta data från olika källor, skapa dina instrumentpaneler och rapporter och publicera dem till molnet. Mer information finns på webbplatsen för [Power BI](https://powerbi.microsoft.com). Du kan hitta Power BI Desktop på **Start** -menyn.

> [!NOTE]
> Du behöver ett Microsoft Office 365-konto till Power BI.

### <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK för python

Data forskare och AI-utvecklare använder Azure Machine Learning SDK för python för att skapa och köra Machine Learning-arbetsflöden med [tjänsten Azure Machine Learning](../service/overview-what-is-azure-ml.md). Du kan interagera med tjänsten i Jupyter-anteckningsböcker eller en annan python IDE med hjälp av ramverk för öppen källkod som TensorFlow och scikit-lär.

Python SDK är förinstallerat på Microsoft Data Science Virtual Machine. Om du vill börja använda python SDK läser [du Använd python för att komma igång med Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Fler Microsoft-utvecklingsverktyg

Du kan använda [installations programmet för Microsoft Web Platform](https://www.microsoft.com/web/downloads/platform.aspx) för att hitta och hämta andra verktyg för Microsoft-utveckling. Det finns också en genväg till verktyget på Microsoft Data Science Virtual Machine Desktop.  

## <a name="important-directories-on-the-vm"></a>Viktiga kataloger på den virtuella datorn

| Objekt | Katalog |
| --- | --- |
| Jupyter Notebook server-konfigurationer | C:\ProgramData\jupyter |
| Arbetskatalog för Jupyter Notebook-exempel | C:\dsvm\notebooks och c:\Users\\< användar\>namn \notebooks |
| Andra exempel | C:\dsvm\samples |
| Anaconda, standard: Python 3,6 | C:\Anaconda |
| Anaconda Python 2.7 miljö | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (fristående) för python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Standard R-instans, Machine Learning Server (fristående) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services databas instans katalog | C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Diverse verktyg | C:\dsvm\tools |

> [!NOTE]
> På Windows Server 2012-utgåvan av DSVM och Windows Server 2016 Edition före mars 2018 är standard miljön python 2,7. Den sekundära miljön är python 3,5, som finns på C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Nästa steg

* Utforska verktygen på DSVM genom att öppna **Start** -menyn.
* Lär dig mer om Azure Machine Learning tjänsten genom att läsa [Vad är Azure Machine Learning tjänst?](../service/overview-what-is-azure-ml.md) och testa [snabb starter och självstudier](../service/index.yml).
* I Utforskaren bläddrar du till C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts for samples som använder RevoScaleR-biblioteket i R som stöder data analys i företags skala. 
* Läs artikeln [tio saker du kan göra på data science Virtual Machine](https://aka.ms/dsvmtenthings).
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](../team-data-science-process/index.yml).
* Gå till den [Azure AI-galleriet](https://gallery.cortanaintelligence.com) för machine learning och data analytics-exempel som använder Azure Machine Learning och relaterade data services på Azure. Vi har också tillhandahållit en ikon för det här galleriet på **Start** -menyn och på Skriv bordet på den virtuella datorn.
