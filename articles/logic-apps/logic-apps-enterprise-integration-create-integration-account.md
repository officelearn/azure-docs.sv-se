---
title: Skapa och hantera integrationskonton B2B - lösningar som Azure Logic Apps | Microsoft Docs
description: Skapa, länkar, flytta och ta bort integrationskonton för enterprise integration och B2B-lösningar med Azure Logikappar
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 97cdc809a00078ee86a40fd4567ab2d3357ed3bd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726439"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Skapa och hantera integrationskonton för B2B-lösningar med logic apps

Innan du kan skapa [enterprise integration och B2B lösningar](../logic-apps/logic-apps-enterprise-integration-overview.md) med [Azure Logikappar](../logic-apps/logic-apps-overview.md), måste du en integration konto, vilket är där du skapar, lagrar och hanterar B2B artefakter som handelspartner, avtal, kartor, scheman, certifikat, och så vidare. Innan din logikapp kan arbeta med artefakter i ditt konto för integrering och använda Logic Apps B2B-kopplingar, till exempel XML-verifiering måste du [länka ditt konto integration](#link-account) till din logikapp. Om du vill länka dem båda integration konto och logik appen måste ha den *samma* Azure-plats eller region.

Den här artikeln visar hur du utför dessa uppgifter:

* Skapa kontot för integrering.
* Länka ditt integration konto till en logikapp.
* Flytta integration kontot till en annan Azure resursgrupp eller prenumeration.
* Ta bort ditt konto för integrering.

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="create-integration-account"></a>Skapa integration konto

1. Azure Huvudmeny, Välj **alla tjänster**. I sökrutan anger du ”integrationskonton” som filter och välj **integrationskonton**.

   ![Hitta integrationskonton](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Under **integrationskonton**, Välj **Lägg till**.

   ![Välj ”Lägg till” skapa integration konto](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Innehåller information om ditt konto för integrering: 

   ![Ange information för ditt konto för integrering](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Egenskap  | Krävs | Exempelvärde | Beskrivning | 
   |----------|----------|---------------|-------------|
   | Namn | Ja | test-integrering-konto | Namnet för kontot för integrering. Använd det angivna namnet för det här exemplet. | 
   | Prenumeration | Ja | <*Azure-prenumeration-name*> | Namnet för Azure-prenumerationen ska användas | 
   | Resursgrupp | Ja | test-integrering-konto-rg | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) används för att organisera relaterade resurser. Skapa en ny resursgrupp med det angivna namnet för det här exemplet. | 
   | Prisnivå | Ja | Kostnadsfri | Den prisnivå som du vill använda. Det här exemplet väljer du **lediga**, men mer information finns [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md) och [Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Plats | Ja | Västra USA | Regionen var du vill lagra din kontoinformation för integrering. Välj samma plats som din logikapp eller skapa en logikapp på samma plats som ditt konto för integrering. | 
   | Log Analytics | Nej | Av | Behåll inställningen **Av** för diagnostisk loggning. | 
   ||||| 

4. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

   När Azure distribuerar integration kontot till den valda platsen, vilket vanligtvis slutförs inom en minut, öppnar Azure integration-konto.

   ![Azure öppnar integration-konto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Nu innan din logikapp kan använda ditt konto integration, måste du koppla kontot integration till din logikapp.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Länka till logikapp

Om du vill ge dina logic apps åtkomst till ett konto för integrering med din B2B-artefakter, till exempel handel partners, avtal, kartor och scheman, måste du koppla kontot integration till din logikapp. 

> [!NOTE]
> Appen integration konto och logik måste finnas i samma region.

1. Hitta och öppna logikappen i Azure-portalen.

2. På menyn din logikapp under **inställningar**väljer **inställningar för arbetsflöde**. I den **välja ett konto för integrering** väljer du kontot integration att länka till din logikapp.

   ![Välj kontot integrering](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Slutför Länkning väljer **spara**.

   ![Välj kontot integrering](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   När kontot integrering har kopplats visas Azure ett bekräftelsemeddelande. 

   ![Azure bekräftar lyckade länk](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Din logikapp kan nu använda alla och alla artefakter i ditt konto integration plus B2B-kopplingar, till exempel XML-verifiering och flat fil kodning eller avkodning.  

## <a name="unlink-from-logic-app"></a>Avlänka från logikapp

För att länka din logikapp till ett annat konto för integrering eller inte längre använda ett konto för integrering med din logikapp, kan du ta bort länk via Azure Resource Explorer.

1. I din webbläsare går du till <a href="https://resources.azure.com" target="_blank">resursutforskaren i Azure (https://resources.azure.com)</a>. Kontrollera att du har loggat in med samma autentiseringsuppgifter för Azure.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. I sökrutan ange din logikapp namn, och sedan söka efter och välj din logikapp.

   ![Leta upp och markera logikapp](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Välj på namnlisten i explorer **Skrivskyddstyp**.

   ![Aktivera läget ”Läs/Skriv”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. På den **Data** , Välj **redigera**.

   ![På fliken ”Data” Välj ”Redigera”](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. I Redigeraren för att hitta den `integrationAccount` egenskapen för integrering konto och ta bort den egenskap som har följande format:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Exempel:

   ![Sök efter ”integrationAccount” egenskapsdefinition](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. På den **Data** , Välj **placera** att spara ändringarna. 

   ![Välj ”placera” för att spara ändringar](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. I Azure portal under din logikapp **inställningar för arbetsflöde**, kontrollera att den **integrering konto** egenskapen visas nu tomt.

   ![Kontrollera att integration kontot inte är länkat](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Flytta integration konto

Du kan flytta integration kontot till en annan Azure-prenumerationen eller resursen.

1. På Azure huvudmenyn, Välj **alla tjänster**. I sökrutan anger du ”integrationskonton” som filter och välj **integrationskonton**.

   ![Hitta integration-konto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Under **integrationskonton**, Välj integration-konto som du vill flytta. Om ditt konto menyn under **inställningar**, Välj **egenskaper**.

   ![Välj under ”inställningar”, ”egenskaper”](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Ändra Azure-resursgrupp eller prenumeration för ditt konto för integrering.

   ![Välj ”Ändra resursgrupp” eller ”ändra prenumerationen”](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Kontrollera att du uppdaterar alla skript med ny resurs-ID: N för din artefakter när du är klar.  

## <a name="delete-integration-account"></a>Ta bort kontot på integrering

1. På Azure huvudmenyn, Välj **alla tjänster**. I sökrutan anger du ”integrationskonton” som filter och välj **integrationskonton**.

   ![Hitta integration-konto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Under **integrationskonton**, Välj integration-konto som du vill ta bort. Välj på menyn integration, **översikt**, Välj **ta bort**. 

   ![Välj integration konto. På sidan ”Overview” väljer du ”ta bort”](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. För att bekräfta att du vill ta bort ditt konto integration, Välj **Ja**.

   ![Välj ”Ja” om du vill bekräfta borttagning av](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Skapa avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md)
