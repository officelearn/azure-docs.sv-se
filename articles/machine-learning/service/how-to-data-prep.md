---
title: Förbereda data med Machine Learning Data Prep SDK för Python – Azure
description: Lär dig hur du använder Azure Machine Learning Data Prep SDK för Python för att läsa in data av olika format, omvandla den till att vara mer användbara och skriva dessa data till en plats för dina modeller att få åtkomst till.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 83373f5d6e4a2227bcafdbc4b25b686b0b8d651d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867195"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Förbereda data för modellering med Azure Machine Learning
 
Förberedelse av data är en viktig del av ett machine learning-arbetsflöde. Dina modeller blir mer korrekta och effektiva om de har åtkomst till rensa data i ett format som är enklare att använda. 

Du kan förbereda dina data i Python med hjälp av den [Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Pipeline för förberedelse av data

De viktigaste stegen för dataförberedelse är:

1. [Läsa in data](how-to-load-data.md), vilket kan vara i olika format
2. [Omvandla](how-to-transform-data.md) den i en mer användbara struktur
3. [Skriva](how-to-write-data.md) dessa data till en plats som är tillgänglig för dina modeller

![Processen att förbereda data](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Nästa steg
Granska en [exempel notebook](https://github.com/Microsoft/MSDataPrepDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) för förberedelse av data med hjälp av Azure Machine Learning Data Prep SDK.
