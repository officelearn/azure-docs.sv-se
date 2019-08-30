---
title: 'Kör Python-skript: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen kör Python-skript i Azure Machine Learning-tjänsten för att köra python-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 4bd3433db92767f2d0d733ab71e4298fc5e618f8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128814"
---
# <a name="execute-python-script-module"></a>Köra Python-skript modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att köra python-kod. Mer information om arkitektur och design principer för python finns i [följande artikel.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Med python kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler, till exempel:

+ Visualisera data med`matplotlib`
+ Använda python-bibliotek för att räkna upp data uppsättningar och modeller i din arbets yta
+ Läsning, inläsning och manipulering av data från källor som inte stöds av modulen [Importera data](./import-data.md)
+ Kör din egen djup inlärnings kod 


Azure Machine Learning använder Anaconda-distributionen av python, som innehåller många vanliga verktyg för data bearbetning. Vi kommer att uppdatera Anaconda-versionen automatiskt. Aktuell version är:
 -  Anaconda 4.5 + distribution för python 3,6 

De förinstallerade paketen är:
-  asn1crypto==0.24.0
- attr = = 19.1.0
- azure-common==1.1.18
- Azure-Storage-BLOB = = 1.5.0
- azure-storage-common==1.4.0
- certifiera = = 2019.3.9
- cffi==1.12.2
- chardet = = 3.0.4
- kryptografi = = 2.6.1
- distribution = = 1.4.0
- IDNA = = 2,8
- jsonschema = = 3.0.1
- lightgbm = = 2.2.3
- Mer-itertools = = 6.0.0
- numpy = = 1.16.2
- pandas==0.24.2
- Pillow = = 6.0.0
- PIP = = 19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- begär Anden = = 2.21.0
- scikit – lära = = 0.20.3
- scipy = = 1.2.1
- installations verktyg = = 40.8.0
- sex = = 1.12.0
- Torch = = 1.0.1. post2
- torchvision==0.2.2.post3
- urllib3 = = 1.24.1
- Wheel = = 0.33.1 

 Om du vill installera andra paket som inte finns i listan i förväg, till exempel *scikit-misc*, lägger du till följande kod i skriptet: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Hur du ska använda detta

**Execute Python-skript** module innehåller exempel på python-kod som du kan använda som start punkt. Om du vill konfigurera **execute Python-skript** -modulen anger du en uppsättning indata och python-kod som ska köras i text rutan python- **skript** .

1. Lägg till modulen **Kör Python-skript** i experimentet.

2. Lägg till och Anslut på **Dataset1** alla data uppsättningar från gränssnittet som du vill använda för indata. Referera till den här data uppsättningen i python-skriptet som **DataFrame1**.

    Användningen av en data uppsättning är valfri, om du vill generera data med python eller använda python-kod för att importera data direkt till modulen.

    Den här modulen stöder tillägg av en andra data uppsättning på **Dataset2**. Referera till den andra data mängden i python-skriptet som DataFrame2.

    Data uppsättningar som lagras i Azure Machine Learning konverteras automatiskt till **Pandas** -data. bild rutor när de läses in med den här modulen.

    ![Köra python-indatamängds karta](media/module/python-module.png)

4. Om du vill inkludera nya python-paket eller-kod lägger du till den zippade filen som innehåller de här anpassade resurserna i **skript paket**. Indata till **skript paket** måste vara en zippad fil som redan har laddats upp till din arbets yta. 

    Alla filer som finns i det överförda zippade arkivet kan användas under experiment körningen. Om arkivet innehåller en katalog struktur bevaras strukturen, men du måste lägga en katalog med namnet **src** till sökvägen.

5. I text rutan **Python-skript** skriver eller klistrar du in giltigt Python-skript.

    Text rutan **Python-skript** fylls i automatiskt med instruktioner i kommentarer och exempel kod för data åtkomst och utdata. **Du måste redigera eller ersätta den här koden.** Se till att följa python-konventioner om indrag och Skift läge.

    + Skriptet måste innehålla en funktion som heter `azureml_main` som start punkt för den här modulen.
    + Start punkts funktionen kan innehålla upp till två indataargument: `Param<dataframe1>` och`Param<dataframe2>`
    + Zippade filer som är anslutna till den tredje Indataporten är zippade och lagras `.\Script Bundle`i katalogen, som också läggs till i `sys.path`python. 

    Om din zip-fil innehåller `mymodule.py`kan du därför importera den med hjälp av. `import mymodule`

    + Två data uppsättningar kan returneras till gränssnittet, vilket måste vara en sekvens av typen `pandas.DataFrame`. Du kan skapa andra utdata i python-koden och skriva dem direkt till Azure Storage.

6. Kör experimentet eller Välj modulen och klicka på **Kör valt** för att bara köra python-skriptet.

    Alla data och all kod läses in i en virtuell dator och körs med den angivna python-miljön.

## <a name="results"></a>Resultat

Resultaten av alla beräkningar som utförs av den inbäddade python-koden måste anges som Pandas. DataFrame, som automatiskt konverteras till det Azure Machine Learning data uppsättnings formatet, så att du kan använda resultatet med andra moduler i experimentet.

Modulen returnerar två data uppsättningar:  
  
+ **Resultat data uppsättning 1**, som definieras av den första returnerade Pandas-Dataframe i python-skriptet

+ **Resultat data uppsättning 2**, som definieras av den andra returnerade Pandas-Dataframe i python-skriptet


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 