---
title: Hantera logikappar med Visual Studio - Azure Logic Apps | Microsoft Docs
description: Hantera logikappar och andra Azure tillgångar med Visual Studio Cloud Explorer
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 7914bce6ca71b1b3f00c69fb6f33154f0f52dc7a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>Hantera logikappar med Visual Studio

Även om du kan skapa, redigera, hantera och distribuera logikappar i den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, du kan också använda Visual Studio när du vill lägga till logikappar kontroll, publicera olika versioner och skapa [Azure-resurshanteraren Manager](../azure-resource-manager/resource-group-overview.md) mallar för av olika distributionsmiljöer. Du kan använda Visual Studio Cloud Explorer för att hitta och hantera dina logic apps tillsammans med andra Azure-resurser. Du kan till exempel öppna, hämta, redigera, köra, visa körningshistorik, inaktivera och aktivera logikappar som redan har distribuerats i Azure-portalen. Om du inte har använt för att arbeta med Azure Logikappar i Visual Studio, lär du dig [skapa logikappar med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Distribuera eller publicera en logikapp från Visual Studio skriver du över versionen av appen på Azure-portalen. Så om du gör ändringar i Azure-portalen som du vill behålla, se till att du [uppdatera logikappen i Visual Studio](#refresh) från Azure-portalen innan nästa gång du distribuerar eller publicerar från Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Hämta och installera följande verktyg, om du inte redan har dem: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 eller Visual Studio 2015 – Community Edition eller senare</a>. 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Azure SDK (2.9.1 eller senare)</a> och <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Azure Logic Apps Tools för Visual Studio 2017</a> eller för <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a> 
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">hur du installerar tillägget från Visual Studio</a>. 
    Se till att starta om Visual Studio när installationen är klar.

* Tillgång till Internet när du använder inbäddade Logic Apps Designer

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Hitta dina logic apps

Du kan hitta alla logikappar som är associerade med din Azure-prenumeration och distribueras på Azure-portalen med hjälp av Cloud Explorer i Visual Studio.

1. Öppna Visual Studio. På den **visa** väljer du **Cloud Explorer**.

2. Välj i Cloud Explorer **kontohantering**. Välj Azure-prenumerationen som är kopplade till dina logic apps och sedan **tillämpa**. Exempel:

   ![Välj ”kontohantering”](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Baserat på om du vill söka genom **resursgrupper** eller **resurstyper**, gör du följande:

   * **Resursgrupper**: Under din Azure-prenumeration Cloud Explorer visar alla resursgrupper som är kopplade till den prenumerationen. 
   Expandera resursgruppen som innehåller din logikapp, och välj sedan din logikapp.

   * **Resurstyper**: Expandera Under din Azure-prenumeration **Logikappar**. När Cloud Explorer visar alla distribuerade logikappar som är associerade med din prenumeration, Välj din logikapp.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Öppna i Visual Studio

I Visual Studio kan du öppna logikappar tidigare skapas och distribueras direkt via Azure-portalen eller som Azure Resource Manager-projekt i Visual Studio.

1. Öppna Utforskaren i molnet och hitta din logikapp. 

2. På snabbmenyn för den logikapp, Välj **öppna med logik App Editor**.

   Det här exemplet illustrerar logikappar uppdelat efter resurstyp, så att dina logic apps visas den **Logic Apps** avsnitt.

  ![Öppna distribuerade logikapp från Azure-portalen](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   När logikappen öppnas i Logic Apps Designer längst ned i Designer väljer du **kodvy** så att du kan granska den underliggande strukturen av logik app-definition. 
   Lär dig mer om du vill skapa en Distributionsmall för logikappen [hur du hämtar en Azure Resource Manager-mall](#download-logic-app) för logikappen. Lär dig mer om [Resource Manager-mallar](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Ladda ned från Azure

Du kan hämta logikappar från den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> och spara dem som [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) mallar. Du kan sedan lokalt Redigera mallar med Visual Studio och anpassa logikappar för av olika distributionsmiljöer. Hämta logikappar automatiskt *parameterizes* deras definitioner i [Resource Manager-mallar](../azure-resource-manager/resource-group-overview.md#template-deployment), som också använder JavaScript Object Notation (JSON).

1. Öppna Cloud Explorer i Visual Studio och sedan söka efter och välj logikappen som du vill hämta från Azure.

2. På snabbmenyn för den appen, Välj **öppna med logik App Editor**.

   Logik App Designer öppnas och visar logikappen. 
   Om du vill granska logic Apps underliggande definition och struktur längst ned i Designer väljer **kodvy**. 

3. Välj verktygsfältet designer **hämta**.

   ![Välj ”ladda ned”](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. När du ombeds ange en plats, bläddra till platsen och spara Resource Manager-mall för app-definition logik i JSON (JSON)-format. 

Din logik app definition visas i den `resources` underavsnitt i Resource Manager-mallen. Du kan nu redigera logik app definitions- och Resource Manager-mall med Visual Studio. Du kan också lägga till mallen som ett projekt för Azure Resource Manager en Visual Studio-lösning. Lär dig mer om [Resource Manager projekt för logic apps i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Uppdatera från Azure

Om du redigerar logikappen i Azure-portalen och vill behålla ändringarna, se till att uppdatera det app-versionen i Visual Studio med sådana ändringar. 

* I Visual Studio, i verktygsfältet logik App Designer väljer **uppdatera**.

  ELLER

* I Visual Studio Cloud Explorer öppnar din logikapp snabbmenyn och välj **uppdatera**. 

![Uppdatera logikapp med uppdateringar](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicera logik app-uppdateringar

När du är redo att distribuera din logik app-uppdateringar från Visual Studio till Azure i verktygsfältet logik App Designer väljer **publicera**.

![Publicera uppdaterade logikapp](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Kör logikappen manuellt

Du kan utlösa en logikapp som distribueras i Azure från Visual Studio manuellt. Välj verktygsfältet logik App Designer **köra utlösaren**.

![Köra manuellt logikapp](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Granska körningshistorik

Du kan granska information som indata och utdata för de körs i Visual Studio för att kontrollera status och diagnostisera problem med logik appen körs.

1. I Cloud Explorer öppnar din logikapp snabbmenyn och välj **öppna kör historik**.

   ![Öppna kör historik](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Om du vill visa information för en specifik kör, dubbelklickar du på en körning. Exempel:

   ![Kör detaljerad historik](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Om du vill sortera tabellen efter egenskap, väljer du kolumnrubriken för den egenskapen. 

3. Expandera stegen vars indata och utdata som du vill granska. Exempel:

   ![Visa indata och utdata för varje steg](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikapp

Du kan stoppa utlösaren från startar när utlösarvillkoret är uppfyllt nästa gång utan att ta bort din logikapp. Inaktivera din logikapp förhindrar Logic Apps-motorn från skapa och köra framtida arbetsflödesinstanser för din logikapp.
I Cloud Explorer öppnar din logikapp snabbmenyn och välj **inaktivera**.

![Inaktivera din logikapp](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Du kan återaktivera logikappen när du är redo för din logikapp att fortsätta. I Cloud Explorer öppnar din logikapp snabbmenyn och välj **aktivera**.

![Aktivera din logikapp](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Ta bort din logikapp

Om du vill ta bort din logikapp från Azure-portalen i Cloud Explorer öppnar din logikapp snabbmenyn och välj **ta bort**.

![Ta bort din logikapp](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du hanterar distribuerade logikappar med Visual Studio. Lär dig hur du anpassar logik app definitioner för distribution:

> [!div class="nextstepaction"]
> [Författare logik app definitioner i JSON](../logic-apps/logic-apps-author-definitions.md)
