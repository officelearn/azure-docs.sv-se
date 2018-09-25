---
title: Vad är automatisk Maskininlärning – Azure Machine Learning
description: I den här artikeln får du lära dig om automatisk machine learning. Azure Machine Learning-tjänsten kan välja en algoritm för dig, och automatiskt generera en modell från den. Automatiserad machine learning kan du spara mycket tid med hjälp av parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960047"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

I den här artikeln får du lära dig om automatisk machine learning. Azure Machine Learning-tjänsten kan välja en algoritm för dig, och automatiskt generera en modell från den. Automatiserad machine learning kan du spara mycket tid med hjälp av parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.

## <a name="how-it-works"></a>Hur det fungerar

1. Du konfigurerar typ av machine learning problem du försöker lösa. Två typer av övervakad inlärning stöds:
   + Klassificering
   + Regression

   Se den [listan över modeller](how-to-configure-auto-train.md#select-your-experiment-type) Azure Machine Learning kan försöka vid utbildning.

1. Du kan ange käll- och format för träningsdata. Data som ska visas och kan lagras i din utvecklingsmiljö eller i Azure Blob Storage. Om data lagras i din utvecklingsmiljö, måste den vara i samma katalog som dina utbildningsskript. Den här katalogen kopieras till beräkningsmål som du väljer för utbildning.

    Data kan läsas in Numpy matriser eller en Pandas-dataframe i din utbildning-skript.

    Du kan konfigurera delningsalternativ för att välja utbildning och verifiering eller du kan ange separata datauppsättningar för utbildning och verifiering.

1. Konfigurera den [beräkningsmålet](how-to-set-up-training-targets.md) som används för att träna modellen.

1. Konfigurera automatisk machine learning-konfiguration. Detta styr de parametrar som används som Azure Machine Learning itererar över olika modeller, inställningar för finjustering, och vilka mått som ska titta på när du avgör den bästa modellen. 

1. Skicka ett utbildnings kör.

Vid träning skapar Azure Machine Learning-tjänsten ett antal pipelines som prova olika algoritmer och parametrar. Det slutar när den når gränsen för iteration som du anger, eller när den når målvärdet för det mått som du anger.

[ ![Automatiserad Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Du kan granska loggade kör informationen, som innehåller mätvärden som samlats in under körningen. Kör utbildning ger också ett Python serialiserat objekt (.pkl-fil) som innehåller modellen och dataförbearbetning.

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk Machine Learning:

+ [Självstudie: Automatiskt träna en klassificering modell med Azure automatiserad Machine Learning](tutorial-auto-train-models.md)

+ [Så här konfigurerar du inställningar för automatisk utbildning](how-to-configure-auto-train.md)

+ [Hur du använder automatisk utbildning på en fjärransluten resurs](how-to-auto-train-remote.md) 
