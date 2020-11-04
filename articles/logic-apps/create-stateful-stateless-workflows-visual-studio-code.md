---
title: Skapa Automation-arbetsflöden (för hands version) i Visual Studio Code
description: Skapa tillstånds lösa eller tillstånds känsliga Automation-arbetsflöden med tillägget Azure Logic Apps (förhands granskning) i Visual Studio Code för att integrera appar, data, moln tjänster och lokala system
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 51fd8b8427dd8214e22fa59e50b26bb9db237946
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322063"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Skapa tillståndskänsliga eller tillståndslösa arbetsflöden i Visual Studio Code med Azure Logic Apps-tillägget (förhandsversion)

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du vill skapa Logic app-arbetsflöden som integrerar över appar, data, moln tjänster och system kan du använda Visual Studio Code och Azure Logic Apps (för hands version) för att skapa och lokalt köra [ *tillstånds lösa* och *tillstånds lösa* Logic app-arbetsflöden](#stateful-stateless) i utvecklings miljön.

![Skärm bild som visar Visual Studio Code och Logic app-arbetsflöde.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Logi Kap par som du skapar med det offentliga för hands versions tillägget använder den nya **Logic app (förhands granskning)** resurs typ och drivs av [Azure Functions](../azure-functions/functions-overview.md) runtime i din lokala miljö. Den här nya resurs typen kan innehålla flera arbets flöden och fungerar på ett liknande sätt som **Funktionsapp** resurs typ, som kan innehålla flera funktioner.

Under tiden finns den ursprungliga **Logic Apps** resurs typen fortfarande för dig att skapa och använda i Visual Studio Code och i Azure Portal. Dock är upplevelserna för den ursprungliga resurs typen åtskilda från den nya resurs typen. För närvarande kan båda resurs typerna **Logic Apps** och **Logic app (för hands version)** finnas samtidigt i Visual Studio Code och i Azure Portal. Du kan visa och komma åt alla distribuerade Logi Kap par i din Azure-prenumeration, men de visas och bevaras separat i sina egna kategorier och avsnitt.

Den här artikeln innehåller en [Översikt över den här offentliga för hands versionen](#whats-new), som beskriver olika aspekter av resurs typen **Logic app (för hands version)** och hur du skapar den här resursen med hjälp av Visual Studio Code:

* Hur [tillstånds känsliga och tillstånds lösa](#stateful-stateless) Logic-appar skiljer sig från varandra.

* Hur du uppfyller [installations kraven](#prerequisites) och [konfigurerar Visual Studio Code](#set-up) för Public Preview-tillägget.

* Hur du skapar nya **Logic app-arbetsflöden (förhands granskning)** genom [att skapa ett projekt och välja en arbetsflödesmall](#create-project).

* Hur du kan köra och felsöka dina nya Logi Kap par lokalt i Visual Studio Code.

* Publicera dessa nya Logic Apps direkt från Visual Studio Code [till Azure](#publish-azure) eller [till en Docker-behållare](#deploy-docker) som du kan köra var som helst. Mer information om Docker finns i [Vad är Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)?

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Vad är i den här offentliga för hands versionen?

Med tillägget Azure Logic Apps (förhands granskning) får du till gång till många aktuella och ytterligare Logic Apps funktioner i den lokala utvecklings miljön i Visual Studio Code, till exempel:

* Bygg Logi Kap par för integrerings-och automatiserings arbets flöden från [390 + kopplingar](/connectors/connector-reference/connector-reference-logicapps-connectors) för SaaS-appar (program vara som en tjänst) och PaaS (Platform-as-a-Service) och-tjänster plus anslutningar för lokala system.

  * Vissa hanterade anslutningar, till exempel Azure Service Bus, Azure Event Hubs och SQL Server köra på samma sätt som inbyggda interna utlösare och åtgärder, till exempel HTTP-åtgärden.

  * Skapa och distribuera Logi Kap par som kan köras var som helst eftersom Azure Logic Apps tjänsten genererar anslutnings strängar för delad åtkomst (SAS) som dessa Logi Kap par kan använda för att skicka begär anden till moln anslutningens runtime-slutpunkt. Tjänsten Logic Apps sparar dessa anslutnings strängar med andra program inställningar så att du enkelt kan lagra dessa värden i Azure Key Vault när du distribuerar till Azure.

    > [!NOTE]
    > Som standard har en **Logic app-resurs (förhands granskning)** den [systemtilldelade hanterade identiteten](../logic-apps/create-managed-service-identity.md) aktive ras automatiskt för att autentisera anslutningar vid körning. Den här identiteten skiljer sig från autentiseringsuppgifterna för autentisering eller anslutnings sträng som du använder när du skapar en anslutning. Om du inaktiverar den här identiteten fungerar inte anslutningarna vid körning.

* Skapa tillstånds lösa Logi Kap par som bara körs i minnet så att de avslutas snabbare, svarar snabbare, har högre genomflöde och kostar mindre att köra eftersom körnings historiken och data mellan åtgärder inte finns kvar i extern lagring. Du kan också aktivera körnings historik för enklare fel sökning. Mer information finns i [tillstånds känsliga jämfört med tillstånds lösa Logic Apps](#stateful-stateless).

* Kör och Felsök dina Logi Kap par lokalt i Visual Studio Code Development Environment.

* Publicera och distribuera Logi Kap par från Visual Studio Code direkt till olika värd miljöer, till exempel [Azure App Service](../app-service/environment/intro.md) och [Docker-behållare](/dotnet/core/docker/introduction).

> [!NOTE]
> Om du vill ha mer information om aktuella kända problem granskar du sidan för förhands gransknings tillägget [kända problem GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Tillstånds känsliga gentemot tillstånds lösa Logic Apps

* *Tillstånds känsliga*

  Skapa tillstånds känsliga Logic-appar när du behöver behålla, granska eller referera till data från tidigare händelser. Dessa Logi Kap par är både indata och utdata för varje åtgärd och deras arbets flödes tillstånd i extern lagring, som gör det möjligt att granska körnings informationen och historiken när varje körning har slutförts. Tillstånds känsliga Logic Apps ger hög återhämtning om eller när avbrott inträffar. När tjänster och system har återställts kan du återskapa avbruten Logic app från det sparade läget och köra Logi kap Apps på nytt. Tillstånds känsliga arbets flöden kan fortsätta att köras i upp till ett år.

* *Tillståndslös*

  Skapa tillstånds lösa Logic Apps när du inte behöver spara, granska eller referera till data från tidigare händelser i extern lagring för senare granskning. Dessa Logic Apps behåller både indata och utdata för varje åtgärd och deras arbets flödes tillstånd endast i minnet, i stället för att överföra den här informationen till extern lagring. Därför har tillstånds lösa logiska appar kortare körningar som vanligt vis inte är längre än 5 minuter, snabbare prestanda med snabbare svars tider, högre data flöde och minskade drifts kostnader eftersom körnings informationen och historiken inte lagras i extern lagring. Men om eller när avbrott inträffar återställs inte avbrutna körningar automatiskt, så att anroparen måste skicka om avbrutna körningar manuellt. Dessa Logi Kap par kan bara köras synkront och för enklare fel sökning kan du [Aktivera körnings historik](#run-history), vilket påverkar prestanda.

  Tillstånds lösa arbets flöden stöder för närvarande endast *åtgärder* för [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), som distribueras i Azure och inte utlösare. Starta arbets flödet genom att välja den [inbyggda begäran, Event Hubs eller Service Bus utlösare](../connectors/apis-list.md#built-ins)som körs internt med Logic Apps Runtime. Mer information om utlösare, åtgärder och anslutningar som inte stöds finns i [funktioner som inte stöds eller som inte är tillgängliga](#unsupported).

Information om hur kapslade Logic Apps beter sig annorlunda mellan tillstånds känsliga och tillstånds lösa Logic Apps finns i [kapslade beteende skillnader mellan tillstånds känsliga och tillstånds lösa Logic Apps](#nested-behavior).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prismodell

När du distribuerar den nya resurs typen **Logic app (förhands granskning)** uppmanas du att välja en värd plan, särskilt den [App Service plan eller Premium-plan](../azure-functions/functions-scale.md) som ska användas som pris modell. Om du väljer App Service plan uppmanas du också att välja en [pris nivå](../app-service/overview-hosting-plans.md). Under den allmänt tillgängliga för hands versionen debiteras inte *ytterligare* avgifter ovanpå den valda planen när du kör Logic apps på App Service.

Tillstånds känsliga Logic Apps använder [extern lagring](../azure-functions/functions-scale.md#storage-account-requirements), så Azure Storage prissättnings modell gäller för lagrings transaktioner som används av Azure Logic Apps Runtime. Köer används till exempel för schemaläggning, medan tabeller och blobbar används för att lagra arbets flödes tillstånd.

Läs följande avsnitt om du vill ha mer information om de pris modeller som gäller för den här nya resurs typen:

* [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)
* [Skala upp en i Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions pris information](https://azure.microsoft.com/pricing/details/functions/)
* [App Service pris information](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Azure Storage pris information](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Funktioner som inte är tillgängliga eller som inte stöds

Dessa funktioner är inte tillgängliga eller stöds inte för den här offentliga för hands versionen:

* Det går inte att skapa den nya **Logic app-resursen (för hands version)** på MacOS.

* Det finns inte stöd för alla Azure-regioner än. För för närvarande tillgängliga regioner kontrollerar du [listan region](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

* Starta arbets flödet genom att använda den [inbyggda begäran, http, Event Hubs eller Service Bus utlösare](../connectors/apis-list.md)som körs internt med Logic Apps Runtime. För närvarande kan [företags anslutningar](../connectors/apis-list.md#enterprise-connectors), [lokala datagatewayer](../connectors/apis-list.md#on-premises-connectors), webhook-baserade utlösare, glidning fönster utlösare, [anpassade anslutningar](../connectors/apis-list.md#custom-apis-and-connectors), integrations konton, deras artefakter och [deras anslutningar](../connectors/apis-list.md#integration-account-connectors) inte hanteras i den här för hands versionen. Funktionen "anropa en Azure Function" är inte tillgänglig, så nu använder du HTTP- *åtgärden* för att anropa URL: en för Azure-funktionen.

  Förutom de tidigare angivna utlösarna kan *tillstånds känsliga* arbets flöden använda både utlösare och åtgärder för [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), som distribueras i Azure kontra inbyggda utlösare och åtgärder som körs internt med Logic Apps Runtime. *Tillstånds lösa* arbets flöden stöder dock för närvarande endast *åtgärder* för hanterade anslutningar, inte utlösare. Även om du kan aktivera anslutningar i Azure för ditt tillstånds lösa arbets flöde, visar designern inte några hanterade kopplings utlösare som du kan välja.

* Du kan distribuera den nya **Logic app (för hands version)** -resurs typen enbart till en [Premium-eller App Service värd plan i Azure](#publish-azure) eller till en [Docker-behållare](#deploy-docker)och inte [integrerings tjänst miljöer (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). **Förbruknings** värd planer stöds inte eller är inte tillgängliga för distribution av den här resurs typen.

* I Azure Portal kan du inte skapa nya Logi Kap par med resurs typen ny **Logic app (förhands granskning)** . Du kan bara skapa dessa Logic Apps i Visual Studio Code. När du har distribuerat Logi Kap par med den här resurs typen från Visual Studio Code till Azure kan du dock [lägga till nya arbets flöden i dessa Logic Apps](#add-workflows).

## <a name="prerequisites"></a>Förutsättningar

### <a name="access-and-connectivity"></a>Åtkomst och anslutning

* Åtkomst till Internet så att du kan hämta kraven, ansluta från Visual Studio Code till ditt Azure-konto och publicera från Visual Studio Code till Azure, en Docker-behållare eller en annan miljö.

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Om du vill bygga samma exempel på Logic-appen i den här artikeln behöver du ett Office 365 Outlook-e-postkonto som använder ett arbets-eller skol konto från Microsoft för att logga in.

  Om du väljer att använda en annan [e-postanslutning som stöds av Azure Logic Apps](/connectors/), till exempel Outlook.com eller [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), kan du fortfarande följa exemplet och de allmänna övergripande stegen är desamma, men användar gränssnittet och alternativen kan variera på vissa sätt. Om du till exempel använder Outlook.com-anslutningsprogrammet använder du din personliga Microsoft-konto i stället för att logga in.

### <a name="storage-requirements"></a>Lagrings krav

1. Hämta och installera [Azure Storage Emulator 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Om du vill köra emulatorn måste du ha en lokal SQL DB-installation, till exempel den kostnads fria [SQL Server 2019 Express-versionen](https://go.microsoft.com/fwlink/p/?linkid=866658). Mer information finns i [använda Azure Storage emulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Innan du öppnar Logic Apps designer för att skapa ditt Logic app-arbetsflöde, se till att du startar emulatorn. Annars får du ett meddelande om att `Workflow design time could not be started` .
   >
   > ![Skärm bild som visar Azure Storage-emulatorn som körs.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>Verktyg

* [Visual Studio Code 1.30.1 (januari 2019) eller högre](https://code.visualstudio.com/), som är kostnads fri. Hämta och installera även dessa ytterligare verktyg för Visual Studio Code, om du inte redan har dem:

  * [Tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)för Azure-konto, som ger en enda gemensam Azure-inloggning och prenumerations filtrerings upplevelse för alla andra Azure-tillägg i Visual Studio Code.

  * [C# för kod tillägg för Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), som gör att F5-funktionen kan köra din Logic app.

  * [Azure Functions Core tools](../azure-functions/functions-run-local.md), antingen version [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) eller [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), via Microsoft Installer (MSI). Dessa verktyg innehåller en version av samma körnings miljö som ger den Azure Functions körning som körs i Visual Studio Code.

    > [!IMPORTANT]
    > Om du har en tidigare installation än dessa versioner måste du först avinstallera den versionen eller kontrol lera att sökvägen till miljövariabeln pekar på den version som du hämtar och installerar.
    >
    > Om du vill använda den [ **infogade kod** åtgärden](../logic-apps/logic-apps-add-run-inline-code.md) för att köra JavaScript-kod måste du använda Azure Functions runtime-version 3x eftersom åtgärden inte stöder version 2x. Den här åtgärden stöds inte heller för Linux-operativsystem.

  * [Azure Logic Apps (för hands version)-tillägget för Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Detta offentliga för hands versions tillägg ger dig möjlighet att skapa tillstånds känsliga och tillstånds lösa Logic Apps och köra dem lokalt i Visual Studio Code.

    För närvarande kan du ha både det ursprungliga **Azure Logic Apps** -tillägget och det nya **Azure Logic Apps (förhands gransknings tillägget)** installerat samtidigt i Visual Studio Code. Genom att välja Azure-ikonen i Visual Studio Code-verktygsfältet kan du Visa alla Logic Apps som distribuerats i Azure, men varje resurs typ visas i sina egna tilläggs avsnitt, **Logic Apps** och **Azure Logic Apps (för hands version)**.

    > [!IMPORTANT]
    > Om du har skapat Logi Kap par med hjälp av tillägget **Azure Logic Apps (privat förhands granskning)** fungerar inte dessa Logic Apps med det offentliga för hands versions tillägget. Du kan dock migrera dessa Logi Kap par genom att avinstallera det privata förhands gransknings tillägget, utföra rensningen och installera det offentliga för hands versions tillägget. Du kan sedan skapa ditt nya projekt i Visual Studio Code och kopiera din tidigare skapade Logic Apps **. definitions** fil till det nya projektet.
    >
    > Innan du installerar det offentliga förhands gransknings tillägget kontrollerar du att du avinstallerar tidigare versioner och tar bort dessa artefakter:
    >
    > * Mappen **Microsoft. Azure. functions. ExtensionBundle.** Workflows, som innehåller tidigare anknytnings paket och finns antingen i sökvägen här:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * Mappen **Microsoft. Azure. Workflows. WebJobs. extension** , som är [NuGet](/nuget/what-is-nuget) -cache för det privata förhands gransknings tillägget och finns på den här sökvägen:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Följ dessa steg om du vill installera **Azure Logic Apps (för hands version)** -tillägget:

    1. Välj **tillägg** i Visual Studio Code i det vänstra verktygsfältet.

    1. I sökrutan tillägg anger du `azure logic apps preview` . Välj **Azure Logic Apps (för hands version)** i listan resultat **>** **Install**.

       När installationen är klar visas det offentliga för hands versions tillägget i listan **tillägg: installerad** .

       ![Skärm bild som visar Visual Studio Codes installerade tillägg-lista med tillägget "Azure Logic Apps (för hands version)" understruket.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Om du vill testa den exempel Logic-app som du skapar i den här artikeln behöver du ett verktyg som kan skicka anrop till utlösaren för begäran, vilket är det första steget i exempel Logic app. Om du inte har ett sådant verktyg kan du ladda ned, installera och använda [Postman](https://www.postman.com/downloads/).

* För enklare diagnostik-loggning och spårning kan du lägga till och använda en [Application Insights](../azure-monitor/app/app-insights-overview.md) -resurs. Du kan skapa den här resursen under en Logic app-distribution eller i Azure Portal när du har distribuerat din Logic app.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Konfigurera Visual Studio Code

1. Kontrol lera att alla tillägg är korrekt installerade genom att läsa in eller starta om Visual Studio Code.

1. Aktivera eller kontrol lera att Visual Studio Code automatiskt hittar och installerar tilläggs uppdateringar så att ditt offentliga för hands versions tillägg får de senaste uppdateringarna.

   Följ dessa steg om du vill kontrol lera den här inställningen:

   1. På **Arkiv** -menyn går **du till inställningar** **>** **Inställningar**.

   1. På fliken **användare** går du till **funktioner** **>** **tillägg**.

   1. Bekräfta att **automatisk kontroll av uppdateringar** och **Automatisk uppdatering** är markerat.

1. Aktivera eller kontrol lera att dessa inställningar för **Azure Logic Apps (för hands version)** har kon figurer ATS i Visual Studio Code:

   * **Azure Logic Apps v2: panel läge**
   * **Azure Logic Apps v2: Project runtime**

   1. På **Arkiv** -menyn går **du till inställningar** **>** **Inställningar**.

   1. På fliken **användare** går du till **>** **tillägg** **>** **Azure Logic Apps (för hands version)**.

   1. Under **Azure Logic Apps v2: panel läge** bekräftar du att **Aktivera panel läge** är markerat. Under **Azure Logic Apps v2: Project runtime** , ställer du in versionen på **~ 3** eller **~ 2** , baserat på den [Azure Functions Core tools version](#prerequisites) som du installerade tidigare.

      > [!IMPORTANT]
      > Om du vill använda den [ **infogade kod** åtgärden](../logic-apps/logic-apps-add-run-inline-code.md) för att köra JavaScript-kod kontrollerar du att du använder Project runtime version 3 eftersom åtgärden inte stöder version 2. Den här åtgärden stöds inte heller för Linux-operativsystem.

      ![Skärm bild som visar tillägg för Visual Studio-kod för tillägget Azure Logic Apps (för hands version).](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

1. Välj Azure-ikonen i verktygsfältet Visual Studio Code.

   ![Skärm bild som visar Visual Studio Code-verktygsfältet och den valda Azure-ikonen.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. I fönstret Azure under **Azure: Logic Apps (för hands version)** väljer **du logga in på Azure**. När sidan Visual Studio Code Authentication visas loggar du in med ditt Azure-konto.

   ![Skärm bild som visar Azure-fönstret och den valda länken för Azure-inloggning.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   När du har loggat in visar Azure-fönstret prenumerationerna på ditt Azure-konto. Om du har den offentligt utgivna Logic Apps tillägget kan du hitta eventuella original Logic Appss resurser som du har skapat med hjälp av det ursprungliga tillägget i avsnittet om utgivna tilläggets **Logic Apps** , inte för hands versions tillägget **Logic Apps (för hands version)** .
   
   Om de förväntade prenumerationerna inte visas, eller om du vill att fönstret endast ska visa vissa prenumerationer, följer du dessa steg:

   1. I listan prenumerationer flyttar du pekaren bredvid den första prenumerationen tills knappen **Välj prenumerationer** (filter ikon) visas. Välj filter ikonen.

      ![Skärm bild som visar Azure-fönstret och den valda filter ikonen.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Alternativt väljer du ditt Azure-konto i statusfältet i Visual Studio Code. 

   1. När en annan prenumerations lista visas väljer du de prenumerationer som du vill använda och kontrollerar sedan att du väljer **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Skapa ett lokalt projekt

Innan du kan skapa din Logi Kap par skapar du ett lokalt projekt så att du kan hantera och distribuera din Logic app från Visual Studio Code. Det underliggande projektet liknar ett Azure Functions projekt, även kallat ett Function Apps-projekt. Dessa projekt typer skiljer sig dock från varandra, så Logic app-arbetsflöden och-funktioner kan inte finnas i samma projekt.

1. På din dator skapar du en *Tom* lokal mapp som ska användas för projektet som du senare skapar i Visual Studio Code.

1. Stäng alla öppna mappar i Visual Studio Code.

1. I Azure-fönstret, bredvid **Azure: Logic Apps (för hands version)** väljer du **Skapa nytt projekt** (ikon som visar en mapp och blixt).

   ![Skärm bild som visar verktygsfältet i Azure-fönstret med alternativet "Skapa nytt projekt" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Om Windows Defender-brandväggen uppmanas att bevilja nätverks åtkomst för `Code.exe` , som är Visual Studio Code och för `func.exe` , som är Azure Functions Core tools, väljer du **privata nätverk, till exempel mitt hem-eller arbets nätverk** **>** **Allow access**.

1. Bläddra till den plats där du skapade projektmappen, Välj mappen och fortsätt.

   ![Skärm bild som visar dialog rutan Välj mapp med en nyligen skapad projektmapp och knappen Välj markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Välj antingen **tillstånds känsligt arbets flöde** eller **arbets flöde för tillstånds lösa** i listan mallar som visas. I det här exemplet väljs **tillstånds känsligt arbets flöde**.

   ![Skärm bild som visar listan över arbetsflödesmallar med "tillstånds känsligt arbets flöde" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Ange ett namn för ditt Logic app-arbetsflöde och tryck på RETUR. I det här exemplet används `example-workflow` som namn.

   ![Skärm bild som visar rutan "skapa ett nytt tillstånds känsligt arbets flöde (3/4)" och "exempel-Workflow" som arbets flödes namn.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. I nästa lista som visas väljer du **Öppna i aktuellt fönster**.

   ![Skärm bild som visar listan med alternativet öppna i aktuellt fönster.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code läses in på nytt, öppnar fönstret Utforskaren och visar projektet, som nu innehåller automatiskt genererade projektfiler. Projektet har till exempel en mapp som visar namnet på din Logic app-arbetsflöde. I den här mappen innehåller **workflow.jsi** filen ditt Logic app-arbetsflödes underliggande JSON-definition.

   ![Skärm bild som visar Explorer-fönstret med projektmappen, arbetsflödes mapp och "workflow.jspå"-filen.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Öppna arbets flödets definitions fil i Logic App Designer

1. Kontrol lera de versioner som är installerade på datorn genom att köra det här kommandot:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Om du har .NET Core SDK 5. x kan den här versionen förhindra att du öppnar logik appens underliggande arbets flödes definition i designern. I stället för att avinstallera den här versionen går du till projektets rot plats och skapar en **global.jspå** en fil som refererar till .net Core runtime 3. x-versionen som är senare än 3.1.201, till exempel:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Se till att du uttryckligen lägger till den **global.jspå** filen till projektet på rot platsen från Visual Studio Code. Annars öppnas inte designern.

1. Om Visual Studio Code körs på Windows eller Linux kontrollerar du att Azure Storage emulatorn körs. Läs igenom [kraven](#prerequisites)för mer information.

1. Expandera projektmappen för arbets flödet. Öppna snabb menyn **workflow.jspå** filen och välj **Öppna i designern**.

   ![Skärm bild som visar Explorer-fönstret och gen vägs fönstret för workflow.jspå filen med alternativet öppna i designer valt.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Om du får fel meddelandet som ser till att `Workflow design time could not be started` Azure Storage emulatorn körs. Annars kan du prova följande fel söknings förslag:

   I Visual Studio Code kontrollerar du resultatet från förhands gransknings tillägget.

   1. I menyn **Visa** väljer du **utdata**.

   1. I listan i namn listen för **utdata** väljer du **Azure Logic Apps** så att du kan visa utdata för förhands gransknings tillägget, till exempel:

      ![Skärm bild som visar Visual Studio Codes output-fönster med "Azure Logic Apps" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Granska utdata och kontrol lera om det här fel meddelandet visas:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Det här felet kan inträffa om du tidigare har försökt öppna designern och sedan har utgått eller tagit bort projektet. Lös problemet genom att ta bort mappen **ExtensionBundles** på den här platsen **. ..\Users \\ {your-username} \AppData\Local\Temp\Functions\ExtensionBundles** och försök **workflow.js** att öppna filen i designern igen.

1. I listan **aktivera anslutningar i Azure** väljer du **Använd anslutningar från Azure** , som gäller för alla hanterade anslutningar som är tillgängliga och distribuerade i Azure, inte bara anslutningar för Azure-tjänster.

   ![Skärm bild som visar Explorer-fönstret med alternativet "aktivera anslutningar i Azure" och "Använd anslutningar från Azure" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Tillstånds lösa arbets flöden stöder för närvarande endast *åtgärder* för [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), som distribueras i Azure och inte utlösare. Även om du har möjlighet att aktivera anslutningar i Azure för ditt tillstånds lösa arbets flöde, visar inte designern några hanterade kopplings utlösare som du kan välja.

1. I listan resurs grupper väljer du **Skapa ny resurs grupp**.

   ![Skärm bild som visar fönstret Utforskaren med listan resurs grupper och "Skapa ny resurs grupp" valt](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Ange ett namn för resurs gruppen och tryck på RETUR. I det här exemplet används `example-logic-app-preview-rg` .

   ![Skärm bild som visar Explorer-fönstret och namn rutan för resurs gruppen.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. I listan platser letar du upp och väljer en [Azure-region som stöds](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) för att skapa resurs gruppen och resurserna. I det här exemplet används **västra centrala USA**.

   > [!IMPORTANT]
   > Det finns för närvarande inte stöd för alla regioner, men uppdateringar för att lägga till fler regioner pågår. Om du väljer en region som inte stöds kan det leda till problem, t. ex. skapa anslutningar. För regioner som stöds för närvarande granskar du sidan för förhands gransknings tilläggets [kända problem GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

   ![Skärm bild som visar Explorer-fönstret med plats listan och "västra centrala USA" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   När du har utfört det här steget öppnar Visual Studio Code appen Logic App Designer.

   > [!NOTE]
   > När Visual Studio Code startar API: t för arbets flödets design tid visas ett meddelande om att starten kan ta några sekunder. Du kan ignorera det här meddelandet eller välja **OK**.

   När Logic Apps Designer visas visas **åtgärden Välj en åtgärd** i designern och är markerad som standard, vilket visar fönstret **Lägg till en åtgärd** .

   ![Skärm bild som visar Logic App Designer.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Lägg sedan [till en utlösare och åtgärder](#add-trigger-actions) i arbets flödet.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Lägg till en utlösare och åtgärder

När du har öppnat Logic app designer från **workflow.jspå** filens snabb meny, visas **åtgärden Välj en åtgärd** i designern och är markerad som standard. Nu kan du börja skapa arbets flödet genom att lägga till en utlösare och åtgärder.

Logic app-arbetsflödet i det här exemplet använder utlösaren och följande åtgärder:

* Den inbyggda [begär ande utlösaren](../connectors/connectors-native-reqres.md), **när en http-begäran tas emot** , som tar emot inkommande samtal eller begär Anden och skapar en slut punkt som andra tjänster eller Logi Kap par kan anropa.

* [Office 365 Outlook-åtgärd](../connectors/connectors-create-api-office365-outlook.md), **Skicka ett e-postmeddelande**.

* Den inbyggda [svars åtgärden](../connectors/connectors-native-reqres.md)som du använder för att skicka ett svar och returnera data tillbaka till anroparen.

### <a name="add-the-request-trigger"></a>Lägg till begär ande utlösare

1. Bredvid designern, i rutan **Lägg till utlösare** , under Sök i rutan **Välj en åtgärd** , kontrollerar du att **inbyggda** är markerat så att du kan välja en utlösare som körs internt.

1. I sökrutan **Välj en åtgärd** anger `when a http request` du och väljer den inbyggda begär ande utlösaren som är namngiven **när en http-begäran tas emot**.

   ![Skärm bild som visar Logic App Designer och * * Lägg till ett utlösare * *-fönster med alternativet "när en HTTP-förfrågan tas emot" aktive ras.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   När utlösaren visas i designern öppnas utlösarens informations fönster för att Visa utlösarens egenskaper, inställningar och andra åtgärder.

   ![Skärm bild som visar Logic app designer med alternativet "när en HTTP-begäran tas emot" Utlös valda och utlösnings informations fönster öppnas.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Om informations fönstret inte visas ser du till att utlösaren är markerad i designern.

1. Följ dessa steg om du måste ta bort ett objekt i designern:

   1. I designern väljer du objektet, som öppnar objektets informations fönster till höger.

   1. Expandera Visual Studio Code-fönstret tillräckligt mycket så att knappen ellipser ( **...** ) visas i det översta högra hörnet bredvid utlösaren eller åtgärds namnet. 

   1. Öppna ellipser-menyn ( **...** ) och välj **ta bort**. Bekräfta borttagningen genom att välja **OK**.

      ![Skärm bild som visar det valda objektet i designern med öppna informations fönstret och med knappen med valda ellipser och alternativet "ta bort".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Lägg till åtgärden Office 365 Outlook

1. Välj **nytt steg** under den utlösare som du har lagt till i designern.

   Frågan **Välj en åtgärd** visas i designern och **fönstret Lägg till en åtgärd** öppnas så att du kan välja nästa åtgärd.

1. I fönstret **Lägg till en åtgärd** går du till rutan **Välj en åtgärd** och väljer **Azure** så att du kan hitta och välja en åtgärd för en hanterad koppling som distribueras i Azure.

   Det här exemplet väljer och använder åtgärden Office 365 Outlook, **Skicka ett e-postmeddelande (v2)**.

   ![Skärm bild som visar Logic App Designer och * * Lägg till åtgärd * * fönstret med Office 365 Outlook "Skicka ett e-postmeddelande" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. I åtgärdens informations fönster väljer du **Logga** in så att du kan skapa en anslutning till ditt e-postkonto.

   ![Skärm bild som visar Logic App Designer och * * skicka ett e-postmeddelande (v2) * * fönstret med "logga in" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Om du får felet kan `Failed to create connection...` du ha valt en region som inte stöds för din Logic app. Uppdateringar för att lägga till fler regioner pågår. För de regioner som stöds för närvarande kan du gå igenom förhands gransknings [sidan kända problem GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

1. När Visual Studio Code efterfrågar dig att du får åtkomst till ditt e-postkonto väljer du **Öppna**.

   ![Skärm bild som visar Visual Studio Code-prompten för att tillåta åtkomst.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Om du vill förhindra framtida prompter väljer du **Konfigurera betrodda domäner** så att du kan lägga till sidan autentisering som en betrodd domän.

1. Följ de efterföljande anvisningarna för att logga in, tillåta åtkomst och tillåta att du återgår till Visual Studio Code.

   > [!NOTE]
   > Om tiden är för lång tid innan du slutför prompterna går det att göra en timeout och Miss lyckas med autentiseringsprocessen. I det här fallet går du tillbaka till designern och försöker logga in igen för att skapa anslutningen.

1. När du uppmanas att få åtkomst till ditt e-postkonto i förhands gransknings tillägget för Azure Logic Apps väljer du **Öppna**. Följ efterföljande prompt för att tillåta åtkomst.

   ![Skärm bild som visar utöknings meddelandet för förhands granskning för att tillåta åtkomst.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Om du vill förhindra framtida prompter väljer du **fråga inte igen för det här tillägget**.

   När Visual Studio Code skapar anslutningen visar vissa kopplingar meddelandet att `The connection will be valid for {n} days only.` denna tids gräns bara gäller varaktigheten när du redigerar din Logic app i Visual Studio Code. Efter distributionen gäller den här gränsen inte längre eftersom din Logi Kap par kan autentisera vid körning genom att använda den automatiskt aktiverade [systemtilldelade hanterade identiteten](../logic-apps/create-managed-service-identity.md). Den här hanterade identiteten skiljer sig från autentiseringsuppgifterna för autentisering eller anslutnings sträng som du använder när du skapar en anslutning. Om du inaktiverar den här systemtilldelade hanterade identiteten fungerar inte anslutningarna vid körning.

1. Om åtgärden **skicka e-post** inte visas i designern väljer du den åtgärden.

1. I åtgärdens informations fönster, på fliken **parametrar** , anger du nödvändig information för åtgärden, till exempel:

   ![Skärm bild som visar Logic app designer med information för Office 365 Outlook "Skicka ett e-postmeddelande"-åtgärd.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Att** | Ja | <*din-e-postadress*> | E-postmottagaren, som kan vara din e-postadress i test syfte. I det här exemplet används det fiktiva e-postmeddelandet `sophiaowen@fabrikam.com` . |
   | **Ämne** | Ja | `An email from your example workflow` | E-postmeddelandets ämne |
   | **Brödtext** | Ja | `Hello from your example workflow!` | Innehållet i e-postmeddelandet |
   ||||

   > [!NOTE]
   > Om du vill göra ändringar i informations fönstret på fliken **Inställningar** , **kör efter** eller **statiskt resultat** , se till att du väljer **genomför** ändringarna innan du växlar flikar eller byter fokus till designern. Annars behåller Visual Studio-koden inte dina ändringar. Mer information finns på sidan om förhands gransknings tilläggets [kända problem GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. I designern väljer du **Spara**.

Kör sedan och Felsök ditt arbets flöde lokalt i Visual Studio Code.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Kör och Felsök lokalt

Testa din Logi Kap par genom att följa dessa steg för att starta en felsökningssession och hitta URL: en för den slut punkt som skapas av begär ande utlösaren. Du behöver den här URL: en så att du senare kan skicka en begäran till den slut punkten.

1. För att hjälpa dig att enkelt felsöka ett tillstånds lösa Logic app-arbetsflöde kan du [Aktivera körnings historiken för det arbets flödet](#run-history).

1. Öppna menyn **Kör** i Visual Studio Code-verktygsfältet och välj **Starta fel sökning** (F5).

   **Terminalfönstret** öppnas så att du kan gå igenom felsökningssessionen.

1. Nu hittar du återanrops-URL: en för slut punkten i utlösaren för begäran.

   1. Öppna fönstret Utforskaren igen så att du kan visa projektet.

   1. I snabb menyn **workflow.jspå** filen väljer du **Översikt**.

      ![Skärm bild som visar Explorer-fönstret och gen vägs fönstret för workflow.jspå filen med "Översikt" vald.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Hitta URL-värdet för **motringning** , som ser ut ungefär som den här URL: en för utlösaren exempel på begäran:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Skärm bild som visar din arbets flödes översikt sida med återanrops-URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Om du vill testa återanrops-URL: en genom att utlösa Logic app-arbetsflödet öppnar du [Postman](https://www.postman.com/downloads/) eller önskat verktyg för att skapa och skicka förfrågningar.

   Detta exempel fortsätter med Postman. Mer information finns i [postman komma igång](https://learning.postman.com/docs/getting-started/introduction/).

   1. I verktygsfältet Postman väljer du **nytt**.

      ![Skärm bild som visar Postman med knappen Ny markerad](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. I fönstret **Skapa nytt** under **Bygg block** väljer du **begäran**.

   1. I fönstret **Spara begäran** , under **namn för begäran** , anger du ett namn för begäran, till exempel `Test workflow trigger` .

   1. Under **Välj en samling eller mapp att spara till** väljer du **skapa samling**.

   1. Under **alla samlingar** anger du ett namn för samlingen som ska skapas för att ordna dina begär Anden, trycker på RETUR och väljer **Spara till < *samlings namn* >**. I det här exemplet används `Logic Apps requests` som samlings namn.

      Postman fönster för begär ande öppnas så att du kan skicka en begäran till återanrops-URL: en för begäran utlösare.

      ![Skärm bild som visar Postman med fönstret öppen förfrågan](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Gå tillbaka till Visual Studio Code. på sidan Översikt för arbets flödet kopierar du värdet för **återanrops-URL** .

   1. Återgå till Postman. I fönstret begäran, bredvid listan metod **, som för närvarande visar som** standard metod för begäran, klistra in återanrops-URL: en som du tidigare kopierade i rutan adress och väljer **Skicka**.

      ![Skärm bild som visar Postman och återanrops-URL i rutan adress med knappen Skicka vald](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Exempel på ett Logic app-arbetsflöde skickar ett e-postmeddelande som ser ut ungefär som i det här exemplet:

      ![Skärm bild som visar Outlook-e-post enligt beskrivningen i exemplet](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Gå tillbaka till arbets flödets översikts sida i Visual Studio Code.

   Om du har skapat ett tillstånds känsligt arbets flöde, kommer sidan Översikt att Visa arbets flödets körnings status och historik när du har skickat begäran.

   > [!TIP]
   > Om körnings statusen inte visas kan du försöka att uppdatera översikts sidan genom att välja **Uppdatera**. Ingen körning sker för en utlösare som hoppas över på grund av ouppfyllda-villkor eller inga data hittas.

   ![Skärm bild som visar sidan för arbets flödets översikt med körnings status och historik](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Körnings status | Beskrivning |
   |------------|-------------|
   | **Avbruten** | Körningen stoppades eller slutfördes inte på grund av externa problem, till exempel ett system avbrott eller en upphörde Azure-prenumeration. |
   | **Avbröts** | Körningen utlöstes och startades men tog emot en begäran om annullering. |
   | **Misslyckad** | Minst en åtgärd i körningen misslyckades. Inga efterföljande åtgärder i arbets flödet har ställts in för att hantera det här problemet. |
   | **Körs** | Körningen utlöstes och pågår, men den här statusen kan också visas för en körning som är begränsad på grund av [Åtgärds gränser](logic-apps-limits-and-config.md) eller den [aktuella pris Planen](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tips** : om du konfigurerar [diagnostikloggning](monitor-logic-apps-log-analytics.md)kan du få information om eventuella begränsnings händelser som inträffar. |
   | **Brutit** | Körningen lyckades. Om en åtgärd Miss lyckas, hanterar en efterföljande åtgärd i arbets flödet detta fel. |
   | **Tids gränsen uppnåddes** | Tids gränsen för körningen uppnåddes eftersom den aktuella varaktigheten överskred tids gränsen för körning, vilket styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits). Körningens varaktighet beräknas med hjälp av körningens start tid och tids gräns för körning vid den Start tiden. <p><p>**Obs!** om Körningens varaktighet också överskrider den aktuella *gränsen för körnings historik* , som också styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits), rensas körningen från körnings historiken med ett dagligt rensnings jobb. Oavsett om tids gränsen för körningen är slut eller slutförd beräknas alltid kvarhållningsperioden med hjälp av start tiden och den *aktuella* kvarhållningsperioden. Så om du minskar tids gränsen för en pågående körnings tid för en flygning. Körningen är dock antingen kvar eller så tas den bort från körnings historiken, baserat på om Körningens varaktighet överskred gränsen för kvarhållning. |
   | **Väntar** | Körningen har inte startat eller pausats, till exempel på grund av en tidigare arbets flödes instans som fortfarande körs. |
   |||

1. Om du vill granska status för varje steg i en speciell körning och stegets indata och utdata, väljer du knappen med tre punkter ( **...** ) för den här körningen och väljer **Visa kör**.

   ![Skärm bild som visar arbets flödets körnings historik rad med ellipser-knappen och "Visa körning" valt](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code öppnar vyn övervakning och visar status för varje steg i körningen.

   ![Skärm bild som visar varje steg i arbets flödets körning och deras status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   Här är möjliga statusar som varje steg i arbets flödet kan ha:

   | Åtgärds status | Ikon | Beskrivning |
   |---------------|------|-------------|
   | Avbruten | ![Ikon för status för avbrutna åtgärder][aborted-icon] | Åtgärden stoppades eller avslutades inte på grund av externa problem, till exempel ett system avbrott eller en upphördende Azure-prenumeration. |
   | Avbrutet | ![Ikon för status för avbrutna åtgärder][cancelled-icon] | Åtgärden kördes men tog emot en begäran om annullering. |
   | Misslyckad | ![Ikon för åtgärds status misslyckades][failed-icon] | Det gick inte att utföra åtgärden. |
   | Körs | ![Ikon för åtgärds status som körs][running-icon] | Åtgärden körs för närvarande. |
   | Överhoppad | ![Ikon för åtgärds status "hoppar över"][skipped-icon] | Åtgärden hoppades över eftersom den direkt föregående åtgärden misslyckades. En åtgärd har ett `runAfter` villkor som kräver att föregående åtgärd har slutförts innan den aktuella åtgärden kan köras. |
   | Lyckades | ![Ikon för status lyckad åtgärd][succeeded-icon] | Åtgärden har slutförts. |
   | Lyckades med återförsök | ![Ikon för åtgärds status slutförd med återförsök][succeeded-with-retries-icon] | Åtgärden lyckades men endast efter ett eller flera återförsök. Om du vill granska återförsöks historiken går du till vyn körnings historik och väljer den åtgärden så att du kan visa indata och utdata. |
   | Tids gränsen uppnåddes | ![Ikon för åtgärds status uppnåddes][timed-out-icon] | Åtgärden stoppades på grund av timeout-gränsen som anges i den åtgärdens inställningar. |
   | Väntar | ![Ikon för status för "väntande" åtgärd][waiting-icon] | Gäller för en webhook-åtgärd som väntar på en inkommande begäran från en anropare. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Om du vill granska indata och utdata för varje steg väljer du det steg som du vill granska.

   ![Skärm bild som visar status för varje steg i arbets flödet plus indata och utdata i den expanderade åtgärden "skicka e-post"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Om du vill granska rå data och utdata för det steget, väljer du **Visa rå data** eller **visar rå** data.

1. Om du vill stoppa felsökningssessionen väljer du **stoppa fel sökning** på menyn **Kör** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Returnera ett svar till anroparen

Du kan använda den inbyggda [svars åtgärden](../connectors/connectors-native-reqres.md) för ett arbets flöde som startar med begäran utlösare för att returnera ett svar tillbaka till anroparen som skickade en begäran till din Logic app.

1. I Logic Apps designer, under åtgärden **skicka e-post** , väljer du **nytt steg**.

   Frågan **Välj en åtgärd** visas i designern och **fönstret Lägg till en åtgärd** öppnas så att du kan välja nästa åtgärd.

1. I fönstret **Lägg till en åtgärd** , under sökrutan **Välj en åtgärd** , kontrollerar du att **inbyggt** är markerat. I rutan Sök anger du `response` och väljer **svars** åtgärden.

   ![Skärm bild som visar Logic app designer med den valda svars åtgärden.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   När **svars** åtgärden visas i designern öppnas åtgärdens informations fönster automatiskt.

   ![Skärm bild som visar Logic app designer med åtgärds informations fönstret för "Response" öppet och egenskapen "brödtext" har angetts till egenskap svärdet "Skicka ett e-postmeddelande"-åtgärd.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. På fliken **parametrar** anger du den information som krävs för den funktion som du vill anropa.

   I det här exemplet returneras **text** egenskap svärdet som matas från åtgärden **skicka e-post** .

   1. Klicka i egenskaps rutan för **brödtext** så att listan med dynamiskt innehåll visas och visar tillgängliga värden från föregående utlösare och åtgärder i arbets flödet.

      ![Skärm bild som visar åtgärds fönstret för Response-åtgärden med mus pekaren inuti egenskapen "Body" så att listan med dynamiskt innehåll visas.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. I listan med dynamiskt innehåll under **Skicka ett e-postmeddelande** väljer du **brödtext**.

      ![Skärm bild som visar listan med öppna dynamiskt innehåll. I listan, under rubriken "Skicka ett e-postmeddelande", är utmatning svärdet "Body" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      När du är klar har svars åtgärdens egenskap **Body** nu angetts till åtgärden **skicka en e-post-** åtgärds värde för **brödtext** .

      ![Skärm bild som visar status för varje steg i arbets flödet plus indata och utdata i den expanderade åtgärden "respons".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. I designern väljer du **Spara**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Testa om din Logic app

När du har gjort uppdateringar till din Logic app kan du köra ett annat test genom att köra fel söknings programmet i Visual Studio och skicka en annan begäran om att utlösa din uppdaterade Logic-app, på samma sätt som i [Felsöka och testa din Logic app](#debug-test-locally).

1. Öppna menyn **Kör** i Visual Studio Code-verktygsfältet och välj **Starta fel sökning** (F5).

1. Skicka en annan begäran om att utlösa arbets flödet i Postman eller ditt verktyg för att skapa och skicka begär Anden.

1. Om du har skapat ett tillstånds känsligt arbets flöde kontrollerar du status för den senaste körningen på arbets flödets översikts sida. Om du vill visa status, indata och utdata för varje steg i den här körningen väljer du knappen med tre punkter ( **...** ) för den här körningen och väljer **Visa kör**.

   Här är till exempel steg för steg-status för en körning när exempel arbets flödet har uppdaterats med svars åtgärden.

   ![Skärm bild som visar status för varje steg i det uppdaterade arbets flödet plus indata och utdata i den expanderade åtgärden "respons".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Om du vill stoppa felsökningssessionen väljer du **stoppa fel sökning** på menyn **Kör** (Shift + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publicera till Azure

Från Visual Studio Code kan du distribuera ditt projekt direkt till Azure, som publicerar din Logic app med hjälp av resurs typen ny **Logic app (förhands granskning)** . På samma sätt som för Function app-resursen i Azure Functions, kräver distributionen för den här nya resurs typen att du väljer en [värd plan och pris nivå](../app-service/overview-hosting-plans.md), som du kan konfigurera under distributionen. Läs följande avsnitt om du vill ha mer information om att vara värd för planer och priser:

* [Skala upp en i Azure App Service](../app-service/manage-scale-up.md)
* [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)

Du kan publicera din Logi Kap par som en ny resurs, vilket automatiskt skapar eventuella ytterligare nödvändiga resurser, till exempel ett [Azure Storage konto, på samma sätt som i funktions kraven för appen](../azure-functions/storage-considerations.md). Eller så kan du publicera din Logic app till en tidigare distribuerad **Logic app-resurs (för hands version)** , som skriver över den logiska appen.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publicera som en ny Logic app (förhands granskning) resurs

1. Välj Azure-ikonen i verktygsfältet Visual Studio Code.

1. I verktygsfältet **Azure: Logic Apps (för hands version)** väljer du **distribuera till Logic app**.

   ![Skärm bild som visar rutan "Azure: Logic Apps (för hands version)" och fönstrets verktygsfält med "distribuera till Logic app" vald.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. I listan som Visual Studio Code öppnas väljer du bland följande alternativ:

   * **Skapa ny Logic app (för hands version) i Azure** (snabb)
   * **Skapa ny Logic app (för hands version) i Azure Advanced**
   * En tidigare distribuerad **Logic app-resurs (för hands version)** , om sådan finns

   Det här exemplet fortsätter med **Skapa ny Logic app (för hands version) i Azure Advanced**.

   ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" med en lista med "Skapa ny Logic app (för hands version) i Azure" vald.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Följ dessa steg om du vill skapa en ny **Logic app-resurs (förhands granskning)** :

   1. Ange ett globalt unikt namn för din nya Logic app, vilket är det namn som ska användas för **Logic app (för hands version)** -resursen. I det här exemplet används `example-logic-app-preview` .

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppvarning för att ange ett namn för den nya Logic-appen att skapa.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Välj en värd plan för den nya Logic-appen, antingen [**App Service plan**](../azure-functions/functions-scale.md#app-service-plan) eller [**Premium**](../azure-functions/functions-scale.md#premium-plan). I det här exemplet väljer du **App Service plan**.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en prompt för att välja "App Service plan" eller "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Skapa en ny App Service plan eller Välj en befintlig plan. I det här exemplet väljer du **Skapa nytt App Service plan**.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppvarning till "Skapa ny App Service plan" eller Välj en befintlig App Service plan.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Ange ett namn för din App Service plan och välj sedan en [pris nivå](../app-service/overview-hosting-plans.md) för planen. I det här exemplet väljs den **kostnads fria** prenumerationen F1.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppvarning för att välja en pris nivå.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. För optimala prestanda kan du söka efter och välja samma resurs grupp som ditt projekt för distributionen.

      > [!NOTE]
      > Även om du kan skapa eller använda en annan resurs grupp kan det påverka prestandan. Om du skapar eller väljer en annan resurs grupp, men om du avbryter när bekräftelse meddelandet visas, avbryts även distributionen.

   1. För tillstånds känsliga arbets flöden väljer du **Skapa nytt lagrings konto** eller ett befintligt lagrings konto.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppsvars tolk för att skapa eller välja ett lagrings konto.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. För enklare diagnostik-loggning och spårning kan du välja en befintlig Application Insights-resurs. Annars kan du välja **Skapa ny Application Insights resurs** eller konfigurera Application Insights i Azure Portal efter att du har distribuerat din app.

      Innan du distribuerar ska du se till att lägga till `logLevel` objektet till `logging` objektet i **host.jspå** filen som finns på projektets rotnivå, och ange `Host.Triggers.Workflow` till `Information` exempel:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Så här kan **host.jsi** filen se ut:

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   När du är klar börjar Visual Studio Code att skapa och distribuera de resurser som krävs för att publicera din Logic app.

1. Om du vill granska och övervaka distributions processen väljer du **utdata** på **Visa** -menyn. I verktygsfältet utdata väljer du **Azure Logic Apps**.

   ![Skärm bild som visar utdatafönstret med "Azure Logic Apps" som marker ATS i listan verktygsfält tillsammans med distributionens förlopp och status.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   När Visual Studio Code har distribuerat ditt Logic app-arbetsflöde till Azure visas det här meddelandet:

   ![Skärm bild som visar ett meddelande om att distributionen till Azure har slutförts.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Grattis, din Logi Kap par är nu Live i Azure och aktiverat som standard.

Sedan kan du lära dig hur du utför dessa uppgifter:

* [Hitta din distribuerade Logic-app i Azure Portal](#find-manage-deployed-workflows-portal) eller [i Visual Studio Code](#find-manage-deployed-workflows-vs-code).

* [Aktivera körnings historik för tillstånds lösa Logic app-arbetsflöden](#run-history).

* [Aktivera övervakning i en distribuerad **Logic app-resurs (för hands version)**](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Hitta och hantera distribuerade Logic Apps i Visual Studio Code

I Visual Studio Code kan du Visa alla distribuerade Logi Kap par i din Azure-prenumeration, oavsett om de är de ursprungliga **Logic Apps** eller om du har en resurs typ för **Logic app (för hands version)** och väljer aktiviteter som hjälper dig att hantera dessa Logic Apps. Men för att få åtkomst till båda resurs typerna behöver du både **Azure Logic Apps** och **Azure Logic Apps-tillägg (för hands version)** för Visual Studio Code.

1. Välj Azure-ikonen i det vänstra verktygsfältet. I fönstret **Azure: Logic Apps (förhands granskning)** expanderar du din prenumeration, som visar alla distribuerade Logic-appar för den prenumerationen.

1. Sök efter och välj den Logic-app som du vill hantera. Öppna den logiska appens snabb meny och välj den aktivitet som du vill utföra.

   Du kan till exempel välja uppgifter som att stoppa, starta, starta om eller ta bort din distribuerade Logic-app.

   ![Skärm bild som visar Visual Studio Code med anknytnings fönstret Azure Logic Apps (förhands granskning) och det distribuerade arbets flödet.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Om du vill visa alla arbets flöden i Logic-appen expanderar du din Logic app och expanderar sedan noden **arbets flöden** .

1. Om du vill visa ett särskilt arbets flöde öppnar du arbets flödets snabb meny och väljer **Öppna i designer** , som öppnar arbets flödet i skrivskyddat läge.

   För att redigera arbets flödet har du följande alternativ:

   * I Visual Studio Code öppnar du projektets **workflow.jspå** filen i Logic Apps designer, gör dina redigeringar och distribuerar om din Logic app till Azure.

   * [Leta upp och öppna din Logic app](#find-manage-deployed-workflows-portal)i Azure Portal. Hitta, redigera och spara arbets flödet.

1. Öppna den distribuerade Logic-appen i Azure Portal genom att öppna den logiska appens snabb meny och välja **Öppna i portalen**.

   Azure Portal öppnas i webbläsaren, och du loggas in på portalen automatiskt om du är inloggad i Visual Studio Code och visar din Logic app.

   ![Skärm bild som visar Azure Portal sidan för din Logic app i Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Du kan också logga in separat till Azure Portal, använda portalen Sök i rutan för att hitta din Logi Kap par och sedan välja din Logi Kap par i resultat listan.

   ![Skärm bild som visar Azure Portal och Sök fältet med Sök Resultat för distribuerad Logic-app, som visas markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Hitta och hantera distribuerade Logic Apps i portalen

I Azure Portal kan du Visa alla distribuerade Logic-appar som finns i din Azure-prenumeration, oavsett om de är den ursprungliga **Logic Apps** resurs typen eller resurs typen **Logic app (för hands version)** . För närvarande organiseras och hanteras varje resurs typ som separata kategorier i Azure.

> [!NOTE]
> För offentlig för hands version kan du bara Visa distribuerade **Logic app-resurser (förhands granskning)** i Azure Portal, inte skapa nya resurser för **Logic app (för hands version)** . Du kan bara skapa dessa Logic Apps i Visual Studio Code. Du kan dock [lägga till arbets flöden](#add-workflows) till distribuerade Logic Apps med den här resurs typen.

Följ dessa steg om du vill hitta Logi Kap par som har resurs typen **Logic app (förhands granskning)** :

1. Skriv i rutan Azure Portal Sök `logic app preview` . När resultat listan visas väljer du **Logic app (för hands version)** under **tjänster**.

   ![Skärm bild som visar sökrutan Azure Portal med "Logic Apps Preview" Sök text.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. I fönstret **Logic app (för hands version)** letar du reda på och väljer den Logic-app som du distribuerade från Visual Studio Code.

   ![Skärm bild som visar Azure Portal och den Logic app (för hands version) resurser som distribueras i Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal öppnar den enskilda resurs sidan för den valda Logic-appen.

   ![Skärm bild som visar ditt Logic app-arbetsflödes resurs sida i Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Om du vill visa arbets flödena i den här Logic-appen väljer du **arbets flöden** på fliken Logic Apps.

   I fönstret **arbets flöden** visas alla arbets flöden i den aktuella Logic-appen. Det här exemplet visar det arbets flöde som du skapade i Visual Studio Code.

   ![Skärm bild som visar resurs sidan "Logic app (för hands version)" i fönstret "arbets flöden" öppen och det distribuerade arbets flödet](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Om du vill visa ett arbets flöde väljer du det i **arbets** flödes fönstret.

   Fönstret arbets flöde öppnas och visar mer information och uppgifter som du kan utföra på det arbets flödet.

   Om du till exempel vill visa stegen i arbets flödet väljer du **Designer**.

   ![Skärm bild som visar det valda arbets flödets översikts fönster, medan arbets flödes menyn visar det valda design kommandot.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Logic App Designer öppnas och visar det arbets flöde som du skapade i Visual Studio Code. Nu kan du göra ändringar i det här arbets flödet i Azure Portal.

   ![Skärm bild som visar Logic App Designer och arbets flödet som distribueras från Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Lägg till ett arbets flöde till distribuerade Logic Apps

Du kan lägga till tomma arbets flöden till en **logisk app-resurs (för hands version)** som du har distribuerat från Visual Studio Code och skapa dessa arbets flöden i Azure Portal med hjälp av Azure Portal.

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer din distribuerade **Logic app-resurs (för hands version)** .

1. På menyn Logic Apps väljer du **arbets flöden**. I fönstret **arbets flöden** väljer du **Lägg till**.

   ![Skärm bild som visar den valda Logic Apps-fönstret och verktygsfältet med kommandot Lägg till markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Ange ett namn för arbets flödet i fönstret **nytt arbets flöde** . Välj antingen **tillstånds känslig** eller **tillstånds lös** **>** **skapa**.

   När Azure har distribuerat ditt nya arbets flöde, som visas i fönstret **arbets flöden** , väljer du arbets flödet för att utföra hantering och andra uppgifter, till exempel öppna Logic Apps designer eller kodvyn.

   ![Skärm bild som visar det valda arbets flödet med alternativ för hantering och granskning.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Om du till exempel öppnar designern för ett nytt arbets flöde visas en tom arbets yta. Nu kan du bygga det här arbets flödet i Azure Portal.

   ![Skärm bild som visar Logic App Designer och ett tomt arbets flöde.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Körnings historik för tillstånds lösa Logic app-arbetsflöden

För att lättare felsöka ett tillstånds löst Logic app-arbetsflöde kan du aktivera körnings historiken för det arbets flödet i Visual Studio Code eller i Azure Portal och sedan inaktivera körnings historiken när du är klar.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>För ett tillstånds löst Logic app-arbetsflöde i Visual Studio Code

Om du arbetar med och kör det tillstånds lösa Logic app-arbetsflödet lokalt i Visual Studio Code, följer du dessa steg:

1. I ditt projekt kan du söka efter och expandera mappen **designtime för arbets flöde** . Sök efter och öppna filen **local.settings.js** .

1. Lägg till `Workflows.{yourWorkflowName}.operationOptions` egenskapen och ange värdet till `WithStatelessRunHistory` , till exempel:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Om du vill inaktivera körnings historiken när du är klar tar du antingen bort `Workflows.{yourWorkflowName}.OperationOptions` egenskapen och dess värde, eller anger egenskapen till `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>För ett tillstånds lösa Logic app-arbetsflöde i Azure Portal

Följ dessa steg om du redan har distribuerat projektet till Azure Portal:

1. Leta upp och öppna din **Logic app-resurs (förhands granskning)** i [Azure Portal](https://portal.azure.com).

1. På menyn Logic Apps, under **Inställningar** , väljer du **konfiguration**.

1. På fliken **program inställningar** väljer du **ny program inställning**.

1. I rutan **namn** i rutan **Lägg till/redigera program inställningar** anger du namnet på åtgärds alternativet: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. I rutan **värde** anger du följande värde: `WithStatelessRunHistory`

   Exempel:

   ![Skärm bild som visar resursen Azure Portal och Logic app (för hands version) med inställningen "konfiguration" > nya program inställningar "<" Lägg till/redigera program inställning "och" arbets flöden. {yourWorkflowName}. Alternativet OperationOptions "är inställt på" WithStatelessRunHistory ".](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. När du är klar väljer du **Ok**. I fönstret **konfiguration** väljer du **Spara**.

Fortsätt till nästa avsnitt om du vill aktivera övervakning i den distribuerade Logic app-resursen (förhands granskning).

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Aktivera övervakning av resurser för distribuerad Logic app (för hands version)

Följ dessa steg om du vill aktivera övervakning i en distribuerad **Logic app-resurs (förhands granskning)** :

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer resursen distribuerad **Logic app (för hands version)** .

1. På den resurs menyn, under **API** , väljer du **CORS**.

1. Lägg till jokertecknet (*) under **tillåtna ursprung** i **CORS** -fönstret.

1. När du är klar väljer du **Spara** i verktygsfältet **CORS** .

   ![Skärm bild som visar Azure Portal med en distribuerad Logic Apps-resurs (förhands granskning). På resurs-menyn väljs "CORS" med en ny post för "tillåtna ursprung" på jokertecknet "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Distribuera till Docker-behållare

Med hjälp av [verktyget .net Core kommando rads gränssnitt (CLI)](/dotnet/core/tools/)kan du bygga projektet och sedan publicera bygget. Du kan sedan bygga och använda en [Docker-behållare](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) som mål för att distribuera ditt Logic app-arbetsflöde. Mer information finns i följande avsnitt:

* [Introduktion till behållare och Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduktion till .NET och Docker](/dotnet/core/docker/introduction)
* [Docker-terminologi](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Självstudie: kom igång med Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Skapa projektet genom att öppna en kommando tolk och köra följande kommando:

   `dotnet build -c release`

   Mer information finns på referens sidan för [dotNet-version](/dotnet/core/tools/dotnet-build/) .

1. Publicera din version genom att köra det här kommandot:

   `dotnet publish -c release`

   Mer information finns på sidan [dotNet Publish](/dotnet/core/tools/dotnet-publish/) referens.

1. Bygg en Docker-behållare med hjälp av en Docker-fil för ett .NET-arbets flöde och kör det här kommandot:

   `docker build --tag local/workflowcontainer .`

   Här är till exempel en exempel Docker-fil som distribuerar en tillstånds känslig Logic-app och anger anslutnings strängen för det Azure Storage konto som användes för att publicera Logic-appen till Azure Portal. Om du vill söka efter och kopiera lagrings kontots anslutnings sträng i Azure Portal granskar du [Hantera lagrings konto nycklar](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Skärm bild som visar Azure Portal med åtkomst nycklar för lagrings kontot och en anslutnings sträng som har kopierats.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Anslutnings strängen ser ut ungefär som i det här exemplet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Här är formatet för Docker-filen:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Mer information finns i [Docker-build](https://docs.docker.com/engine/reference/commandline/build/).

1. Spara strängen på ett säkert sätt så att du senare kan lägga till strängen i **local.settings.jspå** filer i projektet som du använder för att skapa din Logic app i Visual Studio Code.

1. Kör behållaren lokalt genom att använda det här kommandot:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Mer information finns i [Docker-körning](https://docs.docker.com/engine/reference/commandline/run/).

1. Skicka följande begäran om du vill hämta återanrops-URL: en för utlösaren för begäran:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   Värdet för < *Master-key* > definieras i det Azure Storage-konto som du anger `AzureWebJobsStorage` i filen, **Azure-WebJobs-hemligheter/{Deployment-Name}/host.jspå** , där du hittar värdet i det här avsnittet:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Kapslade beteende skillnader mellan tillstånds känsliga och tillstånds lösa Logic Apps

Du kan [skapa ett Logic app-arbetsflöde som kan anropas](../logic-apps/logic-apps-http-endpoint.md) från andra Logic app-arbetsflöden som finns i samma **Logic app-resurs (förhands granskning)** genom att använda [begär](../connectors/connectors-native-reqres.md) ande utlösare, [http-webhook](../connectors/connectors-native-webhook.md) eller hanterade anslutnings utlösare som har [typen ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och kan ta emot HTTPS-begäranden

Här följer beteende mönster som kapslade Logic app-arbetsflöden kan följa efter att ett överordnat arbets flöde anropar ett underordnat arbets flöde

* Asynkront avsöknings mönster

  Överordnad väntar inte på svar på sitt första anrop, men kontrollerar kontinuerligt den underordnade körnings historiken tills den underordnade har körts. Tillstånds känsliga arbets flöden följer som standard det här mönstret, vilket är idealiskt för långvariga underordnade arbets flöden som kan överskrida [tids gräns gränsen för begäran](../logic-apps/logic-apps-limits-and-config.md).

* Synkront mönster ("eld och glömma")

  Underordnade bekräftar anropet genom att omedelbart returnera ett `202 ACCEPTED` svar och den överordnade åtgärden fortsätter till nästa åtgärd utan att vänta på resultatet från den underordnade. I stället får den överordnade resultatet när den underordnade har körts. Underordnade tillstånds känsliga arbets flöden som inte innehåller någon svars åtgärd följer alltid synkront mönster. För underordnade tillstånds känsliga arbets flöden finns körnings historiken som du kan granska.

  Om du vill aktivera det här beteendet anger du egenskapen till i arbets flödets JSON-definition `operationOptions` `DisableAsyncPattern` . Mer information finns i [utlösare och åtgärds typer – åtgärds alternativ](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Utlös och vänta

  För ett underordnat tillstånds löst arbets flöde väntar den överordnade svaret på ett svar som returnerar resultatet från den underordnade. Det här mönstret fungerar ungefär som att använda den inbyggda [http-utlösaren eller åtgärder](../connectors/connectors-native-http.md) för att anropa ett underordnat arbets flöde. Underordnade tillstånds lösa arbets flöden som inte innehåller någon svars åtgärd returnerar omedelbart ett `202 ACCEPTED` svar, men överordnad väntar på att barnet ska slutföras innan nästa åtgärd fortsätter. Dessa beteenden gäller endast för underordnade tillstånds lösa arbets flöden.

Den här tabellen anger beteendet för det underordnade arbets flödet baserat på om den överordnade och underordnade är tillstånds känsliga, tillstånds lösa eller är blandade arbets flödes typer:

| Överordnat arbets flöde | Underordnat arbets flöde | Underordnat beteende |
|-----------------|----------------|----------------|
| Tillståndskänsliga | Tillståndskänsliga | Asynkron eller synkron med `"operationOptions": "DisableAsyncPattern"` inställning |
| Tillståndskänsliga | Tillståndslös | Utlös och vänta |
| Tillståndslös | Tillståndskänsliga | Synkront |
| Tillståndslös | Tillståndslös | Utlös och vänta |
||||

## <a name="limits"></a>Gränser

Även om många [befintliga gränser för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) är desamma för den här resurs typen, är här skillnaderna i detta offentliga för hands versions tillägg:

* Hanterade anslutningar: 50 förfrågningar per minut per anslutning

* För den [infogade kod åtgärden för JavaScript](../logic-apps/logic-apps-add-run-inline-code.md) -åtgärder har följande gränser ändrats:

  * Gränsen på kod tecken ökar med 1 024 tecken till 100 000 tecken.

  * Tids gränsen för att köra koden ökar från fem sekunder till 15 sekunder.

## <a name="next-steps"></a>Nästa steg

Vi vill gärna höra om dina upplevelser med detta offentliga för hands versions tillägg!

* [Skapa problem i GitHub](https://github.com/Azure/logicapps/issues)för buggar eller problem.
* [Använd det här formuläret](https://aka.ms/lafeedback)för frågor, förfrågningar, kommentarer och annan feedback.
