---
title: Excel-tillägg för webbtjänster
titleSuffix: ML Studio (classic) - Azure
description: Så här använder du Azure Machine Learning Web-tjänster direkt i Excel utan att skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204383"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-tillägg för Azure Machine Learning Studio (klassiska) webbtjänster

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Excel gör det enkelt att ringa webbtjänster direkt utan att behöva skriva någon kod.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Steg för att använda en befintlig webbtjänst i arbetsboken

1. Öppna [exempelet Excel-fil](https://aka.ms/amlexcel-sample-2), som innehåller Excel-tillägget och data om passagerare på Titanic. 
 
    > [!NOTE]
    > Du kommer att se listan över de webbtjänster som är relaterade till filen och längst ner en kryssruta för "Auto-predict". Om du aktiverar automatisk förutsägelser av **alla** dina tjänster kommer att uppdateras varje gång det finns en förändring på indata. Om avmarkerad måste du klicka på "Förutsäga alla" för uppdatering. För att aktivera automatisk förutsägelse på en servicenivå gå till steg 6.

2. Välj webbtjänsten genom att klicka på den - "Titanic Survivor Predictor (Excel Add-in Sample) [Score]" i det här exemplet.
   
    ![Välj webbtjänst](./media/excel-add-in-for-web-services/image1.png)
3. Detta tar dig till avsnittet **Förutsäga.**  Arbetsboken innehåller redan exempeldata, men för en tom arbetsbok kan du markera en cell i Excel och klicka på **Använd exempeldata**.
4. Markera data med rubriker och klicka på ikonen för indataområde.  Kontrollera att rutan "Mina data har rubriker" är markerad.
5. Under **Utdata**anger du det cellnummer där du vill att utdata ska vara, till exempel "H1" här.
6. Klicka på **Förutsäga**. Om du markerar kryssrutan "automatisk förutsägelse" utlöser någon ändring på de markerade områdena (de som anges som indata) en begäran och en uppdatering av utdatacellerna utan att du behöver trycka på försanningsknappen.
   
    ![Förutsäga avsnitt](./media/excel-add-in-for-web-services/image1.png)

Distribuera en webbtjänst eller använd en befintlig webbtjänst. Mer information om hur du distribuerar en webbtjänst finns i [självstudiekurs 3: Distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md).

Hämta API-nyckeln för din webbtjänst. Var du utför den här åtgärden beror på om du har publicerat en webbtjänst för klassisk maskininlärning i en ny machine learning-webbtjänst.

**Använda en klassisk webbtjänst** 

1. I Machine Learning Studio (klassisk) klickar du på avsnittet **WEBBTJÄNSTER** i den vänstra rutan och väljer sedan webbtjänsten.
   
    ![Studio välja en webbtjänst](./media/excel-add-in-for-web-services/image4.png)
2. Kopiera API-nyckeln för webbtjänsten.
   
    ![Api-nyckel för studio](./media/excel-add-in-for-web-services/image5.png)
3. Klicka på länken **BEGÄR/SVAR** på fliken **INSTRUMENTPANEL** för webbtjänsten.
4. Leta efter avsnittet **Begär URI.**  Kopiera och spara webbadressen.

> [!NOTE]
> Det är nu möjligt att logga in på [Azure Machine Learning Web Services-portalen](https://services.azureml.net) för att hämta API-nyckeln för en klassisk machine learning-webbtjänst.
> 
> 

**Använda en ny webbtjänst**

1. Klicka på **Webbtjänster**i portalen [för Azure Machine Learning Web Services](https://services.azureml.net) och välj sedan din webbtjänst. 
2. Klicka på **Förbruka**.
3. Leta efter avsnittet **Grundläggande förbrukningsinformation.** Kopiera och spara **primärnyckeln** och **URL:en för begäran.Copy** and save the Primary Key and the Request-Response URL.

## <a name="steps-to-add-a-new-web-service"></a>Steg för att lägga till en ny webbtjänst

1. Distribuera en webbtjänst eller använd en befintlig webbtjänst. Mer information om hur du distribuerar en webbtjänst finns i [självstudiekurs 3: Distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md).
2. Klicka på **Förbruka**.
3. Leta efter avsnittet **Grundläggande förbrukningsinformation.** Kopiera och spara **primärnyckeln** och **URL:en för begäran.Copy** and save the Primary Key and the Request-Response URL.
4. I Excel går du till avsnittet **Webbtjänster** (om du är i avsnittet **Förutsäga** klickar du på bakåtpilen för att gå till listan över webbtjänster).
   
    ![Gå till Val av webbtjänst](./media/excel-add-in-for-web-services/image3.png)
5. Klicka på **Lägg till webbtjänst**.
6. Klistra in URL:en i textrutan Excel med namnet **URL**.
7. Klistra in API/Primärnyckeln i textrutan med namnet **API-nyckel**.
8. Klicka på **Lägg till**.
   
    ![URL- och API-nyckel för en klassisk webbtjänst.](./media/excel-add-in-for-web-services/image6.png)
9. Om du vill använda webbtjänsten följer du anvisningarna i föregående anvisningar, "Steg för att använda en befintlig webbtjänst".

## <a name="sharing-your-workbook"></a>Dela arbetsboken
Om du sparar arbetsboken sparas även API/primärnyckeln för de webbtjänster som du har lagt till. Det innebär att du bara ska dela arbetsboken med personer som du litar på.

Ställ några frågor i följande kommentar avsnitt eller på vårt [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
