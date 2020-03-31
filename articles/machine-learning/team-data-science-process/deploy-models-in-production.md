---
title: Distribuera modeller i produktion - Team Data Science Process
description: Hur man distribuerar modeller till produktion så att de kan spela en aktiv roll i att fatta affärsbeslut.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722245"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Distribuera modeller till produktion för att spela en aktiv roll i affärsbeslut

Produktionsdistribution gör det möjligt för en modell att spela en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för affärsbeslut.

## <a name="production-platforms"></a>Produktionsplattformar

Det finns olika metoder och plattformar för att sätta modeller i produktion. Här är några alternativ:

- [Var du kan distribuera modeller med Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Före distributionen måste man försäkra att svarstiden för modellbedömning är tillräckligt låg för att användas i produktionen.
>

>[!NOTE]
>För distribution med Azure Machine Learning Studio finns i [Distribuera en Azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-testning

När flera modeller är i produktion kan [A/B-testning](https://en.wikipedia.org/wiki/A/B_testing) användas för att jämföra modellprestanda. 
 
## <a name="next-steps"></a>Nästa steg

Genomgångar som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och länkas med miniatyrbeskrivningar i artikeln [Exempelgenomgångar.](walkthroughs.md) De illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 
