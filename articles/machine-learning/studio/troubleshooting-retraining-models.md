---
title: Felsöka omtrimning av en Machine Learning Studio klassisk webbtjänst – Azure | Microsoft Docs
description: Identifiera och åtgärda vanliga problem uppstod när du träna modellen för en Azure Machine Learning Studio-webbtjänst.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 1105b81d0f8ba80bd76bcdf140fe79b9e8a7102d
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52307210"
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-studio-classic-web-service"></a>Felsöka omtrimning av en klassisk Azure Machine Learning Studio-webbtjänst
## <a name="retraining-overview"></a>Omtränings översikt
När du distribuerar ett förutsägelseexperiment som en bedömning av webbtjänsten är en statisk modell. När nya data blir tillgängliga eller när användaren av API: et har sina egna data, måste vara modellkomponenten modellen. 

En fullständig genomgång av hur omtränings en klassisk webbtjänst finns i [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Omtränings process
När du behöver träna om webbtjänsten måste du lägga till vissa ytterligare delar:

* En webbtjänst som distribueras från Träningsexperimentet. Experimentet måste ha en **Web Service utdata** modulen ansluten till utdataporten för den **Träningsmodell** modulen.  
  
    ![Koppla web service-utdata till train-modell.][image1]
* En ny slutpunkt som lagts till i din bedömning av webbtjänsten.  Du kan lägga till slutpunkten via programmering med exempelkoden som refereras i träna om Machine Learning-modeller via programmering avsnittet eller via Azure Machine Learning Web Services-portalen.

Du kan sedan använda exemplet C# kod från API-hjälpsidan för utbildning-webbtjänsten att träna modellen. När du har granskat resultatet och är nöjd med dem kan uppdatera du den tränade modellen värdera webbtjänst med hjälp av den nya slutpunkten som du har lagt till.

Alla delar är på plats är de viktigaste stegen som du måste vidta för att träna modellen på följande sätt:

1. Anropa webbtjänsten utbildning: anropet är att BES Batch Execution Service (), inte på begäran (RRS-Response Service). Du kan använda exemplet C# kod på API-hjälpsidan för att anropa. 
2. Hitta värdena för den *BaseLocation*, *RelativeLocation*, och *SasBlobToken*: dessa värden returneras i utdata från anrop till webbtjänsten utbildning. 
   ![Visar utdata i omtränings-exemplet och BaseLocation och RelativeLocation SasBlobToken värdena.][image6]
3. Uppdatera från bedömning av webbtjänsten har lagts till slutpunkten med den nya tränade modellen: med exempelkoden som anges i träna om Machine Learning-modeller via programmering, uppdatera den nya slutpunkten som du lade till bedömningsmodell med den nyligen tränade modellen från den Webbtjänst för utbildning.

## <a name="common-obstacles"></a>Vanliga hinder
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Kontrollera om du har rätt KORRIGERA URL
KORRIGERA URL: en du använder måste vara det som är associerade med den nya bedömnings slutpunkten som du lade till bedömning av webbtjänsten. Det finns ett antal sätt att hämta PATCH-URL:

**Alternativ 1: programmässigt**

Hämta rätt KORRIGERA URL:

1. Kör den [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) exempelkoden.
2. Från utdata från AddEndpoint, hitta den *HelpLocation* värde och kopiera URL: en.
   
   ![HelpLocation i utdata från addEndpoint-exemplet.][image2]
3. Klistra in URL: en i en webbläsare och navigera till en sida som innehåller länkar för webbtjänsten.
4. Klicka på den **resursuppdatering** länk för att öppna sidan patch hjälp.

**Alternativ 2: Använd Azure Machine Learning Web Services-portalen**

1. Logga in på den [Azure Machine Learning Web Services](https://services.azureml.net/) portal.
2. Klicka på **webbtjänster** eller **klassiska webbtjänster** högst upp.
4. Klicka på bedömnings webbtjänst som du arbetar med (om du använde standardnamnet för webbtjänsten, upphör den i ”[beräkning Exp.]”).
5. Klicka på **+ ny**.
6. När slutpunkten har lagts till, klickar du på namnet på slutpunkten.
7. Under den **Patch** URL: en, klicka på **API Help** att öppna sidan uppdatering hjälp.

> [!NOTE]
> Om du har lagt till slutpunkten till webbtjänsten utbildning i stället för förutsägande webbtjänsten, du får följande felmeddelande när du klickar på den **resursuppdatering** länk: ”Vi beklagar, men den här funktionen är inte eller är inte tillgängligt i den här kontexten. Den här webbtjänsten har inga uppdateringsbara resurser. Vi ber om ursäkt för besväret och arbetar på att förbättra det här arbetsflödet ”.
> 
> 

PATCH-hjälpsidan innehåller KORRIGERA URL: en måste du använda och visar exempelkod som du kan använda för att anropa den.

![Patch-URL.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Kontrollera att du uppdaterar rätt bedömnings slutpunkt
* Inte korrigera web Utbildningstjänsten: patch-åtgärden måste utföras på bedömning av webbtjänsten.
* Korrigera inte standardslutpunkten Webbtjänstmetoder: patch-åtgärden måste utföras på den nya bedömnings webbtjänstslutpunkt som du har lagt till.

Du kan kontrollera vilka webbtjänsten slutpunkten är på genom att gå till Web Services-portalen. 

> [!NOTE]
> Var noga med att du lägger till slutpunkten för förutsägande Web Service, inte webbtjänsten utbildning. Om du har distribuerat både ett utbildnings- och en förutsägbar webbtjänst korrekt, bör du se två separata webbtjänster som visas. Förutsägande webbtjänsten ska sluta med ”[förutsägande exp.]”.
> 
> 

1. Logga in på den [Azure Machine Learning Web Services](https://services.azureml.net/) portal.
2. Klicka på **webbtjänster** eller **klassiska webbtjänster**.
3. Välj din förutsägbar webbtjänst.
4. Kontrollera att din nya slutpunkt har lagts till webbtjänsten.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Kontrollera att din arbetsyta är i samma region som webbtjänsten
1. Logga in på [Machine Learning Studio](https://studio.azureml.net/).
2. Överst på sidan klickar du på den nedrullningsbara listan över dina arbetsytor.

   ![Machine learning region Användargränssnittet.][image4]

3. Kontrollera den region som din arbetsyta är i.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
