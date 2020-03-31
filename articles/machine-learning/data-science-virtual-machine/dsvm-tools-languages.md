---
title: Språk som stöds
titleSuffix: Azure Data Science Virtual Machine
description: Programspråken som stöds och relaterade verktyg förinstallerade på virtual machine för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283659"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Språk som stöds på virtual machine för datavetenskap 

Data Science Virtual Machine (DSVM) levereras med flera färdiga språk och utvecklingsverktyg för att bygga dina AI-applikationer (Artificial Intelligence). Här är några av de anmärkningsvärda.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016-utgåva)

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | Python 2.7 och 3.7 |
| DSVM-utgåvor som stöds      | Windows Server 2016     |
| Hur är det konfigurerat / installerat på DSVM?  | Två `conda` globala miljöer skapas: <br /> * `root` Miljön som `/anaconda/` finns på är Python 3.7. <br/> * `python2` Miljön som `/anaconda/envs/python2` finns på är Python 2.7.       |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för Python ingår.     |
| Relaterade verktyg på DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Versioner av Windows Server 2016 som skapades före mars 2018 innehåller Python 3.5 och Python 2.7. Python 2.7 är **conda-rotmiljön** och **py37** är Python 3.7-miljön.

### <a name="how-to-use-and-run-it"></a>Så här använder och kör du den    

* Kör i en kommandotolk:

  Öppna en kommandotolk och använd någon av följande metoder, beroende på vilken version av Python du vill köra:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Används i en IDE:

  Använd Python Tools för Visual Studio (PTVS), installerat i Visual Studio Community-utgåvan. Som standard är den enda miljön som ställs in automatiskt i PTVS Python 3.6. 

    > [!NOTE]
    > Om du vill peka PTVS på Python 2.7 måste du skapa en anpassad miljö i PTVS. Om du vill ange den här miljösökvägen i Visual Studio Community Edition går du till **Tools** -> **Python Tools** -> **Python-miljöer** och väljer **+ Anpassad**. Ange sedan platsen till **c:\anaconda\envs\python2** och välj **Identifiera automatiskt**.

* Användning i Jupyter:

  Öppna Jupyter och välj **Nytt** för att skapa en ny anteckningsbok. Du kan ange kärntypen som _Python [Conda Root]_ för Python 3.7 och _Python [Conda env:python2]_ för Python 2.7.

* Installera Python-paket:

  Standardinställningarna för Python på DSVM är globala miljöer som kan läsas av alla användare. Men bara administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön aktiverar `activate` du rot- eller python2-miljön med kommandot som administratör. Sedan kan du använda en `conda` `pip` pakethanterare som eller för att installera eller uppdatera paket.

## <a name="python-linux-edition"></a>Python (Linux-utgåva)

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | Python 2.7 och 3.5 |
| DSVM-utgåvor som stöds      | Linux   |
| Hur är det konfigurerat / installerat på DSVM?  | Två `conda` globala miljöer skapas: <br /> * `root`miljö som `/anaconda/` finns på är Python 2.7. <br/> * `py35`miljö som `/anaconda/envs/py35`finns på är Python 3.5.       |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för Python ingår.     |
| Relaterade verktyg på DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Så här använder och kör du den    

* Kör i en terminal:

  Öppna terminalen och gör något av följande, beroende på vilken version av Python du vill köra:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Används i en IDE:

  Använd PyCharm, som är installerad i Visual Studio Community-utgåvan. 

* Användning i Jupyter:

  Öppna Jupyter och välj **Nytt** för att skapa en ny anteckningsbok. Du kan ange kärntypen som **Python [Conda Root]** för Python 2.7 och **Python [Conda env:py35]** för Python 3.5-miljön. 

* Installera Python-paket:

  Standardinställningarna för Python på DSVM är globala miljöer som kan läsas av alla användare. Men bara administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön aktiverar du `source activate` rot- eller py35-miljön med kommandot som administratör eller som användare med sudo-behörigheter. Sedan kan du använda en `conda` `pip` pakethanterare som eller för att installera eller uppdatera paket.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | Microsoft R Open 3.x (100 % kompatibel med CRAN-R)<br /> Microsoft R Server 9.x Developer Edition (en skalbar R-plattform för företagsberörande)|
| DSVM-utgåvor som stöds      | Linux, Windows     |
| Hur är det konfigurerat / installerat på DSVM?  | Windows:`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux:`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för R ingår.     |
| Relaterade verktyg på DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Så här använder och kör du den    

