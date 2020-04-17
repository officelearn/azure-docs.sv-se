---
title: In-/utdata för webbtjänsten
description: Lär dig mer om webbtjänstmodulerna i Azure Machine Learning designer (förhandsversion)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462092"
---
# <a name="web-service-inputoutput"></a>In-/utdata för webbtjänsten

I den här artikeln beskrivs **inmatningsmodulen för webbtjänst** och **webbtjänstutdatamodul** i Azure Machine Learning designer (förhandsversion).

**Indatamodulen för webbtjänst** kan bara ansluta till indataporten med typen **DataFrameDirectory**. Och **Web Service Output** module kan bara anslutas från utdataport med typ **DataFrameDirectory**. De två modulerna finns i modulträdet, under **kategorin Webbtjänst.** 

**Indatamodul för webbtjänsten** används för att ange var användardata kommer in i pipelinen och **webtjänstutdatamodulen** används för att ange var användardata returneras i en pipeline för inferens i realtid.

## <a name="how-to-use-web-service-inputoutput"></a>Så här använder du in-/utdata för webbtjänsten

- När du skapar en inferenpipeline i realtid från träningspipelinen läggs **modulen Webbtjänstinmatning** och **webbtjänstutdata** automatiskt till för att visa var användardata kommer in i pipelinen och var data returneras. 

    Läs mer om [att skapa en inferenpipeline i realtid](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Att automatiskt generera inferenpipeline i realtid är en regelbaserad metod för bästa insats, det finns ingen garanti för riktigheten. Du kan manuellt lägga till eller ta bort **moduler för in- och utdata till webbtjänsten** för att uppfylla dina krav. Kontrollera att det finns minst en **inmatningsmodul för webbtjänsten** och en **webbtjänstutmatningsmodul** i pipelinen för inferens i realtid. Om du har flera moduler för **inmatning av webbtjänst** eller **webbtjänstutdata** kontrollerar du att de har unika namn som du kan mata in namnet på modulens högra panel.

- Du kan också manuellt skapa en inferenspipeline i realtid genom att lägga till **webbtjänstindata-** och **webbtjänstutdatamoduler** i den oinlämnade pipelinen.

    > [!NOTE]
    >  Pipeline-typen bestäms första gången du skickar den. Så se till att lägga till **Web Service Input** och Web Service **Output** modul innan du skickar för första gången om du vill skapa en pipeline för inferen i realtid.

   Nedan visas hur du manuellt skapar inferenspipeline i realtid från Execute Python Script-modulen. **Execute Python Script** 

   ![Exempel](media/module/web-service-input-output-example.png)
   
   När du har skickat pipelinen och körningen har slutförts kan du distribuera slutpunkten i realtid.
   
   > [!NOTE]
   >  I exemplet ovan tillhandahåller **Ange data manuellt dataschemat** för webbtjänstinmatning och är nödvändigt för att distribuera slutpunkten i realtid. I allmänhet bör du alltid ansluta en modul eller datauppsättning till porten som **Webbtjänstindata** är ansluten för att tillhandahålla dataschemat.
   
## <a name="next-steps"></a>Nästa steg
Läs mer om [att distribuera slutpunkten i realtid](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.