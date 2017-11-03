---
title: "Språk för datavetenskap virtuell dator på Azure | Microsoft Docs"
description: "Språk för datavetenskap virtuell dator på Azure"
keywords: "datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 2f2125e739b738847e03ce429d65801969611685
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Språk som stöds på den datavetenskap virtuella datorn 

Den virtuella datorn på vetenskap (DSVM) innehåller flera fördefinierade språk och utvecklingsverktyg för att skapa AI-program. Här är några av viktigaste de. 

## <a name="python"></a>Python

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | 2.7 och 3.5 |
| Stöds DSVM versioner      | Linux, Windows     |
| Hur är det konfigurerade / installerad på DSVM?  | Två globala `conda` miljöer skapas. <br /> * `root`miljö finns i `/anaconda/` är Python 2.7. <br/> * `py35`miljö finns i `/anaconda/envs/py35`är Python 3.5       |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker för Python ingår     |
| Relaterade verktyg på DSVM      | PySpark R Julia      |
### <a name="how-to-use--run-it"></a>Hur du använder / kör den?    

**Windows**:

* Körs i Kommandotolken

Öppna Kommandotolken och gör följande beroende på vilken version av Python som du vill köra. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Använda i IDE-miljö

Använd Python Tools för Visual Studio (PTVS) installeras i Visual Studio Community edition. Endast miljö installationsprogrammet automatiskt i PTVS i Python 2.7. 
> [!NOTE]
> För att peka på PTVS på Python 3.5, måste du skapa en anpassad miljö i PTVS. Om du vill ställa in den här miljön sökvägar i Visual Studio Community Edition, gå till **verktyg** -> **Python Tools** -> **Python-miljöer** och klicka sedan på **+ anpassad**. Ange plats för `c:\anaconda\envs\py35` och klicka sedan på _automatisk identifiering_. 

* Använda i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Nu kan du välja kernel-typ som _Python [Conda Root]_ för Python 2.7 och _Python [Conda env:py35]_ för Python 3.5-miljö. 

* Installera Python-paket

Standard Python-miljöer på DSVM är globala miljön som kan läsas av alla användare. Men endast administratörer kan skriva / globala paket installeras. För att kunna installera paketet till den globala miljön, aktivera till rot- eller py35 miljö med den `activate` kommandot som administratör. Du kan använda package manager som `conda` eller `pip` att installera eller uppdatera paket. 


**Linux**:

* Kör i terminal

Öppna terminal och gör följande beroende på vilken version av Python som du vill köra. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Använda i IDE-miljö

Använd PyCharm installerad i Visual Studio Community edition. 

* Använda i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Nu kan du välja kernel-typ som _Python [Conda Root]_ för Python 2.7 och _Python [Conda env:py35]_ för Python 3.5-miljö. 

* Installera Python-paket

