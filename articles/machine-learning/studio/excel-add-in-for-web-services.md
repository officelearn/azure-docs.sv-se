---
title: Excel-tillägg för webbtjänster för Machine Learning | Microsoft Docs
description: Hur du använder Azure Machine Learning-webbtjänster direkt i Excel utan att skriva någon kod.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: 6610777ff4ad3a04f9d0d5b47f402aea7db59d9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Excel-tillägg för Azure Machine Learning-webbtjänster
Excel gör det enkelt att anropa webbtjänster direkt utan att behöva skriva någon kod.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Steg för att använda en befintlig webbtjänst i arbetsboken

1. Öppna den [Excel exempelfilen](http://aka.ms/amlexcel-sample-2), som innehåller Excel-tillägget och data om passagerare på Titanic.
2. Välj webbtjänsten genom att klicka på den-”Titanic efterlevande ge prognoser (Excel-tillägg prov) [poäng]” i det här exemplet.
   
    ![Välj-webbtjänst][01]
3. Då kommer du att den **Predict** avsnitt.  Den här arbetsboken innehåller redan exempeldata, men en tom arbetsbok du kan markera en cell i Excel och klickar på **använda exempeldata**.
4. Välj data med rubriker och klicka på ikonen indata intervall.  Kontrollera att kryssrutan ”Mina data har rubriker” är markerad.
5. Under **utdata**, ange antalet celler där du vill att utdata ska vara, till exempel ”H1” här.
6. Klicka på **förutsäga**.
   
    ![Förutsäga avsnitt][02]

Distribuera en webbtjänst eller Använd en befintlig webbtjänst. Mer information om hur du distribuerar en webbtjänst finns [genomgången steg 5: distribuera webbtjänsten Azure Machine Learning](walkthrough-5-publish-web-service.md).

Hämta API-nyckeln för webbtjänsten. Om du utför beror den här åtgärden på om du har publicerat en klassiska Machine Learning-webbtjänst för en ny Machine Learning-webbtjänst.

**Använda en klassisk-webbtjänst** 

1. I Machine Learning Studio klickar du på den **WEB SERVICES** avsnittet i det vänstra fönstret och väljer sedan webbtjänsten.
   
    ![Studio väljer du en webbtjänst][04]
2. Kopiera API-nyckeln för webbtjänsten.
   
    ![Studio API-nyckel][05]
3. På den **INSTRUMENTPANELEN** för webbtjänsten klickar du på den **frågor och svar** länk.
4. Leta efter den **Begärd URI** avsnitt.  Kopiera och spara URL: en.

> [!NOTE]
> Nu är det möjligt att logga in på den [Azure Machine Learning-webbtjänster](https://services.azureml.net) portalen för att hämta API-nyckel för en klassiska Machine Learning-webbtjänst.
> 
> 

**Använd en ny webbtjänst**

1. I den [Azure Machine Learning-webbtjänster](https://services.azureml.net) klickar du på **Web Services**, Välj din webbtjänst. 
2. Klicka på **använda**.
3. Leta efter den **grundläggande förbrukning info** avsnitt. Kopiera och spara den **primärnyckel** och **begäran och svar** URL.

## <a name="steps-to-add-a-new-web-service"></a>Steg för att lägga till en ny webbtjänst

1. Distribuera en webbtjänst eller Använd en befintlig webbtjänst. Mer information om hur du distribuerar en webbtjänst finns [genomgången steg 5: distribuera webbtjänsten Azure Machine Learning](walkthrough-5-publish-web-service.md).
2. Klicka på **använda**.
3. Leta efter den **grundläggande förbrukning info** avsnitt. Kopiera och spara den **primärnyckel** och **begäran och svar** URL.
4. I Excel går du till den **Web Services** avsnittet (om du är i den **Predict** avsnittet, klickar du på pilen Bakåt för att gå till listan över webbtjänster).
   
    ![Gå till val av Web service][03]
5. Klicka på **Lägg till webbtjänst**.
6. Klistra in Webbadressen till Excel-tillägget textruta med etiketten **URL**.
7. Klistra in den API eller den primära nyckeln i textrutan **API-nyckeln**.
8. Klicka på **Lägg till**.
   
    ![URL: en och API-nyckeln för det klassiska.][06]
9. Om du vill använda webbtjänsten följer du föregående anvisningarna och ”steg för att använda en befintlig web Service”.

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
Om du sparar arbetsboken sparas också API eller den primära nyckeln för webbtjänster som du har lagt till. Det innebär att du ska dela arbetsboken med personer som du litar på.

Frågor i avsnittet kommentar eller på vår [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
