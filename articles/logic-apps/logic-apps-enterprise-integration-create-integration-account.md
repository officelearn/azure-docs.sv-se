---
title: Skapa eller hantera B2B-integrations konton
description: Skapa, länka och hantera integrations konton för företags integrering med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 3cdabbd5f527934492ce7ff37ae7d0f756d91fc1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979424"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Skapa och hantera integrations konton för B2B Enterprise-integration i Azure Logic Apps

Innan du kan skapa [Enterprise-integrering och B2B-lösningar](../logic-apps/logic-apps-enterprise-integration-overview.md) med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md) behöver du skapa ett integrationskonto, vilket är en separat Azure-resurs som ger en säker, skalbar, och hanterbar container för de integreringsartefakter som du definierar och använder med dina arbetsflöden för logikappar.

Du kan till exempel skapa, lagra och hantera B2B-artefakter, till exempel handels partner, avtal, kartor, scheman, certifikat och batch-konfigurationer. Innan din Logic-app kan arbeta med dessa artefakter och använda Logic Apps B2B-kopplingar måste du dessutom [Länka ditt integrations konto](#link-account) till din Logic app. Både ditt integrations konto och din Logic app måste finnas på *samma* plats eller region.

> [!TIP]
> Om du vill skapa ett integrations konto i en [integrerings tjänst miljö](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), se [skapa integrations konton i en ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Det här avsnittet visar hur du utför dessa uppgifter:

* Skapa ditt integrations konto.
* Länka ditt integrations konto till en Logic app.
* Ändra pris nivån för ditt integrations konto.
* Ta bort länken till integrations kontot från en Logic app.
* Flytta ditt integrations konto till en annan Azure-resurs grupp eller prenumeration.
* Ta bort ditt integrations konto.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Skapa integrationskonto

För den här uppgiften kan du använda antingen Azure Portal genom att följa stegen i det här avsnittet [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)eller [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. Välj **Skapa en resurs** på Azure-huvudmenyn. I sökrutan anger du "integrations konto" som filter och väljer **integrations konto**.

   ![Skapa nytt integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Under **integrations konto**väljer du **skapa**.

   ![Välj "Lägg till" för att skapa integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Ange den här informationen om ditt integrations konto:

   ![Ange information om integrations kontot](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*integration-account-name*> | Integrations kontots namn, som endast får innehålla bokstäver, siffror, bindestreck (`-`), under streck (`_`), parenteser (`(`, `)`) och punkter (`.`). I det här exemplet används "Fabrikam-integration". |
   | **Prenumeration** | Ja | <*Azure-prenumerationsnamn*> | Azure-prenumerationens namn |
   | **Resursgrupp** | Ja | <*Azure-resource-group-name*> | Namnet på den [Azure-resurs grupp](../azure-resource-manager/management/overview.md) som ska användas för att organisera relaterade resurser. I det här exemplet skapar du en ny resurs grupp med namnet "FabrikamIntegration-RG". |
   | **Prisnivå** | Ja | > på <*pris nivå* | Pris nivån för integrations kontot, som du kan ändra senare. I det här exemplet väljer du **kostnads fri**. Mer information finns i de här ämnena: <p>[pris modell](../logic-apps/logic-apps-pricing.md#integration-accounts) för - Logic Apps <p>- [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>priser för - [Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Plats** | Ja | <*Azure-region*> | Den region där du vill lagra dina metadata för integrations kontot. Välj antingen samma plats som din Logic app eller skapa Logi Kap par på samma plats som ditt integrations konto. I det här exemplet använder du "västra USA". <p>**Obs!** om du vill skapa ett integrations konto i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)väljer du denna ISE som plats. Mer information finns i [skapa integrations konton i en ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Inga | Av, på | Behåll inställningen **av** i det här exemplet. |
   |||||

1. När du är klar väljer du **skapa**.

   När distributionen är klar öppnas ditt integrations konto i Azure.

   ![Azure öppnar integrations kontot](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Innan din Logi Kap par kan använda ditt integrations konto, följer du stegen nedan för att länka ditt integrerings konto och din Logic app tillsammans.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Länka till Logic app

Om du vill ge dina Logi Kap par åtkomst till ett integrations konto som innehåller dina B2B-artefakter måste du först länka ditt integrations konto till din Logic app. Både Logic app-och integrations kontot måste finnas i samma region. Du kan utföra den här uppgiften med hjälp av Azure Portal. Om du använder Visual Studio och din Logic-app finns i ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)kan du [Länka din Logic app till ett integrations konto med hjälp av Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Leta upp och öppna din Logic app i Azure Portal.

1. Öppna en befintlig Logic-app eller skapa en ny Logic app i [Azure Portal](https://portal.azure.com).

1. På din Logic Apps-meny, under **Inställningar**, väljer du **arbets flödes inställningar**. Öppna listan **Välj ett integrations konto** under **integrations konto**. Välj det integrations konto som ska länkas till din Logic app.

   ![Välj ditt integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Slutför länkningen genom att välja **Spara**.

   ![Välj ditt integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   När integrations kontot har länkats visar Azure ett bekräftelse meddelande.

   ![Azure bekräftar en lyckad länk](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Nu kan din Logic-app använda artefakterna i ditt integrations konto plus B2B-kopplingarna, till exempel XML-verifiering och flat fil kodning eller avkodning.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Ändra prisnivå

Om du vill öka [gränserna](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) för ett integrations konto kan du [Uppgradera till en högre pris nivå](#upgrade-pricing-tier), om det är tillgängligt. Du kan till exempel uppgradera från den kostnads fria nivån till Basic-nivån eller standard nivån. Du kan också [nedgradera till en lägre nivå](#downgrade-pricing-tier), om det är tillgängligt. Mer information om pris information finns i följande avsnitt:

* [Prissättning för Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Uppgradera pris nivå

Om du vill göra den här ändringen kan du använda antingen Azure Portal genom att följa stegen i det här avsnittet eller i [Azure CLI](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. I den huvudsakliga Azure Search-rutan anger du "integration Accounts" som filter och väljer **integrations konton**.

   ![Hitta integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrations konton i dina Azure-prenumerationer.

1. Under **integrations konton**väljer du det integrations konto som du vill flytta. På integrations konto menyn väljer du **Översikt**.

   ![På menyn integrations konto väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. I översikts fönstret väljer du **uppgraderings pris nivå**, som visar en lista över tillgängliga högre nivåer. När du väljer en nivå börjar ändringen gälla.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Om du inte redan har gjort det måste du [Installera kraven för Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Öppna Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) -miljön i Azure Portal.

   ![Öppna Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. I kommando tolken anger du kommandot [ **AZ Resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)och anger `skuName` till den högre nivån som du vill ha.

   ```Azure CLI
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Om du till exempel har nivån Basic kan du ange `skuName` `Standard`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Nedgradera pris nivå

Använd [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)för att göra den här ändringen.

1. Om du inte redan har gjort det måste du [Installera kraven för Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Öppna Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) -miljön i Azure Portal.

   ![Öppna Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. I kommando tolken anger du kommandot [ **AZ Resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) och anger `skuName` till den lägre nivån som du vill ha.

   ```Azure CLI
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Om du till exempel har standard nivån kan du ange `skuName` `Basic`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Ta bort länk från Logic app

Om du vill länka din Logic app till ett annat integrations konto, eller om du inte längre vill använda ett integrations konto med din Logic-app, tar du bort länken med hjälp av Azure Resource Explorer.

1. Öppna webbläsarens fönster och gå till [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Logga in med samma autentiseringsuppgifter för Azure-kontot.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. I sökrutan anger du namnet på din Logic app så att du kan hitta och välja din Logic app.

   ![Hitta och välj Logic app](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. I namn listen i Utforskaren väljer du **Läs/skriv**.

   ![Aktivera läget Läs/skriv](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. På fliken **data** väljer du **Redigera**.

   ![På fliken "data" väljer du "redigera"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Leta upp `integrationAccount`-objektet i redigeraren och ta bort egenskapen som har det här formatet:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Ett exempel:

   ![Hitta "integrationAccount"-objekt](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. På fliken **data** väljer du **Lägg** för att spara ändringarna.

   ![Om du vill spara ändringarna väljer du "Lägg"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Leta upp och välj din Logic app i Azure Portal. Under appens **arbets flödes inställningar**kontrollerar du att **integrations kontots** egenskap nu är tom.

   ![Kontrol lera att integrations kontot inte är länkat](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Flytta integrations konto

Du kan flytta ditt integrations konto till en annan Azure-resurs grupp eller Azure-prenumeration. När du flyttar resurser skapar Azure nya resurs-ID: n, så se till att du använder de nya ID: na i stället och uppdaterar eventuella skript eller verktyg som är kopplade till de flyttade resurserna. Om du vill ändra prenumerationen måste du också ange en befintlig eller ny resurs grupp.

För den här uppgiften kan du använda antingen Azure Portal genom att följa stegen i det här avsnittet eller i [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. I den huvudsakliga Azure Search-rutan anger du "integration Accounts" som filter och väljer **integrations konton**.

   ![Hitta integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrations konton i dina Azure-prenumerationer.

1. Under **integrations konton**väljer du det integrations konto som du vill flytta. På integrations konto menyn väljer du **Översikt**.

   ![På menyn integrations konto väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Klicka på **ändra**bredvid antingen **resurs grupp** eller **prenumerations namn**.

   ![Ändra resurs grupp eller prenumeration](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Välj eventuella relaterade resurser som du vill flytta.

1. Använd följande steg för att ändra resurs gruppen eller prenumerationen baserat på ditt val:

   * Resurs grupp: Välj mål resurs gruppen från listan **resurs grupp** . Om du vill skapa en annan resurs grupp väljer du **skapa en ny resurs grupp**.

   * Prenumeration: Välj mål prenumeration från listan **prenumeration** . Välj mål resurs grupp i listan **resurs grupp** . Om du vill skapa en annan resurs grupp väljer du **skapa en ny resurs grupp**.

1. För att bekräfta att alla skript eller verktyg som är kopplade till de flyttade resurserna inte fungerar förrän du uppdaterar dem med de nya resurs-ID: n, markerar du rutan bekräftelse och väljer sedan **OK**.

1. När du är klar kontrollerar du att du uppdaterar alla skript med de nya resurs-ID: na för dina flyttade resurser.  

## <a name="delete-integration-account"></a>Ta bort integrations konto

För den här uppgiften kan du använda antingen Azure Portal genom att följa stegen i det här avsnittet, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)eller [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. I den huvudsakliga Azure Search-rutan anger du "integration Accounts" som filter och väljer **integrations konton**.

   ![Hitta integrations konto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrations konton i dina Azure-prenumerationer.

1. Under **integrations konton**väljer du det integrations konto som du vill ta bort. På integrations konto menyn väljer du **Översikt**.

   ![På menyn integrations konto väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. I översikts fönstret väljer du **ta bort**.

   ![I fönstret Översikt väljer du ta bort](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Bekräfta att du vill ta bort integrations kontot genom att välja **Ja**.

   ![Bekräfta borttagningen genom att välja Ja](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa handels partner i ditt integrations konto](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Skapa avtal mellan partner i ditt integrations konto](../logic-apps/logic-apps-enterprise-integration-agreements.md)
