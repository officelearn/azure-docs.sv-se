---
title: Exportera flöden från Power Automate till Azure Logic Apps
description: Migrera flöden från Power Automate till Azure Logic Apps genom att exportera som Azure Resource Manager-mallar
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428886"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportera flöden från Power Automate och distribuera till Azure Logic Apps

Om du vill utöka och utöka flödets funktioner kan du migrera flödet från [Power Automate](https://flow.microsoft.com) till [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Du kan exportera flödet som en Azure Resource Manager-mall för en logikapp, distribuera den logikappmallen till en Azure-resursgrupp och sedan öppna logikappen i Logic App Designer.

> [!NOTE]
> Alla Power Automate-kopplingar är inte tillgängliga i Azure Logic Apps. Du kan importera flöden som har [likvärdiga kopplingar](../connectors/apis-list.md) i Azure Logic Apps. Knapputlösaren, godkännandeanslutningen och meddelandekopplingen är till exempel specifika för Power Automate.
>
> OpenAPI-baserade flöden som exporteras från Power Automate stöds för närvarande inte för distribution som logikappmallar. 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Flödet som du vill exportera från Power Automate

## <a name="export-a-flow"></a>Exportera ett flöde

1. Logga in på [Power Automate](https://flow.microsoft.com)och välj **Mina flöden**. Hitta och välj flöde. Markera knappen ellipser (**...**) i verktygsfältet. Välj **mallen Exportera** > **logikappar (.json)**.

   ![Exportera flöde](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Spara mallen på den plats du vill ha.

Mer information finns i [Öka till Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Distribuera mall med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

1. Välj **Skapa en resurs** på Azure-huvudmenyn. Skriv "malldistribution" i sökrutan. Välj **Malldistribution (distribuera med anpassade mallar)** och välj sedan **Skapa**.

   ![Välj "Malldistribution"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Under **Anpassad distribution**väljer du Skapa en egen mall i **redigeraren**.

   ![Välj "Bygg din egen mall i redigeraren"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Välj **Läs in fil**i **verktygsfältet Redigera mall** . Leta reda på och välj den JSON-mall som du exporterade från Power Automate och välj **Öppna**.

   ![Välj "Läs in fil"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. När redigeraren visar JSON, parametrar och resurser i mallen väljer du **Spara**.
  
   ![Spara mallen](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Ange nu dessa indataparametrar för mallen:

   * Azure-prenumeration som ska användas för fakturering
   * Azure-resursgrupp
   * Plats för Azure-resursgruppen
   * Namn på logikappresursen
   * Plats för logikappresursen, om den skiljer sig från Azure-resursgruppen
   * Namnet på alla tidigare skapade anslutningar som logikappen kan återanvända

      Om du skapar din första logikapp skapas alla anslutningar som nya, så att du kan acceptera standardnamnen. Annars kan du ange namnen för tidigare skapade anslutningar, som du kan använda i flera logikappar.

   När du har ange den här informationen för mallen granskar och godkänner du villkoren för Azure Marketplace för att skapa nödvändiga Azure-resurser och fakturera din Azure-prenumeration därefter och välj sedan **Köp**.
  
   ![Ange indataparametrar för mall](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure distribuerar din mall som en logikapp till din angivna resursgrupp. Alla logikappar som du migrerar från Power Automate distribueras i inaktiverat tillstånd.

1. Innan du aktiverar logikappen godkänner du alla nya anslutningar genom att följa dessa steg:

   1. Öppna logikappen som du har skapat. På logikappens meny väljer du **Logikappdesigner**.

      Varje anslutning som kräver auktorisering visar en varningsikon:

      ![Varningsikon](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Expandera det steget för varje steg som kräver en auktoriserad anslutning och välj **Lägg till ny**.

      ![Lägg till ny anslutning](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Logga in på varje tjänst eller ange nödvändiga autentiseringsuppgifter för att auktorisera anslutningen.

1. Spara din logikapp. När du är redo att aktivera logikappen väljer du **Översikt**på logikappens meny och väljer sedan **Aktivera**.

   ![Aktivera logikapp](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Om du vill undvika att köra dubbla arbetsflöden måste du inaktivera eller ta bort det ursprungliga flödet.

## <a name="deploy-template-by-using-visual-studio"></a>Distribuera mall med Visual Studio

Om du har konfigurerat Visual Studio med förutsättningar för att skapa [logikappar](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) kan du distribuera den exporterade mallen från Visual Studio till Azure Logic Apps.

1. Öppna mallfilen som du exporterade från Power Automate i Visual Studio.

1. Skapa ett Azure Resource Group-projekt i Visual Studio och välj Logic **App-mallen** genom att följa stegen i [Snabbstart: Skapa automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), till exempel:

   ![Skapa ett projekt för en Azure-resursgrupp](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Från Solution Explorer öppnar du filen **LogicApp.json** om filen inte redan är öppen.

1. Kopiera innehållet från den exporterade mallen och skriv över innehållet i **filen LogicApp.json.**

1. Innan du distribuerar logikappen godkänner du alla nya anslutningar genom att följa dessa steg:

   1. Öppna snabbmenyn **LogicApp.json** och välj sedan **Öppna med Logic App Designer**.

      ![Öppna mall med Logic App Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Om du uppmanas till det väljer du den Azure-prenumeration och resursgrupp som du vill använda för att distribuera logikappen.

      ![Välj Azure-prenumeration och resursgrupp](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      När logikappen har visas i designern visar alla anslutningar som kräver varningsikoner:

      ![Anslutningar med varningsikoner](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Expandera det steget för varje steg som kräver en auktoriserad anslutning och välj **Lägg till ny**.

      ![Lägg till ny anslutning](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Logga in på varje tjänst eller ange nödvändiga autentiseringsuppgifter för att auktorisera anslutningen.

   1. Spara lösningen innan du distribuerar logikappen.

1. Öppna snabbmenyn för projektet i Solution Explorer och välj **Distribuera** > **ny**. Om du uppmanas logga in med ditt Azure-konto ska du göra det.

1. Bekräfta Azure-prenumerationen, Azure-resursgruppen och alla andra inställningar som du vill använda för distribution, till exempel en [parameterfil](../azure-resource-manager/templates/parameter-files.md) som ska användas för att skicka mallparametervärden, och välj sedan **Distribuera**.

   ![Bekräfta distributionsinställningar](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Om rutan **Redigera parametrar** visas anger du namnet på logikappresursen i Azure och väljer **Spara**.  

   ![Redigera distributionsparametrar](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   När distributionen startar visas appens distributionsstatus i Visual Studios **utdatafönster**. Om statusen inte visas ska du öppna listan **Show output from** (Visa utdata från) och välja Azure-resursgruppen. Ett exempel:

   ![Utdatafönster](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Om några anslutningar i logikappen behöver matas in från dig öppnas ett PowerShell-fönster i bakgrunden och manar till eventuella nödvändiga lösenord eller hemliga nycklar. När du har angett den här informationen fortsätter distributionen.

   ![Autentisera anslutningar](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   När distributionen är klar publiceras logikappen men aktiveras inte i Azure-portalen.

1. När du är redo att aktivera logikappen i Azure-portalen letar du reda på och öppnar logikappen i Logic App Designer. På logikappens meny väljer du **Översikt**och väljer sedan **Aktivera**.

1. Om du vill undvika att köra dubbla arbetsflöden måste du inaktivera eller ta bort det ursprungliga flödet.

Mer information om de här distributionsstegen finns i [Snabbstart: Skapa automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [kopplingar för Azure Logic Apps](../connectors/apis-list.md)
* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
