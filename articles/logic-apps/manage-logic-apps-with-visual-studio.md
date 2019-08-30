---
title: Hantera Logic Apps med hjälp av Visual Studio – Azure Logic Apps
description: Hantera Logic Apps och andra Azure-tillgångar med Visual Studio Cloud Explorer
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: f628be48039df63700f8e786821f29ba55cfd943
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164878"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Hantera Logic Apps med Visual Studio

Även om du kan skapa, redigera, hantera och distribuera Logi Kap par i [Azure Portal](https://portal.azure.com)kan du också använda Visual Studio när du vill lägga till dina Logi Kap par i käll kontrollen, publicera olika versioner och skapa [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) mallar för olika distributions miljöer. Med Visual Studio Cloud Explorer kan du hitta och hantera dina Logi Kap par tillsammans med andra Azure-resurser. Du kan till exempel öppna, Hämta, redigera, köra, Visa körnings historik, inaktivera och aktivera Logi Kap par som redan har distribuerats i Azure Portal. Om du inte har arbetat med Azure Logic Apps i Visual Studio kan du lära dig [hur du skapar Logi Kap par med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Om du distribuerar eller publicerar en Logic app från Visual Studio skrivs versionen av appen över i Azure Portal. Om du gör ändringar i Azure Portal som du vill behålla, se till att du [uppdaterar Logic-appen i Visual Studio](#refresh) från Azure Portal innan du distribuerar eller publicerar från Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Hämta och installera följande verktyg, om du inte redan har dem: 

  * [Visual Studio 2019, 2017 eller 2015 – Community Edition eller senare](https://aka.ms/download-visual-studio). 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 ser du till att du väljer arbets belastningen **Azure Development** .
    > Mer information finns i [Hantera resurser som är kopplade till dina Azure-konton i Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Om du vill installera Cloud Explorer för Visual Studio 2015 [laddar du ned Cloud Explorer från Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Mer information finns i [Hantera resurser som är associerade med dina Azure-konton i Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 eller senare)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps verktyg för Visual Studio-versionen som du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Se till att starta om Visual Studio när installationen är klar.

* Åtkomst till webben när du använder den inbäddade Logic Apps designer

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Hitta dina Logi Kap par

I Visual Studio kan du hitta alla Logic Apps som är associerade med din Azure-prenumeration och distribueras i Azure Portal med hjälp av Cloud Explorer.

1. Öppna Visual Studio. I menyn **Visa** väljer du **Cloud Explorer**.

1. Välj **konto hantering**i Cloud Explorer. Välj den Azure-prenumeration som är associerad med dina Logi Kap par och välj sedan **Använd**. Exempel:

   ![Välj "konto hantering"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Följ dessa steg baserat på om du söker efter **resurs grupper** eller **resurs typer**:

   * **Resurs grupper**: I Azure-prenumerationen visar Cloud Explorer alla resurs grupper som är associerade med den prenumerationen. 
   Expandera resurs gruppen som innehåller din Logic app och välj sedan din Logic app.

   * **Resurs typer**: Expandera **Logic Apps**under din Azure-prenumeration. När Cloud Explorer visar alla distribuerade Logic-appar som är associerade med din prenumeration väljer du din Logic app.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Öppna i Visual Studio

I Visual Studio kan du öppna Logi Kap par som tidigare har skapats och distribuerats antingen direkt via Azure Portal eller som Azure Resource Manager projekt med Visual Studio.

1. Öppna Cloud Explorer och hitta din Logic app. 

1. På den logiska appens snabb meny väljer du **Öppna med Logic app Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   I det här exemplet visas Logi Kap par efter resurs typ, så dina Logi Kap par visas i avsnittet **Logic Apps** .

   ![Öppna distribuerad Logic-app från Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   När Logic-appen öppnas i Logic Apps designer, längst ned i designern, kan du välja **kodvyn** så att du kan granska den underliggande strukturen för Logic app-definitionen. 
   Om du vill skapa en Distributionsmall för Logic-appen, lär du dig [att ladda ned en Azure Resource Manager mall](#download-logic-app) för den Logic-appen. Lär dig mer om [Resource Manager-mallar](../azure-resource-manager/template-deployment-overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Ladda ned från Azure

Du kan ladda ned Logic Apps från [Azure Portal](https://portal.azure.com) och spara dem som [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) mallar. Du kan sedan lokalt redigera mallarna med Visual Studio och anpassa Logic Apps för olika distributions miljöer. Hämtning av Logic Apps *parameterizes* automatiskt sina definitioner inuti [Resource Manager-mallar](../azure-resource-manager/template-deployment-overview.md), som också använder JavaScript Object Notation (JSON).

1. Öppna Cloud Explorer i Visual Studio och leta upp och välj den Logic-app som du vill ladda ned från Azure.

2. På den appens snabb meny väljer du **Öppna med Logic app Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   Logic App Designer öppnas och visar Logic app. 
   Om du vill granska logik appens underliggande definition och struktur väljer du **kodvyn**längst ned i designern. 

3. I verktygsfältet designer väljer du **Hämta**.

   ![Välj "Ladda ned"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. När du uppmanas att ange en plats kan du bläddra till den platsen och spara Resource Manager-mallen för Logic app-definitionen i JSON-filformat (. JSON). 

Din Logic app-definition visas i `resources` underavsnittet i Resource Manager-mallen. Nu kan du redigera Logic app-definitionen och Resource Manager-mallen med Visual Studio. Du kan också lägga till mallen som ett Azure Resource Manager projekt i en Visual Studio-lösning. Lär dig mer om [Resource Manager-projekt för Logic Apps i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Uppdatera från Azure

Om du redigerar din Logi Kap par i Azure Portal och vill behålla ändringarna, måste du uppdatera appens version i Visual Studio med dessa ändringar. 

* I Visual Studio i verktygsfältet Logic App Designer väljer du **Uppdatera**.

  ELLER

* Öppna din Logic Apps snabb meny i Visual Studio Cloud Explorer och välj **Uppdatera**.

![Uppdatera Logic app med uppdateringar](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicera Logic app-uppdateringar

När du är redo att distribuera dina Logic app-uppdateringar från Visual Studio till Azure, går du till verktygsfältet Logic App Designer och väljer **publicera**.

![Publicera uppdaterad Logic-app](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Kör din Logic app manuellt

Du kan utlösa en logisk app som distribuerats i Azure från Visual Studio manuellt. I verktygsfältet Logic App Designer väljer du **Kör**utlösare.

![Kör Logic app manuellt](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Granska körningshistorik

Om du vill kontrol lera statusen och diagnostisera problem med att Logic App körs kan du granska informationen, till exempel indata och utdata, för de som körs i Visual Studio.

1. Öppna din Logic Apps snabb meny i Cloud Explorer och välj **Öppna körnings historik**.

   ![Öppna körnings historik](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Om du vill visa information om en speciell körning dubbelklickar du på en körning. Exempel:

   ![Detaljerad körnings historik](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Om du vill sortera tabellen efter egenskap väljer du kolumn rubriken för den egenskapen. 

1. Expandera stegen vars indata och utdata du vill granska. Exempel:

   ![Visa indata och utdata för varje steg](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera Logic app

Utan att ta bort din Logic app kan du stoppa utlösaren från att starta vid nästa tillfälle när utlösarens villkor är uppfyllt. Om du inaktiverar din Logic app förhindrar Logic Appss motorn att skapa och köra framtida arbets flödes instanser för din Logic app.
Öppna din Logic Apps snabb meny i Cloud Explorer och välj **inaktivera**.

![Inaktivera din Logic app](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> När du inaktiverar en Logic app instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de har slutförts, vilket kan ta lång tid att slutföra. 

När du är redo för din Logic app att återuppta åtgärden kan du återaktivera din Logic app. Öppna din Logic Apps snabb meny i Cloud Explorer och välj **Aktivera**.

![Aktivera din Logic app](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Ta bort din logikapp

För att ta bort din Logic app från Azure Portal, i Cloud Explorer, öppnar du din Logic Apps snabb meny och väljer **ta bort**.

![Ta bort din logikapp](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem. 

## <a name="troubleshooting"></a>Felsökning

När du öppnar ditt Logic Apps-projekt i Logic Apps designer kanske du inte får möjlighet att välja din Azure-prenumeration. I stället öppnas din Logic-app med en Azure-prenumeration som inte är den som du vill använda. Detta beror på att när du har öppnat en Logic Apps. JSON-fil cachelagrar Visual Studio den första valda prenumerationen för framtida användning. Försök med något av följande steg för att lösa problemet:

* Byt namn på Logic Apps. JSON-filen. Prenumerationens cacheminne beror på fil namnet.

* Ta bort tidigare valda prenumerationer för *alla* Logi Kap par i lösningen genom att ta bort den dolda mappen Visual Studio Settings (. vs) i din lösnings katalog. Den här platsen lagrar din prenumerations information.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar distribuerade Logic Apps med Visual Studio. Sedan kan du läsa mer om att anpassa definitioner för Logic app för distribution:

> [!div class="nextstepaction"]
> [Redigera definitioner för Logic app i JSON](../logic-apps/logic-apps-author-definitions.md)
