---
title: Skapa en Windows virtuell dator för datavetenskap
titleSuffix: Azure
description: Konfigurera och skapa en virtuell dator för datavetenskap på Azure för analys och maskininlärning.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: gokuma
ms.openlocfilehash: 1f9ee5cf28de8fdb824bebf222e5e8d80e22c34f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712429"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Etablera en Windows Data Science-dator på Azure

Microsoft Windows Data Science Virtual Machine (DSVM) är en Windows Server 2016-dator (VM)-avbildning i Azure som är förinstallerat och har konfigurerats med verktyg för dataanalys och maskininlärning.

## <a name="included-data-science-tools"></a>Ingår verktyg för datavetenskap

Följande verktyg ingår i en DSVM:

* [Azure Machine Learning-tjänsten](../service/index.yml) Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition
* Anaconda Python-distribution
* Jupyter-anteckningsbok med R, Python och PySpark-kärnor
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 Developer edition
* En fristående Apache Spark-instans för lokal utveckling och testning
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Machine learning och data analytics-verktyg:
  * Ramverk för djupinlärning - ett stort utbud av AI-ramverk finns på den virtuella datorn: [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, och Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) – snabb machine learning-system som har stöd för tekniker som hash-online, allreduce, sänkt, learning2search och aktiva och interaktiv utbildning
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) – ett verktyg som ger snabba och exakta bättre trädet implementering
  * [Rattle](https://togaware.com/rattle/) -R analysverktyg som hjälper dig att komma igång med dataanalys och maskininlärning i R. Det omfattar GUI-baserade datagranskning och modellering med automatisk generering av klientkod R.
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) -visual datautvinning och machine learning-programvara i Java
  * [Apache Drill](https://drill.apache.org/) – en schemafria SQL-frågemotor för Apache Hadoop-, NoSQL- och molnlagring. Det har stöd för ODBC- och JDBC-gränssnitt för frågor till NoSQL och filer från standard BI-verktyg som Power BI, Microsoft Excel och Tableau.
* Bibliotek i R och Python för använder i Azure Machine Learning och andra Azure-tjänster
* Git, inklusive Git Bash, att arbeta med lagringsutrymmen för källkod som omfattar GitHub och Azure DevOps. Git innehåller flera populära Linux-kommandoradsverktyg som är tillgängliga både på Git Bash och en kommandotolk. Exempel är awk, sed, perl, grep, Sök, wget eller curl.

### <a name="about-data-science"></a>Om datavetenskap

Datavetenskap omfattar iteration av en serie uppgifter:

1. Hitta, läsa in och Förbearbeta data
1. Skapa och testa modeller
1. Distribuera modeller för användning i intelligenta program

Dataexperter kan du använda flera verktyg för dessa uppgifter. Det kan ta lång tid att hitta rätt versioner av programvaran och sedan ladda ned och installera den. DSVM sparar tid genom att tillhandahålla en färdiga att använda-avbildning som kan etableras på Azure med flera populära verktyg förinstallerat och har konfigurerats.

DSVM jump-starts ditt analysprojekt. Du kan arbeta med aktiviteter på olika språk, inklusive R, Python, SQL och C#. Visual Studio tillhandahåller en enkel att använda integrerad utvecklingsmiljö (IDE) för att utveckla och testa din kod. Azure SDK ingår i den virtuella datorn så att du kan skapa dina program med hjälp av olika tjänster på Microsofts molnplattform.

Det finns inga programvaruavgifter för den här data science VM-avbildning. Du betalar bara användningen av Azure-avgifter. De beror på storleken på den virtuella datorn som du etablerar. Mer information om beräkning avgifter finns i den **prisinformation** avsnittet på den [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) sidan.

### <a name="other-dsvm-versions"></a>Andra DSVM-versioner

* En [Ubuntu](dsvm-ubuntu-intro.md) bild. Har många verktyg som liknar DSVM plus några ytterligare deep learning-ramverk.
* En [Linux CentOS](linux-dsvm-intro.md) bild.
* Den [Windows Server 2012-versionen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) av den virtuella datorn för datavetenskap. Det finns några verktyg endast på Windows Server 2016-versionen. Annars kan gäller den här artikeln även för Windows Server 2012-versionen.

## <a name="prerequisite"></a>Krav

Om du vill skapa en Microsoft Data Science Virtual Machine, måste du ha en Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.com/free).

## <a name="create-your-dsvm"></a>Skapa din DSVM

