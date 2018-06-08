---
title: 'Steg 6: Få åtkomst till webbtjänsten för Machine Learning | Microsoft Docs'
description: 'Steg 6 i utveckla en förutsägelselösning genomgång: komma åt en aktiva Azure Machine Learning-webbtjänst.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
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
ms.openlocfilehash: be63a04d0fe71039b19eb6bc0678a319f622a811
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836712"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Genomgång steg 6: Få åtkomst till Azure Machine Learning-webbtjänsten

Detta är det sista steget i den här genomgången [utveckla en förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. [Skapa ett nytt experiment](walkthrough-3-create-new-experiment.md)
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. **Få åtkomst till webbtjänsten**

- - -
I föregående steg i den här genomgången distribuerade vi en webbtjänst som använder vår kredit risk förutsägelse modell. Användare ska nu kunna skicka data till den och få resultat. 

Webbtjänsten är en Azure-webbtjänst som kan ta emot och returnera data med hjälp av REST API: er i ett av två sätt:  

* **Frågor och svar** – användaren skickar en eller flera datarader kredit till tjänsten med hjälp av en HTTP-protokollet och tjänsten svarar med en eller flera uppsättningar av resultaten.
* **Batch-körningen** – användaren lagrar en eller fler rader med kredit data i ett Azure blob- och skickar sedan blobbplats till tjänsten. Tjänsten poäng alla rader med data i indata blob, lagrar resultaten i en annan blob och returnerar URL för behållaren.  

Det snabbaste och enklaste sättet att komma åt en klassisk webbtjänst är via den [Azure ML-svar på begäranden Service Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) eller [Azure ML Batch Execution Service Web Appmallen](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Mallarna web app kan skapa ett anpassat webbprogram som känner din webbtjänsten indata och vad returneras. Allt du behöver göra är att ge åtkomst till webbtjänsten och data och mallen gör resten.

Mer information om hur du använder app webbmallar finns [använda en Azure Machine Learning webbtjänst med en mall för app](consume-web-service-with-web-app-template.md).

Du kan också skapa ett anpassat program för att få åtkomst till webbtjänsten genom att använda starter code enligt du R, C# och Python programmeringsspråk.

Du hittar information i [använda en Azure Machine Learning-webbtjänst](consume-web-services.md).

