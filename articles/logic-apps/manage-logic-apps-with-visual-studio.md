---
title: Redigera och hantera logikappar med hjälp av Visual Studio med Cloud Explorer
description: Redigera, uppdatera, hantera, lägga till källkontroll och distribuera logikappar med hjälp av Visual Studio med Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 6319d2d72df69cc9633bd2b2ff8e777c2a48966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270256"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Hantera Logic Apps i Visual Studio

Även om du kan skapa, redigera, hantera och distribuera logikappar i [Azure-portalen](https://portal.azure.com)kan du också använda Visual Studio när du vill lägga till dina logikappar i källkontroll, publicera olika versioner och skapa [Azure Resource Manager-mallar](../azure-resource-manager/management/overview.md) för olika distributionsmiljöer. Med Visual Studio Cloud Explorer kan du hitta och hantera dina logikappar tillsammans med andra Azure-resurser. Du kan till exempel öppna, ladda ned, redigera, köra, visa körningshistorik, inaktivera och aktivera logikappar som redan har distribuerats i Azure-portalen. Om du inte har arbetat med Azure Logic Apps i Visual Studio tidigare kan du läsa om hur du [skapar logikappar med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Distribuera eller publicera en logikapp från Visual Studio skriver över versionen av appen i Azure-portalen. Så om du gör ändringar i Azure-portalen som du vill behålla, se till att du [uppdaterar logikappen i Visual Studio](#refresh) från Azure-portalen innan du distribuerar eller publicerar från Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio 2019, 2017 eller 2015 - Community edition eller mer](https://aka.ms/download-visual-studio). I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 kontrollerar du att du väljer **Azure-utvecklingsarbetsbelastningen.**
    > Mer information finns i [Hantera resurser som är associerade med dina Azure-konton i Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Om du vill installera Cloud Explorer för Visual Studio 2015 [laddar du ned Cloud Explorer från Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Mer information finns i [Hantera resurser som är associerade med dina Azure-konton i Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 eller senare)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De senaste Azure Logic Apps-verktygen för Visual Studio-tillägget för den version du vill ha:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Se till att starta om Visual Studio när installationen är klar.

* Tillgång till webben när du använder den inbäddade Logic Apps Designer

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Hitta dina logikappar

I Visual Studio hittar du alla logikappar som är associerade med din Azure-prenumeration och distribueras i Azure-portalen med hjälp av Cloud Explorer.

1. Öppna Visual Studio. Välj **Cloud Explorer**på **Visa-menyn** .

1. Välj **Kontohantering**i Cloud Explorer . Välj den Azure-prenumeration som är associerad med dina logikappar och välj sedan **Använd**. Ett exempel:

   ![Välj "Kontohantering"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Gör så här beroende på om du söker efter **resursgrupper** eller **resurstyper:**

   * **Resursgrupper**: Under din Azure-prenumeration visar Cloud Explorer alla resursgrupper som är associerade med den prenumerationen. Expandera resursgruppen som innehåller logikappen och välj sedan logikappen.

   * **Resurstyper:** Expandera **Logic Apps**under din Azure-prenumeration . När Cloud Explorer visar alla distribuerade logikappar som är associerade med din prenumeration väljer du din logikapp.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Öppna i Visual Studio

I Visual Studio kan du öppna logikappar som tidigare skapats och distribuerats antingen direkt via Azure-portalen eller som Azure Resource Group-projekt med Visual Studio.

1. Öppna Cloud Explorer och hitta logikappen.

1. På logikappens snabbmeny väljer du **Öppna med Logic App Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   I det här exemplet visas logikappar efter resurstyp, så att logikapparna visas under avsnittet **Logic Apps.**

   ![Öppna distribuerad logikapp från Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   När logikappen har öppnats i Logic Apps Designer längst ned i designern kan du välja **Kodvy** så att du kan granska den underliggande logikappdefinitionsstrukturen. Om du vill skapa en distributionsmall för logikappen kan du läsa om hur du [hämtar en Azure Resource Manager-mall](#download-logic-app) för den logikappen. Läs mer om [Resource Manager-mallar](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Hämta från Azure

Du kan hämta logikappar från [Azure-portalen](https://portal.azure.com) och spara dem som Azure Resource Manager-mallar. [Azure Resource Manager](../azure-resource-manager/management/overview.md) Du kan sedan redigera mallarna lokalt med Visual Studio och anpassa logikappar för olika distributionsmiljöer.  Om du hämtar *logikappar parameters* deras definitioner automatiskt i [Resource Manager-mallar](../azure-resource-manager/templates/overview.md), som också använder JSON (JavaScript Object Notation).

1. Öppna Cloud Explorer i Visual Studio. Hitta och välj den logikapp som du vill hämta från Azure.

1. På appens snabbmeny väljer du **Öppna med Logic App Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   Logikappdesignern öppnas och visar logikappen. Om du vill granska logikappens underliggande definition och struktur väljer du **Kodvy**längst ned i designern .

1. Välj **Hämta**i verktygsfältet Designer .

   ![Ladda ned logikapp från Azure portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. När du uppmanas att ange en plats bläddrar du till den platsen och sparar resource manager-mallen för logikappdefinitionen i JSON-filformat (.json).

   Logikappdefinitionen visas `resources` i underavsnittet i Resurshanteraren-mallen. Nu kan du redigera logikappdefinitionen och Resource Manager-mallen med Visual Studio. Du kan också lägga till mallen som ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) i en Visual Studio-lösning. Lär dig mer om [Azure Resource Group-projekt för logikappar i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Länk till integrationskonto

Om du vill skapa logikappar för B2B-scenarier (Business-to-Business) kan du länka logikappen till ett tidigare skapat [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som finns i samma region som logikappen. Ett integrationskonto innehåller B2B-artefakter, till exempel handelspartner, avtal, scheman och kartor, och låter logikappen använda B2B-kopplingar för XML-validering och platt filkodning eller avkodning. Även om du kan [skapa den här länken med hjälp av Azure-portalen](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)kan du också använda Visual Studio efter att ha [uppfyllat förutsättningarna](#requirements)och din logikapp finns som en JSON-fil (.json) i ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Lär dig mer om [Azure Resource Group-projekt för logikappar i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. Öppna Azure Resource Group-projektet som innehåller logikappen i Visual Studio.

1. Öppna **<logik-app-namn>.json-filens** snabbmeny i Solution Explorer och välj **Öppna med Logikappdesigner**. (Tangentbord: Ctrl + L)

   ![Öppna logikappens .json-fil med Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio och Azure Logic Apps Tools-tillägget.

1. Kontrollera att Logic App Designer har fokus genom att välja designerns flik eller yta så att egenskapen Egenskaper visar egenskapen **Integrationskonto** för logikappen.

   ![Fönstret Egenskaper - egenskapen "Integrationskonto"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Om fönstret Egenskaper inte redan är öppet väljer du **Egenskapsfönster**på **Visa-menyn** . (Tangentbord: Tryck på F4)

1. Öppna egenskapslistan **integrationskonto** och välj det integrationskonto som du vill länka till logikappen, till exempel:

   ![Öppna egenskapslistan "Integrationskonto"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. När du är klar ska du komma ihåg att spara visual studio-lösningen.

När du anger egenskapen **Integration Konto** i Visual Studio och sparar logikappen som en Azure Resource Manager-mall, innehåller mallen även en parameterdeklaration för det valda integrationskontot. Mer information om mallparametrar och logikappar finns i [Översikt: Automatisera distribution av logikappar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Ändra distributionsplats

Om logikappen finns som en JSON-fil (.json) i ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) som du använder för att automatisera distributionen i Visual Studio, är den logikappen inställd på en platstyp och en viss plats. Den här platsen är antingen en Azure-region eller en befintlig [integrationstjänstmiljö (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Om du vill ändra logikappens platstyp eller plats måste du öppna logikappens arbetsflödesdefinition (.json) från Solution Explorer med hjälp av Logic App Designer. Du kan inte ändra dessa egenskaper med Hjälp av Cloud Explorer.

> [!IMPORTANT]
> Om du ändrar platstypen från **region** till [**integrationstjänstmiljö**](connect-virtual-network-vnet-isolated-environment-overview.md) påverkas logikappens [prismodell](logic-apps-pricing.md#fixed-pricing) som används för fakturering, [begränsningar,](logic-apps-limits-and-config.md#integration-account-limits) [support för integrationskonto](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)och så vidare. Innan du väljer en annan platstyp måste du förstå den resulterande effekten på logikappen.

1. Öppna Azure Resource Group-projektet som innehåller logikappen i Visual Studio.

1. Öppna filens snabbmeny i `<logic-app-name>.json` Solution Explorer och välj Öppna med **Logikappdesigner**. (Tangentbord: Ctrl + L)

   ![Öppna logikappens .json-fil med Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio och Azure Logic Apps Tools-tillägget.

1. Kontrollera att Logic App Designer har fokus genom att välja designerns flik eller yta så att fönstret Egenskaper visar egenskaperna **Välj platstyp** och **plats** för logikappen. Projektets platstyp är inställd på antingen **region-** eller **integrationstjänstmiljö**.

   ![Egenskapsfönster - "Välj platstyp" & egenskaper för plats](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Om fönstret Egenskaper inte redan är öppet väljer du **Egenskapsfönster**på **Visa-menyn** . (Tangentbord: Tryck på F4)

1. Om du vill ändra platstypen öppnar du egenskapslistan **Välj platstyp** och väljer den platstyp du vill använda.

   Om till exempel platstypen är **integrationstjänstmiljö**kan du välja **Region**.

   !["Välj platstyp" egenskap - ändra platstyp](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Om du vill ändra den specifika platsen öppnar du egenskapslistan **Plats.** Baserat på platstypen väljer du den plats du vill använda, till exempel:

   * Välj en annan Azure-region:

     ![Öppna egenskapslistan Plats, välj en annan Azure-region](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Välj en annan ISE:

     ![Öppna egenskapslistan "Plats", välj en annan ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. När du är klar ska du komma ihåg att spara visual studio-lösningen.

När du ändrar platstyp eller plats i Visual Studio och sparar logikappen som en Azure Resource Manager-mall, innehåller mallen även parameterdeklarationer för den platstypen och platsen. Mer information om mallparametrar och logikappar finns i [Översikt: Automatisera distribution av logikappar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Uppdatera från Azure

Om du redigerar logikappen i Azure-portalen och vill behålla ändringarna kontrollerar du att du uppdaterar appens version i Visual Studio med dessa ändringar.

* Välj **Uppdatera**i verktygsfältet Logikappdesigner i Visual Studio.

  ELLER

* Öppna logikappens snabbmeny i Visual Studio Cloud Explorer och välj **Uppdatera**.

![Uppdatera logikapp med uppdateringar](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicera uppdateringar av logikappen

När du är redo att distribuera dina logikappuppdateringar från Visual Studio till Azure väljer du **Publicera**i verktygsfältet Logic App Designer .

![Publicera uppdaterad logikapp till Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Köra logikappen manuellt

Du kan manuellt utlösa en logikapp som distribueras i Azure från Visual Studio. Välj **Kör utlösare**i verktygsfältet Logikappdesigner .

![Kör utlösaren manuellt för logikappen](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Granska körningshistorik

Om du vill kontrollera status och diagnostisera problem med logikappkörningar kan du granska information, till exempel indata och utdata, för de körningar i Visual Studio.

1. Öppna logikappens snabbmeny i Cloud Explorer och välj **Öppna körhistorik**.

   ![Öppna körningshistorik för logikappen](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Om du vill visa information om en viss körning dubbelklickar du på en körning. Ett exempel:

   ![Visa information om specifik körning](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Om du vill sortera tabellen efter egenskap markerar du kolumnrubriken för den egenskapen.

1. Expandera de steg vars in- och utdata du vill granska, till exempel:

   ![Visa in- och utdata för varje steg](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikappen

Utan att ta bort logikappen kan du stoppa utlösaren från att avfyras nästa gång utlösarvillkoret uppfylls. Om du inaktiverar logikappen kan logikappen skapa och köra framtida arbetsflödesinstanser för logikappen. Öppna logikappens snabbmeny i Cloud Explorer och välj **Inaktivera**.

![Inaktivera logikappen i Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> När du inaktiverar en logikapp instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de är klara, vilket kan ta tid att slutföra.

Om du vill återaktivera logikappen öppnar du logikappens snabbmeny i Cloud Explorer och väljer **Aktivera**.

![Aktivera logikapp i Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Ta bort din logikapp

Om du vill ta bort logikappen från Azure-portalen öppnar du logikappens snabbmeny i Cloud Explorer och väljer **Ta bort**.

![Ta bort logikappen från Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem. 

## <a name="troubleshooting"></a>Felsökning

När du öppnar ditt logikappprojekt i Logic Apps Designer kanske du inte får möjlighet att välja din Azure-prenumeration. I stället öppnas logikappen med en Azure-prenumeration som inte är den du vill använda. Detta beror på att När du har öppnat en logikapps .json-fil cachelagrar Visual Studio den första valda prenumerationen för framtida bruk. LÃ¶s problemet genom att fÃ¶ã¶sa något av följande:

* Byt namn på logikappens .json-fil. Prenumerationscachen beror på filnamnet.

* Om du vill ta bort tidigare valda prenumerationer för *alla* logikappar i din lösning tar du bort den dolda visual studio-inställningsmappen (.vs) i lösningens katalog. Den här platsen lagrar din prenumerationsinformation.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar distribuerade logikappar med Visual Studio. Läs sedan om hur du anpassar logikappdefinitioner för distribution:

> [!div class="nextstepaction"]
> [Författare logik appdefinitioner i JSON](../logic-apps/logic-apps-author-definitions.md)
