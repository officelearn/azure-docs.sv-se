---
title: 'Referens: Windows DSVM'
description: Information om verktyg som ingår i Windows Data Science VM
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200013"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Referens: Windows Data Science Virtual Machine

Nedan visas en lista över tillgängliga verktyg på Windows-Data Science Virtual Machine. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Du kan använda Microsoft Enterprise Library för din analys eftersom Machine Learning Server Developer Edition är installerat på den virtuella datorn. Machine Learning Server som tidigare kallades Microsoft R Server, är en mycket distributions bara analys plattform. Den är skalbar och stöds kommersiellt.

Machine Learning Server har stöd för olika stordatastatistik, förutsägande modellering och maskininlärning uppgifter. Det har stöd för en fullständig uppsättning analytics: utforskning, analys, visualisering och modellering. Genom att använda och utökar R och Python för öppen källkod, är Machine Learning Server kompatibel med R och Python-skript och funktioner. Det är också kompatibla med CRAN, pip och Conda-paket för att analysera data i enterprise-skala.

Machine Learning Server löser minnesbegränsningarna för R med öppen källkod genom att lägga till parallell och segmenterat bearbetning av data. Det innebär att du kan köra analyser på mycket större data än vad som passar i huvud minnet. 

Visual Studio Community ska tas med på den virtuella datorn. Den har R Tools för Visual Studio-och Python Tools for Visual Studio-tillägg (PTVS) som tillhandahåller en fullständig IDE för att arbeta med R eller python. Vi tillhandahåller även andra IDE: er som [RStudio](https://www.rstudio.com) och [pycharm med Community Edition](https://www.jetbrains.com/pycharm/) på den virtuella datorn.

## <a name="python"></a>Python

För utveckling med hjälp av python installeras Anaconda python-distributioner 2,7 och 3,6. Dessa distributioner har grundläggande Python tillsammans med ungefär 300 av de mest populära analyspaket matematiska, teknik och data. Du kan använda PTVS, som installeras i Visual Studio Community 2017. Eller så kan du använda en av IDE: er som är paketerad med Anaconda, t. ex. inaktiv eller Spyder. Sök efter och öppna ett av dessa paket (Windows-tangenten + S).

> [!NOTE]
> Om du vill peka Python-verktygen för Visual Studio på Anaconda Python 2.7, måste du skapa anpassade miljöer för varje version. Om du vill ange dessa miljö Sök vägar i Visual Studio 2017-communityn går du till **verktyg** > **python tools** > **python-miljöer**. Välj sedan **+ anpassad**.

Anaconda python 3,6 installeras under C:\Anaconda. Anaconda python 2,7 installeras under C:\Anaconda\envs\python2. Detaljerade anvisningar finns i PTVS- [dokumentationen](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>Jupyter-anteckningsbok

Anaconda-distribution levereras också med Jupyter Notebook, en miljö för att dela kod och analys. Jupyter Notebook-server är förkonfigurerad med Python 2.7, Python 3.x, PySpark, Julia och R-kärnor. Starta Jupyter-servern och öppna webbläsaren för att få åtkomst till Notebook-servern med hjälp av ikonen **Jupyter Notebook** skriv bord.

Vi paketera flera exempelanteckningsböcker i Python och R. När du har åtkomst till Jupyter visar hur du arbetar med följande tekniker de bärbara datorerna:

* Machine Learning Server
* SQL Server Machine Learning Services, i databas analys
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Andra Azure-tekniker

Du hittar länken till exemplen på hem sidan för notebook när du har autentiserat dig för Jupyter Notebook med hjälp av lösen ordet som du skapade tidigare.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM innehåller Visual Studio Community. Det här är en kostnads fri version av den populära IDE-versionen från Microsoft som du kan använda för utvärderings syfte och små team. Se [licens villkoren för program vara från Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Öppna Visual Studio med hjälp av Skriv bords ikonen eller **Start** -menyn. Sök efter program (Windows-tangenten + S) följt av **Visual Studio**. Därifrån kan du skapa projekt på språk som C#, python, R och Node. js. Installerade plugin-program gör det enkelt att arbeta med följande Azure-tjänster:

* Azure Data Catalog
* Azure HDInsight för Hadoop och Spark
* Azure Data Lake

Ett plugin-program som kallas Azure Machine Learning för Visual Studio Code integreras också med Azure Machine Learning och hjälper dig att snabbt bygga AI-program.

> [!NOTE]
> Du kan få ett meddelande om att utvärderingsperioden har upphört att gälla. Ange autentiseringsuppgifterna för ditt Microsoft-konto. Eller skapa ett nytt kostnadsfritt konto för att få åtkomst till Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

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

## <a name="azure"></a>Azure

Flera Azure-verktygen är installerade på den virtuella datorn:

* En genväg på skrivbordet går till Azure SDK-dokumentationen.
* Använd AzCopy för att kopiera data in och ut ur ditt Azure Storage-konto. Om du vill se användning, ange **Azcopy** i en kommandotolk.
* Använd Azure Storage Explorer för att bläddra igenom de objekt som du lagrar på ditt Azure Storage-konto. Den kopierar även data till och från Azure Storage. För att komma åt det här verktyget anger du **Storage Explorer** i **Sök** fältet. Eller hitta på Windows **starta** menyn.
* AdlCopy kopierar data till Azure Data Lake. Om du vill se användning, ange **adlcopy** i en kommandotolk.
* Dtui-verktyget kopierar data till och från Azure Cosmos DB, en NoSQL-databas i molnet. Ange **dtui** i en kommandotolk.
* Integrerings körningen kopierar data mellan lokala data källor och molnet. Den används i verktyg som Azure Data Factory.
* Använd Azure PowerShell för att administrera dina Azure-resurser i PowerShell-skript språket. Det är också installeras på den virtuella datorn.

## <a name="power-bi"></a>Power BI

DSVM levereras med Power BI Desktop installerat för att hjälpa dig att bygga instrument paneler och visualiseringar. Använda verktyget för att hämta data från olika källor, skapa dina instrumentpaneler och rapporter och publicera dem till molnet. Mer information finns på webbplatsen för [Power BI](https://powerbi.microsoft.com). Du kan hitta Power BI Desktop på **Start** -menyn.

> [!NOTE]
> Du behöver ett Microsoft Office 365-konto till Power BI.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK för python

Data forskare och AI-utvecklare använder Azure Machine Learning SDK för python för att skapa och köra Machine Learning-arbetsflöden med [tjänsten Azure Machine Learning](../service/overview-what-is-azure-ml.md). Du kan interagera med tjänsten i Jupyter-anteckningsböcker eller en annan python IDE med hjälp av ramverk för öppen källkod som TensorFlow och scikit-lär.

Python SDK är förinstallerat på Microsoft Data Science Virtual Machine. Om du vill börja använda python SDK läser [du Använd python för att komma igång med Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Fler utvecklings verktyg för Microsoft

Du kan använda [installations programmet för Microsoft Web Platform](https://www.microsoft.com/web/downloads/platform.aspx) för att hitta och hämta andra verktyg för Microsoft-utveckling. Det finns också en genväg till verktyget på Microsoft Data Science Virtual Machine Desktop.  

## <a name="important-directories-on-the-virtual-machine"></a>Viktiga kataloger på den virtuella datorn

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

Har du fler frågor? Överväg att skapa ett [support ärende](https://azure.microsoft.com/support/create-ticket/).
