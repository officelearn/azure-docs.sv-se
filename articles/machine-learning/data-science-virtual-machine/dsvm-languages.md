---
title: Språk som stöds för den virtuella datorn för datavetenskap
titleSuffix: Azure
description: Läs mer om programmet språk och relaterade verktyg som har förinstallerats på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 883e1c11e3485c0bf3441a67a3a191563ea3aed4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990792"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Språk som stöds på den virtuella datorn för datavetenskap 

Den virtuella datorn på datavetenskap (DSVM) innehåller flera fördefinierade språk och utvecklingsverktyg för att skapa AI-program. Här är några av viktigaste som. 

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016-versioner)

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | 2.7 och 3.6 |
| Stöds DSVM-versioner      | Windows Server 2016     |
| Hur är det konfigurerade / installerad på DSVM?  | Två globala `conda` miljöer skapas. <br /> * `root` miljö finns på `/anaconda/` är Python 3.6. <br/> * `python2` miljö finns på `/anaconda/envs/python2`är Python 2.7       |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker för Python ingår     |
| Relaterade verktyg på DSVM      | PySpark, R, Julia      |

> [!NOTE]
> Windows Server 2016 som skapats före mars 2018 innehåller Python 3.5 och Python 2.7. Python 2.7 är också conda **rot** miljö och **py35** är Python 3.5-miljö. 

### <a name="how-to-use--run-it"></a>Hur du använder / köra den?    

* Som körs i Kommandotolken

Öppna Kommandotolken och gör följande beroende på vilken version av Python som du vill köra. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Använda i en IDE-miljö

Använd Python Tools för Visual Studio (PTVS) installeras i Visual Studio Community-utgåvan. Den enda miljö installationsprogrammet automatiskt i PTVS som standard är Python 3.6. 

> [!NOTE]
> Om du vill peka exempelmallarna i PTVS på Python 2.7, måste du skapa en anpassad miljö i PTVS. Ange den här miljön sökvägar i Visual Studio Community Edition och navigera till **verktyg** -> **Python Tools** -> **Python-miljöer** och klicka sedan på **+ anpassad**. Anger platsen till `c:\anaconda\envs\python2` och klicka sedan på _automatisk identifiering_. 

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som _Python [Conda Root]_ för Python 3.6 och _Python [Conda env:python2]_ för Python 2.7 miljö. 

* Installerar Python-paket

Standard Python-miljöer på DSVM är global miljö kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. För att kunna installera paketet till den globala miljön, aktivera till rot- eller python2 miljö med hjälp av den `activate` kommandot som administratör. Du kan använda Pakethanteraren som `conda` eller `pip` att installera eller uppdatera paket. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux och Windows Server 2012 Edition)

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | 2.7 och 3.5 |
| Stöds DSVM-versioner      | Linux, Windows Server 2012    |
| Hur är det konfigurerade / installerad på DSVM?  | Två globala `conda` miljöer skapas. <br /> * `root` miljö finns på `/anaconda/` är Python 2.7. <br/> * `py35` miljö finns på `/anaconda/envs/py35`är Python 3.5       |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker för Python ingår     |
| Relaterade verktyg på DSVM      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Hur du använder / köra den?    

**Linux**
* Som körs i terminal

Öppna terminalen och gör följande beroende på vilken version av Python som du vill köra. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Använda i en IDE-miljö

Använd PyCharm installerad i Visual Studio Community-utgåvan. 

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som _Python [Conda Root]_ för Python 2.7 och _Python [Conda env:py35]_ för Python 3.5-miljö. 

* Installerar Python-paket

Standard Python-miljöer på DSVM är globala miljöer kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. För att kunna installera paketet till den globala miljön, aktivera till rot- eller py35 miljö med hjälp av den `source activate` kommandot som administratör eller en användare med sudo-behörighet. Du kan använda en pakethanterare som `conda` eller `pip` att installera eller uppdatera paket. 

**Windows 2012**
* Som körs i Kommandotolken

Öppna Kommandotolken och gör följande beroende på vilken version av Python som du vill köra. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Använda i en IDE-miljö

Använd Python Tools för Visual Studio (PTVS) installeras i Visual Studio Community-utgåvan. Den enda miljö installationsprogrammet automatiskt i PTVS i Python 2.7. 
> [!NOTE]
> Om du vill peka exempelmallarna i PTVS på Python 3.5, måste du skapa en anpassad miljö i PTVS. Ange den här miljön sökvägar i Visual Studio Community Edition och navigera till **verktyg** -> **Python Tools** -> **Python-miljöer** och klicka sedan på **+ anpassad**. Anger platsen till `c:\anaconda\envs\py35` och klicka sedan på _automatisk identifiering_. 

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som _Python [Conda Root]_ för Python 2.7 och _Python [Conda env:py35]_ för Python 3.5-miljö. 

