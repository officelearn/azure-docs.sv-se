---
title: "Hur du använder GPU för Azure Machine Learning | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder grafiska bearbetning av enheter (GPU) för att träna djupa neurala nätverk i Azure Machine Learning-arbetsstationen."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 552a4c9565bef62bb1aa7071b88473feb21a05f8
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Hur du använder GPU i Azure Machine Learning
Grafisk grafikprocessor (GPU) används ofta för att utföra beräkningsmässigt beräkningsintensiva aktiviteter som vanligtvis kan inträffa när utbildning vissa djupa neurala nätverket modeller. Genom att använda GPU-kort kan minska du tiden utbildning av modeller avsevärt. I det här dokumentet beskrivs hur du konfigurerar Azure ML-arbetsstationen för att använda [DSVM (datavetenskap virtuell dator)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) utrustade med GPU-kort som mål för körning. 

## <a name="prerequisites"></a>Krav
- Om du vill gå igenom den här instruktioner, måste du första [installera Azure ML-arbetsstationen](quickstart-installation.md).
- Du behöver ha åtkomst till datorer med NVidia GPU-kort.
    - Du kan köra skripten direkt på den lokala datorn (Windows eller macOS) med GPU-kort.
    - Du kan också köra skript i en dockerbehållare på en dator med GPU-kort.

## <a name="execute-in-local-environment-with-gpus"></a>Kör i _lokala_ miljö med GPU-kort
Du kan installera Azure ML-arbetsstationen på en dator med GPU-kort och köra mot _lokala_ miljö. Detta kan vara:
- En fysisk dator Windows eller macOS.
- En virtuell Windows-dator (virtuell dator), till exempel en Windows-DSVM etablerats med hjälp av mallen för virtuella datorer i Azure NC-serien.

I detta fall finns ingen särskild konfiguration som krävs i Azure ML-arbetsstationen. Se till att du har alla nödvändiga drivrutiner, verktyg och GPU-aktiverade maskininlärning bibliotek installeras lokalt. Bara köras mot _lokala_ miljö där Python-körning har direkt åtkomst till lokala GPU-maskinvara.

1. Öppna AML arbetsstationen. Gå till **filen** och **öppnar du kommandotolken**. 
2. Kommandoraden för att installera GPU-aktiverade djup learning framework som Microsoft kognitiva Toolkit, TensorFlow och osv. Exempel:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Skriv Python-kod som utnyttjar djup learning bibliotek.
4. Välj _lokala_ som compute-miljö och köra Python-kod.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Kör i _docker_ miljö på Linux VM med GPU-kort
Azure ML-arbetsstationen stöder också körning i Docker i en Azure Linux-VM. Här har du ett bra alternativ för att köra beräkningsmässigt beräkningsintensiva på kraftfulla virtuell maskinvara och betalar bara för användningen genom att inaktivera den när du är klar. Det är möjligt att använda GPU-kort på en virtuell Linux-dator i princip, medföljer DSVM Ubuntu-baserade obligatoriska CUDA drivrutiner och bibliotek förväg installerad gör på hur mycket enklare. Följ de nedanstående steg:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Skapa en baserat på Ubuntu Linux Data vetenskap virtuell dator i Azure
1. Öppna webbläsaren och gå till den [Azure-portalen](https://portal.azure.com)

2. Välj **+ ny** till vänster i portalen.

3. Sök efter ”datavetenskap virtuell dator för Linux (Ubuntu)” i marketplace.

4. Klicka på **skapa** att skapa en Ubuntu DSVM.

5. Fyll i den **grunderna** formulär med informationen som krävs.
När du väljer en plats för den virtuella datorn, Observera att GPU virtuella datorer är bara tillgängliga i vissa Azure områden, till exempel **södra centrala USA**. Se [compute produkter som är tillgängliga efter region](https://azure.microsoft.com/en-us/regions/services/).
Klicka på OK om du vill spara den **grunderna** information.

6. Välj storleken på den virtuella datorn. Välj en av storlekar med NC-prefixet virtuella datorer, som är utrustade med NVidia GPU-minnet.  Klicka på **visa alla** att se en fullständig lista efter behov. Lär dig mer om [GPU-utrustade virtuella Azure-datorer](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu).

7. Slutför de återstående inställningarna och granska informationen om inköp. Klicka på Köp för att skapa den virtuella datorn. Anteckna den IP-adress som tilldelats den virtuella datorn. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Skapa ett nytt projekt i Azure ML-arbetsstationen 
Du kan använda den _klassificera MNIST med TensorFlow_ exempelvis eller _klassificera MNIST datamängd med CNTK_ exempel.

### <a name="create-a-new-compute-target"></a>Skapa ett nytt Compute-mål
Starta kommandoraden från Azure ML-arbetsstationen. Ange följande kommando. Ersätt platshållartexten från exemplet nedan med egna värden för namn, IP-adress, användarnamn och lösenord. 

```batch
C:\MyProj> az ml computetarget attach --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" --type remotedocker
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Konfigurera Azure ML-arbetsstationen för att få åtkomst till GPU
Gå tillbaka till projektet och öppna **Filvyn**, och träffar i **uppdatera** knappen. Nu visas två nya konfigurationsfiler `my_dsvm.compute` och `my_dsvm.runconfig`.
 
Öppna den `my_dsvm.compute`. Ändra den `baseDockerImage` till `microsoft/mmlspark:plus-gpu-0.7.9` och lägga till en ny rad `nvidiaDocker: true`. Filen måste därför ha dessa två rader:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Öppna `my_dsvm.runconfig`, ändra `Framework` värde från `PySpark` till `Python`. Om du inte ser den här raden, lägga till den, eftersom standardvärdet `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Referens för djup Learning Framework 
Öppna `conda_dependencies.yml` filen och kontrollera att du använder GPU-versionen av djup learning framework Python-paket. Exempelprojekten lista CPU-versioner, så du behöver göra den här ändringen.

Exempel TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Exempel för Microsoft kognitiva Toolkit:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

Du kan också använda 1 bitars SGD versionen av Microsoft kognitiva Toolkit som ger bättre prestanda på flera GPU virtuella datorer. Observera [licenskrav för 1 bitars SGD](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Genomförande
Du är nu redo att köra Python-skript. Du kan köra dem i Azure ML-arbetsstationen med hjälp av den `my_dsvm` kontext, eller så kan det startas från kommandoraden:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Om du vill verifiera att GPU används, granska kör utdata om du vill se något som liknar följande:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Grattis! Skriptet harnessed bara power GPU via en dockerbehållare!

## <a name="next-steps"></a>Nästa steg
Se ett exempel på hur du använder GPU för att öka takten djupa neurala nätverket kurser på Azure ML-galleriet.
