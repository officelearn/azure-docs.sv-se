---
title: Hantera, registrera, distribuera och övervaka ML-modeller
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning-tjänsten på den lokala datorn eller från andra källor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275450"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du använder Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning, på din lokala dator eller från andra källor. 

Följande diagram illustrerar arbetsflödet slutförts: [![Arbetsflöde för distribution för Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Arbetsflödet innehåller följande steg:
1. **Registrera modellen** i ett register som lagras i din arbetsyta för Azure Machine Learning-tjänsten
1. **Registrera en bild** som kombinationer av en modell med ett bedömningsskript och beroenden i en bärbar behållare 
1. **Distribuera** bilden som en webbtjänst i molnet eller edge-enheter
1. **Övervaka och samla in data**
1. **Uppdatera** en distribution som du använder en ny avbildning.

Varje steg kan utföras separat eller som en del av en enskild distribution-kommando. Dessutom kan du integrera distribution i en **CI/CD-arbetsflöde** enligt beskrivningen i den här bilden.

[!['Utvärderingscykel för kontinuerlig integrering/kontinuerlig distribution (CI/CD) i Azure Machine Learning'](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>Steg 1: Registrera modellen

Modellen kan du lagra och version dina modeller i Azure-molnet, på din arbetsyta. Modellen registret gör det enkelt att ordna och Håll koll på dina tränade modeller.
 
Registrerade modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig ökas registret versionen. Du kan också ange ytterligare metadatataggar under registreringen som kan användas när du söker för modeller. Azure Machine Learning-tjänsten har stöd för alla modeller som kan läsas in med hjälp av Python 3. 

Du kan inte ta bort modeller som används av en avbildning.

Mer information finns i avsnittet registrera modellen i [distribuera modeller](how-to-deploy-and-where.md#registermodel).

Ett exempel med att registrera en modell som lagras i pickle-format finns i [självstudien: Träna en modell för klassificering av bild](tutorial-deploy-models-with-aml.md).

Information om hur du använder ONNX-modeller finns i den [ONNX och Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentet.

## <a name="step-2-register-image"></a>Steg 2: Registrera bild

Avbildningar kan för tillförlitlig modelldistribution, tillsammans med alla komponenter som krävs för att använda modellen. En avbildning innehåller följande objekt:

* Modellen
* Bedömnings motorn
* Bedömnings fil- eller programdata
* Eventuella beroenden som behövs för att poängsätt modellen

Avbildningen kan även inkludera SDK-komponenterna för loggning och övervakning. Data i SDK-loggar kan användas för att finjustera eller träna din modell, inklusive indata och utdata av modellen.

Azure Machine Learning stöder de populäraste ramverken, men i allmänhet valfritt ramverk som kan vara pip installerade fungerar.

När din arbetsyta skapades, så användes andra flera andra Azure-resurser av arbetsytan.
Alla objekt som används för att skapa standardavbildningen lagras i Azure storage-kontot i din arbetsyta. Du kan ange ytterligare metadatataggar när du skapar avbildningen. Metadatataggar finns även som bild-registret och kan efterfrågas för att hitta din avbildning.

Du kan också använda anpassade avbildningar, som kan överföras till Azure Container Registry och används av Azure Machine Learning-tjänsten.

Mer information finns i Konfigurera och registrera bilddelen av [distribuera modeller](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>Steg 3: Distribuera avbildningen

Du kan distribuera registrerade avbildningar till molnet eller edge-enheter. Distributionsprocessen skapar alla resurser som behövs för att övervaka belastningen och Autoskala din modell. Åtkomst till distribuerade tjänster kan skyddas med certifikatbaserad autentisering genom att tillhandahålla säkerhet-tillgångar under distributionen. Du kan också uppgradera en befintlig distribution om du vill använda en avbildning av en nyare.

Webbtjänstdistributioner är också sökbara. Du kan exempelvis söka efter alla distributioner för en viss modell eller en bild.

[![Inferencing mål](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Du kan distribuera dina avbildningar till följande distribution i molnet:

* Azure Container-instans
* Azure Kubernetes Service
* Azure FPGA-datorer
* Azure IoT Edge-enheter

När tjänsten har distribuerats inferensjobb-begäran är automatiskt Utjämning av nätverksbelastning och klustret skalas för att uppfylla eventuella toppar på begäran. [Telemetri om din tjänst kan läsas in](how-to-enable-app-insights.md) i Azure Application Insights-tjänsten som är associerade med din arbetsyta.

Mer information finns i avsnittet distribuera i [distribuera modeller](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>Steg 4: Övervaka modeller och samla in data

Ett SDK för modellen loggning och data capture är tillgänglig så att du kan övervaka indata, utdata och andra relevanta data från din modell. Data lagras som en blob i Azure Storage-kontot för arbetsytan.

Om du vill använda SDK: N med din modell importerar du SDK till din bedömnings program eller skript. Du kan sedan använda SDK: N för att logga data, till exempel parametrar, resultat eller indatainformation.

Om du vill aktivera insamling av modelldata varje gång du distribuerar avbildningen kan etableras automatiskt de information som krävs för att avbilda data, till exempel autentiseringsuppgifterna som din personliga blob store.

> [!Important]
> Microsoft kan inte se data som samlas in från din modell. Data skickas direkt till Azure storage-kontot för arbetsytan.

Mer information finns i [hur du aktiverar insamling av modelldata](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>Steg 5: Uppdatera distributionen

Uppdateringar i din modell registreras inte automatiskt. På samma sätt kan uppdateras registrera en ny avbildning inte automatiskt distributioner som har skapats från en tidigare version av avbildningen. I stället måste du manuellt registrera modellen, registrera avbildningen och sedan uppdatera modellen. Mer information finns i uppdatera avsnittet [distribuera modeller](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du kan distribuera modeller](how-to-deploy-and-where.md) med Azure Machine Learning-tjänsten. Ett exempel på distribution finns i [självstudien: Distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

Lär dig hur du skapar klienten program och tjänster som [Använd en modell som distribueras som en webbtjänst](how-to-consume-web-service.md).
