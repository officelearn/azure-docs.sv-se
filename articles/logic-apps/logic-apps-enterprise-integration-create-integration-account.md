---
title: Skapa eller hantera B2B-integrationskonton
description: Skapa, länka och hantera integrationskonton för företagsintegrering med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270334"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Skapa och hantera integrationskonton för B2B-företagsintegreringar i Azure Logic Apps

Innan du kan skapa [Enterprise-integrering och B2B-lösningar](../logic-apps/logic-apps-enterprise-integration-overview.md) med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md) behöver du skapa ett integrationskonto, vilket är en separat Azure-resurs som ger en säker, skalbar, och hanterbar container för de integreringsartefakter som du definierar och använder med dina arbetsflöden för logikappar.

Du kan till exempel skapa, lagra och hantera B2B-artefakter, till exempel handelspartner, avtal, kartor, scheman, certifikat och batchkonfigurationer. Innan logikappen kan arbeta med dessa artefakter och använda Logic Apps B2B-kopplingar måste du länka [ditt integrationskonto](#link-account) till logikappen. Både ditt integrationskonto och logikapp måste finnas på *samma* plats eller region.

> [!TIP]
> Om du vill skapa ett integrationskonto i en [integrationstjänstmiljö](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)finns i [Skapa integrationskonton i en ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

I det här avsnittet visas hur du utför följande uppgifter:

* Skapa ditt integrationskonto.
* Länka ditt integrationskonto till en logikapp.
* Ändra prisnivån för ditt integrationskonto.
* Ta bort länken till ditt integrationskonto från en logikapp.
* Flytta ditt integrationskonto till en annan Azure-resursgrupp eller prenumeration.
* Ta bort ditt integrationskonto.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Skapa integrationskonto

För den här uppgiften kan du använda antingen Azure-portalen genom att följa stegen i det här avsnittet, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)eller [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. Välj **Skapa en resurs** på Azure-huvudmenyn. I sökrutan anger du "integrationskonto" som filter och väljer **Integrationskonto**.

   ![Skapa nytt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Under **Integrationskonto**väljer du **Skapa**.

   ![Välj "Lägg till" för att skapa integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Ange den här informationen om ditt integrationskonto:

   ![Ange information om integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*integration-konto-namn*> | Namnet på integrationskontot, som bara kan innehålla bokstäver, siffror,`_`bindestreck`(`( `)``-`), understreck ( ), parenteser ( och perioder (`.`). I det här exemplet används "Fabrikam-Integration". |
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Azure-prenumerationens namn |
   | **Resursgrupp** | Ja | <*Azure-resource-group-namn*> | Namnet på [azure-resursgruppen](../azure-resource-manager/management/overview.md) som ska användas för att organisera relaterade resurser. Skapa i det här exemplet en ny resursgrupp med namnet "FabrikamIntegration-RG". |
   | **Prisnivå** | Ja | <*prisnivå*> | Prisnivån för integrationskontot, som du kan ändra senare. I det här exemplet väljer du **Gratis**. Mer information finns i de här ämnena: <p>- [Logic Apps prismodell](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Begränsningar och konfiguration av Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Priser för Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Location** | Ja | <*Azure-region*> | Den region där du ska lagra metadata för ditt integrationskonto. Välj antingen samma plats som logikappen eller skapa logikapparna på samma plats som ditt integrationskonto. Använd i det här exemplet "Västra USA". <p>**Om**du vill skapa ett integrationskonto i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)väljer du ISE som plats. Mer information finns [i Skapa integrationskonton i en ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Logga Analytics** | Inga | Av, På | Behåll inställningen **Av** för det här exemplet. |
   |||||

1. När du är klar väljer du **Skapa**.

   När distributionen är klar öppnar Azure ditt integrationskonto.

   ![Azure öppnar integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Innan logikappen kan använda ditt integrationskonto följer du nästa steg för att länka ihop ditt integrationskonto och logikapp.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Länka till logikapp

Om du vill ge dina logikappar åtkomst till ett integrationskonto som innehåller dina B2B-artefakter måste du först länka ditt integrationskonto till logikappen. Både logikapp och integrationskonto måste finnas i samma region. För att slutföra den här uppgiften kan du använda Azure-portalen. Om du använder Visual Studio och logikappen finns i ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)kan du [länka logikappen till ett integrationskonto med Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Hitta och öppna logikappen i Azure-portalen.

1. Öppna en befintlig logikapp i [Azure-portalen](https://portal.azure.com)eller skapa en ny logikapp.

1. Välj **Arbetsflödesinställningar**under **Inställningar**på logikappens meny . Öppna listan Välj **ett integrationskonto under integrationskonto.** **Integration account** Välj det integrationskonto som ska länkas till logikappen.

   ![Välj ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Om du vill slutföra länkningen väljer du **Spara**.

   ![Välj ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   När ditt integrationskonto har länkats visar Azure ett bekräftelsemeddelande.

   ![Azure bekräftar lyckad länk](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Nu kan logikappen använda artefakterna i ditt integrationskonto plus B2B-kopplingarna, till exempel XML-validering och platt filkodning eller avkodning.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Ändra prisnivå

Om du vill öka [gränserna](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) för ett integrationskonto kan du [uppgradera till en högre prisnivå](#upgrade-pricing-tier)om det finns tillgängligt. Du kan till exempel uppgradera från den kostnadsfria nivån till basic-nivån eller standardnivån. Du kan också [nedgradera till en lägre nivå](#downgrade-pricing-tier), om det är tillgängligt. Mer information om prissättning finns i följande avsnitt:

* [Priser för Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Prisnivå för uppgradering

Om du vill göra den här ändringen kan du använda antingen Azure-portalen genom att följa stegen i det här avsnittet eller [Azure CLI](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. I den huvudsakliga Sökrutan för Azure anger du "integrationskonton" som filter och väljer **Integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrationskonton i dina Azure-prenumerationer.

1. Under **Integrationskonton**väljer du det integrationskonto som du vill flytta. På menyn för integrationskonto väljer du **Översikt**.

   ![På integrationskontomenyn väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Välj **Uppgradera prisnivå**i fönstret Översikt , som visar alla tillgängliga högre nivåer. När du väljer en nivå börjar ändringen omedelbart att gälla.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Om du inte redan har gjort det [installerar du Azure CLI-förutsättningarna](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Öppna Azure [**Cloud Shell-miljön**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) i Azure-portalen.

   ![Öppna Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. I kommandotolken anger du kommandot [ **az-resurs** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)och anger `skuName` den högre nivå som du vill ha.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Om du till exempel har basic-nivån `skuName` `Standard`kan du ställa in :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Nedgradera prisnivå

Använd [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)för att göra den här ändringen .

1. Om du inte redan har gjort det [installerar du Azure CLI-förutsättningarna](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Öppna Azure [**Cloud Shell-miljön**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) i Azure-portalen.

   ![Öppna Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. I kommandotolken anger du kommandot `skuName` [ **az-resurs** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) och anger den lägre nivå som du vill ha.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Om du till exempel har standardnivån `skuName` kan `Basic`du ställa in :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Ta bort länk från logikapp

Om du vill länka logikappen till ett annat integrationskonto eller inte längre använda ett integrationskonto med logikappen tar du bort länken med Hjälp av Azure Resource Explorer.

1. Öppna webbläsarfönstret och gå till [Azurehttps://resources.azure.com)Resource Explorer (](https://resources.azure.com). Logga in med samma Azure-kontouppgifter.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. I sökrutan anger du logikappens namn så att du kan hitta och välja logikappen.

   ![Hitta och välj logikapp](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Välj **Läs/skriv**i namnfältet för utforskaren .

   ![Aktivera läget "Läs/skriv"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Välj **Redigera**på fliken **Data** .

   ![På fliken "Data" väljer du "Redigera"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Leta reda på `integrationAccount` objektet i redigeraren och ta bort egenskapen, som har det här formatet:

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

   ![Hitta objekt för "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. På fliken **Data** väljer du **Placera** för att spara ändringarna.

   ![Om du vill spara ändringar väljer du "Sätt"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Leta reda på och välj logikapp i Azure-portalen. Kontrollera att egenskapen **integrationskonto** nu är tom under appens **arbetsflödesinställningar.**

   ![Kontrollera att integrationskontot inte är länkat](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Flytta integrationskonto

Du kan flytta ditt integrationskonto till en annan Azure-resursgrupp eller Azure-prenumeration. När du flyttar resurser skapar Azure nya resurs-ID:n, så se till att du använder de nya ID:na i stället och uppdatera skript eller verktyg som är associerade med de flyttade resurserna. Om du vill ändra prenumerationen måste du också ange en befintlig eller ny resursgrupp.

För den här uppgiften kan du använda antingen Azure-portalen genom att följa stegen i det här avsnittet eller [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. I den huvudsakliga Sökrutan för Azure anger du "integrationskonton" som filter och väljer **Integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrationskonton i dina Azure-prenumerationer.

1. Under **Integrationskonton**väljer du det integrationskonto som du vill flytta. På menyn för integrationskonto väljer du **Översikt**.

   ![På integrationskontomenyn väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Välj **ändra**bredvid **resursgrupp** eller **Prenumerationsnamn.**

   ![Ändra resursgrupp eller prenumeration](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Välj alla relaterade resurser som du också vill flytta.

1. Gör så här för att ändra resursgruppen eller prenumerationen, beroende på ditt val:

   * Resursgrupp: Välj målresursgruppen i listan **Resursgrupp.** Om du vill skapa en annan resursgrupp väljer du **Skapa en ny resursgrupp**.

   * Prenumeration: Välj målprenumeration i listan **Prenumeration.** Välj målresursgruppen i listan **Resursgrupp.** Om du vill skapa en annan resursgrupp väljer du **Skapa en ny resursgrupp**.

1. Om du vill bekräfta din förståelse för att skript eller verktyg som är associerade med de flyttade resurserna inte fungerar förrän du uppdaterar dem med de nya resurs-ID:na markerar du bekräftelserutan och väljer sedan **OK**.

1. När du är klar kontrollerar du att du uppdaterar alla skript med de nya resurs-ID:na för de flyttade resurserna.  

## <a name="delete-integration-account"></a>Ta bort integrationskonto

För den här uppgiften kan du använda antingen Azure-portalen genom att följa stegen i det här avsnittet, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)eller [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. I den huvudsakliga Sökrutan för Azure anger du "integrationskonton" som filter och väljer **Integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrationskonton i dina Azure-prenumerationer.

1. Under **Integrationskonton**väljer du det integrationskonto som du vill ta bort. På menyn för integrationskonto väljer du **Översikt**.

   ![På integrationskontomenyn väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Välj **Ta bort**i fönstret Översikt .

   ![I fönstret Översikt väljer du "Ta bort"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Om du vill bekräfta att du vill ta bort ditt integrationskonto väljer du **Ja**.

   ![Om du vill bekräfta borttagning väljer du "Ja"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa handelspartner på ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Skapa avtal mellan partner i ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-agreements.md)
