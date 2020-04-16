---
title: Konfigurera en QnA Maker-tjänst - QnA Maker
description: Innan du kan skapa några QnA Maker-kunskapsbaser måste du först konfigurera en QnA Maker-tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan skapa en QnA Maker-tjänst.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402994"
---
# <a name="manage-qna-maker-resources"></a>Hantera QnA Maker-resurser

Innan du kan skapa några QnA Maker-kunskapsbaser måste du först konfigurera en QnA Maker-tjänst i Azure. Alla som har behörighet att skapa nya resurser i en prenumeration kan skapa en QnA Maker-tjänst.

En gedigen förståelse för följande begrepp är till hjälp innan du skapar resursen:

* [QnA Maker-resurser](../Concepts/azure-resources.md)
* [Redigerings- och publiceringsnycklar](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Skapa en ny QnA Maker-tjänst

Den här proceduren skapar de Azure-resurser som behövs för att hantera kunskapsbasinnehållet. När du har slutfört de här stegen hittar du _prenumerationsnycklarna_ på sidan **Nycklar** för resursen i Azure-portalen.

1. Logga in på Azure-portalen och [skapa en QnA](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) Maker-resurs.

1. Välj **Skapa** när du har läst villkoren:

    ![Skapa en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. I **QnA Maker**väljer du lämpliga nivåer och regioner:

    ![Skapa en ny QnA Maker-tjänst – prisnivå och regioner](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Ange ett unikt namn i fältet **Namn** för att identifiera den här QnA Maker-tjänsten. Det här namnet identifierar också QnA Maker-slutpunkten som dina kunskapsbaser kommer att associeras med.
    * Välj den **prenumeration** som QnA Maker-resursen ska distribueras under.
    * Välj **prisnivå** för QnA Maker-hanteringstjänster (portal- och hanterings-API:er). Se [mer information om SKU prissättning](https://aka.ms/qnamaker-pricing).
    * Skapa en ny **resursgrupp** (rekommenderas) eller använd en befintlig resurs där du kan distribuera den här QnA Maker-resursen. QnA Maker skapar flera Azure-resurser. När du skapar en resursgrupp för att lagra dessa resurser kan du enkelt hitta, hantera och ta bort dessa resurser efter resursgruppsnamnet.
    * Välj en **resursgruppsplats**.
    * Välj **prisnivån Sök** i Azure Cognitive Search-tjänsten. Om alternativet Kostnadsfri nivå inte är tillgängligt (visas nedtonat) betyder det att du redan har en kostnadsfri tjänst distribuerad via din prenumeration. I så fall måste du börja med basic-nivån. Se [Azure Cognitive Search prisinformation](https://azure.microsoft.com/pricing/details/search/).
    * Välj den **sökplats** där du vill att Azure Cognitive Search-index ska distribueras. Begränsningar för var kunddata måste lagras hjälper dig att avgöra vilken plats du väljer för Azure Cognitive Search.
    * Ange ett namn för din Azure App Service-instans i fältet **Appnamn.**
    * Som standard är App Service standardnivå (S1). Du kan ändra planen när du har skapat den. Läs mer om [priser för App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Välj den **webbplatsplats** där App Service ska distribueras.

        > [!NOTE]
        > **Sökplatsen** kan skilja sig från **webbplatsplatsen**.

    * Välj om du vill aktivera **Application Insights**eller inte . Om **Application Insights** är aktiverat samlar QnA Maker in telemetri på trafik, chattloggar och fel.
    * Välj den plats för **appstatistik** där application insights-resursen ska distribueras.
    * För kostnadsbesparingsåtgärder kan du [dela](#configure-qna-maker-to-use-different-cognitive-search-resource) några men inte alla Azure-resurser som skapats för QnA Maker.

1. När alla fält har validerats väljer du **Skapa**. Processen kan ta några minuter att slutföra.

1. När distributionen är klar visas följande resurser som skapats i prenumerationen:

   ![Resursen har skapat en ny QnA Maker-tjänst](../media/qnamaker-how-to-setup-service/resources-created.png)

    Resursen med typen _Cognitive Services_ har dina _prenumerationsnycklar._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Hitta prenumerationsnycklar i Azure-portalen

Du kan visa och återställa dina prenumerationsnycklar från Azure-portalen, där du skapade QnA Maker-resursen.

1. Gå till QnA Maker-resursen i Azure-portalen och välj den resurs som har typen _Cognitive Services:_

    ![Resurslista för QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Gå till **Nycklar:**

    ![Prenumerationsnyckel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Hitta slutpunktsnycklar i QnA Maker-portalen

Slutpunkten finns i samma region som resursen eftersom slutpunktsnycklarna används för att ringa ett anrop till kunskapsbasen.

Slutpunktsnycklar kan hanteras från [QnA Maker-portalen](https://qnamaker.ai).

1. Logga in på [QnA Maker-portalen,](https://qnamaker.ai)gå till din profil och välj sedan **Tjänstinställningar:**

    ![Slutpunktsnyckel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visa eller återställ dina nycklar:

    > [!div class="mx-imgBorder"]
    > ![Nyckelhanterare för slutpunkt](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Uppdatera dina nycklar om du tror att de har utsatts för intrång. Detta kan kräva motsvarande ändringar i klientprogrammet eller botkoden.

## <a name="upgrade-qna-maker-sku"></a>Uppgradera QnA Maker SKU

När du vill ha fler frågor och svar i din kunskapsbas, utöver din nuvarande nivå, uppgraderar du qnA Maker-tjänstprisnivån.

Så här uppgraderar du QnA Maker management SKU:

1. Gå till din QnA Maker-resurs i Azure-portalen och välj **prisnivå**.

    ![QnA Maker-resurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Välj lämplig SKU och tryck på **Välj**.

    ![QnA Maker prissättning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Uppgradera apptjänst

 När din kunskapsbas behöver visa fler förfrågningar från klientappen uppgraderar du prisnivån för App Service.

Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller skala ut App Service.

Gå till App Service-resursen i Azure-portalen och välj alternativet **Skala upp** eller **skala ut** efter behov.

![QnA Maker App Service skala](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Uppgradera Azure Cognitive Search-tjänsten

Om du planerar att ha många kunskapsbaser uppgraderar du prisnivån för Azure Cognitive Search-tjänsten.

För närvarande kan du inte utföra en uppgradering på plats av Azure search SKU. Du kan dock skapa en ny Azure-sökresurs med önskad SKU, återställa data till den nya resursen och sedan länka den till QnA Maker-stacken. Det gör du genom att följa dessa steg:

1. Skapa en ny Azure-sökresurs i Azure-portalen och välj önskad SKU.

    ![QnA Maker Azure-sökresurs](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Återställ indexen från den ursprungliga Azure-sökresursen till den nya. Se [exempelkoden för återställning av säkerhetskopiering](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. När data har återställts går du till din nya Azure-sökresurs, väljer **Nycklar**och skriver ned **namn-** och **administratörsnyckeln:**

    ![QnA Maker Azure-söknycklar](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Om du vill länka den nya Azure-sökresursen till QnA Maker-stacken går du till QnA Maker App Service-instansen.

    ![QnA Maker App Service-instans](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Välj **Programinställningar** och ändra inställningarna i fälten **AzureSearchName** och **AzureSearchAdminKey** från steg 3.

    ![QnA Maker App Service-inställning](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Starta om apptjänstinstansen.

    ![Omstart av QnA Maker App Service-instansen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Hämta de senaste runtime-uppdateringarna

QnAMaker-körningen är en del av Azure App Service-instansen som distribueras när du [skapar en QnAMaker-tjänst](./set-up-qnamaker-service-azure.md) i Azure-portalen. Uppdateringar görs regelbundet till körningen. QnA Maker App Service-instansen är i automatiskt uppdateringsläge efter släppet av webbplatstillägget april 2019 (version 5+). Den här uppdateringen är utformad för att ta hand om noll driftstopp under uppgraderingar.

Du kan kontrollera din https://www.qnamaker.ai/UserSettingsnuvarande version på . Om din version är äldre än version 5.x måste du starta om App Service för att kunna installera de senaste uppdateringarna:

1. Gå till din QnAMaker-tjänst (resursgrupp) i [Azure-portalen](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure-resursgrupp](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj App Service-instansen och öppna avsnittet **Översikt.**

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service-instans](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Starta om apptjänsten. Uppdateringsprocessen ska slutföras om ett par sekunder. Beroende program eller robotar som använder den här QnAMaker-tjänsten kommer inte att vara tillgängliga för slutanvändare under den här omstartsperioden.

    ![Omstart av QnAMaker App Service-instansen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Konfigurera QnA Maker så att den använder olika kognitiva sökresurser

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en söktjänst åt dig och länkas till QnA Maker-tjänsten. När dessa resurser har skapats kan du uppdatera apptjänstinställningen för att använda en tidigare befintlig söktjänst och ta bort den du just skapade.

QnA Maker's **App Service-resurs** använder cognitive search-resursen. För att ändra den kognitiva sökresurs som används av QnA Maker måste du ändra inställningen i Azure-portalen.

1. Hämta **administratörsnyckeln** och **namnet** på den kognitiva sökresurs som du vill att QnA Maker ska använda.

1. Logga in på [Azure-portalen](https://portal.azure.com) och hitta **apptjänsten som** är associerad med din QnA Maker-resurs. Båda med har samma namn.

1. Välj **Inställningar**och sedan **Konfiguration**. Då visas alla befintliga inställningar för QnA Maker's App Service.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av Azure-portalen som visar konfigurationsinställningar för App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Ändra värdena för följande tangenter:

    * **AzureSearchAdminKey**
    * **AzureSearchName (AzureSearchName)**

1. Om du vill använda de nya inställningarna måste du starta om apptjänsten. Välj **Översikt**och välj sedan **Starta om**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av App Service för Azure-portalen efter att konfigurationsinställningarna ändrats](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Om du skapar en QnA-tjänst via Azure Resource Manager-mallar kan du skapa alla resurser och styra skapandet av App-tjänsten för att använda en befintlig söktjänst.

Läs mer om hur du konfigurerar inställningarna för [Programinställningar för](../../../app-service/configure-common.md#configure-app-settings)App Service .

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Konfigurera inaktiv inställning för apptjänst för att undvika timeout

Apptjänsten, som betjänar QnA Maker-förutsägelsekörningen för en publicerad kunskapsbas, har en inaktiv timeout-konfiguration, som standard automatiskt timeout om tjänsten är inaktiv. För QnA Maker innebär detta att din förutsägelsekörning genererarAnswer API ibland time out efter perioder utan trafik.

För att hålla förutsägelseslutpunktsappen inläst även när det inte finns någon trafik ställer du in inaktiv till alltid på.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj din QnA Maker-resurss apptjänst. Det kommer att ha samma namn som QnA Maker-resursen, men den kommer att ha en annan **typ** av App Service.
1. Sök **inställningar** och välj sedan **Konfiguration**.
1. Välj **Allmänna inställningar**i konfigurationsfönstret och leta sedan upp **Alltid på**och välj **På** som värde.

    > [!div class="mx-imgBorder"]
    > ![Välj **Allmänna inställningar**i konfigurationsfönstret, leta sedan efter **Alltid på**och välj **På** som värde.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Välj **Spara** om du vill spara konfigurationen.
1. Du tillfrågas om du vill starta om appen för att använda den nya inställningen. Välj **Fortsätt**.

Läs mer om hur du konfigurerar [standardinställningarna](../../../app-service/configure-common.md#configure-general-settings)för App Service Allmänt .

## <a name="delete-azure-resources"></a>Ta bort Azure-resurser

Om du tar bort någon av de Azure-resurser som används för dina QnA Maker-kunskapsbaser fungerar inte kunskapsbaserna längre. Innan du tar bort några resurser måste du exportera kunskapsbaserna från sidan **Inställningar.**

## <a name="next-steps"></a>Nästa steg

Läs mer om [apptjänsten](../../../app-service/index.yml) och [söktjänsten](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Skapa och publicera en kunskapsbas](../Quickstarts/create-publish-knowledge-base.md)