Standard Python-miljöer på DSVM är globala miljöer kan läsas av alla användare. Men endast administratörer kan skriva / globala paket installeras. För att kunna installera paketet till den globala miljön, aktivera till rot- eller py35 miljö med den `source activate` kommandot som administratör eller en användare med sudo-behörighet. Du kan använda en Pakethanteraren som `conda` eller `pip` att installera eller uppdatera paket. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | Microsoft R öppna 3.x (100% kompatibel med R CRAN<br /> Microsoft R Server 9.x Developer edition (en skalbar Enterprise redo R platform)|
| Stöds DSVM versioner      | Linux, Windows     |
| Hur är det konfigurerade / installerad på DSVM?  | Windows:`C:\Program Files\Microsoft\R Server\R_SERVER` <br />Linux:` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker för R ingår     |
| Relaterade verktyg på DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Hur du använder / kör den?    

**Windows**:

* Körs i Kommandotolken

Öppna Kommandotolken och skriv `R`.

* Använda i IDE-miljö

Använd RTools för Visual Studio (RTVS) installerats i Visual Studio Community edition eller RStudio. Dessa är tillgängliga på start-menyn eller som en skrivbordsikon. 

* Använda i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Nu kan du välja kernel-typ som _R_ att använda Jupyter R kernel (IRKernel). 

* Installera R-paket

R har installerats på DSVM i en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva / globala paket installeras. Kör R med någon av metoderna ovan för att kunna installera paketet till den globala miljön. Sedan kör du R package manager `install.packages()` att installera eller uppdatera paket. 

**Linux**:

* Kör i terminal

Öppna terminal och bara kör `R`.  

* Använda i IDE-miljö

Använd RStudio installerad på Linux DSVM.  

* Använda i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Nu kan du välja kernel-typ som _R_ att använda Jupyter R kernel (IRKernel). 

* Installera R-paket

R har installerats på DSVM i en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva / globala paket installeras. Kör R med någon av metoderna ovan för att kunna installera paketet till den globala miljön. Sedan kör du R package manager `install.packages()` att installera eller uppdatera paket. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Språkversioner stöds | 0.5 |
| Stöds DSVM versioner      | Linux, Windows     |
| Hur är det konfigurerade / installerad på DSVM?  | Windows: Installerad på`C:\JuliaPro-VERSION`<br /> Linux: Installerad på`/opt/JuliaPro-VERSION`    |
| Länkar till exempel      | Exempel Jupyter-anteckningsböcker för Julia ingår     |
| Relaterade verktyg på DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Hur du använder / kör den?    

**Windows**:

* Körs i Kommandotolken

Öppna Kommandotolken och kör du bara `julia`. 
* Använda i IDE-miljö

Använd `Juno` Julia IDE installeras på DSVM och tillgänglig som en genväg på skrivbordet.

* Använda i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Nu kan du välja kernel-typ som`Julia VERSION` 

* Installera Julia paket

Standard Julia plats är en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva / globala paket installeras. Kör Julia med någon av metoderna ovan för att kunna installera paketet till den globala miljön. Sedan kör du Julia package manager-kommandon som `Pkg.add()` att installera eller uppdatera paket. 


**Linux**:
* Körs i terminal.

Öppna terminal och bara kör `julia`. 
* Använda i IDE-miljö

Använd `Juno` Julia IDE installeras på DSVM och tillgänglig som en genväg-menyn.

* Använda i Jupyter

Öppna Jupyter och klicka på den `New` för att skapa en ny anteckningsbok. Nu kan du välja kernel-typ som`Julia VERSION` 

* Installera Julia paket

Standard Julia plats är en global miljö som kan läsas av alla användare. Men endast administratörer kan skriva / globala paket installeras. Kör Julia med någon av metoderna ovan för att kunna installera paketet till den globala miljön. Sedan kör du Julia package manager-kommandon som `Pkg.add()` att installera eller uppdatera paket. 

## <a name="other-languages"></a>Andra språk

**C#**: tillgänglig i Windows och kan nås via Visual Studio Community edition eller på en `Developer Command Prompt for Visual Studio` där du kan bara köra `csc` kommando. 

**Java**: OpenJDK är tillgänglig för både Linux och Windows-utgåva av DSVM och ange i sökvägen. Du kan skriva `javac` eller `java` kommando i Kommandotolken i Windows eller bash-gränssnitt i Linux för att använda Java. 

**node.js**: node.js är tillgänglig för både Linux och Windows-utgåva av DSVM och ange i sökvägen. Du kan skriva `node` eller `npm` kommando i Kommandotolken i Windows eller bash-gränssnitt i Linux för att komma åt node.js. Node.js-verktyg för Visual Studio-tillägg installeras på Windows, för att tillhandahålla en grafisk IDE för att utveckla node.js-programmet. 

**F #**: tillgänglig i Windows och kan nås via Visual Studio Community edition eller på en `Developer Command Prompt for Visual Studio` där du kan bara köra `fsc` kommando. 


