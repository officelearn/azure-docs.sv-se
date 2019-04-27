---
title: Automatiserad ML-algoritmen och val av justering
titleSuffix: Azure Machine Learning service
description: Lär dig hur Azure Machine Learning-tjänsten kan automatiskt välja en algoritm för dig och skapa en modell från den för att spara tid genom att använda parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 620dbd22613df37fdc3c20e34906684446b2251f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821178"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

Automatiserad machine learning är tar träningsdata med ett definierat mål-funktionen och går igenom kombinationer av algoritmer och val av funktioner att automatiskt välja modellen med bäst för dina data baserat på poäng för utbildning. De traditionella machine learning-modell utvecklingsprocessen är mycket resurskrävande och kräver betydande domän kunskap och tid att köra och jämför resultatet från flera olika modeller. Automatiserad machine learning förenklar processen genom att generera modeller justerade från mål och begränsningar som du har definierat för ditt experiment, till exempel tiden för experiment att köra eller som modeller för att blockeringslista.

## <a name="how-it-works"></a>Hur det fungerar

1. Du konfigurerar typ av machine learning problem du försöker lösa. Kategorier av övervakad inlärning stöds:
   + Klassificering
   + Regression
   + Prognosticering

   När automatisk machine learning är allmänt tillgänglig **prognosmodellen funktionen är fortfarande i förhandsversion.**

   Se den [listan över modeller](how-to-configure-auto-train.md#select-your-experiment-type) Azure Machine Learning kan försöka vid utbildning.

1. Du kan ange käll- och format för träningsdata. Data som ska visas och kan lagras i din utvecklingsmiljö eller i Azure Blob Storage. Om data lagras i din utvecklingsmiljö, måste den vara i samma katalog som dina utbildningsskript. Den här katalogen kopieras till beräkningsmål som du väljer för utbildning.

    Data kan läsas in Numpy matriser eller en Pandas-dataframe i din utbildning-skript.

    Du kan konfigurera delningsalternativ för att välja utbildning och verifiering eller du kan ange separata datauppsättningar för utbildning och verifiering.

1. Konfigurera den [beräkningsmålet](how-to-set-up-training-targets.md) som används för att träna modellen.

1. Konfigurera automatisk machine learning-konfiguration. Detta styr de parametrar som används som Azure Machine Learning itererar över olika modeller, inställningar för finjustering, och vilka mått som ska titta på när du avgör den bästa modellen

1. Skicka ett utbildnings kör.

Vid träning skapar Azure Machine Learning-tjänsten ett antal pipelines som prova olika algoritmer och parametrar. Det slutar när den når gränsen för iteration som du anger, eller när den når målvärdet för det mått som du anger.

[![Automatiserad Machine learning](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Du kan granska loggade kör informationen, som innehåller mätvärden som samlats in under körningen. Kör utbildning ger också ett Python-serialiserat objekt (`.pkl` fil) som innehåller modellen och dataförbearbetning.


## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk Machine Learning:

+ [Självstudie: Automatiskt träna en modell för klassificering med Azure automatiserad Machine Learning](tutorial-auto-train-models.md)

+ [Notebook-exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

+ [Använda automatisk utbildning på en fjärransluten resurs](how-to-auto-train-remote.md)

+ [Konfigurera inställningar för automatisk utbildning](how-to-configure-auto-train.md)