Skapa en DSVM-instans:

1. Gå till den virtuella datorn på den [Azure-portalen](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). Du kan uppmanas att logga in på ditt Azure-konto om du redan inte är inloggad.
1. Välj den **skapa** längst ned.

   ![Konfigurera –--virtuell dator för datavetenskap](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Du måste ange följande information för att konfigurera var och en av de steg som visas i den högra rutan i skärmbilden:

   1. **Grunderna**:
      * **Namn på**: namnet på DSVM
      * **Typ av virtuell Datordisk**: antingen **SSD** eller **HDD**. En NC_v1 GPU-instans som NVidia Tesla K80 baserat, Välj **HDD** som typ av disk.
      * **Användarnamnet**: admin konto-ID
      * **Lösenord**: administratörslösenordet
      * **Prenumeration**: Om du har mer än en prenumeration väljer du det som datorn är skapas och faktureras.
      * **Resursgrupp**. Du kan skapa en ny eller Använd en befintlig grupp.
      * **Plats**. Välj det datacenter som är mest lämplig. Snabbaste nätverksåtkomst är det datacenter som har de flesta av dina data eller som är närmast dina fysiska plats.
   1. **Storlek**: Välj en av de servertyper som uppfyller dina funktionella krav och begränsningar för kostnad. Fler alternativ med VM-storlekar väljer **visa alla**.
   1. **Inställningar för**:  
      * **Använda hanterade diskar**. Välj **hanterade** om du vill att Azure för att hantera diskar för den virtuella datorn. Om inte, måste du ange ett nytt eller befintligt lagringskonto.  
      * **Andra parametrar**. Du kan använda standardvärdena. Om du vill använda icke-standardvärden hovra över informationsmeddelande länken Hjälp om specifika fält.
   1. **Sammanfattning**: Kontrollera att all information som du angett är korrekt. Välj **Skapa**.

> [!NOTE]
> * Den virtuella datorn inte några ytterligare avgifter utöver beräkningskostnaden för serverstorlek som du valde i den **storlek** steg.
> * Etableringen tar cirka 10 till 20 minuter. Du kan visa statusen för din virtuella dator på Azure portal.

## <a name="how-to-access-the-dsvm"></a>Hur du kommer åt DSVM

När den virtuella datorn skapas och etableras, kan du fjärrskrivbord till den med hjälp av autentiseringsuppgifter som administratör-konto som du konfigurerade i föregående **grunderna** avsnittet. Du är redo att börja använda verktygen som installeras och konfigureras på den virtuella datorn. Många av verktygen kan nås via start-menyn paneler och ikoner på skrivbordet.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Verktygen som installeras på Microsoft Data Science Virtual Machine

Läs mer om de verktyg som finns installerad på DSVM:

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Du kan använda Microsoft Enterprise Library för skalbar R eller Python för analys, eftersom Machine Learning Server Developer edition är installerat på den virtuella datorn. Machine Learning Server är, som tidigare kallades Microsoft R Server, ett brett distribuerbara, analysplattform i företagsklass. Det finns för både R och Python. Det är också skalbar, kommersiellt stöd och säker.

Machine Learning Server har stöd för olika stordatastatistik, förutsägande modellering och maskininlärning uppgifter. Det har stöd för en fullständig uppsättning analytics: utforskning, analys, visualisering och modellering. Genom att använda och utökar R och Python för öppen källkod, är Machine Learning Server kompatibel med R och Python-skript och funktioner. Det är också kompatibla med CRAN, pip och Conda-paket för att analysera data i enterprise-skala.

Machine Learning Server löser minnesbegränsningarna för R med öppen källkod genom att lägga till parallell och segmenterat bearbetning av data. Det innebär att du kan köra analyser i mycket större data än vad som passar i huvudminnet. Visual Studio Community ska tas med på den virtuella datorn. Den har R tools för Visual Studio och Python Tools för Visual Studio (PTVS)-tillägg som ger en fullständig IDE för att arbeta med R eller Python. Vi ger även andra IDE: er som [RStudio](https://www.rstudio.com) och [PyCharm Community edition](https://www.jetbrains.com/pycharm/) på den virtuella datorn.

### <a name="python"></a>Python

För utveckling med hjälp av Python är 2.7 och 3.6 Anaconda Python-distributioner installerade. Dessa distributioner har grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data. Du kan använda PTVS som installeras i Visual Studio Community 2017. Du kan också använda en av IDE: er som levereras tillsammans med Anaconda som INAKTIV eller Spyder. Sök efter och starta en av dessa paket (Win + S).

> [!NOTE]
> Om du vill peka Python-verktygen för Visual Studio på Anaconda Python 2.7, måste du skapa anpassade miljöer för varje version. Ange sökvägarna miljö i Visual Studio 2017 Community och navigera till **verktyg** > **Python Tools** > **Python-miljöer**. Välj sedan **+ anpassad**.

Anaconda Python 3.6 är installerat **C:\Anaconda**. Anaconda Python 2.7 är installerat **c:\Anaconda\envs\python2**. Detaljerade anvisningar finns i [dokumentationen till PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

### <a name="the-jupyter-notebook"></a>Jupyter-anteckningsbok

Anaconda distribution levereras också med Jupyter-anteckningsboken en miljö för att dela kod och analys. Jupyter Notebook-server är förkonfigurerad med Python 2.7, Python 3.x, PySpark, Julia och R-kärnor. Om du vill starta Jupyter-server och starta om webbläsaren för att komma åt notebook-server, använder den **Jupyter Notebook** skrivbordsikonen.

Vi paketera flera exempelanteckningsböcker i Python och R. När du har åtkomst till Jupyter visar hur du arbetar med följande tekniker de bärbara datorerna:

* Machine Learning Server
* SQL Server Machine Learning Services analys i databasen
* Python
* Microsoft Cognitive ToolKit
* Tensorflow
* Andra Azure-tekniker

Du hittar en länk till exempel på startsidan för bärbar dator när du autentiserar till Jupyter-anteckningsboken med hjälp av lösenordet som du skapade i ett tidigare steg.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM innehåller Visual Studio Community. Det här är en kostnadsfri version av populära IDE från Microsoft som du kan använda för utvärderingssyfte och små team. Se den [licensvillkor](https://www.visualstudio.com/support/legal/mt171547).

Öppna Visual Studio med hjälp av ikonen skrivbord eller **starta** menyn. Sök efter program (Win + S), följt av **Visual Studio**. Därifrån kan skapa du projekt på språk som C#, Python, R och node.js. Installerade plugin-program gör det enkelt att arbeta med följande Azure-tjänster:

* Azure Data Catalog
* Azure HDInsight Hadoop och Spark
* Azure Data Lake

Det finns också ett plugin-program kallas **Azure Machine Learning för Visual Studio Code** som smidigt kan integreras till Azure Machine Learning och hjälper dig att snabbt skapa AI-program.

> [!NOTE]
> Du kan få ett meddelande om att utvärderingsperioden har upphört att gälla. Ange autentiseringsuppgifterna för ditt Microsoft-konto. Eller skapa ett nytt kostnadsfritt konto för att få åtkomst till Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

DSVM levereras med en Utvecklarversionen av SQL Server 2017 med Machine Learning Services. Den här versionen av SQL Server finns i R eller Python och kan köra analyser i databasen. Machine Learning Services tillhandahåller en plattform för utveckling och distribution intelligenta program. Du kan använda dessa språk och många paket från communityn för att skapa modeller och genererar förutsägelser för SQL Server-data. Du kan behålla analytics nära data eftersom Machine Learning Services, i databasen, integrerar både R och Python språk i SQL Server. Den här integreringen slipper du kostnaderna och säkerhetsrisker som förknippas med dataförflyttning.

> [!NOTE]
> SQL Server Developer-utgåvan är endast för utveckling och testning. Du behöver en licens för att köra den i produktion.

Du kan komma åt SQL Server genom att starta Microsoft SQL Server Management Studio. VM-name har fyllts i som den **servernamn**. Använd Windows-autentisering när du loggar in som administratör på Windows. När du använder SQL Server Management Studio kan du skapa andra användare, skapa databaser, importera data och köra SQL-frågor.

Om du vill aktivera databasanalys med SQL Machine Learning Services, kör du följande kommando som en engångsåtgärd i SQL Server Management Studio när du har loggat in som serveradministratör:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Ersätt `%COMPUTERNAME%` med namnet på din virtuella dator.

### <a name="azure"></a>Azure

Flera Azure-verktygen är installerade på den virtuella datorn:

* En genväg på skrivbordet går till Azure SDK-dokumentationen.
* Använd **AzCopy** att kopiera data till och från Azure Storage-kontot. Om du vill se användning, ange **Azcopy** i en kommandotolk.
* Använd **Azure Storage Explorer** att bläddra bland de objekt som lagras i Azure Storage-kontot. Det kan också kopierar data till och från Azure Storage. Om du vill komma åt det här verktyget kan du ange **Lagringsutforskaren** i den **Search** fält. Eller hitta det i Windows **starta** menyn.
* **Adlcopy** kopierar data till Azure Data Lake. Om du vill se användning, ange **adlcopy** i en kommandotolk.
* **dtui** kopierar data till och från Azure Cosmos DB, en NoSQL-databas i molnet. Ange **dtui** i en kommandotolk.
* **Azure Data Factory Integration Runtime** kopierar data mellan molnet och lokala datakällor. Den används i verktyg som Azure Data Factory.
* Använd **Microsoft Azure PowerShell** att administrera dina Azure-resurser i PowerShell-skriptspråket. Det är också installeras på den virtuella datorn.

### <a name="power-bi"></a>Power BI

DSVM levereras med **Power BI Desktop** installerat för att hjälpa dig att skapa instrumentpaneler och visualiseringar. Använda verktyget för att hämta data från olika källor, skapa dina instrumentpaneler och rapporter och publicera dem till molnet. Mer information finns i den [Power BI](https://powerbi.microsoft.com) plats. Du hittar Power BI Desktop på den **starta** menyn.

> [!NOTE]
> Du behöver ett Microsoft Office 365-konto till Power BI.

### <a name="azure-machine-learning-service-python-sdk"></a>Azure Machine Learning-tjänsten Python SDK

Dataanalytiker och AI-utvecklare använder du Azure Machine Learning-SDK för Python för att skapa och köra machine learning-arbetsflöden med den [Azure Machine Learning-tjänsten](../service/overview-what-is-azure-ml.md). Du kan interagera med i Jupyter-anteckningsböcker eller en annan Python IDE-tjänsten med hjälp av ramverk med öppen källkod, till exempel TensorFlow och scikit-Läs.

Kom igång med hjälp av Python-SDK, se [använda Python för att komma igång med Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

Python SDK är förinstallerat på Microsoft Data Science Virtual Machine.

## <a name="more-microsoft-development-tools"></a>Fler Microsoft-utvecklingsverktyg

Du kan använda den [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) att söka efter och hämta andra Microsoft-utvecklingsverktyg. Det finns också en genväg till verktyget Microsoft Data Science Virtual Machine-skrivbordet.  

## <a name="important-directories-on-the-vm"></a>Viktiga kataloger på den virtuella datorn

| Objekt | Katalog |
| --- | --- |
| Jupyter Notebook server-konfigurationer | C:\ProgramData\jupyter |
| Arbetskatalog för Jupyter Notebook-exempel | C:\dsvm\notebooks och c:\users\\< användarnamn\>\notebooks |
| Andra exempel | C:\dsvm\samples |
| Anaconda, standard: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 miljö | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (fristående) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Standardinstans R, Machine Learning Server (fristående) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL-Maskininlärningstjänster i databasinstans directory | C:\Program Files\Microsoft SQL Server\MSSQL14. MSSQLSERVER |
| Diverse verktyg | c:\dsvm\tools |

> [!NOTE]
> På Windows Server 2012-versionen av DSVM och Windows Server 2016-versionen före mars 2018 är standardmiljön för Anaconda Python 2.7. Den sekundära miljön är Python 3.5, finns på **C:\Anaconda\envs\py35**.

## <a name="next-steps"></a>Nästa steg

* Utforska verktygen på dator för datavetenskap genom att välja den **starta** menyn.
* Lär dig mer om Azure Machine Learning-tjänsten genom att läsa [vad är Azure Machine Learning-tjänsten?](../service/overview-what-is-azure-ml.md) och testa den [snabbstarter och självstudier](../service/index.yml) som är tillgängliga.
* I Utforskaren går du till **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** för exempel som använder sig av RevoScaleR library i R som har stöd för dataanalys i enterprise-skala.  
* Läs artikeln [tio saker som du kan göra på den virtuella datorn för datavetenskap](https://aka.ms/dsvmtenthings).
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](../team-data-science-process/index.yml).
* Gå till den [Azure AI-galleriet](https://gallery.cortanaintelligence.com) för machine learning och data analytics-exempel som använder Azure Machine Learning och relaterade data services på Azure. Vi har också lagt till en ikon för det här galleriet på den **starta** -menyn och skrivbordet för den virtuella datorn.
