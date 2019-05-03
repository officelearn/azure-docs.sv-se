---
title: 'Köra Python-skriptet: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen kör Python-skript i Azure Machine Learning-tjänsten för att köra Python-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029137"
---
# <a name="execute-python-script-module"></a>Köra Python-skriptet modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att köra Python-kod. Mer information om arkitektur och design-principerna för Python finns i [i följande artikel.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Med Python, kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler som:

+ Visualisera data med hjälp av `matplotlib`
+ Med hjälp av Python-biblioteken att räkna upp modeller i din arbetsyta
+ Läsa, inläsning och manipulera data från källor som inte stöds av den [importdata](./import-data.md) modul
+ Kör din egen kod för djupinlärning 


Azure Machine Learning använder autentiseringstrafikens Anaconda Python, som innehåller många vanliga verktyg för att bearbeta. Vi kan uppdatera Anaconda versionen automatiskt. Aktuella versionen är:
 -  Anaconda 4.5 + distribution för Python 3.6 

Förinstallerade paket är:
-  asn1crypto==0.24.0
- för granskningsspår == 19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- kryptografi == 2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm == 2.2.3
- more-itertools==6.0.0
- numpy == 1.16.2
- pandas==0.24.2
- Konkav == 6.0.0
- PIP == 19.0.3
- pyarrow == 0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- begäranden == 2.21.0
- scikit-Läs == 0.20.3
- scipy == 1.2.1
- installationsverktyg == 40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- hjul == 0.33.1 

 Att installera andra paket inte i listan med förinstallerade, till exempel *scikit-diverse*, Lägg till följande kod i skriptet: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Hur du ska använda detta

Den **kör Python-skript** modulen innehåller Python-exempelkoden som du kan använda som startpunkt. Så här konfigurerar du den **kör Python-skript** modulen, som du anger en uppsättning indata och Python-koden i för att köra den **Python-skriptet** textrutan.

1. Lägg till den **kör Python-skript** modulen i experimentet.

2. Lägg till och koppla på **Dataset1** datauppsättningar från det gränssnitt som du vill använda för indata. Referera till den här datauppsättningen i Python-skriptet som **DataFrame1**.

    Användning av en datauppsättning är valfritt, om du vill generera data med hjälp av Python eller Använd Python-kod för att importera data direkt i modulen.

    Den här modulen kan lägga till en andra datauppsättning på **Dataset2**. Referera till andra datauppsättningen i Python-skriptet som DataFrame2.

    Datauppsättningar som lagras i Azure Machine Learning automatiskt konverteras till **pandas** data.frames vid har lästs in med den här modulen.

    ![Köra Python inkommande karta](media/module/python-module.png)

4. För att inkludera nya Python-paket eller kod, lägger du till den komprimerade filen som innehåller dessa anpassade resurser på **skriptet paket**. Indata till **skriptet paket** måste vara en ZIP-fil redan överförd till din arbetsyta. 

    Alla filer som finns i det överförda zippade arkivet kan användas vid körning av experiment. Om arkivet innehåller en katalogstruktur, strukturen bevaras, men du måste har lagt till en katalog med namnet **src** till sökvägen.

5. I den **Python-skriptet** textrutan skriver eller klistrar du in giltiga Python-skriptet.

    Den **Python-skriptet** textrutan är förifylld med anvisningar i kommentarer och exempelkod för dataåtkomst och utdata. **Du måste redigera eller ersätta den här koden.** Var noga med att följa Python konventioner om indraget och gemener och versaler.

    + Skriptet måste innehålla en funktion som heter `azureml_main` som startpunkt för den här modulen.
    + Funktionens startadress kan innehålla upp till två inkommande argument: `Param<dataframe1>` och `Param<dataframe2>`
    + Komprimerade filer som är anslutna till den tredje indataporten packat och lagras i katalogen, `.\Script Bundle`, vilket läggs också till Python `sys.path`. 

    Därför om zip-filen innehåller `mymodule.py`, importera det med `import mymodule`.

    + Två datauppsättningar kan returneras till gränssnittet, vilket måste vara en sekvens av typen `pandas.DataFrame`. Du kan skapa andra utdata i Python-koden och skriver dem direkt till Azure storage.

6. Kör experimentet, eller väljer du modulen och klickar på **kör valda** att köra bara Python-skriptet.

    Alla data och kod läses in i en virtuell dator och kör med hjälp av den angivna Python-miljön.

## <a name="results"></a>Resultat

Resultatet av alla beräkningar som utförs av en inbäddad Python-kod måste anges som en pandas. Nu när dataramen, som automatiskt konverteras till formatet Azure Machine Learning datauppsättningen så att du kan använda resultatet med andra moduler i experimentet.

Modulen returnerar två datauppsättningar:  
  
+ **Resulterar datauppsättning 1**har definierats av den första returnerade pandas-dataframe i Python-skriptet

+ **Resultera datauppsättning 2**har definierats av andra returnerade pandas-dataframe i Python-skriptet


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 