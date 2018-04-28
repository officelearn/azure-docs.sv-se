---
title: Distribuera modeller i produktionen – Azure Machine Learning | Microsoft Docs
description: Så här distribuerar du modeller för produktion, så att de kan spela upp en aktiv roll i att göra affärsbeslut.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: bradsev
ms.openlocfilehash: 681f227a04802c4e27e42083c1eb63f620a8fc77
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="deploy-models-in-production"></a>Distribuera modeller i produktion

Produktionsdistribution gör det möjligt för en modell att spela upp en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för affärsbeslut.

## <a name="production-platforms"></a>Produktion plattformar
Det finns olika strategier och plattformar för att placera modeller i produktionen. Här följer några alternativ:


- [Distribution av modellen i Azure Machine Learning](../desktop-workbench/model-management-overview.md)
- [Distribution av en modell i SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)


>[!NOTE]
>Före distributionen för en är att försäkra dig om svarstiden för modellen bedömningen är tillräckligt små för att använda i produktionen.
>


>[!NOTE]
>Distribution med hjälp av Azure Machine Learning Studio finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B-testning
När det finns flera modeller i produktion kan det vara bra att utföra [A / B-testning](https://en.wikipedia.org/wiki/A/B_testing) att jämföra prestanda av modeller. 

 
## <a name="next-steps"></a>Nästa steg

Genomgång som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) artikel. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 
 


