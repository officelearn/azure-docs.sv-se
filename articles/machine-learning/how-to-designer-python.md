---
title: Python
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Python i Azure Machine Learning designer för att omvandla data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455799"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Kör Python-kod i Azure Machine Learning-designer

I den här artikeln får du lära dig hur du använder modulen [Kör Python-skript](algorithm-module-reference/execute-python-script.md) för att lägga till anpassad logik i Azure Machine Learning-designern. I följande instruktioner använder du Pandas-biblioteket för att göra enkel funktionsteknik.

Du kan använda den inbyggda kodredigeraren för att snabbt lägga till enkel Python-logik. Om du vill lägga till mer komplex kod eller ladda upp ytterligare Python-bibliotek bör du använda zip-filmetoden.

Standardkörningsmiljön använder Anacondas-distributionen av Python. En fullständig lista över förinstallerade paket finns på [referenssidan för körning av Python-skriptmodulen.](algorithm-module-reference/execute-python-script.md)

![Kör Python-indatakarta](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Kör Python skrivet i designern

### <a name="add-the-execute-python-script-module"></a>Lägga till modulen Kör Python-skript

1. Hitta modulen **Kör Python-skript** i designerpaletten. Den finns i avsnittet **Python-språk.**

1. Dra och släpp modulen på pipeline-arbetsytan.

### <a name="connect-input-datasets"></a>Ansluta indatauppsättningar

Den här artikeln använder exempeldatauppsättningen **Automobile price data (Raw)**. 

1. Dra och släpp datauppsättningen till pipeline-arbetsytan.

1. Anslut datauppsättningens utdataport till indatamodulens **Execute Python Script** indataport överst till vänster. Designern exponerar indata som en parameter för startpunktsskriptet.
    
    Rätt indataport är reserverad för pythonbibliotek med zippade.

    ![Ansluta datauppsättningar](media/how-to-designer-python/connect-dataset.png)
        

1. Notera vilken indataport du använder. Designern tilldelar den vänstra indataporten till `dataset1` `dataset2`variabeln och den mellersta indataporten till . 

Indatamoduler är valfria eftersom du kan generera eller importera data direkt i modulen **Kör Python-skript.**

### <a name="write-your-python-code"></a>Skriv din Python-kod

Designern tillhandahåller ett första startpunktsskript där du kan redigera och ange din egen Python-kod. 

I det här exemplet använder du Pandor för att kombinera två kolumner som finns i bildatauppsättningen, **Pris** och **Hästkrafter**, för att skapa en ny kolumn, **Dollar per hästkrafter**. Denna kolumn representerar hur mycket du betalar för varje hästkrafter, vilket kan vara en användbar funktion för att avgöra om en bil är en bra affär för pengarna. 

1. Välj modulen **Kör Python-skript.**

1. Markera textrutan **Python-skript** i fönstret som visas till höger om arbetsytan.

1. Kopiera och klistra in följande kod i textrutan.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Pipelinen ska se ut som följande:
    
    ![Kör Python-pipeline](media/how-to-designer-python/execute-python-pipeline.png)

    Startpunktsskriptet måste `azureml_main`innehålla funktionen . Det finns två funktionsparametrar som mappas till de två indataportarna för modulen **Kör Python-skript.**

    Returvärdet måste vara en Pandas Dataframe. Du kan returnera upp till två dataramar som modulutdata.
    
1. Skicka pipelinen.

Nu har du en datauppsättning med den nya funktionen **Dollar / HP**, vilket kan vara användbart i utbildning en bil recommender. Detta är ett exempel på funktionen extraktion och dimensionalitet minskning. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [importerar dina egna data](how-to-designer-import-data.md) i Azure Machine Learning designer.