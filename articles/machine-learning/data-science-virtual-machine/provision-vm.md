---
title: Skapa en Windows virtuell dator för datavetenskap
titleSuffix: Azure
description: Konfigurera och skapa en virtuell dator för datavetenskap på Azure för analys och maskininlärning.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 488dc7db01bd865268e143b68cdaccd989010912
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534932"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Etablera en Windows Data Science Virtual Machine på Azure

Microsoft Windows Data Science Virtual Machine (DSVM) är en Windows Server 2016 Virtual Machine-avbildning (VM) på Azure som är förinstallerad och konfigurerad med verktyg för data analys och maskin inlärning.

## <a name="included-data-science-tools"></a>Inkluderade verktyg för data vetenskap

Följande verktyg ingår i en DSVM:

* [Azure Machine Learning tjänst](../service/index.yml) Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Anaconda Python-distribution
* Jupyter Notebook med R-, python-och PySpark-kärnor
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 Developer Edition
* En fristående Apache Spark instans för lokal utveckling och testning
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning och data analytics-verktyg:
  * Ramverk för djup inlärning – en omfattande uppsättning AI-ramverk ingår på den virtuella datorn: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [kedjer](https://chainer.org/), mxNet och keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – ett snabbt Machine Learning-system som stöder metoder som online-hashing, allreduce, reduktioner, learning2search och aktiv och interaktiv utbildning
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – ett verktyg som ger snabb och korrekt utökat träd implementering
  * [Rattle](https://togaware.com/rattle/) – r analys verktyg som hjälper dig att komma igång med data analys och maskin inlärning i R. Den innehåller GUI-baserad data utforskning och modellering med automatisk generering av R-kod.
  * [Wekas](https://www.cs.waikato.ac.nz/ml/weka/) – program vara för visuell Data utvinning och maskin inlärning i Java
  * [Apache-granskning](https://drill.apache.org/) – en schema fri SQL-frågemotor för Apache Hadoop, NoSQL och moln lagring. Det har stöd för ODBC- och JDBC-gränssnitt för frågor till NoSQL och filer från standard BI-verktyg som Power BI, Microsoft Excel och Tableau.
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster
* Git, inklusive Git Bash, att arbeta med lagringsutrymmen för källkod som omfattar GitHub och Azure DevOps. Git innehåller flera populära Linux-kommandoradsverktyg som är tillgängliga både på Git Bash och en kommandotolk. Exempel är awk, sed, perl, grep, Sök, wget eller curl.
* Utvecklingsverktyg och redigerare (RStudio, pycharm med)

### <a name="about-data-science"></a>Om data vetenskap

Datavetenskap omfattar iteration av en serie uppgifter:

1. Hitta, läsa in och Förbearbeta data
1. Bygga och testa modeller
1. Distribuera modeller för användning i intelligenta program

Dataexperter kan du använda flera verktyg för dessa uppgifter. Det kan ta lång tid att hitta rätt versioner av programvaran och sedan ladda ned och installera den. DSVM sparar tid genom att tillhandahålla en färdig avbildning som kan tillhandahållas i Azure med flera populära verktyg förinstallerade och konfigurerade.

DSVM-hopp – startar ditt analys projekt. Du kan arbeta med aktiviteter på olika språk, inklusive R, Python, SQL och C#. Visual Studio tillhandahåller en enkel att använda integrerad utvecklingsmiljö (IDE) för att utveckla och testa din kod. Azure SDK ingår i den virtuella datorn så att du kan bygga dina program med hjälp av olika tjänster på Microsofts moln plattform.

Det finns inga programvaruavgifter för den här data science VM-avbildning. Du betalar bara användningen av Azure-avgifter. De beror på storleken på den virtuella datorn som du etablerar. Mer information om beräkning avgifter finns i den **prisinformation** avsnittet på den [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) sidan.

### <a name="other-dsvm-versions"></a>Andra DSVM-versioner

* En [Ubuntu](dsvm-ubuntu-intro.md) bild. Har många verktyg som liknar DSVM plus några ytterligare deep learning-ramverk.
* En [Linux CentOS](linux-dsvm-intro.md) bild.
* Den [Windows Server 2012-versionen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) av den virtuella datorn för datavetenskap. Det finns några verktyg endast på Windows Server 2016-versionen. Annars kan gäller den här artikeln även för Windows Server 2012-versionen.

## <a name="prerequisite"></a>Krav

Om du vill skapa en Microsoft Data Science Virtual Machine, måste du ha en Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Skapa din DSVM

Så här skapar du en DSVM-instans:

1. Gå till listan med virtuella datorer på [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Du kan uppmanas att logga in på ditt Azure-konto om du inte redan har loggat in.
1. Välj knappen **skapa** längst ned.

   ![Konfigurera –--virtuell dator för datavetenskap](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Du måste ange följande information för att konfigurera vart och ett av de steg som visas i den högra rutan i skärm bilden:

   1. **Grunderna**:
      * **Namn**: namnet på DSVM
      * **Typ av virtuell dator disk**: antingen **SSD** eller **HDD**. En NC_v1 GPU-instans som NVidia Tesla K80 baserat, Välj **HDD** som typ av disk.
      * **Användar namn**: ID för administratörs konto
      * **Lösen ord**: lösen ordet för administratörs kontot
      * **Prenumeration**: Om du har mer än en prenumeration väljer du det som datorn är skapas och faktureras.
      * **Resursgrupp**. Du kan skapa en ny eller Använd en befintlig grupp.
      * **Plats**. Välj det datacenter som är mest lämplig. Snabbaste nätverksåtkomst är det datacenter som har de flesta av dina data eller som är närmast dina fysiska plats.
   1. **Storlek**: Välj en av de servertyper som uppfyller dina funktionella krav och begränsningar för kostnad. Fler alternativ med VM-storlekar väljer **visa alla**.
   1. **Inställningar för**:  
      * **Använda hanterade diskar**. Välj **hanterade** om du vill att Azure för att hantera diskar för den virtuella datorn. Om inte, måste du ange ett nytt eller befintligt lagringskonto.  
      * **Andra parametrar**. Du kan använda standardvärdena. Om du vill använda värden som inte är standardvärden hovrar du över informations länken för att få hjälp med de angivna fälten.
   1. **Sammanfattning av**: Kontrollera att all information som du angett är korrekt. Välj **Skapa**.

> [!NOTE]
> * Den virtuella datorn debiteras inte ytterligare avgifter utöver beräknings kostnaden för den server storlek du valde i steget **storlek** .
> * Etableringen tar cirka 10 till 20 minuter. Du kan visa statusen för den virtuella datorn på Azure Portal.

## <a name="how-to-access-the-dsvm"></a>Så här får du åtkomst till DSVM

När den virtuella datorn skapas och etableras, kan du fjärrskrivbord till den med hjälp av autentiseringsuppgifter som administratör-konto som du konfigurerade i föregående **grunderna** avsnittet. Du är redo att börja använda verktygen som installeras och konfigureras på den virtuella datorn. Många av verktygen kan nås via Start-menyns paneler och skriv bords ikoner.

Du kan också bifoga en Data Science VM till Azure Notebooks för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna i den kostnads fria tjänst nivån. Mer information finns i [Hantera och konfigurera antecknings projekt – beräknings nivå](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Verktygen som installeras på Microsoft Data Science Virtual Machine

Läs mer om de verktyg som finns installerade på DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Du kan använda Microsoft Enterprise Library för skalbar R eller Python för analys, eftersom Machine Learning Server Developer edition är installerat på den virtuella datorn. Machine Learning Server som tidigare kallades Microsoft R Server, är en brett distributions plattform i företags klass. Den är tillgänglig för både R och python. Det är också skalbart, kommersiellt stöd och säkert.

Machine Learning Server har stöd för olika stordatastatistik, förutsägande modellering och maskininlärning uppgifter. Det har stöd för en fullständig uppsättning analytics: utforskning, analys, visualisering och modellering. Genom att använda och utökar R och Python för öppen källkod, är Machine Learning Server kompatibel med R och Python-skript och funktioner. Det är också kompatibla med CRAN, pip och Conda-paket för att analysera data i enterprise-skala.

Machine Learning Server löser minnesbegränsningarna för R med öppen källkod genom att lägga till parallell och segmenterat bearbetning av data. Det innebär att du kan köra analyser på mycket större data än vad som passar i huvud minnet. Visual Studio Community ska tas med på den virtuella datorn. Den har R Tools för Visual Studio-och Python Tools for Visual Studio-tillägg (PTVS) som tillhandahåller en fullständig IDE för att arbeta med R eller python. Vi ger även andra IDE: er som [RStudio](https://www.rstudio.com) och [PyCharm Community edition](https://www.jetbrains.com/pycharm/) på den virtuella datorn.

### <a name="python"></a>Python

För utveckling med hjälp av python installeras Anaconda python-distributioner 2,7 och 3,6. Dessa distributioner har grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data. Du kan använda PTVS som installeras i Visual Studio Community 2017. Du kan också använda en av IDE: er som levereras tillsammans med Anaconda som INAKTIV eller Spyder. Sök efter och starta en av dessa paket (Win + S).

> [!NOTE]
> Om du vill peka Python-verktygen för Visual Studio på Anaconda Python 2.7, måste du skapa anpassade miljöer för varje version. Ange sökvägarna miljö i Visual Studio 2017 Community och navigera till **verktyg** > **Python Tools** > **Python-miljöer**. Välj sedan **+ anpassad**.

Anaconda Python 3.6 är installerat **C:\Anaconda**. Anaconda Python 2.7 är installerat **c:\Anaconda\envs\python2**. Detaljerade anvisningar finns i [dokumentationen till PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Jupyter-anteckningsbok

Anaconda distribution levereras också med Jupyter-anteckningsboken en miljö för att dela kod och analys. Jupyter Notebook-server är förkonfigurerad med Python 2.7, Python 3.x, PySpark, Julia och R-kärnor. Starta Jupyter-servern och starta webbläsaren för att få åtkomst till Notebook-servern med hjälp av ikonen **Jupyter Notebook** skriv bord.

Vi paketera flera exempelanteckningsböcker i Python och R. När du har åtkomst till Jupyter visar hur du arbetar med följande tekniker de bärbara datorerna:

* Machine Learning Server
* SQL Server Machine Learning Services, i databas analys
* Python
* Microsoft kognitiv ToolKit
* Tensorflow
* Andra Azure-tekniker

Du hittar länken till exemplen på hem sidan för notebook när du har autentiserat dig för Jupyter Notebook med hjälp av lösen ordet som du skapade i ett tidigare steg.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM innehåller Visual Studio Community. Det här är en kostnads fri version av den populära IDE-versionen från Microsoft som du kan använda för utvärderings syfte och små team. Se den [licensvillkor](https://www.visualstudio.com/support/legal/mt171547).

Öppna Visual Studio med hjälp av Skriv bords ikonen eller **Start** -menyn. Sök efter program (Win + S), följt av **Visual Studio**. Därifrån kan skapa du projekt på språk som C#, Python, R och node.js. Installerade plugin-program gör det enkelt att arbeta med följande Azure-tjänster:

* Azure Data Catalog
* Azure HDInsight Hadoop och Spark
* Azure Data Lake

Det finns också ett plugin-program som kallas **Azure Machine Learning för Visual Studio-kod** som sömlöst kan integreras med Azure Machine Learning och hjälper dig att snabbt bygga AI-program.

> [!NOTE]
> Du kan få ett meddelande om att utvärderingsperioden har upphört att gälla. Ange autentiseringsuppgifterna för ditt Microsoft-konto. Eller skapa ett nytt kostnadsfritt konto för att få åtkomst till Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM levereras med en Developer-version av SQL Server 2017 med Machine Learning Services. Den här SQL Server versionen kommer antingen i R eller python och kan köras i databas analys. Machine Learning Services tillhandahåller en plattform för utveckling och distribution intelligenta program. Du kan använda dessa språk och många paket från communityn för att skapa modeller och genererar förutsägelser för SQL Server-data. Du kan behålla analytics nära data eftersom Machine Learning Services, i databasen, integrerar både R och Python språk i SQL Server. Den här integreringen slipper du kostnaderna och säkerhetsrisker som förknippas med dataförflyttning.

> [!NOTE]
> SQL Server Developer-utgåvan är endast för utveckling och testning. Du behöver en licens för att köra den i produktion.

Du kan komma åt SQL Server genom att starta Microsoft SQL Server Management Studio. VM-name har fyllts i som den **servernamn**. Använd Windows-autentisering när du loggar in som administratör på Windows. När du använder SQL Server Management Studio kan du skapa andra användare, skapa databaser, importera data och köra SQL-frågor.

Om du vill aktivera databasanalys med SQL Machine Learning Services, kör du följande kommando som en engångsåtgärd i SQL Server Management Studio när du har loggat in som serveradministratör:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Ersätt `%COMPUTERNAME%` med namnet på den virtuella datorn.

### <a name="azure"></a>Azure

Flera Azure-verktygen är installerade på den virtuella datorn:

* En genväg på skrivbordet går till Azure SDK-dokumentationen.
* Använd **AzCopy** för att kopiera data in och ut ur ditt Azure Storage-konto. Om du vill se användning, ange **Azcopy** i en kommandotolk.
* Använd **Azure Storage Explorer** att bläddra bland de objekt som lagras i Azure Storage-kontot. Den kopierar även data till och från Azure Storage. För att komma åt det här verktyget anger du **Storage Explorer** i **Sök** fältet. Eller hitta den på **Start** -menyn i Windows.
* **AdlCopy** kopierar data till Azure Data Lake. Om du vill se användning, ange **adlcopy** i en kommandotolk.
* **dtui** kopierar data till och från Azure Cosmos DB, en NoSQL-databas i molnet. Ange **dtui** i en kommandotolk.
* **Azure Data Factory integration runtime** kopierar data mellan lokala data källor och molnet. Den används i verktyg som Azure Data Factory.
* Använd **Microsoft Azure PowerShell** för att administrera dina Azure-resurser i PowerShell-skript språket. Det är också installeras på den virtuella datorn.

### <a name="power-bi"></a>Power BI

DSVM levereras med **Power BI Desktop** installerat för att hjälpa dig att bygga instrument paneler och visualiseringar. Använda verktyget för att hämta data från olika källor, skapa dina instrumentpaneler och rapporter och publicera dem till molnet. Mer information finns i den [Power BI](https://powerbi.microsoft.com) plats. Du kan hitta Power BI Desktop på **Start** -menyn.

> [!NOTE]
> Du behöver ett Microsoft Office 365-konto till Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Azure Machine Learning-tjänsten Python SDK

Data forskare och AI-utvecklare använder Azure Machine Learning SDK för python för att skapa och köra Machine Learning-arbetsflöden med [tjänsten Azure Machine Learning](../service/overview-what-is-azure-ml.md). Du kan interagera med tjänsten i Jupyter-anteckningsböcker eller en annan python IDE med hjälp av ramverk för öppen källkod som TensorFlow och scikit-lär.

Kom igång med hjälp av Python-SDK, se [använda Python för att komma igång med Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Python SDK är förinstallerat på Microsoft Data Science Virtual Machine.

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
| Microsoft Machine Learning Server (fristående) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Standardinstans R, Machine Learning Server (fristående) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL-Maskininlärningstjänster i databasinstans directory | C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Diverse verktyg | C:\dsvm\tools |

> [!NOTE]
> På Windows Server 2012-versionen av DSVM och Windows Server 2016-versionen före mars 2018 är standardmiljön för Anaconda Python 2.7. Den sekundära miljön är python 3,5, som finns på **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Nästa steg

* Utforska verktygen på dator för datavetenskap genom att välja den **starta** menyn.
* Lär dig mer om Azure Machine Learning-tjänsten genom att läsa [vad är Azure Machine Learning-tjänsten?](../service/overview-what-is-azure-ml.md) och testa den [snabbstarter och självstudier](../service/index.yml) som är tillgängliga.
* I Utforskaren navigerar du till **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** for samples som använder RevoScaleR-biblioteket i R som stöder data analys i företags skalan.  
* Läs artikeln [tio saker som du kan göra på den virtuella datorn för datavetenskap](https://aka.ms/dsvmtenthings).
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](../team-data-science-process/index.yml).
* Gå till den [Azure AI-galleriet](https://gallery.cortanaintelligence.com) för machine learning och data analytics-exempel som använder Azure Machine Learning och relaterade data services på Azure. Vi har också lagt till en ikon för det här galleriet på den **starta** -menyn och skrivbordet för den virtuella datorn.
