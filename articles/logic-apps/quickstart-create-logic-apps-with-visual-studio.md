---
title: Automatisera arbetsflöden för uppgifter i Visual Studio
description: Skapa, schemalägga och köra återkommande arbetsflöden för företagsintegrering med hjälp av Azure Logic Apps och Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.openlocfilehash: 693b2fd8ac7440b67f53de0aedb9a8268a90de76
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241656"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Snabbstart: Skapa automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps - Visual Studio

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio kan du skapa arbetsflöden för att automatisera uppgifter och processer som integrerar appar, data, system och tjänster åt företag och organisationer. Den här snabbstarten visar hur du kan utforma och skapa dessa arbetsflöden genom att skapa logikappar i Visual Studio och distribuera dessa appar till Azure. Även om du kan utföra dessa uppgifter i Azure-portalen, kan du i Visual Studio lägga till dina logikappar i källkontroll, publicera olika versioner och skapa Azure Resource Manager-mallar för olika distributionsmiljöer.

Om du inte har tidigare i Azure Logic Apps och bara vill ha de grundläggande begreppen kan du prova [snabbstarten för att skapa en logikapp i Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md). Logic App Designer fungerar på samma sätt i både Azure-portalen och Visual Studio.

I den här snabbstarten skapar du samma logikapp med Visual Studio som snabbstart för Azure-portalen. Den här logikappen övervakar en webbplats RSS-flöde och skickar e-post för varje nytt objekt i flödet. Din färdiga logikapp ser ut som det här arbetsflödet på hög nivå:

