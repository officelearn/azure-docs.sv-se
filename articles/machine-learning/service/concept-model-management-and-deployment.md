---
title: 'MLOps: Hantera, distribuera och övervaka ML-modeller'
titleSuffix: Azure Machine Learning service
description: 'Lär dig hur du använder Azure Machine Learning-tjänsten för MLOps: distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning-tjänsten på den lokala datorn eller från andra källor.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 18a80af8422e30ce3e87395449fca7b5f6a73762
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025024"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Hantera, distribuera och övervaka modeller med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur du använder Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka dina modeller för att kontinuerligt förbättra dem. Du kan distribuera modeller som du tränas med Azure Machine Learning, på din lokala dator eller från andra källor. 

Följande diagram illustrerar arbetsflödet slutförts: [![Arbetsflöde för distribution för Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

MLOps / arbetsflöde för distribution omfattar följande steg:
1. **Registrera modellen** i ett register som lagras i din arbetsyta för Azure Machine Learning-tjänsten
1. **Använd** modellen i en webbtjänst i molnet, på en IoT-enhet eller för analyser med Power BI.
1. **Övervaka och samla in data**
1. **Uppdatera** en distribution som du använder en ny avbildning.

Varje steg kan utföras separat eller som en del av ett enda kommando. Du kan även skapa en **CI/CD-arbetsflöde** enligt beskrivningen i den här bilden.

[![”Azure Machine Learning kontinuerlig integrering/kontinuerlig distribution (CI/CD) cykel'](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.youtube.com/embed/0MaHb070H_8]

## <a name="step-1-register-model"></a>Steg 1: Registrera modellen

Modellen kan du lagra och version dina modeller i Azure-molnet, på din arbetsyta. Modellen registret gör det enkelt att ordna och Håll koll på dina tränade modeller.
 
Registrerade modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig ökas registret versionen. Du kan också ange ytterligare metadatataggar under registreringen som kan användas när du söker för modeller. Azure Machine Learning-tjänsten har stöd för alla modeller som kan vara inlästa med hjälp av Python 3.5.2 eller högre.

Du kan inte ta bort modeller som används i en aktiv distribution.

Mer information finns i avsnittet registrera modellen i [distribuera modeller](how-to-deploy-and-where.md#registermodel).

Ett exempel med att registrera en modell som lagras i pickle-format finns i [självstudien: Träna en modell för klassificering av bild](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Steg 2: Använd modellen

Machine learning-modeller kan användas som en webbtjänst på IoT Edge-enheter eller för analys från tjänster som Power BI.

### <a name="web-service"></a>Webbtjänst

Du kan använda dina modeller i **webbtjänster** compute med följande mål:

* Azure Container-instans
* Azure Kubernetes Service

För att distribuera modellen som en webbtjänst, måste du ange följande:

* Modellen eller ensemble av modeller.
* Beroenden som krävs för att använda modellen. Till exempel ett skript som tar emot förfrågningar och anropar modell, conda-beroenden, osv.
* Distributionskonfiguration som beskriver hur och var att distribuera modellen.

Mer information finns i [distribuera modeller](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>IoT Edge-enheter

Du kan använda modeller med IoT-enheter via **Azure IoT Edge-moduler**. IoT Edge-moduler distribueras till maskinvaruenheter, vilket gör att inferensjobb på enheten.

Mer information finns i [distribuera modeller](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI stöder användningen av machine learning-modeller för dataanalys. Mer information finns i [Azure Machine Learning-integrering i Power BI (förhandsgranskning)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Steg 3: Övervaka modeller och samla in data

Övervakning kan du förstå vilka data som skickas till din modell och förutsägelser som returneras.

Den här informationen hjälper dig att förstå hur din modell används. Insamlade inkommande data kan också vara användbara i utbildning framtida versioner av modellen.

Mer information finns i [hur du aktiverar insamling av modelldata](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Steg 4: Uppdatera distributionen

Distributioner måste uppdateras uttryckligen. Mer information finns i uppdatera avsnittet [distribuera modeller](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du kan distribuera modeller](how-to-deploy-and-where.md) med Azure Machine Learning-tjänsten. Ett exempel på distribution finns i [självstudien: Distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

Lär dig hur du skapar klienten program och tjänster som [Använd en modell som distribueras som en webbtjänst](how-to-consume-web-service.md).
