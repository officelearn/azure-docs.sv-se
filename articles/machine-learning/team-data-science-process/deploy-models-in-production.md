---
title: Distribuera modeller i produktion - Team Data Science Process
description: Så här distribuerar modeller till produktion så att de kan spela upp en aktiv roll i att göra affärsbeslut.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 35f9f369e752fa7c86a6bd295a79b79b23104d23
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137879"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Distribuera modeller till produktion för att spela upp en aktiv roll i att göra affärsbeslut

Produktionsdistribution gör det möjligt för en modell att spela upp en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för bättre affärsbeslut.

## <a name="production-platforms"></a>Plattformar för produktion

Det finns olika metoder och plattformar för att använda modeller i produktion. Här följer några alternativ:

- [Var du vill distribuera modeller med Azure Machine Learning-tjänsten](../service/how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Ett måste försäkra dig om svarstiden för bedömning av modellen är låg ska användas i produktion före distributionen.
>

>[!NOTE]
>Distribution med hjälp av Azure Machine Learning Studio finns i [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B-testning

När det finns flera modeller i produktion, kan det vara praktiskt att utföra [A / B-testning](https://en.wikipedia.org/wiki/A/B_testing) att jämföra prestanda för modellerna. 
 
## <a name="next-steps"></a>Nästa steg

Genomgångar som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och som är kopplad till miniatyrbilder beskrivningarna i den [exempel genomgångar](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 
