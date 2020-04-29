---
title: Indata/utdata för webbtjänst
description: Lär dig mer om webbtjänst-modulerna i Azure Machine Learning designer (för hands version)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462092"
---
# <a name="web-service-inputoutput"></a>Indata/utdata för webbtjänst

Den här artikeln beskriver modulen för **indata-** och **webb** tjänst i Azure Machine Learning designer (för hands version).

**Webb tjänstens indataport** kan bara ansluta till Indataporten med typen **DataFrameDirectory**. Och **webb tjänstens output-** modul kan bara anslutas från utdataporten med typen **DataFrameDirectory**. Du hittar de två modulerna i modul trädet under **webb tjänst** kategori. 

**Webb tjänstens inmatnings** modul används för att ange var användar data ska gå in i pipeline och **webb tjänstens output-** modul används för att ange var användar data returneras i en pipeline för real tids härledning.

## <a name="how-to-use-web-service-inputoutput"></a>Använda webb tjänstens indata/utdata

- När du skapar en härlednings pipeline i real tid från din utbildnings pipeline läggs **webb tjänstens indata-** och **webb tjänstens output** -modul till automatiskt för att visa var användar data går in i pipelinen och var data returneras. 

    Läs mer om att [skapa en pipeline för real tids härledning](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Automatisk generering av härlednings pipelinen i real tid är en regel baserad process för bästa prestanda, men det finns ingen garanti för att det är korrekt. Du kan lägga till eller ta bort moduler för **indata/utdata för webb tjänster** manuellt för att uppfylla dina krav. Se till att det finns minst en modul för **webb tjänst indata** och en **webb tjänst för utdata** i real tids härlednings pipelinen. Om du har flera **webb tjänst indata** eller moduler för **webb tjänst utmatning** kontrollerar du att de har unika namn, som du kan ange namnet på i den högra panelen i modulen.

- Du kan också skapa en pipeline för real tids härledning manuellt genom att lägga till **webb tjänstens indata** och moduler för **webb tjänstens utdata** i en inskickad pipeline.

    > [!NOTE]
    >  Pipeline-typen bestäms första gången du skickar den. Se därför till att lägga till utdata för **webb tjänst** och **webb tjänst** innan du skickar för första gången om du vill skapa en pipeline för real tids härledning.

   Nedan visas ett exempel på hur du skapar en real tids härlednings pipeline manuellt från **köra Python-skript** . 

   ![Exempel](media/module/web-service-input-output-example.png)
   
   När du har skickat pipelinen och körningen är klar kan du distribuera real tids slut punkten.
   
   > [!NOTE]
   >  I exemplet ovan anger du **data manuellt** som data schema för webb tjänstens indata och är nödvändigt för att distribuera real tids slut punkten. I allmänhet bör du alltid ansluta en modul eller data uppsättning till porten som **webb tjänstens indata** är anslutna till för att tillhandahålla data schemat.
   
## <a name="next-steps"></a>Nästa steg
Lär dig mer om [att distribuera real tids slut punkten](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.