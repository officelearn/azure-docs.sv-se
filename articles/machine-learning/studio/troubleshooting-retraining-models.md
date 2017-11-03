---
title: "Felsöka omtränings en Azure Machine Learning klassiska webbtjänst | Microsoft Docs"
description: "Identifiera och lösa vanliga problem uppstod när du omtränings modellen för en Azure Machine Learning-webbtjänst."
services: machine-learning
documentationcenter: 
author: VDonGlover
manager: raymondl
editor: 
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 85cf9175bb4a5f253c7b47b2edc3ac8b00616ba2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Felsökning av omtränings av en Azure Machine Learning klassiska-webbtjänst
## <a name="retraining-overview"></a>Omtränings översikt
När du distribuerar en prediktivt experiment som en bedömningsprofil webbtjänst är en statisk modell. När nya data blir tillgängliga eller när konsumenten API har sina egna data, måste vara retrained modellen. 

En fullständig genomgång av hur omtränings klassiska webbtjänsten finns [träna om Machine Learning-modeller via programmering](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Omtränings process
När du behöver träna om webbtjänsten måste du lägga till vissa ytterligare delar:

* En webbtjänst som distribueras från utbildning experimentet. Experimentet måste ha en **Web Service utdata** modulen ansluten till utdataporten för den **Träningsmodell** modul.  
  
    ![Koppla web service utdata till train-modell.][image1]
* En ny slutpunkt som lagts till bedömningsprofil webbtjänsten.  Du kan lägga till slutpunkten programmässigt med exempelkoden som refereras i träna om Machine Learning-modeller via programmering avsnittet eller via den klassiska Azure-portalen.

Du kan sedan använda C# exempelkoden från hjälpsidan för utbildning-webbtjänsten API för att träna om modellen. När du har utvärderat resultaten och är nöjd med dem kan uppdatera du den tränade modellen bedömningen webbtjänst med hjälp av den nya slutpunkt som du har lagt till.

Med alla delar på plats är de viktigaste stegen som du måste vidta för att träna om modellen följande:

1. Anropa webbtjänsten utbildning: anropet är att den Batch körning Service BES-, inte de begär svar Service (RR). Du kan använda C# exempelkoden på hjälpsidan API för att ringa. 
2. Värden för att hitta den *BaseLocation*, *RelativeLocation*, och *SasBlobToken*: dessa värden returneras i utdata från anrop till webbtjänsten utbildning. 
   ![Visar resultatet av omtränings provet och värdena BaseLocation, RelativeLocation och SasBlobToken.][image6]
3. Uppdatera tillagda slutpunkten från bedömningsprofil webbtjänsten med den nya tränade modellen: med den exempelkoden i träna om Machine Learning-modeller via programmering, uppdatera ny slutpunkt som du har lagt till bedömningsprofil modellen med den nyligen tränade modellen från webbtjänsten utbildning.

## <a name="common-obstacles"></a>Vanliga hinder
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Kontrollera om du har rätt korrigering URL
KORRIGERING av Webbadressen som du använder måste vara den som är kopplade till den nya bedömningsprofil slutpunkt som du lagt till bedömningsprofil webbtjänsten. Det finns ett antal sätt att hämta URL: en korrigering:

**Alternativ 1: genom att programmera**

Hämta rätt korrigering URL:

1. Kör den [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) exempelkod.
2. Utdata från AddEndpoint, Sök efter den *HelpLocation* värdet och kopiera Webbadressen.
   
   ![HelpLocation i utdata addEndpoint-exemplet.][image2]
3. Klistra in Webbadressen i en webbläsare för att navigera till en sida som innehåller hjälplänkar för webbtjänsten.
4. Klicka på den **uppdatering resurs** länk för att öppna sidan korrigering hjälp.

**Alternativ 2: Använd den klassiska Azure-portalen**

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Öppna fliken Machine Learning. ![Datorn lutande fliken.][image4]
3. Klicka på arbetsytans namn, sedan **Web Services**.
4. Klicka på bedömningsprofil webbtjänsten som du arbetar med. (Om du inte ändrar standardnamnet på webbtjänsten, det går ut om [bedömningen Exp.].)
5. Klicka på **lägga till slutpunkten**.
6. När slutpunkten har lagts till, klickar du på namnet på slutpunkten. Klicka på **uppdatering resurs** att öppna sidan uppdatering hjälp.

> [!NOTE]
> Om du har lagt till slutpunkten för utbildning Web Service i stället för förutsägande webbtjänsten, du får följande felmeddelande när du klickar på den **uppdatering resurs** länk: tyvärr, men den här funktionen kan inte stöds i den här kontexten. Den här webbtjänsten har inga resurser för uppdateras. Vi ber om ursäkt för besväret och arbetar med att förbättra det här arbetsflödet.
> 
> 

![Ny slutpunkt instrumentpanel.][image3]

Hjälpsidan korrigering innehåller korrigering URL: en måste du använda och innehåller exempelkod som du kan använda för att anropa den.

![Patch-URL.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Se till att du uppdaterar korrekt bedömningsprofil slutpunkt
* Inte korrigering webbtjänsten utbildning: patch-åtgärden måste utföras på bedömningsprofil webbtjänsten.
* Inte korrigering standardslutpunkten webbtjänsten: patch-åtgärden måste utföras på den nya bedömningsprofil webbtjänstslutpunkt som du har lagt till.

Du kan kontrollera vilka webbtjänsten slutpunkten finns på den klassiska Azure-portalen. 

> [!NOTE]
> Var noga med att du lägger till slutpunkten förutsägande webbtjänsten inte utbildning-webbtjänsten. Om du har distribuerat en utbildnings- och en förutsägbar webbtjänst korrekt, bör du se två separata webbtjänster som anges. Förutsägande webbtjänsten ska avslutas med ”[förutsägande exp.]”.
> 
> 

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Öppna fliken Machine Learning. ![Machine learning-arbetsytan Användargränssnittet.][image4]
3. Välj din arbetsyta.
4. Klicka på **webbtjänster**.
5. Välj förutsägbara webbtjänsten.
6. Kontrollera att din nya slutpunkt har lagts till webbtjänsten.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Kontrollera arbetsytan som webbtjänsten i ska se till att den är i rätt region
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj Machine Learning på menyn.
   ![Machine learning region Användargränssnittet.][image4]
3. Kontrollera platsen för din arbetsyta.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
