---
title: Vad är automatisk Maskininlärning – Azure Machine Learning-tjänsten
description: Läs mer om hur Azure Machine Learning-tjänsten kan automatiskt välja en algoritm för dig och skapa en modell från att spara tid genom att använda parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4b9356c7cba14cf5bd112c7d7ae1aab2842fb9d1
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53010900"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

I den här artikeln får du lära dig om automatisk machine learning. Azure Machine Learning-tjänsten kan välja en algoritm för dig, och automatiskt generera en modell från den. Automatiserad maskininlärning sparar du tid genom att generera modeller justerade från mål och begränsningar som du har definierat för ditt experiment, till exempel tiden för experiment att köra eller som modeller för att svartlista.

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
