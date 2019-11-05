---
title: Excel-tillägg för webb tjänster
titleSuffix: Azure Machine Learning Studio (classic)
description: Använda Azure Machine Learning webb tjänster direkt i Excel utan att skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 18e7e2c2c57bd44cb974e5de668045d6322bc4ce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492986"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Excel-tillägg för Azure Machine Learning Studio (klassiska) webb tjänster
Excel gör det enkelt att anropa webb tjänster direkt utan att behöva skriva någon kod.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Steg för att använda en befintlig webb tjänst i arbets boken

1. Öppna [exempel Excel-filen](https://aka.ms/amlexcel-sample-2)som innehåller Excel-tillägget och data om passagerare på Titanic. 
 
    > [!NOTE]
    > Du ser listan över webb tjänster som är relaterade till filen och längst ned i en kryss ruta för "automatisk förutsägelse". Om du aktiverar automatisk förutsägelse kommer förutsägelserna för **alla** dina tjänster att uppdateras varje gång en ändring görs av indata. Om alternativet är avmarkerat måste du klicka på "förutse alla" för att uppdatera. För att aktivera automatisk förutsägelse på en service nivå går du till steg 6.

2. Välj webb tjänsten genom att klicka på den – "Titanic efterlevandepension-förväntare (Excel-tillägg) [score]" i det här exemplet.
   
    ![Välj webb tjänst](./media/excel-add-in-for-web-services/image1.png)
3. Det tar dig till **predict** -avsnittet.  Den här arbets boken innehåller redan exempel data, men för en tom arbets bok kan du välja en cell i Excel och klicka på **Använd exempel data**.
4. Välj data med rubriker och klicka på ikonen för indata-området.  Kontrol lera att rutan mina data har rubriker är markerad.
5. Under **utdata**anger du det cell nummer där du vill att utdata ska vara, till exempel "H1" här.
6. Klicka på **predict**. Om du markerar kryss rutan "Auto-predict" för alla ändringar på de valda områdena (de som anges som indata) utlöser en begäran och en uppdatering av utdata-cellerna utan att du behöver trycka på predict-knappen.
   
    ![Förutsägelse avsnitt](./media/excel-add-in-for-web-services/image1.png)

Distribuera en webb tjänst eller Använd en befintlig webb tjänst. Mer information om hur du distribuerar en webb tjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).

Hämta API-nyckeln för webb tjänsten. Var du utför den här åtgärden beror på om du har publicerat en klassisk Machine Learning-webbtjänst för en ny Machine Learning-webbtjänst.

**Använda en klassisk webb tjänst** 

1. I Machine Learning Studio (klassisk) klickar du på avsnittet **webb tjänster** i den vänstra rutan och väljer sedan webb tjänsten.
   
    ![Studio Välj en webb tjänst](./media/excel-add-in-for-web-services/image4.png)
2. Kopiera API-nyckeln för webb tjänsten.
   
    ![API-nyckel för Studio](./media/excel-add-in-for-web-services/image5.png)
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

## <a name="steps-to-add-a-new-web-service"></a>Steg för att lägga till en ny webb tjänst

1. Distribuera en webb tjänst eller Använd en befintlig webb tjänst. Mer information om hur du distribuerar en webb tjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).
2. Klicka på **förbruka**.
3. Leta upp avsnittet **grundläggande förbruknings information** . Kopiera och spara **primär nyckeln** och URL: en för **begäran-svar** .
4. I Excel går du till avsnittet **webb tjänster** (om du är i avsnittet **predict** klickar du på pilen tillbaka för att gå till listan med webb tjänster).
   
    ![Gå till val av webb tjänst](./media/excel-add-in-for-web-services/image3.png)
5. Klicka på **Lägg till webb tjänst**.
6. Klistra in webb adressen i text rutan för Excel-tillägget med etiketten **URL**.
7. Klistra in API/primär nyckel i text rutan med etiketten **API-nyckel**.
8. Klicka på **Lägg till**.
   
    ![URL och API-nyckel för en klassisk webb tjänst.](./media/excel-add-in-for-web-services/image6.png)
9. Följ anvisningarna ovan om du vill använda en befintlig webb tjänst för att använda webb tjänsten.

## <a name="sharing-your-workbook"></a>Dela din arbets bok
Om du sparar din arbets bok sparas även API: n/primär nyckeln för de webb tjänster som du har lagt till. Det innebär att du endast bör dela arbets boken med individer som du litar på.

Ställ frågor i följande kommentars avsnitt eller i vårt [Forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
