---
title: Omtrimma en Machine Learning Studio-modell – Azure | Microsoft Docs
description: Lär dig hur du tränar en modell och uppdatera webbtjänsten för att använda den nyligen tränade modellen i Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 614a342dd28b711e3030c19f347fa432e3994a86
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257598"
---
# <a name="retrain-an-azure-machine-learning-studio-model"></a>Omtrimning av en Azure Machine Learning Studio-modell
Som en del av processen för driftsättning av machine learning-modeller i Azure Machine Learning, är din modell tränas och sparas. Du sedan använda den för att skapa en förutsägbar webbtjänst. Webbtjänsten kan sedan användas i webbplatser, instrumentpaneler och mobila appar. 

Modeller som du skapar med Machine Learning är vanligtvis inte statiska. När nya data blir tillgängliga eller när användaren av API: et har sina egna data måste vara modellkomponenten modellen. 

Träna kan inträffa ofta. Med funktionen programmatisk omtrimning API du programmässigt tränar modellen med API: erna träna och uppdatera webbtjänsten med den nyligen tränade modellen. 

Det här dokumentet beskriver hur du omtränings och visar hur du Använd Omtränings-API.

## <a name="why-retrain-defining-the-problem"></a>Varför omtrimma: definiera problemet
En del av de machine learning-träningsprocess är tränas en modell med hjälp av en uppsättning data. Modeller som du skapar med Machine Learning är vanligtvis inte statiska. När nya data blir tillgängliga eller när användaren av API: et har sina egna data måste vara modellkomponenten modellen.

I dessa scenarion använder är en programmatisk API ett enkelt sätt att låta dig eller användaren av dina API: er att skapa en klient som kan regelbundet enstaka tillfällen eller regelbundet, träna modellen med sina egna data. De kan sedan utvärdera resultaten av träna och uppdatera den webbtjänst-API om du vill använda den nyligen tränade modellen.

> [!NOTE]
> Om du har en befintlig Träningsexperiment och nya Web service kan du kolla in omtrimning av en befintlig förutsägande webbtjänst i stället för att följa den här genomgången nämns i följande avsnitt.
> 
> 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
Processen består av följande komponenter: Ett Träningsexperiment och ett Förutsägelseexperiment som publiceras som en webbtjänst. Om du vill aktivera omtrimning av en trained model måste Träningsexperimentet publiceras som en webbtjänst med utdata från en tränad modell. Detta gör det möjligt för API-åtkomst till modellen för att träna. 

Följande steg gäller för både nya och klassiska webbtjänster:

Skapa första förutsägbar webbtjänst:

* Skapa ett träningsexperiment
* Skapa ett förutsägelsewebbtjänster experiment
* Distribuera en förutsägbar webbtjänst

Träna om webbtjänsten:

* Uppdatera träningsexperiment så att träna
* Distribuera omtränings webbtjänsten
* Använd Batch Execution Service-kod för att träna modellen

En genomgång av det föregående steg finns i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

Om du har distribuerat en klassiska webbtjänst:

* Skapa en ny slutpunkt på förutsägbar webbtjänst
* Hämta KORRIGERA URL: en och kod
* Använda KORRIGERA URL: en så att den pekar på ny slutpunkt på retrained modellen 

En genomgång av det föregående steg finns i [Omtrimma en klassisk webbtjänst](retrain-a-classic-web-service.md).

Om du stöter på problem med träna en klassisk webbtjänst, se [felsöka omtrimning av en klassisk webbtjänst för Azure Machine Learning](troubleshooting-retraining-models.md).

Om du har distribuerat en ny webbtjänst:

* Logga in på Azure Resource Manager-konto
* Hämta Web service definition
* Exportera Web Service Definition som JSON
* Uppdatera referensen till den `ilearner` blob i JSON
* Importera JSON till en Web Service Definition
* Uppdatera webbtjänsten med nya Web Service Definition

En genomgång av det föregående steg finns i [omtrimning av en ny webbtjänst med hjälp av Machine Learning Management PowerShell-cmdletar](retrain-new-web-service-using-powershell.md).

Processen för att konfigurera retraining för en klassisk webbtjänst omfattar följande steg:

![Översikt över omtränings][1]

Processen för att konfigurera retraining för en ny webbtjänst omfattar följande steg:

![Översikt över omtränings][7]

## <a name="other-resources"></a>Andra resurser
* [Träna och uppdaterar Azure Machine Learning-modeller med Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Skapa många Machine Learning-modeller och webbtjänstslutpunkter från ett experiment med PowerShell](create-models-and-endpoints-with-powershell.md)
* Den [AML träna modeller med hjälp av API: er](https://www.youtube.com/watch?v=wwjglA8xllg) videon visar hur du träna om Machine Learning-modeller som skapats i Azure Machine Learning med Omtränings-API: er och PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