* Installerar Python-paket

Standard Python-miljöer på DSVM är global miljö kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. För att kunna installera paketet till den globala miljön, aktivera till rot- eller py35 miljö med hjälp av den `activate` kommandot som administratör. Du kan använda Pakethanteraren som `conda` eller `pip` att installera eller uppdatera paket. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | Microsoft R Open 3.x (100% kompatibel med R CRAN<br /> Microsoft R Server 9.x Developer edition (en skalbar Enterprise redo R platform)|
| Stöds DSVM-versioner      | Linux, Windows     |
| Hur är det konfigurerade / installerad på DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker för R ingår     |
| Relaterade verktyg på DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Hur du använder / köra den?    

**Windows**:

* Som körs i Kommandotolken

Öppna Kommandotolken och Skriv bara `R`.

* Använda i en IDE-miljö

Använd RTools för Visual Studio (RTVS) installeras i Visual Studio Community edition eller RStudio. Dessa är tillgängliga på start-menyn eller som en skrivbordsikonen. 

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som _R_ att använda Jupyter R kernel (IRKernel). 

* Installera R-paket

R har installerats på DSVM i en global miljö kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. Kör R med någon av ovanstående metoder för att kunna installera paketet till den globala miljön. Sedan kör du R-Pakethanteraren `install.packages()` att installera eller uppdatera paket. 

**Linux**:

* Som körs i terminal

Öppna terminal och kan bara köras `R`.  

* Använda i en IDE-miljö

Använd RStudio som är installerad på Linux DSVM.  

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som _R_ att använda Jupyter R kernel (IRKernel). 

* Installera R-paket

R har installerats på DSVM i en global miljö kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. Kör R med någon av ovanstående metoder för att kunna installera paketet till den globala miljön. Sedan kör du R-Pakethanteraren `install.packages()` att installera eller uppdatera paket. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | 0.6 |
| Stöds DSVM-versioner      | Linux, Windows     |
| Hur är det konfigurerade / installerad på DSVM?  | Windows: Installerad på `C:\JuliaPro-VERSION`<br /> Linux: Installerad på `/opt/JuliaPro-VERSION`    |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker för Julia ingår     |
| Relaterade verktyg på DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Hur du använder / köra den?    

**Windows**:

* Som körs i Kommandotolken

Öppna Kommandotolken och kör du bara `julia`. 
* Använda i en IDE-miljö

Använd `Juno` Julia-IDE installeras på DSVM och finns tillgängligt som en genväg på skrivbordet.

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som `Julia VERSION` 

* Installera paket för Julia

Förvalet Julia platsen är en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. För att kunna installera paketet till den globala miljön, kör du Julia med någon av metoderna ovan. Sedan kör du Julia-package manager-kommandon som `Pkg.add()` att installera eller uppdatera paket. 


**Linux**:
* Kör i terminalen.

Öppna terminal och kan bara köras `julia`. 
* Använda i en IDE-miljö

Använd `Juno` Julia-IDE installeras på DSVM och finns tillgängligt som en genväg menyn.

* Med hjälp av i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Du kan nu välja av kernel-typ som `Julia VERSION` 

* Installera paket för Julia

Förvalet Julia platsen är en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva / installera globala paket. För att kunna installera paketet till den globala miljön, kör du Julia med någon av metoderna ovan. Sedan kör du Julia-package manager-kommandon som `Pkg.add()` att installera eller uppdatera paket. 

## <a name="other-languages"></a>Andra språk

**C#**: Tillgänglig på Windows och kan nås via Visual Studio Community edition eller på en `Developer Command Prompt for Visual Studio` där du kan bara köra `csc` kommando. 

**Java**: OpenJDK är tillgänglig på både Linux och Windows-versionen av DSVM och ange på vägen. Du kan skriva `javac` eller `java` kommando på Kommandotolken i Windows eller på bash-gränssnittet i Linux för att använda Java. 

**node.js**: node.js finns på både Linux och Windows-versionen av DSVM och ange på vägen. Du kan skriva `node` eller `npm` kommando på Kommandotolken i Windows eller på bash-gränssnittet i Linux för att komma åt node.js. Node.js tools for Visual Studio-tillägget installeras på Windows, för att tillhandahålla en grafisk IDE för att utveckla node.js-programmet. 

**F#**: Tillgänglig på Windows och kan nås via Visual Studio Community edition eller på en `Developer Command Prompt for Visual Studio` där du kan bara köra `fsc` kommando. 