**Windows**:

* Kör i en kommandotolk:

  Öppna en kommandotolk och skriv `R`.

* Används i en IDE:

  Använd RTools för Visual Studio (RTVS) installerat i Visual Studio Community-utgåvan eller RStudio. Dessa finns på Start-menyn eller som en skrivbordsikon. 

* Användning i Jupyter

  Öppna Jupyter och välj **Nytt** för att skapa en ny anteckningsbok. Du kan ställa in kärntypen som **R** för att använda Jupyter R-kärnan (IRKernel).

* Installera R-paket:

  R är installerat på DSVM i en global miljö som kan läsas av alla användare. Men bara administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du R med hjälp av någon av de föregående metoderna. Sedan kan du köra R-pakethanteraren `install.packages()` för att installera eller uppdatera paket.

**Linux**:

* Kör i terminal:

  Öppna en terminal `R`och kör .  

* Används i en IDE:

  Använd RStudio, installerat på Linux DSVM.  

* Användning i Jupyter:

  Öppna Jupyter och välj **Nytt** för att skapa en ny anteckningsbok. Du kan ställa in kärntypen som **R** för att använda Jupyter R-kärnan (IRKernel). 

* Installera R-paket:

  R är installerat på DSVM i en global miljö som kan läsas av alla användare. Men bara administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du R med hjälp av någon av de föregående metoderna. Sedan kan du köra R-pakethanteraren `install.packages()` för att installera eller uppdatera paket.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | 0.6 |
| DSVM-utgåvor som stöds      | Linux, Windows     |
| Hur är det konfigurerat / installerat på DSVM?  | Windows: Installerat på`C:\JuliaPro-VERSION`<br /> Linux: Installerat på`/opt/JuliaPro-VERSION`    |
| Länkar till exempel      | Exempel på Jupyter-anteckningsböcker för Julia ingår.     |
| Relaterade verktyg på DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Så här använder och kör du den    

**Windows**:

* Köra vid en kommandotolk

  Öppna en kommandotolk och kör `julia`.
* Används i en IDE:

  Använd `Juno` med Julia IDE installerad på DSVM och tillgänglig som en genväg på skrivbordet.

* Användning i Jupyter:

  Öppna Jupyter och välj **Nytt** för att skapa en ny anteckningsbok. Du kan ange kärntypen som **Julia VERSION**.

* Installera Julia-paket:

  Julia-standardplatsen är en global miljö som kan läsas av alla användare. Men bara administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du Julia med någon av de föregående metoderna. Sedan kan du köra Julia package `Pkg.add()` manager-kommandon som att installera eller uppdatera paket.


**Linux**:
* Kör i en terminal:

  Öppna en terminal `julia`och kör .
* Används i en IDE:

  Använd `Juno`, med Julia IDE installerat på DSVM och tillgänglig som en programmenygenväg. **Application**

* Användning i Jupyter:

  Öppna Jupyter och välj **Nytt** för att skapa en ny anteckningsbok. Du kan ange kärntypen som **Julia VERSION**.

* Installera Julia-paket:

  Julia-standardplatsen är en global miljö som kan läsas av alla användare. Men bara administratörer kan skriva och installera globala paket. Om du vill installera paket i den globala miljön kör du Julia med någon av de föregående metoderna. Sedan kan du köra Julia package `Pkg.add()` manager-kommandon som att installera eller uppdatera paket.

## <a name="other-languages"></a>Andra språk

**C#**: Finns i Windows och tillgänglig via `Developer Command Prompt for Visual Studio`Visual Studio Community-utgåvan eller på , där du kan köra `csc` kommandot.

**Java**: OpenJDK finns på både Linux- och Windows-utgåvorna av DSVM och är inställt på sökvägen. Om du vill `javac` använda `java` Java skriver du kommandot eller i en kommandotolk i Windows eller på bash-skalet i Linux.

**Node.js**: Node.js finns på både Linux- och Windows-utgåvorna av DSVM och är inställd på sökvägen. Om du vill komma åt `node` Node.js skriver du kommandot eller `npm` vid en kommandotolk i Windows eller på bash-skalet i Linux. I Windows installeras Visual Studio-tillägget för Node.js-verktygen för att tillhandahålla en grafisk IDE för att utveckla node.js-programmet.

**F#**: Finns i Windows och tillgänglig via `Developer Command Prompt for Visual Studio`Visual Studio Community-utgåvan eller på en , där du kan köra `fsc` kommandot.
