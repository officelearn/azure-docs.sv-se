---
title: 'Snabbstart: Skapa, träna och publicera kunskapsbas - QnA Maker'
description: Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. Den här artikeln innehåller ett exempel på att skapa en QnA Maker-kunskapsbas från en enkel faq-webbsida för att svara på frågor som QnA Maker.
ms.topic: conceptual
ms.date: 02/08/2020
ms.openlocfilehash: b1f80006e2dc72b5b623f4c29c093c734dc1efea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220582"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Snabbstart: Skapa, träna och publicera din QnA Maker-kunskapsbas

Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. Den här artikeln innehåller ett exempel på att skapa en QnA Maker-kunskapsbas från en enkel faq-webbsida för att svara på frågor som QnA Maker.

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
> * En QnA [Maker-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) som skapats i Azure-portalen. Kom ihåg ditt Azure Active Directory-ID, Prenumeration, QnA-resursnamn som du valde när du skapade resursen.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Skapa din första QnA Maker-kunskapsbas

1. Logga in [QnAMaker.ai](https://QnAMaker.ai) på QnAMaker.ai-portalen med dina Azure-autentiseringsuppgifter.

1. I QnA Maker-portalen väljer du **Skapa en kunskapsbas**.

1. Hoppa över steg **1** på sidan **Skapa** om du redan har din QnA Maker-resurs.

    Om du inte har skapat resursen ännu väljer du **Skapa en QnA-tjänst**. Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Kom ihåg ditt Azure Active Directory-ID, Prenumeration, QnA-resursnamn som du valde när du skapade resursen.

    När du är klar med att skapa resursen i Azure-portalen går du tillbaka till QnA Maker-portalen, uppdaterar webbläsarsidan och fortsätter till **steg 2**.

1. I **steg 3**väljer du din Active Directory, prenumeration, tjänst (resurs) och språket för alla kunskapsbaser som skapats i tjänsten.

   ![Skärmbild av att välja en kunskapsbas för QnA Maker-tjänsten](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. I **steg 3**namnger du kunskapsbasen **Mitt exempel QnA KB**.

1. Konfigurera inställningarna med följande tabell i **steg 4:**

    |Inställning|Värde|
    |--|--|
    |**Aktivera extrahering med flera svängar från webbadresser, PDF- eller .docx-filer.**|Markerad|
    |**Standardsvarstext**| `Quickstart - default answer not found.`|
    |**+ Lägg till URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chatt**|Välj **Professional**|

1. I **steg 5**väljer du **Skapa kb.**

    Extraheringsprocessen tar en stund att läsa dokumentet och identifiera frågor och svar.

    När QnA Maker har skapat kunskapsbasen öppnas sidan **Kunskapsbas.** Du kan redigera innehållet i kunskapsbasen på den här sidan.

## <a name="add-a-new-question-and-answer-set"></a>Lägga till en ny fråge- och svarsuppsättning

1. På sidan Redigera på portalen **Redigera** i QnA Maker väljer du + Lägg **till QnA-par** i kontextverktygsfältet.
1. Lägg till följande fråga:

    `How many Azure services are used by a knowledge base?`

1. Lägg till svaret formaterat med _markdown:_

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Lägg till frågan som text och svaret formaterat med markeringsmarkering.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Markeringssymbolen `*`används för punktlistor. Används `\n` för en ny rad.

    På sidan **Redigera** visas markeringen. När du använder **testpanelen** senare visas markeringen korrekt.

## <a name="save-and-train"></a>Spara och öva

Längst upp till höger väljer du **Spara och tränar** för att spara dina redigeringar och träna QnA Maker . Redigeringarna försvinner om de inte sparas.

## <a name="test-the-knowledge-base"></a>Testa kunskapsbasen

1. I QnA Maker-portalen, längst upp till höger, väljer du **Testa** för att testa att ändringarna du gjorde trädde i kraft.
1. Ange en exempelanvändarfråga i textrutan.

    `How many Azure services are used by a knowledge base?`

    ![ Ange en exempelanvändarfråga i textrutan. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Välj **Granska** för att undersöka svaret i detalj. Testfönstret används för att testa dina ändringar i kunskapsbasen innan du publicerar din kunskapsbas.

1. Välj **Testa** igen om du vill stänga **testpanelen.**

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen

När du publicerar en kunskapsbas flyttas innehållet `test` i kunskapsbasen från indexet till ett `prod` index i Azure-sökning.

![Skärmbild av hur du flyttar innehållet i kunskapsbasen](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Välj **Publicera**i QnA Maker-portalen . Bekräfta sedan genom att välja **Publicera** på sidan.

    QnA Maker-tjänsten har nu publicerats. Du kan använda slutpunkten i ditt program eller din robotkod.

    ![Skärmbild av lyckad publicering](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Skapa en bot

När du har publicerat kan du skapa en robot från sidan **Publicera:**

* Du kan skapa flera robotar snabbt, alla pekar på samma kunskapsbas för olika regioner eller prisplaner för enskilda robotar.
* Om du bara vill ha en bot för kunskapsbasen använder du **länken Visa alla dina robotar på Azure-portalen** för att visa en lista över dina aktuella robotar.

När du gör ändringar i kunskapsbasen och publicerar om behöver du inte vidta ytterligare åtgärder med roboten. Den är redan konfigurerad för att fungera med kunskapsbasen och fungerar med alla framtida ändringar i kunskapsbasen. Varje gång du publicerar en kunskapsbas uppdateras alla robotar som är anslutna till den automatiskt.

1. Välj **Skapa bot**på sidan **Publicera** på portalen För QnA Maker . Den här knappen visas först när du har publicerat kunskapsbasen.

    ![Skärmbild av hur du skapar en robot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. En ny webbläsarflik öppnas för Azure-portalen, med Azure Bot-tjänstens skapandesida. Konfigurera Azure bot-tjänsten. Roboten och QnA Maker kan dela webbapptjänstplanen, men kan inte dela webbappen. Det innebär att **appnamnet** för roboten måste skilja sig från appnamnet för QnA Maker-tjänsten.

    * **Gör följande**
        * Ändra bot handtag - om det inte är unikt.
        * Välj SDK-språk. När boten har skapats kan du ladda ner koden till din lokala utvecklingsmiljö och fortsätta utvecklingsprocessen.
    * **Gör inte följande**
        * ändra följande inställningar i Azure-portalen när du skapar roboten. De är förifyllda för din befintliga kunskapsbas:
           * QnA Auth-nyckel
           * App serviceplan och plats


1. När roboten har skapats öppnar du **bot-tjänstresursen.**
1. Under **Bot Management**väljer du Testa i **webbchatt**.
1. I chattprodunden **för Skriv in meddelandet**anger du:

    `Azure services?`

    Chattroboten svarar med ett svar från din kunskapsbas.

    ![Ange en användarfråga i testwebbchatten.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Vad åstadkom du?

Du skapade en ny kunskapsbas, lade till en offentlig WEBBADRESS i kunskapsbasen, lade till din egen QnA-uppsättning, tränade, testade och publicerade kunskapsbasen.

När du har publicerat kunskapsbasen skapade du en bot och testade roboten.

Allt detta skedde på några minuter utan att behöva skriva någon kod eller rensa innehållet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte fortsätter till nästa snabbstart tar du bort ramresurserna QnA Maker och Bot i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till frågor med metadata](add-question-metadata-portal.md)

Mer information:

* [Markdown-format i svar](../reference-markdown-format.md)
* QnA [Maker-datakällor](../concepts/knowledge-base.md).


