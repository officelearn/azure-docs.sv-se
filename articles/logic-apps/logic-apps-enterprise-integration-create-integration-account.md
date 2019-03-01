---
title: Skapa och hantera integrationskonton för B2B - lösningar i Azure Logic Apps | Microsoft Docs
description: Skapa, länkar, flytta och ta bort integrationskonton för enterprise-integration och B2B-lösningar med Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191926"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Skapa och hantera integrationskonton för B2B-lösningar med logic apps

Innan du kan skapa [enterprise-integration och B2B-lösningar](../logic-apps/logic-apps-enterprise-integration-overview.md) med [Azure Logic Apps](../logic-apps/logic-apps-overview.md), du måste först ha ett integrationskonto som är där du skapar, lagrar och hantera B2B-artefakter som handel med partners, avtal, kartor, scheman, certifikat och så vidare. Innan din logikapp kan arbeta med artefakter i ditt integrationskonto och använda Logic Apps B2B-anslutningar, till exempel XML-verifiering, måste du [länka ditt integrationskonto](#link-account) i logikappen. Om du vill länka dem båda integration-kontot och logik appen måste ha den *samma* Azure-plats eller region.

Den här artikeln visar hur du utför dessa uppgifter:

* Skapa ditt integrationskonto.
* Länka ditt integrationskonto till en logikapp.
* Flytta ditt integrationskonto till en annan Azure-resursgrupp eller prenumeration.
* Ta bort ditt integrationskonto.

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-integration-account"></a>Skapa integrationskontot

1. Välj den Azure-huvudmenyn **alla tjänster**. Ange ”integrationskonton” i sökrutan som filter och välj **integrationskonton**.

   ![Hitta integrationskonton](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Under **integrationskonton**, Välj **Lägg till**.

   ![Välj ”Lägg till” skapa integrationskontot](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Innehåller information om ditt integrationskonto: 

   ![Ange information för ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Egenskap  | Krävs | Exempelvärde | Beskrivning | 
   |----------|----------|---------------|-------------|
   | Name | Ja | test-integration-account | Namnet på ditt integrationskonto. I det här exemplet använder du det angivna namnet. | 
   | Prenumeration | Ja | <*Azure-prenumerationsnamn*> | Namnet på Azure-prenumeration du använder | 
   | Resursgrupp | Ja | test-integration-account-rg | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) används för att organisera relaterade resurser. I det här exemplet skapar du en ny resursgrupp med det angivna namnet. | 
   | Prisnivå | Ja | Kostnadsfri | Den prisnivå som du vill använda. Det här exemplet väljer **kostnadsfri**, men mer information finns i [Logic Apps gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md) och [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Plats | Ja | Västra USA | Regionen där att lagra din kontoinformation för integrering. Antingen välja samma plats som din logikapp eller skapa en logikapp på samma plats som din integrationskontot. | 
   | Log Analytics-arbetsyta | Nej | Av | Behåll inställningen **Av** för diagnostisk loggning. | 
   ||||| 

4. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

   När Azure distribuerar din integrationskontot till den valda platsen har vanligtvis inom en minut, öppnas Azure ditt integrationskonto.

   ![Öppnar Azure ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Innan din logikapp kan använda ditt integrationskonto, måste du nu koppla integrationskontot till logikappen.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Länka till logikapp

Om du vill ge dina logic apps-åtkomst till ett integrationkonto som innehåller dina B2B-artefakter, till exempel samarbetspartner, avtal, kartor och scheman, måste du koppla din integrationskontot till logikappen. 

> [!NOTE]
> Din integration-konto och logic app måste finnas i samma region.

1. Hitta och öppna logikappen i Azure-portalen.

2. På din logikapp-menyn, under **inställningar**väljer **arbetsflödesinställningarna**. I den **Välj ett integrationskonto** väljer integrationskontot att länka till din logikapp.

   ![Välj ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. För att slutföra länkar, Välj **spara**.

   ![Välj ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   När ditt integrationskonto har kopplats visas ett bekräftelsemeddelande i Azure. 

   ![Azure bekräftar lyckad länk](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Din logikapp kan nu använda eventuella och alla artefakter i ditt integrationskonto plus B2B-anslutningar, till exempel XML-verifiering och flat fil kodning eller avkodning.  

## <a name="unlink-from-logic-app"></a>Avlänka från logikappen

För att länka din logikapp till ett annat konto eller inte längre använda ett konto för integrering med din logikapp, kan du ta bort länk via Azure Resource Explorer.

1. I webbläsaren går du till <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer (https://resources.azure.com)</a>. Kontrollera att du har loggat in med samma Azure-autentiseringsuppgifter.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Ange logikappens namn i sökrutan och hitta sedan din logikapp.

   ![Hitta och välj logikapp](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Välj på namnlisten i explorer **Läs/Skriv**.

   ![Aktivera läget ”Läs/Skriv”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. På den **Data** fliken **redigera**.

   ![På fliken ”Data” Välj ”Redigera”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. I redigeraren, hitta den `integrationAccount` egenskapen för att integrationen konto och ta bort den egenskap som har det här formatet:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Exempel:

   ![Hitta ”integrationAccount” egenskapsdefinition](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. På den **Data** fliken **placera** att spara dina ändringar. 

   ![Välj ”placera” för att spara ändringar](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. I Azure portal under din logikapp **arbetsflödesinställningarna**, kontrollerar du att den **integrationskontot** egenskap visas nu tom.

   ![Kontrollera att integrationskontot inte är länkad](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Flytta integrationskontot

Du kan flytta ditt integrationskonto till en annan Azure-prenumeration eller resursgrupp grupp.

1. Välj på Azure-huvudmenyn **alla tjänster**. Ange ”integrationskonton” i sökrutan som filter och välj **integrationskonton**.

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Under **integrationskonton**, Välj integrationskontot som du vill flytta. På din integration account menyn under **inställningar**, Välj **egenskaper**.

   ![Välj ”egenskaper” under ”inställningar”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Ändra Azure-resursgrupp eller prenumeration för din integrationskontot.

   ![Välj ”Ändra resursgrupp” eller ”ändra prenumeration”](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. När du är klar kan du se till att du uppdaterar alla skript med nya resurs-ID för artefakterna.  

## <a name="delete-integration-account"></a>Ta bort integrationskontot

1. Välj på Azure-huvudmenyn **alla tjänster**. Ange ”integrationskonton” i sökrutan som filter och välj **integrationskonton**.

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Under **integrationskonton**, Välj integrationskontot som du vill ta bort. På menyn integration-kontot väljer **översikt**, välj sedan **ta bort**. 

   ![Välj integrationskontot. På sidan ”Översikt” väljer du ”ta bort”](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. För att bekräfta att du vill ta bort ditt integrationskonto, Välj **Ja**.

   ![Bekräfta borttagningen, väljer du ”Ja”](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Skapa avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
