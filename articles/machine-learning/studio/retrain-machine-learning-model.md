---
title: "Träna om Machine Learning-modellen | Microsoft Docs"
description: "Lär dig mer om att träna om en modell och uppdatera webbtjänsten för att använda den nya tränade modellen i Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: serinakaye
ms.author: serinak
manager: mwinkle
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 65c5e86e47528aab36972507b7ee8324040b9beb
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="retrain-a-machine-learning-model"></a>Träna om Machine Learning-modellen
Som en del av processen för operationalization för machine learning-modeller i Azure Machine Learning, din modell tränas och sparas. Du sedan använda den för att skapa en predicative webbtjänst. Webbtjänsten kan sedan användas i webbplatser, instrumentpaneler och mobilappar. 

Modeller som du skapar med Machine Learning finns vanligtvis inte statisk. När nya data blir tillgängliga eller när konsumenten API har sina egna data måste vara retrained modellen. 

Omtränings kan inträffa ofta. Med funktionen programmässiga Omtränings-API du programmässigt träna om modellen med Omtränings-API och uppdatera webbtjänsten med den nyligen tränade modellen. 

Det här dokumentet beskrivs hur du omtränings och visar hur du använder Omtränings-API.

## <a name="why-retrain-defining-the-problem"></a>Träna om varför: definiera problemet
Som en del av maskininlärning utbildning process, är en modell tränas med hjälp av en uppsättning data. Modeller som du skapar med Machine Learning finns vanligtvis inte statisk. När nya data blir tillgängliga eller när konsumenten API har sina egna data måste vara retrained modellen.

I dessa scenarier ger programmässiga API ett bekvämt sätt att låta dig eller konsumenter av din API: er att skapa en klient som kan, på en gång eller regelbunden basis träna om modellen med sina egna data. De kan sedan utvärdera resultaten av omtränings och uppdatera Web service API för att använda den nya tränade modellen.

> [!NOTE]
> Om du har en befintlig Träningsexperiment och nya Web service kanske du vill checka ut omtrimning en befintlig förutsägande webbtjänst i stället för att följa den här genomgången som nämns i följande avsnitt.
> 
> 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
Processen omfattar följande komponenter: A Träningsexperiment och Prediktivt Experiment publiceras som en webbtjänst. Om du vill aktivera omtränings av en tränad modell måste utbildning experimentet publiceras som en webbtjänst med utdata från en tränad modell. Detta gör det möjligt för API-åtkomst till modellen för omtränings. 

Följande steg gäller för både nya och klassiska Web services:

Skapa första förutsägande webbtjänsten:

* Skapa ett experiment utbildning
* Skapa en prediktiv web experiment
* Distribuera en prediktiv webbtjänst

Träna om webbtjänsten:

* Uppdatera träningsexperiment för omtränings
* Distribuera omtränings-webbtjänst
* Använda Batch Execution Service-kod för att träna om modellen

En genomgång av den föregående steg finns [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

Om du har distribuerat en klassiska webbtjänst:

* Skapa en ny slutpunkt på förutsägande webbtjänsten
* Hämta korrigering URL och kod
* Använda PATCH-URL så att den pekar på ny slutpunkt på retrained modellen 

En genomgång av den föregående steg finns [träna om en klassiska webbtjänst](retrain-a-classic-web-service.md).

Om du stöter på problem med omtränings klassiska webbtjänsten finns [felsökning omtränings av en webbtjänst för Azure Machine Learning klassiska](troubleshooting-retraining-models.md).

Om du har distribuerat en ny webbtjänst:

* Logga in på Azure Resource Manager-konto
* Hämta Web service definition
* Exportera Web Service Definition som JSON
* Uppdatera referens till den `ilearner` blob i JSON
* Importera JSON till en Web Service Definition
* Uppdatera webbtjänsten med nya Web Service Definition

En genomgång av den föregående steg finns [träna om en ny webbtjänst med hjälp av Machine Learning Management PowerShell-cmdlets](retrain-new-web-service-using-powershell.md).

Processen för att ställa in omtränings för det klassiska omfattar följande steg:

![Översikt över omtränings][1]

Processen för att ställa in omtränings för en ny webbtjänst omfattar följande steg:

![Översikt över omtränings][7]

## <a name="other-resources"></a>Andra resurser
* [Omtränings och uppdaterar Azure Machine Learning-modeller med Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Skapa många Machine Learning-modeller och web service slutpunkter från ett experiment med hjälp av PowerShell](create-models-and-endpoints-with-powershell.md)
* Den [AML Omtränings modeller med hjälp av API: er](https://www.youtube.com/watch?v=wwjglA8xllg) videon visar hur du träna om Machine Learning-modeller som skapats i Azure Machine Learning med Omtränings-API: er och PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

