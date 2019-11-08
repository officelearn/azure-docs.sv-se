---
title: Konfigurera en QnA Maker tjänst – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du kan skapa en QnA Maker kunskaps banker måste du först konfigurera en QnA Maker tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan konfigurera en QnA Maker-tjänst.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6d52062561e3f08a214f3e191706583edc844786
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794216"
---
# <a name="manage-qna-maker-resources"></a>Hantera QnA Maker resurser

Innan du kan skapa en QnA Maker kunskaps banker måste du först konfigurera en QnA Maker tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan konfigurera en QnA Maker-tjänst.

## <a name="types-of-keys-in-qna-maker"></a>Typer av nycklar i QnA Maker

Din QnA Maker-tjänst hanterar två typer av nycklar: **prenumerations nycklar** och **slut punkts nycklar**.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

|Namn|Plats|Syfte|
|--|--|--|
|Prenumerationsnyckel|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Dessa nycklar används för att få åtkomst till [API: erna för QNA Maker Management-tjänsten](https://go.microsoft.com/fwlink/?linkid=2092179). Med dessa API: er kan du redigera frågorna och svaren i din kunskaps bas och publicera din kunskaps bas. Nycklarna skapas när du skapar en ny QnA Maker-tjänst.<br><br>Sök efter dessa nycklar på **Cognitive Services** resurs på sidan **nycklar** .|
|Slut punkts nyckel|[QnA Maker Portal](http://www.qnamaker.ai)|Dessa nycklar används för att få åtkomst till den publicerade kunskaps bas slut punkten för att få svar på en användar fråga. Du använder vanligt vis den här slut punkten i din Chat-robot eller i klient program koden som ansluter till QnA Maker-tjänsten. De här nycklarna skapas när du publicerar din QnA Maker-kunskaps bas.<br><br>Sök efter dessa nycklar på sidan **tjänst inställningar** . Hitta den här sidan från användarens meny längst upp till höger på sidan på den nedrullningsbara menyn.|

## <a name="create-a-new-qna-maker-service"></a>Skapa en ny QnA Maker-tjänst

Den här proceduren skapar de Azure-resurser som krävs för att hantera innehållet i kunskaps basen. När du har slutfört de här stegen hittar du _prenumerations_ nycklarna på sidan **nycklar** för resursen i Azure Portal.

1. Logga in på Azure Portal och [skapa en QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resurs.

1. Välj **skapa** efter att du har läst de allmänna villkoren:

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QNA Maker**väljer du lämpliga nivåer och regioner:

    ![Skapa en ny QnA Maker tjänst – pris nivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * I fältet **namn** anger du ett unikt namn som identifierar den här QNA Makers tjänsten. Namnet identifierar också QnA Maker slut punkten som dina kunskaps baser kommer att associeras med.
    * Välj den **prenumeration** som QNA Maker resursen ska distribueras under.
    * Välj **pris nivå** för QNA Maker hanterings tjänster (portal-och hanterings-API: er). Se [Mer information om SKU-prissättning](https://aka.ms/qnamaker-pricing).
    * Skapa en ny **resurs grupp** (rekommenderas) eller Använd en befintlig som distribuerar den här QNA Maker resursen. QnA Maker skapar flera Azure-resurser. När du skapar en resurs grupp som innehåller dessa resurser kan du enkelt hitta, hantera och ta bort dessa resurser med resurs gruppens namn.
    * Välj en **resurs grupps plats**.
    * Välj **pris nivå för sökning** i Azure kognitiv sökning-tjänsten. Om alternativet för den kostnads fria nivån inte är tillgängligt (visas nedtonat) innebär det att du redan har en kostnads fri tjänst som distribuerats via din prenumeration. I så fall måste du börja med Basic-nivån. Se [pris information för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **Sök plats** där du vill att Azure kognitiv sökning-index ska distribueras. Begränsningar för var kund information måste lagras hjälper dig att avgöra vilken plats du väljer för Azure Kognitiv sökning.
    * Ange ett namn på Azure App Service-instansen i fältet **namn på App** .
    * Standardvärdet är App Service standard nivån (S1). Du kan ändra planen när du har skapat den. Läs mer om [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).
    * Välj **plats för webbplatsen** där App Service ska distribueras.

        > [!NOTE]
        > **Sök platsen** kan skilja sig från **webbplatsens plats**.

    * Välj om du vill aktivera **Application Insights**. Om **Application Insights** är aktive rad samlar QNA Maker in telemetri om trafik, chat-loggar och fel.
    * Välj **platsen för App Insights** där Application Insightss resursen ska distribueras.
    * För kostnads besparingar kan du [dela](#share-existing-services-with-qna-maker) några men inte alla Azure-resurser som skapats för QNA Maker. 

1. När alla fält har verifierats väljer du **skapa**. Processen kan ta några minuter att slutföra.

1. När distributionen har slutförts visas följande resurser som skapats i din prenumeration:

   ![Resurs skapade en ny QnA Maker tjänst](../media/qnamaker-how-to-setup-service/resources-created.png)

    Resursen med _Cognitive Services_ typen har dina _prenumerations_ nycklar.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Hitta prenumerations nycklar i Azure Portal

Du kan visa och återställa dina prenumerations nycklar från Azure Portal, där du skapade QnA Maker resursen.

1. Gå till QnA Maker resursen i Azure Portal och välj den resurs som har _Cognitive Servicess_ typ:

    ![QnA Maker resurs lista](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar**:

    ![Prenumerationsnyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Hitta slut punkts nycklar i QnA Maker Portal

Slut punkten finns i samma region som resursen eftersom slut punkts nycklarna används för att anropa kunskaps basen.

Slut punkts nycklar kan hanteras från [QNA Maker-portalen](https://qnamaker.ai).

1. Logga in på [QNA Maker Portal](https://qnamaker.ai), gå till din profil och välj sedan **tjänst inställningar**:

    ![Slut punkts nyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller Återställ dina nycklar:

    ![Slut punkts nyckel hanterare](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tror att de har komprometterats. Detta kan kräva motsvarande ändringar i klient programmet eller bot koden.

## <a name="share-existing-services-with-qna-maker"></a>Dela befintliga tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. Använd följande tabell för att ta reda på vad du kan och inte kan dela för att minska hanteringen och dra nytta av kostnads delning:

|Tjänst|Dela|Orsak|
|--|--|--|
|Cognitive Services|X|Inte möjlig enligt design|
|App Service-plan|✔|Fast disk utrymme som allokerats för en App Service plan. Om andra appar som delar samma App Service plan använder betydande disk utrymme, kommer QnAMaker App Service-instansen att drabbas av problem.|
|App Service|X|Inte möjlig enligt design|
|Application Insights|✔|Kan delas|
|Söktjänst|✔|1. `testkb` är ett reserverat namn för QnAMaker-tjänsten. den kan inte användas av andra.<br>2. synonym mappningen med namnet `synonym-map` är reserverad för QnAMaker-tjänsten.<br>3. antalet publicerade kunskaps baser är begränsat av Sök tjänst nivån. Om det finns lediga index är andra tjänster som kan använda dem.|

Läs mer om [App Service](../../../app-service/index.yml) och [search service](../../../search/index.yml).

### <a name="using-a-single-search-service"></a>Använda en enda Sök tjänst

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en Sök tjänst för dig och länkas till QnA Maker tjänsten. När resurserna har skapats kan du uppdatera App Service-inställningen för att använda en tidigare befintlig Sök tjänst och ta bort den som du nyss skapade.

Om du skapar en QnA-tjänst via Azure Resource Manager mallar kan du skapa alla resurser och kontrol lera App Service skapandet för att använda en befintlig Sök tjänst.

## <a name="upgrade-qna-maker"></a>Uppgradera QnA Maker

|Uppgradera|Orsak|
|--|--|
|[Uppgradera](#upgrade-qna-maker-sku) SKU för QnA Maker hantering|Du vill ha fler frågor och svar i din kunskaps bas.|
|[Uppgradera](#upgrade-app-service) App Service SKU|Kunskaps basen behöver för att betjäna fler förfrågningar från din klient app, till exempel en chatt-robot.|
|[Uppgradera](#upgrade-the-azure-cognitive-search-service) Azure Kognitiv sökning-tjänst|Du planerar att ha många kunskaps baser.|


### <a name="upgrade-qna-maker-sku"></a>Uppgradera QnA Maker SKU

Om du vill ha fler frågor och svar i din kunskaps bas, utöver din nuvarande nivå, uppgraderar du QnA Maker-tjänstens pris nivå.

Så här uppgraderar du SKU: n för QnA Maker hantering:

1. Gå till din QnA Maker-resurs i Azure Portal och välj **pris nivå**.

    ![QnA Maker resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Välj lämplig SKU och tryck på **Välj**.

    ![QnA Maker priser](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Uppgradera App Service

 När din kunskaps bas behöver hantera fler förfrågningar från din klient program, uppgraderar du App Service pris nivå.

Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller skala ut app service.

Gå till App Service resursen i Azure Portal och välj alternativet **skala upp** eller **skala ut** efter behov.

![QnA Maker App Service skala](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Uppgradera Azure Kognitiv sökning-tjänsten

Om du planerar att ha många kunskaps baser uppgraderar du pris nivån för Azure Kognitiv sökning-tjänsten.

För närvarande kan du inte utföra en uppgradering på plats av Azure Search-SKU: n. Du kan dock skapa en ny Azure Search-resurs med önskad SKU, återställa data till den nya resursen och sedan länka den till QnA Maker stacken. Det gör du genom att följa dessa steg:

1. Skapa en ny Azure Search-resurs i Azure Portal och välj önskad SKU.

    ![QnA Maker Azure Search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Återställ indexen från din ursprungliga Azure Search-resurs till den nya. Se [exempel koden för säkerhets kopierings återställning](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. När data har återställts går du till din nya Azure Search-resurs, väljer **nycklar**och skriver ned **namnet** och **Administratörs nyckeln**:

    ![QnA Maker Azure Search-nycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Om du vill länka den nya Azure Search-resursen till QnA Maker stack går du till QnA Maker App Service-instansen.

    ![QnA Maker App Service instans](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Välj **program inställningar** och ändra inställningarna i fälten **AzureSearchName** och **AzureSearchAdminKey** från steg 3.

    ![QnA Maker App Service inställning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Starta om App Service-instansen.

    ![Starta om QnA Maker App Service-instansen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Hämta de senaste körnings uppdateringarna

QnAMaker runtime är en del av Azure App Service-instansen som distribueras när du [skapar en QnAMaker-tjänst](./set-up-qnamaker-service-azure.md) i Azure Portal. Uppdateringar görs regelbundet till körnings miljön. QnA Maker App Service-instansen är i läget för automatisk uppdatering efter 2019-versionen av webbplats tillägget (version 5 +). Den här uppdateringen är utformad för att ta hand om noll stillestånds tid under uppgraderingar.

Du kan kontrol lera din aktuella version på https://www.qnamaker.ai/UserSettings. Om din version är äldre än version 5. x måste du starta om App Service för att tillämpa de senaste uppdateringarna:

1. Gå till din QnAMaker-tjänst (resurs grupp) i [Azure Portal](https://portal.azure.com).

    ![QnAMaker Azure-resurs grupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj App Service instansen och öppna avsnittet **Översikt** .

    ![QnAMaker App Service instans](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Starta om App Service. Uppdaterings processen bör avslutas på några sekunder. Alla beroende program eller robotar som använder den här QnAMaker-tjänsten kommer inte att vara tillgängliga för slutanvändarna under den här omstarts perioden.

    ![Omstart av QnAMaker App Service-instansen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Region för hanterings tjänst

Hanterings tjänsten för QnA Maker används endast för QnA Maker-portalen och för inledande data bearbetning. Den här tjänsten är endast tillgänglig i regionen USA, västra. Ingen kund information lagras i den här västra USA-tjänsten.

## <a name="next-steps"></a>Nästa steg

Läs mer om [App Service](../../../app-service/index.yml) och [search service](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskaps bas](../Quickstarts/create-publish-knowledge-base.md)