![Färdig logikapp](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio 2019, 2017 eller 2015 - Community edition eller mer](https://aka.ms/download-visual-studio). Den här snabbstarten använder Visual Studio Community 2017.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 kontrollerar du att du väljer **Azure-utvecklingsarbetsbelastningen.**

  * [Microsoft Azure SDK för .NET (2.9.1 eller senare)](https://azure.microsoft.com/downloads/). Läs mer om [Azure SDK för .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De senaste Azure Logic Apps-verktygen för Visual Studio-tillägget för den version du vill ha:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Se till att starta om Visual Studio när installationen är klar.

* Tillgång till Internet när du använder den inbäddade Logic App Designer

  Designern behöver en internetanslutning för att skapa resurser i Azure och för att läsa egenskaper och data från kopplingar i logikappen. För Dynamics CRM Online-anslutningar kontrollerar designern exempelvis CRM-instansen för standardegenskaper och anpassade egenskaper.

* Ett e-postkonto som stöds av Logic Apps, t.ex. Office 365 Outlook, Outlook.com eller Gmail. För andra leverantörer, granska [anslutningslistan här](https://docs.microsoft.com/connectors/). I det här exemplet används Office 365 Outlook. Om du använder en annan provider är övergripande stegen desamma, men ditt användargränssnitt kan skilja sig något.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Skapa ett projekt för en Azure-resursgrupp

Kom igång genom att skapa ett [projekt för en Azure-resursgrupp](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Läs mer om [Azure-resursgrupper och resurser](../azure-resource-manager/management/overview.md).

1. Starta Visual Studio. Logga in med ditt Azure-konto.

1. Gå till **Arkiv**-menyn och välj **Nytt** > **Projekt**. (Tangentbord: Ctrl + Skift + N)

   ![Gå till Arkiv-menyn och välj Nytt > Projekt](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Under **Installerad** väljer du **Visual C#** eller **Visual Basic**. Välj **Cloud** > **Azure Resource Group**. Namnge projektet, t.ex.:

   ![Skapa ett projekt för en Azure-resursgrupp](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Resursgruppsnamn kan`.`bara innehålla bokstäver, siffror,`_`punkter (`-`), understreck (`(` `)`), bindestreck ( )`.`och parenteser ( , ), men kan inte *sluta* med punkter ( ).
   >
   > Om **Cloud** eller **Azure Resource Group** inte visas kontrollerar du att du installerar Azure SDK för Visual Studio.

   Om du använder Visual Studio 2019 gör du så här:

   1. I rutan **Skapa ett nytt projekt** väljer du Azure Resource **Group-projektet** för Visual C# eller Visual Basic. Välj **Nästa**.

   1. Ange ett namn för den Azure-resursgrupp som du vill använda och annan projektinformation. Välj **Skapa**.

1. Välj **Logic App-mallen** i malllistan. Välj **OK**.

   ![Välj Logic App-mallen](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   När Visual Studio har skapat ditt projekt öppnas Solution Explorer och visar din lösning. I din lösning lagrar **LogicApp.json-filen** inte bara logikappdefinitionen utan är också en Azure Resource Manager-mall som du kan använda för distribution.

   ![Solution Explorer visar den nya logikapplösningen och distributionsfilen](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Skapa en tom logikapp

När du har ditt Azure Resource Group-projekt skapar du logikappen med mallen **Blank Logic App.**

1. Öppna **snabbmenyn** i Solution Explorer. Välj **Öppna med Logic App Designer**. (Tangentbord: Ctrl + L)

   ![Öppna filen LogicApp.json med Logic App Designer](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   Visual Studio uppmanar dig för din Azure-prenumeration och en Azure-resursgrupp för att skapa och distribuera resurser för din logikapp och anslutningar.

1. För **Prenumeration**väljer du din Azure-prenumeration. För **resursgrupp**väljer du **Skapa ny** för att skapa en annan Azure-resursgrupp.

   ![Välj Azure-prenumeration, resursgrupp och plats för resurser](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Inställning | Exempelvärde | Beskrivning |
   | ------- | ------------- | ----------- |
   | Användarkonto | Fabrikam <br> sophia-owen@fabrikam.com | Kontot som du använde när du loggade in på Visual Studio |
   | **Prenumeration** | Betala per användning <br> (sophia-owen@fabrikam.com) | Namnet på din Azure-prenumeration och det associerade kontot |
   | **Resursgrupp** | MyLogicApp-RG <br> (USA, västra) | Azure-resursgruppen och platsen för lagring och distribution av logikappens resurser |
   | **Location** | **Samma som resursgrupp** | Platstypen och den specifika platsen för distribution av logikappen. Platstypen är antingen en Azure-region eller en befintlig [integrationstjänstmiljö (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>För den här snabbstarten behåller du platstypen till **Region** och platsen inställd **på Samma som resursgrupp**. <p>**När**du har skapat resursgruppsprojektet kan du [ändra platstypen och platsen,](manage-logic-apps-with-visual-studio.md#change-location)men olika platstyper påverkar logikappen på olika sätt. |
   ||||

1. Logic Apps Designer öppnar en sida som visar en introduktionsvideo och vanliga utlösare. Bläddra nedåt förbi videon och utlösare till **mallar**och välj **Tom Logic App**.

   ![Välj Tom logikapp](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Bygg ett arbetsflöde för logikappen

Lägg sedan till en [RSS-utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt feedobjekt visas. Varje logikapp börjar med en utlösare som utlöses när specifika villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans som kör ditt arbetsflöde.

1. Välj **Alla**under sökrutan i Logic App Designer . Skriv "rss" i sökrutan. Välj den här utlösaren i listan utlösare: **När ett flödesobjekt publiceras**

   ![Bygg din logikapp genom att lägga till en utlösare och åtgärder](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. När utlösaren visas i designern slutför du uppbyggnaden av logikappen genom att följa arbetsflödesstegen i [snabbstarten för Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)och gå sedan tillbaka till den här artikeln. När du är klar ser logikappen ut som i det här exemplet:

   ![Färdig logikapp](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Spara din Visual Studio-lösning. (Tangentbord: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Distribuera logikappen till Azure

Innan du kan köra och testa logikappen distribuerar du appen till Azure från Visual Studio.

1. Välj **Distribuera** > **ny**på snabbmenyn i Solution Explorer . Om du uppmanas logga in med ditt Azure-konto ska du göra det.

   ![Skapa en distribution av den logiska appen](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. För den här distributionen behåller du standardprenumerationen, resursgruppen och andra inställningar. Välj **Distribuera**.

   ![Distribuera logikappen till Azure-resursgruppen](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Om rutan **Redigera parametrar** visas anger du ett resursnamn för logikappen. Spara inställningarna.

   ![Ange distributionsnamn för logikapp](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   När distributionen startar visas appens distributionsstatus i Visual Studios **utdatafönster**. Om statusen inte visas ska du öppna listan **Show output from** (Visa utdata från) och välja Azure-resursgruppen.

   ![Status för distribution](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Om dina valda kopplingar behöver indata från dig öppnas ett PowerShell-fönster i bakgrunden och manar till eventuella nödvändiga lösenord eller hemliga nycklar. När du har angett den här informationen fortsätter distributionen.

   ![PowerShell-fönster](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   När distributionen är klar är logikappen live i Azure-portalen och körs enligt ditt angivna schema (varje minut). Om utlösaren hittar nya feedobjekt utlöses utlösaren, vilket skapar en arbetsflödesinstans som kör logikappens åtgärder. Logikappen skickar e-post för varje nytt objekt. Annars, om utlösaren inte hittar nya objekt, utlöses inte utlösaren och "hoppar" instansierar arbetsflödet. Logikappen väntar till nästa intervall innan du checkar.

   Här är exempel på e-postmeddelanden som den här logikappen skickar. Om du inte får e-post kan du titta i mappen Skräppost.

   ![Outlook skickar ett e-postmeddelande för varje nytt RSS-objekt](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Grattis, du har byggt och distribuerat din logikapp med Visual Studio. Om du vill hantera din logikapp och granska dess körningshistorik, se [Hantera Logic Apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Lägga till ny logikapp

När du har ett befintligt Azure Resource Group-projekt kan du lägga till en ny tom logikapp i projektet med hjälp av fönstret JSON Outline.

1. Öppna filen i `<logic-app-name>.json` Solution Explorer.

1. Välj **Andra Windows** > **JSON-disposition**på **Visa-menyn** .

1. Om du vill lägga till en resurs i mallfilen väljer du **Lägg till resurs** högst upp i JSON-dispositionsfönstret. Eller öppna snabbmenyn **resurser** i fönstret JSON-disposition och välj **Lägg till ny resurs**.

   ![Fönstret JSON-disposition](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Leta **Add Resource** reda `logic app`på i sökrutan i dialogrutan Lägg till resurs . **Logic App** Namnge logikappen och välj **Lägg till**.

   ![Lägg till resurs](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med logikappen tar du bort resursgruppen som innehåller logikappen och relaterade resurser.

1. Logga in på [Azure Portal](https://portal.azure.com) med samma konto som användes för att skapa din logikapp.

1. På Azure-portalmenyn väljer du **Resursgrupper**eller söker efter och väljer **Resursgrupper** på valfri sida. Välj logikappens resursgrupp.

1. Välj Ta bort **resursgrupp**på sidan **Översikt** . Ange resursgruppsnamnet som bekräftelse och välj **Ta bort**.

   ![”Resursgrupper” > ”Översikt” > ”Ta bort resursgrupp”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Ta bort Visual Studio-lösningen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

Genom att läsa den här artikeln har du lärt dig att bygga, distribuera och köra din logikapp med Visual Studio. Mer information om hur du hanterar och utför avancerad distribution för logikappar med Visual Studio finns i följande artiklar:

> [!div class="nextstepaction"]
> [Hantera Logic Apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
