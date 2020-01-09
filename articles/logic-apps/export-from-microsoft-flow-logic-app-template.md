---
title: Exportera flöden från Power automatisera till Azure Logic Apps
description: Migrera flöden från Power automatisera till Azure Logic Apps genom att exportera som Azure Resource Manager mallar
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428886"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportera flöden från Power Automate och distribuera till Azure Logic Apps

Om du vill utöka och utöka ditt flödes funktioner kan du migrera flödet från [energi spar läge](https://flow.microsoft.com) till [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Du kan exportera ditt flöde som en Azure Resource Manager mall för en Logic app, distribuera Logic app-mallen till en Azure-resurs grupp och sedan öppna den Logic-appen i Logic App Designer.

> [!NOTE]
> Alla Power inautomatiserade anslutningar är inte tillgängliga i Azure Logic Apps. Du kan importera flöden som har [motsvarande kopplingar](../connectors/apis-list.md) i Azure Logic Apps. Till exempel är knapp utlösaren, godkännande Connector och meddelande Connector särskilt för automatisk energi förbrukning.
>
> OpenAPI-baserade flöden som exporteras från Power automate stöds för närvarande inte för distribution som Logic app-mallar. 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Flödet som du vill exportera från Energis par

## <a name="export-a-flow"></a>Exportera ett flöde

1. Logga in för att [Automatisera energi](https://flow.microsoft.com)och välj **mina flöden**. Hitta och välj ditt flöde. Välj ellipser-knappen ( **...** ) i verktygsfältet. Välj **exportera** > **Logic Apps mall (. JSON)** .

   ![Exportera flöde](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Spara din mall på den plats som du vill ha.

Mer information finns i [öka upp till Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Distribuera mallen med hjälp av Azure Portal

1. Logga in [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

1. Välj **Skapa en resurs** på Azure-huvudmenyn. Skriv "mall distribution" i sökrutan. Välj **malldistribution (distribuera med anpassade mallar)** och välj sedan **skapa**.

   ![Välj "Malldistribution"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Under **Anpassad distribution**väljer du **Bygg en egen mall i redigeraren**.

   ![Välj "Bygg din egen mall i redigeraren"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. I verktygsfältet **Redigera mall** väljer du **Läs in fil**. Leta upp och välj den JSON-mall som du exporterade från Power automatisering och välj **Öppna**.

   ![Välj Läs in fil](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. När redigeraren visar JSON, parametrar och resurser i mallen väljer du **Spara**.
  
   ![Spara mall](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Ange nu följande indataparametrar för mallen:

   * Azure-prenumeration som ska användas för fakturering
   * Azure-resursgrupp
   * Plats för Azure-resurs gruppen
   * Namn på Logic app-resursen
   * Plats för Logic app-resursen, om den skiljer sig från Azures resurs grupp
   * Namnet på alla tidigare skapade anslutningar som Logic-appen kan återanvända

      Om du skapar din första Logic-app skapas alla anslutningar som nya, så att du kan acceptera standard namnen. Annars kan du ange namnen på tidigare skapade anslutningar, som du kan använda i flera Logic Apps.

   När du har angett den här informationen för mallen granskar du och godkänner villkoren för Azure Marketplace för att skapa nödvändiga Azure-resurser och fakturerar din Azure-prenumeration därefter. Välj sedan **köp**.
  
   ![Ange indataparametrar för mall](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure distribuerar din mall som en Logic app till den angivna resurs gruppen. Alla Logi Kap par som du migrerar från energi spar läge distribueras i inaktiverat tillstånd.

1. Innan du aktiverar din Logic app måste du godkänna alla nya anslutningar genom att följa dessa steg:

   1. Öppna den Logic-app som du skapade. På menyn Logic Apps väljer du **Logic App Designer**.

      Varje anslutning som kräver auktorisering visar en varnings ikon:

      ![Varningsikon](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Expandera det steget för varje steg som kräver en auktoriserad anslutning och välj **Lägg till ny**.

      ![Lägg till ny anslutning](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Logga in på varje tjänst eller ange de autentiseringsuppgifter som krävs för att auktorisera anslutningen.

1. Spara din logikapp. När du är redo att aktivera din Logi Kap par väljer du **Översikt**på menyn Logic Apps och väljer sedan **Aktivera**.

   ![Aktivera Logic app](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Undvik att köra dubbla arbets flöden genom att inaktivera eller ta bort det ursprungliga flödet.

## <a name="deploy-template-by-using-visual-studio"></a>Distribuera mall med hjälp av Visual Studio

Om du har konfigurerat Visual Studio med [förutsättningarna](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) för att skapa Logi Kap par kan du distribuera den exporterade mallen från Visual Studio till Azure Logic Apps.

1. I Visual Studio öppnar du mallfilen som du exporterade från Energis par automatisering.

1. I Visual Studio skapar du ett Azure Resource Group-projekt och väljer **Logic app** -mallen genom att följa stegen i [snabb start: skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), till exempel:

   ![Skapa ett projekt för en Azure-resursgrupp](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Öppna filen **LogicApp. JSON** från Solution Explorer, om filen inte redan är öppen.

1. Kopiera innehållet från den exporterade mallen och skriv över innehållet i filen **LogicApp. JSON** .

1. Innan du distribuerar din Logic app måste du godkänna alla nya anslutningar genom att följa dessa steg:

   1. Öppna snabb menyn **LogicApp. JSON** och välj sedan **Öppna med Logic App Designer**.

      ![Öppna mall med Logic App Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Om du uppmanas väljer du den Azure-prenumeration och resurs grupp som du vill använda för att distribuera din Logic app.

      ![Välj Azure-prenumeration och resurs grupp](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      När din Logi Kap par har visats i designern visas varnings ikoner i alla anslutningar som kräver auktorisering:

      ![Anslutningar med varnings ikoner](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Expandera det steget för varje steg som kräver en auktoriserad anslutning och välj **Lägg till ny**.

      ![Lägg till ny anslutning](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Logga in på varje tjänst eller ange de autentiseringsuppgifter som krävs för att auktorisera anslutningen.

   1. Spara lösningen innan du distribuerar Logic-appen.

1. I Solution Explorer öppnar du projektets snabb meny och väljer **distribuera** > **ny**. Om du uppmanas logga in med ditt Azure-konto ska du göra det.

1. När du uppmanas till det bekräftar du Azure-prenumerationen, Azure-resurs gruppen och eventuella andra inställningar som du vill använda för distribution, till exempel en [parameter fil](../azure-resource-manager/templates/parameter-files.md) som ska användas för att skicka parameter värden för en mall och väljer sedan **distribuera**.

   ![Bekräfta distributions inställningar](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Om rutan **Redigera parametrar** visas anger du namnet på din Logic app-resurs i Azure och väljer **Spara**.  

   ![Redigera distributions parametrar](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   När distributionen startar visas appens distributionsstatus i Visual Studios **utdatafönster**. Om statusen inte visas ska du öppna listan **Show output from** (Visa utdata från) och välja Azure-resursgruppen. Ett exempel:

   ![Utdatafönster](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Om några anslutningar i din Logi Kap par behöver inmatas från dig, öppnas ett PowerShell-fönster i bakgrunden och du uppmanas att ange nödvändiga lösen ord eller hemliga nycklar. När du har angett den här informationen fortsätter distributionen.

   ![Autentisera anslutningar](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   När distributionen är klar publiceras din Logic app, men den aktive ras inte i Azure Portal.

1. När du är redo att aktivera din Logi Kap par i Azure Portal kan du söka efter och öppna din Logic app i Logic App Designer. Välj **Översikt**på menyn Logic Apps och välj sedan **Aktivera**.

1. Undvik att köra dubbla arbets flöden genom att inaktivera eller ta bort det ursprungliga flödet.

Mer information om dessa distributions steg finns i [snabb start: skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [anslutningar för Azure Logic Apps](../connectors/apis-list.md)
* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
