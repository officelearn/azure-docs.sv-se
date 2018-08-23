---
title: Hur du använder GPU för Azure Machine Learning | Microsoft Docs
description: Den här artikeln beskriver hur du använder grafiska bearbetning av enheter (GPU) för att träna djupa neurala nätverk i Azure Machine Learning Workbench.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: f3b6c4f6af14615511400650662fe7a350c172ba
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42061721"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Hur du använder GPU i Azure Machine Learning
Grafiska grafikprocessor (GPU) används ofta för att bearbeta beräkningsintensiva uppgifter som vanligtvis kan inträffa vid utbildning av vissa modeller för djupa neurala nätverk. Genom att använda GPU: er kan minska du utbildning tidpunkten för modellerna avsevärt. I det här dokumentet har du lära dig hur du konfigurerar Azure ML Workbench för att använda [DSVM (Data Science Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) utrustad med GPU: er som körningsmål. 

## <a name="prerequisites"></a>Förutsättningar
- För att gå igenom den här guiden, måste du först [installera Azure ML Workbench](../service/quickstart-installation.md).
- Du måste ha åtkomst till datorer med NVidia GPU: er.
    - Du kan köra dina skript direkt på den lokala datorn (Windows eller Mac OS) med GPU: er.
    - Du kan också köra skript i en Docker-behållare på en Linux-dator med GPU: er...

## <a name="execute-in-local-environment-with-gpus"></a>Kör i _lokala_ miljö med GPU: er
Du kan installera Azure ML Workbench på en dator med GPU: er och kör mot _lokala_ miljö. Detta kan vara:
- En fysisk dator Windows- eller macOS.
- En Windows virtuell dator (VM), till exempel en Windows-DSVM etablerats med hjälp av mall för virtuella datorer i Azure NC-serien.

I detta fall finns ingen särskild konfiguration som krävs i Azure ML Workbench. Kontrollera att du har alla nödvändiga drivrutiner, verktyg och GPU-aktiverade machine learning-bibliotek som installerats lokalt. Bara köras mot _lokala_ miljö där Python-körning kan direkt komma åt lokala GPU-maskinvaran.

1. Öppna AML-arbetsstationen. Gå till **filen** och **öppna Kommandotolken**. 
2. Från kommandoraden, installerar du GPU-aktiverade deep learning-ramverk som Microsoft Cognitive Toolkit, TensorFlow och osv. Exempel:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.5 with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

3. Skriv Python-kod som utnyttjar deep learning-bibliotek.
4. Välj _lokala_ när compute-miljö och köra Python-kod.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Kör i _docker_ -miljön på Linux VM med GPU: er
Azure ML Workbench har också stöd för körning i Docker i en virtuell Linux-dator. Här har du en bra möjlighet att köra beräkningsintensiva jobb på en del av kraftfulla virtuell maskinvara och betalar endast för användningen genom att inaktivera den när du är klar. Det är möjligt att använda GPU: er på alla Linux-dator i princip, Ubuntu-baserad DSVM som levereras med obligatoriska CUDA-drivrutiner och bibliotek som är förinstallerade, vilket gör konfiguration enklare. Följ de stegen nedan:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Skapa en Ubuntu-baserad Linux virtuell dator för datavetenskap i Azure
1. Öppna webbläsaren och gå till den [Azure-portalen](https://portal.azure.com)

2. Välj **+ ny** till vänster på portalen.

3. Sök efter ”Data Science Virtual Machine för Linux (Ubuntu)” i marketplace.

4. Klicka på **skapa** att skapa en Ubuntu-DSVM.

5. Fyll i den **grunderna** formuläret med informationen som krävs.
När du väljer plats för din virtuella dator, Observera att virtuella GPU-datorer finns endast i vissa Azure-regioner, till exempel **södra centrala USA**. Se [compute produkttillgänglighet per region](https://azure.microsoft.com/regions/services/).
Klicka på OK om du vill spara den **grunderna** information.

6. Välj storleken på den virtuella datorn. Välj en av storlekar med NC-prefixet virtuella datorer, som är utrustade med NVidia GPU-kretsar.  Klicka på **visa alla** vill se en fullständig lista efter behov. Läs mer om [GPU-utrustade virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Slutför de återstående inställningarna och granska informationen om inköp. Klicka på Köp för att skapa den virtuella datorn. Anteckna den IP-adressen som tilldelas den virtuella datorn. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Skapa ett nytt projekt i Azure ML Workbench 
Du kan använda den _klassificera MNIST med TensorFlow_ exempelvis eller _klassificera MNIST datauppsättning med CNTK_ exempel.

### <a name="create-a-new-compute-target"></a>Skapa ett nytt Compute-mål
Starta Azure ML Workbench på kommandoraden. Ange följande kommando. Ersätt platshållartext från exemplet nedan med dina egna värden för namn, IP-adress, användarnamn och lösenord. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Konfigurera Azure ML Workbench och åtkomst GPU
Gå tillbaka till projektet och öppna **Filvyn**, och därefter den **uppdatera** knappen. Nu visas två nya konfigurationsfiler `my_dsvm.compute` och `my_dsvm.runconfig`.
 
Öppna den `my_dsvm.compute`. Ändra den `baseDockerImage` till `microsoft/mmlspark:plus-gpu-0.7.9` och Lägg till en ny rad `nvidiaDocker: true`. Så att filen ska innehålla de här två raderna:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Öppna nu `my_dsvm.runconfig`, ändra `Framework` värdet från `PySpark` till `Python`. Om du inte ser den här raden, lägga till det, eftersom standardvärdet `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Referensram för Djupinlärning 
Öppna `conda_dependencies.yml` filen och gör att du använder GPU-versionen av Python-paket för ramverk för djupinlärning. Exempelprojekten lista CPU versioner så att du behöver att göra ändringen.

Exempel för TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Exempel för Microsoft Cognitive Toolkit:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.5 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

### <a name="execute"></a>Genomförande
Du är nu redo att köra Python-skript. Du kan köra dem i Azure ML Workbench med hjälp av den `my_dsvm` kontext eller du kan starta den från kommandoraden:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
För att verifiera att GPU används, granska kör utdata om du vill se något som liknar följande:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Grattis! Skriptet harnessed bara kraften hos GPU via en Docker-behållare!

## <a name="next-steps"></a>Nästa steg
Se ett exempel på hur du använder GPU för att påskynda djupa neurala nätverk träning i Azure ML-galleriet.
