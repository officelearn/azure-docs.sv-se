---
title: Excel-tillägg för webb tjänster
titleSuffix: ML Studio (classic) - Azure
description: Hur du använder Azure Machine Learning Web services direkt i Excel utan att behöva skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204383"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-tillägg för Azure Machine Learning Studio (klassiska) webb tjänster

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Excel gör det enkelt att anropa webbtjänster direkt utan att behöva skriva någon kod.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Steg för att använda en befintlig webbtjänst i arbetsboken

1. Öppna [exempel Excel-filen](https://aka.ms/amlexcel-sample-2)som innehåller Excel-tillägget och data om passagerare på Titanic. 
 
    > [!NOTE]
    > Du kan se listan över de webbtjänster relaterade till filen och längst ned på sidan en kryssruta för ”automatisk förutsäga”. Om du aktiverar automatisk förutsägelse kommer förutsägelserna för **alla** dina tjänster att uppdateras varje gång en ändring görs av indata. Om alternativet är avmarkerat måste du klicka på ”förutse alla” för uppdatering. För att aktivera automatisk-förutsäga på en tjänst på Gå till steg 6.

2. Välj webbtjänsten genom att klicka på den-”Titanic efterlevande ge säkrare prognoser (Excel-tillägget prov) [poäng]” i det här exemplet.
   
    ![Välj webbtjänst](./media/excel-add-in-for-web-services/image1.png)
3. Det tar dig till **predict** -avsnittet.  Den här arbets boken innehåller redan exempel data, men för en tom arbets bok kan du välja en cell i Excel och klicka på **Använd exempel data**.
4. Välj data med rubriker och klicka på ikonen indata intervall.  Kontrollera att rutan ”data innehåller rubriker” är markerad.
5. Under **utdata**anger du det cell nummer där du vill att utdata ska vara, till exempel "H1" här.
6. Klicka på **predict**. Om du markerar kryssrutan ”Automatisk predict” ska eventuella ändringar i de markerade områdena (de som anges som indata) utlösa en begäran och en uppdatering av utdata cellerna utan att behöva du ska trycka på knappen predict.
   
    ![Förutsäga avsnittet](./media/excel-add-in-for-web-services/image1.png)

Distribuera en webbtjänst eller Använd en befintlig webbtjänst. Mer information om hur du distribuerar en webb tjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).

Hämta API-nyckel för webbtjänsten. Om du utför beror denna åtgärd på om du har publicerat en klassiska Machine Learning-webbtjänst för en ny Machine Learning-webbtjänst.

**Använda en klassisk webb tjänst** 

1. I Machine Learning Studio (klassisk) klickar du på avsnittet **webb tjänster** i den vänstra rutan och väljer sedan webb tjänsten.
   
    ![Studio väljer en webbtjänst](./media/excel-add-in-for-web-services/image4.png)
2. Kopiera API-nyckel för webbtjänsten.
   
    ![Studio API-nyckel](./media/excel-add-in-for-web-services/image5.png)
3. På fliken **instrument panel** för webb tjänsten klickar du på länken **begär ande/svar** .
4. Sök efter URI-avsnittet för **begäran** .  Kopiera och spara URL: en.

> [!NOTE]
> Nu kan du logga in på [Azure Machine Learning Web Services-](https://services.azureml.net) portalen för att hämta API-nyckeln för en klassisk Machine Learning-webb tjänst.
> 
> 

**Använd en ny webb tjänst**

1. I [Azure Machine Learning Web Services-](https://services.azureml.net) portalen klickar du på **webb tjänster**och väljer sedan din webb tjänst. 
2. Klicka på **förbruka**.
3. Leta upp avsnittet **grundläggande förbruknings information** . Kopiera och spara **primär nyckeln** och URL: en för **begäran-svar** .

## <a name="steps-to-add-a-new-web-service"></a>Hur du lägger till en ny webbtjänst

1. Distribuera en webbtjänst eller Använd en befintlig webbtjänst. Mer information om hur du distribuerar en webb tjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).
2. Klicka på **förbruka**.
3. Leta upp avsnittet **grundläggande förbruknings information** . Kopiera och spara **primär nyckeln** och URL: en för **begäran-svar** .
4. I Excel går du till avsnittet **webb tjänster** (om du är i avsnittet **predict** klickar du på pilen tillbaka för att gå till listan med webb tjänster).
   
    ![Gå till Web service val](./media/excel-add-in-for-web-services/image3.png)
5. Klicka på **Lägg till webb tjänst**.
6. Klistra in webb adressen i text rutan för Excel-tillägget med etiketten **URL**.
7. Klistra in API/primär nyckel i text rutan med etiketten **API-nyckel**.
8. Klicka på **Lägg till**.
   
    ![URL: en och API-nyckeln för en klassiska webbtjänst.](./media/excel-add-in-for-web-services/image6.png)
9. Om du vill använda webbtjänsten, följer du föregående anvisningarna och ”steg för att använda en befintlig web Service”.

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
Om du har sparat din arbetsbok kan sparas även API eller den primära nyckeln för webbtjänster som du har lagt till. Det innebär att du bör bara dela arbetsboken med personer som du litar på.

Ställ frågor i följande kommentars avsnitt eller i vårt [Forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
