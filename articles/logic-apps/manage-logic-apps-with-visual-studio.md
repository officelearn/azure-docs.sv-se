---
title: Hantera logic apps i Visual Studio – Azure Logic Apps
description: Hantera logic apps och andra Azure-tillgångar med Visual Studio Cloud Explorer
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: 6a36770ff623e35c56a972a482e24ebb24849051
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467408"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Hantera logic apps i Visual Studio

Även om du kan skapa, redigera, hantera och distribuera logic apps i den [Azure-portalen](https://portal.azure.com), du kan också använda Visual Studio när du vill lägga till dina logikappar för att köra källkontroll, publicera olika versioner och skapa [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) mallar för olika distributionsmiljöer. Du kan hitta och hantera dina logic apps tillsammans med andra Azure-resurser med Visual Studio Cloud Explorer. Du kan till exempel öppna, hämta, redigera, kör, visa körningshistoriken, inaktivera och aktivera logic apps som redan har distribuerats i Azure-portalen. Om du inte har arbetat med Azure Logic Apps i Visual Studio, lär du dig [hur du skapar logikappar med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Distribuera eller publicera en logikapp från Visual Studio skriver du över versionen av appen i Azure-portalen. Så om du gör ändringar i Azure portal som du vill behålla, se till att du [uppdatera logikappen i Visual Studio](#refresh) från Azure-portalen innan nästa gång du distribuerar eller publicera från Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Hämta och installera följande verktyg, om du inte redan har dem: 

  * [Visual Studio 2019, 2017 eller 2015 – Community edition eller större](https://aka.ms/download-visual-studio). 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017, kontrollera att du väljer den **Azure development** arbetsbelastning. För Visual Studio-2019 Cloud Explorer öppnas Logic App Designer i Azure-portalen, men ännu inte kan öppna den inbäddade Logic App Designer. Mer information finns i [hantera resurser som är associerade med dina Azure-konton i Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Så här installerar du Cloud Explorer för Visual Studio 2015 [hämta Cloud Explorer från Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Mer information finns i [hantera resurser som är associerade med din Azure-konton i Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 eller senare)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps Tools för Visual Studio-version som du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Se till att starta om Visual Studio när installationen är klar.

* Åtkomst till Internet när du använder den inbäddade Logic Apps Designer

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Hitta dina logikappar

Du kan hitta alla logikappar som är associerade med din Azure-prenumeration och distribueras i Azure-portalen med hjälp av Cloud Explorer i Visual Studio.

1. Öppna Visual Studio. På den **visa** menyn och välj **Cloud Explorer**.

1. I Cloud Explorer väljer **kontohantering**. Ange den prenumeration som är associerade med dina logikappar och välj sedan **tillämpa**. Exempel:

   ![Välj ”hantering”](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Baserat på om du vill söka av **resursgrupper** eller **resurstyper**, Följ dessa steg:

   * **Resursgrupper**: Din Azure-prenumeration visar Cloud Explorer alla resursgrupper som är associerade med den aktuella prenumerationen. 
   Expandera resursgruppen som innehåller logikappen och välj sedan din logikapp.

   * **Resurstyper**: Din Azure-prenumeration, expandera **Logikappar**. När Cloud Explorer visar alla distribuerade logic-appar som är associerade med din prenumeration, väljer du din logikapp.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Öppna i Visual Studio

I Visual Studio kan du öppna logikappar tidigare skapas och distribueras direkt via Azure portal eller Azure Resource Manager-projekt med Visual Studio.

1. Öppna Utforskaren i molnet och hitta din logikapp. 

1. På den logikapp snabbmenyn väljer **öppna med Logic App Editor**.

   Det här exemplet visar logikappar efter resurstyp, så att dina logikappar visas under den **Logic Apps** avsnittet.

   ![Öppna distribuerade logikapp från Azure-portalen](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   När logikappen öppnas i Logikappdesigner längst ned i Designern kan du välja **kodvy** så att du kan granska den underliggande strukturen av logic app-definition. 
   Om du vill skapa en Distributionsmall för logikappen kan du läsa [hur du hämtar en Azure Resource Manager-mall](#download-logic-app) för den logikappen. Läs mer om [Resource Manager-mallar](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Ladda ned från Azure

Du kan hämta logikappar från den [Azure-portalen](https://portal.azure.com) och spara dem som [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) mallar. Du kan sedan lokalt Redigera mallar med Visual Studio och anpassa logikappar för olika distributionsmiljöer. Ladda ned logikappar automatiskt *funktionsfråga* deras definitioner i [Resource Manager-mallar](../azure-resource-manager/resource-group-overview.md#template-deployment), som också använder JavaScript Object Notation (JSON).

1. Öppna Cloud Explorer i Visual Studio och sedan söka efter och välj logikappen som du vill ladda ned från Azure.

   > [!NOTE]
   > För Visual Studio-2019 Cloud Explorer öppnas Logic App Designer i Azure-portalen, men ännu inte kan öppna den inbäddade Logic App Designer för att ladda ned din logikapp.

2. På appens snabbmenyn väljer **öppna med Logic App Editor**.

   Logic App Designer öppnas och visar logikappen. 
   Om du vill granska underliggande definitionen och struktur, längst ned i designern för logic app väljer **kodvy**. 

3. I verktygsfältet för appdesignern väljer **hämta**.

   ![Välj ”Hämta”](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. När du uppmanas att ange en plats, bläddra till den platsen och spara Resource Manager-mall för logikappsdefinitionen i JSON (.json)-format. 

Din logikappens definition visas i den `resources` underavsnitt i Resource Manager-mallen. Du kan nu redigera logikappsdefinitionen och Resource Manager-mall med Visual Studio. Du kan också lägga till mallen som ett projekt med Azure Resource Manager en Visual Studio-lösning. Lär dig mer om [Resource Manager-projekt för logic apps i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Uppdatera från Azure

Om du redigerar din logikapp i Azure-portalen och vill behålla ändringarna, se till att du uppdaterar appens version i Visual Studio med dessa ändringar. 

* I Visual Studio Logic App Designer-verktygsfältet väljer **uppdatera**.

  ELLER

* Öppna snabbmenyn för din logikapp i Visual Studio Cloud Explorer och välj **uppdatera**.

![Uppdatera logikapp med uppdateringar](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicera logic app-uppdateringar

När du är redo att distribuera dina logic appuppdateringar från Visual Studio till Azure, i verktygsfältet Logic App Designer väljer **publicera**.

![Publicera uppdaterade logic app](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Manuellt köra din logikapp

Du kan manuellt utlösa en logikapp som distribueras i Azure från Visual Studio. Välj Logic App Designer-verktygsfältet **kör utlösaren**.

![Köra logikappen manuellt](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Granska körningshistorik

För att kontrollera status och diagnostisera problem med logikapp-körningar, hittar du mer information, t.ex. indata och utdata för de som körs i Visual Studio.

1. Öppna snabbmenyn för din logikapp i Cloud Explorer och välj **öppen körningshistorik**.

   ![Öppna körningshistorik](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Dubbelklicka på en körning för att visa information om en specifik körning. Exempel:

   ![Detaljerad körningshistorik](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Om du vill sortera tabellen efter egenskap, väljer du kolumnrubriken för egenskapen. 

1. Expandera stegen vars indata och utdata som du vill granska. Exempel:

   ![Visa indata och utdata för varje steg](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikappen

Du kan stoppa utlösaren från startas nästa gång när utlösarvillkoret är uppfyllt utan att ta bort din logikapp. Inaktiverar logikappen förhindrar Logic Apps-motorn från att skapa och köra framtida arbetsflödesinstanser för din logikapp.
Öppna snabbmenyn för din logikapp i Cloud Explorer och välj **inaktivera**.

![Inaktivera logikappen](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> När du inaktiverar en logikapp kan instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de är klara, vilket kan ta tid att slutföra. 

När du är redo för din logikapp att fortsätta kan återaktivera du din logikapp. Öppna snabbmenyn för din logikapp i Cloud Explorer och välj **aktivera**.

![Aktivera din logikapp](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Ta bort din logikapp

Om du vill ta bort din logikapp från Azure-portalen i Cloud Explorer, öppna snabbmenyn för din logikapp och välj **ta bort**.

![Ta bort din logikapp](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem. 

## <a name="troubleshooting"></a>Felsökning

Du kan inte få alternativet för att välja din Azure-prenumeration när du öppnar ditt logic app-projekt i Logic Apps Designer. I stället öppnas din logikapp med en Azure-prenumeration som inte är den som du vill använda. Detta inträffar eftersom när du har öppnat en logikapp JSON-fil, Visual Studio cachelagrar den första markerade prenumerationen för framtida användning. Lös problemet genom att prova något av följande:

* Byt namn på den logikapp JSON-fil. Prenumerationen-cacheminnet är beroende av filnamnet.

* Ta bort tidigare valda prenumerationer för *alla* logic apps i din lösning, ta bort dolda Visual Studio inställningsmappen (.vs) i katalogen för din lösning. Den här platsen lagrar din prenumerationsinformation.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar distribuerade logic apps i Visual Studio. Därefter lär du dig om hur du anpassar logic app-definitioner för distribution:

> [!div class="nextstepaction"]
> [Skapa logic app-definitioner i JSON](../logic-apps/logic-apps-author-definitions.md)
