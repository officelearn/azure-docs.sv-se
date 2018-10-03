---
title: Hantera och distribuera modeller i Azure Machine Learning-tjänsten
description: Lär dig hur du använder Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning-tjänsten på den lokala datorn eller från andra källor.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: afba483172bc34b9d54afc3af755f0967affc875
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239174"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du använder Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning, på din lokala dator eller från andra källor. 

Följande diagram illustrerar arbetsflödet slutförts: [ ![arbetsflöde för distribution för Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Arbetsflödet innehåller följande steg:
1. **Registrera modellen** i ett register som lagras i din arbetsyta för Azure Machine Learning-tjänsten
1. **Registrera en bild** som kombinationer av en modell med ett bedömningsskript och beroenden i en bärbar behållare 
1. **Distribuera** bilden som en webbtjänst i molnet eller edge-enheter
1. **Övervaka och samla in data**

Varje steg kan utföras separat eller som en del av en enskild distribution-kommando. Dessutom kan du integrera distribution i en **CI/CD-arbetsflöde** enligt beskrivningen i den här bilden.

[ ![”Azure Machine Learning kontinuerlig integrering/kontinuerlig distribution (CI/CD) cykel'](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>Steg 1: Registrera modellen

Modellen registret håller reda på alla modeller i din arbetsyta för Azure Machine Learning-tjänsten.
Modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig ökas registret versionen. Du kan också ange ytterligare metadatataggar under registreringen som kan användas när du söker för modeller.

Du kan inte ta bort modeller som används av en avbildning.

## <a name="step-2-register-image"></a>Steg 2: Registrera bild

Avbildningar kan för tillförlitlig modelldistribution, tillsammans med alla komponenter som krävs för att använda modellen. En avbildning innehåller följande objekt:

* Modellen
* Bedömnings motorn
* Bedömnings fil- eller programdata
* Eventuella beroenden som behövs för att poängsätt modellen

Avbildningen kan även inkludera SDK-komponenterna för loggning och övervakning. Data i SDK-loggar kan användas för att finjustera eller träna din modell, inklusive indata och utdata av modellen.

Azure Machine Learning stöder de populäraste ramverken, men i allmänhet valfritt ramverk som kan vara pip installerade fungerar.

När din arbetsyta skapades, så användes andra flera andra Azure-resurser av arbetsytan.
Alla objekt som används för att skapa avbildningen lagras i Azure storage-kontot i din arbetsyta. Avbildningen skapas och lagras i Azure Container Registry. Du kan ange ytterligare metadatataggar när du skapar avbildningen, som lagras också som bild-registret och kan efterfrågas för att hitta din avbildning.

## <a name="step-3-deploy-image"></a>Steg 3: Distribuera avbildningen

Du kan distribuera registrerade avbildningar till molnet eller edge-enheter. Distributionsprocessen skapar alla resurser som behövs för att övervaka, belastningsutjämna och Autoskala din modell. Åtkomst till distribuerade tjänster kan skyddas med certifikatbaserad autentisering genom att tillhandahålla säkerhet-tillgångar under distributionen. Du kan också uppgradera en befintlig distribution om du vill använda en avbildning av en nyare.

Webbtjänstdistributioner är också sökbara. Du kan exempelvis söka efter alla distributioner för en viss modell eller en bild.

[ ![Inferensjobb mål](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Du kan distribuera dina avbildningar för följande [distributionskanaler](how-to-deploy-and-where.md) i molnet:

* Azure Container-instans
* Azure Kubernetes Service
* Azure FPGA-datorer
* Azure IoT Edge-enheter

När tjänsten har distribuerats inferensjobb-begäran är automatiskt Utjämning av nätverksbelastning och klustret skalas för att uppfylla eventuella toppar på begäran. [Telemetri om din tjänst](https://docs.microsoft.com/python/api/azureml-telemetry/azureml.telemetry?view=azure-ml-py) kan samlas in på Azure Application Insights-tjänsten som är associerade med din arbetsyta.

## <a name="step-4-monitor-models-and-collect-data"></a>Steg 4: Övervaka modeller och samla in data

Ett SDK för modellen loggning och data capture är tillgänglig så att du kan övervaka indata, utdata och andra relevanta data från din modell. Data lagras som en blob i Azure Storage-kontot för arbetsytan.

Om du vill använda SDK: N med din modell importerar du SDK till din bedömnings program eller skript. Du kan sedan använda SDK: N för att logga data, till exempel parametrar, resultat eller indatainformation.

Om du vill [Aktivera insamling av modelldata](how-to-enable-data-collection.md) varje gång du distribuerar avbildningen information som krävs för att avbilda data, till exempel autentiseringsuppgifterna som din personliga blob store etableras automatiskt.

> [!Important]
> Microsoft kan inte se data som samlas in från din modell. Data skickas direkt till Azure storage-kontot för arbetsytan.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du kan distribuera modeller](how-to-deploy-and-where.md) med Azure Machine Learning-tjänsten.
