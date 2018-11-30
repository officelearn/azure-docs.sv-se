---
title: 'Steg 6: Få åtkomst till Machine Learning Studio web sservice | Microsoft Docs'
description: 'Steg 6 i utveckla en förutsägelselösning genomgång: komma åt en active Azure Machine Learning Studio-webbtjänst.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 5402f228a650a1a3d3282180a52f9e9f9cc10a3a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52306462"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-studio-web-service"></a>Genomgång steg 6: Få åtkomst till Azure Machine Learning Studio-webbtjänsten

Det här är det sista steget i den här genomgången [utveckla en lösning för förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. **Få åtkomst till webbtjänsten**

- - -
I föregående steg i den här genomgången distribuerade vi en webbtjänst som använder våra kredit risk förutsägelsemodell. Användare kan nu skicka data till den och ta emot resultaten. 

Webbtjänsten är en Azure-webbtjänst som kan ta emot och returnera data med hjälp av REST API: er på ett av två sätt:  

* **Begäran/svar** – användaren skickar en eller flera rader med kredit-data till tjänsten med hjälp av en HTTP-protokollet och tjänsten svarar med en eller flera uppsättningar med resultat.
* **Batch-körningen** – användaren lagrar en eller flera rader med kredit-data i en Azure blob- och skickar sedan blobbplats till tjänsten. Tjänsten bedömer alla rader med data i indata-blob, lagrar resultatet i en annan blob och returnerar URL: en för behållaren.  

Det snabbaste och enklaste sättet att få åtkomst till en klassisk webbtjänst är via den [Azure ML-Request-Response Service-Webbapp](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) eller [Webbappmall för Azure ML Batch Execution Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Dessa mallar för web app kan skapa ett anpassat webbprogram som känner till din webbtjänst indata och vad den ska returnera. Allt du behöver göra är att ge åtkomst till din webbtjänst och dina data och mallen resten.

Mer information om hur du använder webbappmallar finns i [använda en Azure Machine Learning webbtjänst med en webbappmall](consume-web-service-with-web-app-template.md).

Du kan också skapa en anpassad App för att få åtkomst till webbtjänsten med hjälp av starter koden automatiskt i R, C#, och Python programmeringsspråk.

Fullständig information finns i [hur du använder en Azure Machine Learning-webbtjänst](consume-web-services.md).

