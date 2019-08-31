---
title: Konfigurera en QnA Maker tjänst – QnA Maker
titleSuffix: Azure Cognitive Services
description: Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195319"
---
# <a name="manage-qna-maker-resources"></a>Hantera QnA Maker resurser

Innan du kan skapa alla QnA Maker kunskapsbaser, måste du först ställa in QnA Maker-tjänsten i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan ställa in QnA Maker-tjänsten.

## <a name="types-of-keys-in-qna-maker"></a>Typer av nycklar i QnA Maker

QnA Maker-tjänsten innehåller två typer av nycklar, **prenumerationsnycklar** och **endpoint nycklar**.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Syfte|
|--|--|--|
|Prenumerationsnyckel|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Dessa nycklar används för att få åtkomst till [API: erna för QNA Maker Management-tjänsten](https://go.microsoft.com/fwlink/?linkid=2092179). Med dessa API: er kan du redigera frågorna och svaren i din kunskaps bas och publicera din kunskaps bas. Nycklarna skapas när du skapar en ny QnA Maker-tjänst.<br><br>Sök efter dessa nycklar på **Cognitive Services** resurs på sidan **nycklar** .|
|Slut punkts nyckel|[QnA Maker Portal](http://www.qnamaker.ai)|Dessa nycklar används för att få åtkomst till den publicerade kunskaps bas slut punkten för att få svar på en användar fråga. Du använder vanligt vis den här slut punkten i din chatt-robot eller klient program kod som ansluter till QnA Maker-tjänsten. De här nycklarna skapas när du publicerar din QnA Maker-kunskaps bas.<br><br>Sök efter de här nycklarna på sidan **tjänst inställningar** . Hitta den här sidan från det översta, högra användarens meny som ett alternativ i list rutan.|

## <a name="create-a-new-qna-maker-service"></a>Skapa en ny QnA Maker-tjänst

Den här proceduren skapar de Azure-resurser som krävs för att hantera innehållet i kunskaps basen. När du har slutfört de här stegen hittar du prenumerations nycklarna på sidan **nycklar** för resursen i Azure Portal.

1. Logga in på Azure Portal och [skapa en QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) resurs.

1. Välj **skapa** när du har läst de allmänna villkoren.

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QnA Maker**, väljer du lämpliga nivåer och regioner.

    ![Skapa en ny tjänst för QnA Maker - prisnivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Fyll i **namn** med ett unikt namn som identifierar den här QnA Maker-tjänsten. Det här namnet identifierar även QnA Maker-slutpunkten som din kunskapsbaser kommer att associeras.
    * Välj den **prenumeration** i vilket QnA Maker-resursen ska distribueras.
    * Välj **pris nivå** för QNA Maker hanterings tjänster (portal-och hanterings-API: er). Se [här](https://aka.ms/qnamaker-pricing) för information om priser för SKU: er.
    * Skapa en ny **resursgrupp** (rekommenderas) eller Använd en befintlig där du kan distribuera den här QnA Maker-resursen. QnA Maker skapar flera Azure-resurser. När du skapar en resurs grupp som innehåller dessa resurser kan du enkelt hitta, hantera och ta bort dessa resurser med resurs gruppens namn.
    * Välj en **resurs grupps plats**.
    * Välj den **Search prisnivån** för Azure Search-tjänsten. Om du ser den kostnadsfria nivån nedtonat innebär det att du redan har en kostnadsfri Azure Search-nivå som distribuerats i prenumerationen. I så fall behöver du börjar med den grundläggande Azure Search-nivån. Se information om priser för Azure search [här](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **sökplats** där du vill att Azure Search-data som ska distribueras. Begränsningar i där kunddata måste lagras informerar den plats som du väljer för Azure Search.
    * Namnge din App service i **appnamn**.
    * Som standard i App service som standard till standardnivån (S1). Du kan ändra planen efter har skapats. Se mer information om priser för Apptjänst [här](https://azure.microsoft.com/pricing/details/app-service/).
    * Välj den **plats för webbplats** där App Service ska distribueras.

        > [!NOTE]
        > Sökplats kan skilja sig från den plats för webbplats.

    * Välj om du vill aktivera **Application Insights** eller inte. Om **Application Insights** är aktiverad, QnA Maker samlar in telemetri på trafik, chattloggarna och fel.
    * Välj den **App insights-plats** där Application Insights-resurs ska distribueras.
    * För kostnads besparingar kan du [dela](#share-existing-services-with-qna-maker) några men inte alla Azure-resurser som skapats för QNA Maker. 

1. När alla fält har verifierats väljer du **skapa**. Det kan ta några minuter att slutföra.

1. När distributionen är klar visas i följande resurser som skapats i din prenumeration.

    ![Skapa en ny QnA Maker-tjänsten för resursen](../media/qnamaker-how-to-setup-service/resources-created.png)

    Resursen med typen _Cognitive Services_ har dina prenumerations nycklar.

## <a name="find-subscription-keys-in-azure-portal"></a>Hitta prenumerations nycklar i Azure Portal

Du kan visa och återställa dina prenumerations nycklar, som redigerar från Azure Portal där du skapade QnA Maker resursen. 

1. Gå till QnA Maker resursen i Azure Portal och Välj resursen med typen _Cognitive Services_.

    ![QnA Maker resurslistan](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **nycklar**.

    ![Prenumerationsnyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Hitta slut punkts nycklar i QnA Maker Portal

Slutpunkt-nycklar kan hanteras från den [QnA Maker portal](https://qnamaker.ai).

1. Logga in på [QNA Maker Portal](https://qnamaker.ai), gå till din profil och klicka sedan på **tjänst inställningar**.

    ![Slutpunktsnyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller återställa dina nycklar.

    ![viktiga endpoint-hanteraren](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tycker att de har komprometterats. Detta kan kräva motsvarande ändringar i klient programmet eller bot koden.

## <a name="share-existing-services-with-qna-maker"></a>Dela befintliga tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. För att minska hanteringen och dra nytta av kostnads delning kan du använda följande tabell för att förstå vad du kan och inte kan dela:

|Tjänsten|Dela|Reason|
|--|--|--|
|Cognitive Services|X|Inte möjlig enligt design|
|Apptjänstavtal|✔|Fast disk utrymme som allokerats för en app service-plan. Om andra appar, delar samma App Service plan, använder upp till ett stort disk utrymme, kommer QnAMaker App Service att stöta på problem.|
|Apptjänst|X|Inte möjlig enligt design|
|Application Insights|✔|Kan delas|
|Söktjänst|✔|1. `testkb` är ett reserverat namn för QnAMaker-tjänsten och den kan inte användas av andra.<br>2. Synonym mappning med namnet `synonym-map` är reserverat för QnAMaker-tjänsten.<br>3. Antalet publicerade KB begränsas av Sök tjänst nivån. Om det finns lediga index är andra tjänster som kan använda den.|

### <a name="using-a-single-search-service"></a>Använda en enda Sök tjänst

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en Sök tjänst för dig och är knuten till den QnA Maker tjänsten. När resurserna har skapats kan du uppdatera App Service-inställningen så att den använder en tidigare befintlig Sök tjänst och ta bort den helt skapade Sök tjänsten.

Om du skapar en QnA-tjänst via Azure Resource Manager mallar kan du skapa alla resurser och kontrol lera att App Service har skapats för att använda en befintlig Sök tjänst. 

## <a name="upgrade-qna-maker"></a>Uppgradera QnA Maker

|Uppgradera|Reason|
|--|--|
|[Uppgradera](#upgrade-qna-maker-sku) SKU för QnA Maker hantering|Du måste ha fler frågor och svar i din kunskaps bas.|
|[Uppgradera](#upgrade-app-service) App Service SKU|Din kunskaps bas behöver hantera fler förfrågningar från din klient app, till exempel en chatt-robot.|
|[Uppgradera](#upgrade-azure-search-service) Azure Search tjänst|Du planerar att ha många kunskaps baser.|


### <a name="upgrade-qna-maker-sku"></a>Uppgradera QnA Maker SKU

När du behöver fler frågor och svar i din kunskaps bas, utöver din nuvarande nivå, uppgraderar du QnA Maker-tjänstens pris nivå. 

Så här uppgraderar du SKU: n för QnA Maker hantering:

1. Gå till din QnA Maker-resurs i Azure Portal och välj **pris nivå**.

    ![QnA Maker resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Välj lämplig SKU och tryck på **Välj**.

    ![QnA Maker priser](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Uppgradera App Service

 När din kunskaps bas behöver hantera fler förfrågningar från din klient app uppgraderar du din app service-pris nivå.

Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller ned App Service.

Gå till App Service-resursen i Azure Portal och välj **skala upp** eller **ned ned** alternativ efter behov.

![QnA Maker App Service Scale](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Uppgradera Azure Search-tjänsten

När du planerar att ha många kunskaps baser uppgraderar du Azure Search-tjänstens pris nivå. 

För närvarande går det inte att utföra en uppgradering på plats av Azure Search-SKU: n. Du kan dock skapa en ny Azure Search-resurs med önskad SKU, återställa data till den nya resursen och sedan länka den till QnA Maker stacken.

1. Skapa en ny Azure Search-resurs i Azure Portal och välj önskad SKU.

    ![QnA Maker Azure Search-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Återställ indexen från din ursprungliga Azure Search-resurs till den nya. Se exempel koden för Backup Restore [här](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. När data har återställts går du till din nya Azure Search-resurs, väljer **nycklar**och noterar **namnet** och administratörs **nyckeln**.

    ![QnA Maker Azure Search-nycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Om du vill länka den nya Azure Search-resursen till QnA Maker stack går du till QnA Maker app service.

    ![QnA Maker AppService](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Välj **program inställningar** och ersätt fälten **AzureSearchName** och **AzureSearchAdminKey** från steg 3.

    ![QnA Maker AppService-inställning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Starta om App service.

    ![QnA Maker AppService-omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>Hämta de senaste körnings uppdateringarna

QnAMaker runtime är en del av de Azure App Service som distribueras när du [skapar en QnAMaker-tjänst](./set-up-qnamaker-service-azure.md) i Azure Portal. Uppdateringarna görs regelbundet för körningen. QnA Maker App Service är i läget för automatisk uppdatering efter version 2019 av webbplats tillägget (version 5 +). Detta är redan utformat för att ta hand om noll stillestånd vid uppgraderingar. 

Du kan kontrol lera din aktuella version https://www.qnamaker.ai/UserSettings på. Om din version är äldre än version 5. x måste du starta om App Service för att tillämpa de senaste uppdateringarna.

1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Klicka på App Service och öppna översiktsavsnittet

     ![Apptjänst för QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Starta om App service. Det bör vara klart inom några sekunder. Alla beroende program eller robotar som använder den här QnAMaker-tjänsten kommer inte att vara tillgängliga för slutanvändarna under den här omstarts perioden.

    ![QnAMaker appservice-omstart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Region för hanterings tjänst

Hanterings tjänsten för QnA Maker, som endast används för QnA Maker Portal & för inledande data bearbetning, är endast tillgänglig i västra USA. Ingen kund information lagras i den här västra USA-tjänsten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskapsbas](../Quickstarts/create-publish-knowledge-base.md)
