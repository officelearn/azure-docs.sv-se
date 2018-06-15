---
title: Skapa arbetsflöden för att bearbeta e-postmeddelanden och bifogade filer – Azure Logic Apps | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar automatiska arbetsflöden för bearbetning av e-postmeddelanden och bifogade filer med Azure Logic Apps, Azure Storage och Azure Functions
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 3d6d66dca06c1f34a31155a27c32bbe3e48c8aa3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300641"
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Bearbeta e-postmeddelanden och bilagor med en logikapp

Med Azure Logic Apps lär du dig att automatisera arbetsflöden och integrera data i olika Azure-tjänster, Microsoft-tjänster och andra SaaS-appar (programvara som en tjänst) samt lokala system. I den här självstudien får du lära dig att skapa en [logikapp](../logic-apps/logic-apps-overview.md) som hanterar inkommande e-post och bilagor. Logikappen bearbetar innehållet, sparar det i Azure Storage och skickar meddelanden för granskning av innehållet. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera [Azure Storage](../storage/common/storage-introduction.md) och Storage Explorer så att de kollar sparade e-postmeddelanden och bifogade filer.
> * Skapa en [Azure-funktion](../azure-functions/functions-overview.md) som tar bort HTML från e-postmeddelanden. Den här självstudiekursen innehåller koden som du kan använda för den här funktionen.
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar bifogade filer i e-postmeddelanden.
> * Lägg till ett villkor som kontrollerar om e-postmeddelanden har bifogade filer eller inte.
> * Lägg till en åtgärd som anropar Azure-funktionen när ett e-postmeddelande innehåller bifogade filer.
> * Lägg till en åtgärd som skapar lagringsblobar för e-postmeddelanden och bifogade filer.
> * Lägg till en åtgärd som skickar e-postmeddelanden.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Logikapp på hög nivå](./media/tutorial-process-email-attachments-workflow/overview.png)

Om du inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a> innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, som Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](https://docs.microsoft.com/connectors/).

  För den här logikappen används ett Office 365 Outlook-konto. 
  Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

* Ladda ned och installera <a href="http://storageexplorer.com/" target="_blank">kostnadsfria Microsoft Azure Storage Explorer</a>. Med det här verktyget kan du kontrollera att din lagringsbehållare är korrekt inställd.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a> med autentiseringsuppgifterna för ditt Azure-konto.

## <a name="set-up-storage-to-save-attachments"></a>Konfigurera lagring för att spara bifogade filer

Du kan spara inkommande e-postmeddelanden och blobar i en [Azure-lagringsbehållare](../storage/common/storage-introduction.md). 

1. Innan du kan skapa en lagringsbehållare ska du [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) med de här inställningarna:

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | attachmentstorageacct | Namnet på lagringskontot | 
   | **Distributionsmodell** | Resource manager | [Distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md) för att hantera resursdistributionen | 
   | **Typ av konto** | Generellt syfte | [Lagringskontotyp](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Prestanda** | Standard | Den här inställningen anger datatyper som stöds och media för att lagra data. Se [Typer av lagringskonton](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Replikering** | Lokalt redundant lagring (LRS) | Den här inställningen anger hur dina data kopieras, lagras, hanteras och synkroniseras. Se [Replikering](../storage/common/storage-introduction.md#replication). | 
   | **Säker överföring krävs** | Disabled | Den här inställningen anger den säkerhet som krävs för begäranden från anslutningar. Se [Kräv säker överföring](../storage/common/storage-require-secure-transfer.md). | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Azure-prenumerationens namn | 
   | **Resursgrupp** | LA-Tutorial-RG | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som används för att organisera och hantera relaterade resurser. <p>**Obs!** En resursgrupp finns i en viss region. Trots att objekten i den här självstudien kanske inte är tillgängliga i alla regioner ska du försöka att använda samma region när det är möjligt. | 
   | **Plats** | Östra USA 2 | Regionen där informationen om lagringskontot ska lagras | 
   | **Konfigurera virtuella nätverk** | Disabled | För den här självstudien behåller du inställningen **Disabled** (Inaktiverad). | 
   |||| 

   Du kan även använda [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) eller [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. När Azure har distribuerat ditt lagringskonto hämtar du åtkomstnyckeln för ditt lagringskonto:

   1. I lagringskontots meny går du till **Inställningar** och väljer **Åtkomstnycklar**. 
   2. Hitta **key1** under **Standardnycklar** och namnet på ditt lagringskonto.

      ![Kopiera och spara lagringskontots namn och nyckel](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Du kan även använda [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) eller [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Skapa en lagringsbehållare för dina e-postbilagor.
   
   1. På panelen **Översikt** på lagringskontots meny väljer du **Blobbar** under **Tjänster** och sedan **+ Behållare**.

   2. Skriv ”attachments” som behållarens namn. Under **Offentlig åtkomstnivå** väljer du **Behållare (anonym läsåtkomst för behållare och blobbar)** och sedan **OK**.

   Du kan även använda [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) eller [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   När du är klar kan du hitta din lagringsbehållare i lagringskontot här i Azure-portalen:

   ![Klar lagringsbehållare](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

Anslut sedan Storage Explorer till ditt lagringskonto.

## <a name="set-up-storage-explorer"></a>Konfigurera Storage Explorer

Anslut nu Storage Explorer till lagringskontot så du kan bekräfta att logikappen sparar bilagor och blobar korrekt i lagringsbehållaren.

1. Öppna Microsoft Azure Storage Explorer. När Storage Explorer ber dig om en anslutning till Azure Storage väljer du **Använda lagringskontots namn och nyckel** > **Nästa**.
Om ingen uppmaning visas väljer du **Lägg till konto** i Explorer-verktygsfältet.

2. Under **Attach using Name and Key** (Bifoga med namn och nyckel) anger du namnet på lagringskontot och åtkomstnyckeln du nyss sparade. Välj **Nästa** > **Anslut**.

3. Kontrollera att lagringskontot och behållaren visas korrekt i Storage Explorer:

   1. Under **Explorer** expanderar du **(Lokal och ansluten)** > 
   **Lagringskonton** > **attachmentstorageaccount** > 
   **Blob Containers** (Blob-behållare).

   2. Kontrollera att behållaren ”attachments” nu visas. 
   Till exempel:

      ![Storage Explorer – bekräfta lagringsbehållare](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Skapa sedan en [Azure-funktion](../azure-functions/functions-overview.md) som tar bort HTML från inkommande e-post.

## <a name="create-a-function-to-clean-html"></a>Skapa en funktion för att rensa HTML

Använd nu kodfragmentet som tillhandahålls via de här stegen för att skapa en Azure-funktion som tar bort HTML från varje inkommande e-postmeddelande. På så vis blir e-postinnehållet renare och enklare att bearbeta. Sedan kan du anropa den här funktionen från din logikapp.

1. Innan du kan skapa en funktion ska du [skapa en funktionsapp](../azure-functions/functions-create-function-app-portal.md) med dessa inställningar:

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Appens namn** | CleanTextFunctionApp | Ett globalt unikt och beskrivande namn på funktionsappen | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Samma Azure-prenumeration som du tidigare använt | 
   | **Resursgrupp** | LA-Tutorial-RG | Samma Azure-resursgrupp som du tidigare använt | 
   | **Värdplan** | Förbrukningsplan | Den här inställningen avgör hur resurser ska allokeras och skalas, som datorkraft, för att köra din funktionsapp. Se [jämförelse av värdplaner](../azure-functions/functions-scale.md). | 
   | **Plats** | Östra USA 2 | Samma region som du tidigare använt | 
   | **Storage** | cleantextfunctionstorageacct | Skapa ett lagringskonto för din funktionsapp. Använd bara gemena bokstäver och siffror. <p>**Obs!** Lagringskontot innehåller dina funktionsappar och skiljer sig från ditt tidigare skapade lagringskonto för e-postbilagor. | 
   | **Application Insights** | Av | Aktiverar programövervakning med [Application Insights](../application-insights/app-insights-overview.md), men för den här självstudien ska du behålla inställningen **Av**. | 
   |||| 

   Om funktionsappen inte automatiskt öppnas efter distributionen kan hitta din app i <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>. På Azures huvudmeny väljer du **App Services** och sedan din funktionsapp.

   ![Skapad funktionsapp](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Om **App Services** inte visas på Azure-menyn går du istället till **Fler tjänster**. I sökrutan letar du upp och väljer **Function Apps**. Mer information finns i [Create your function](../azure-functions/functions-create-first-azure-function.md) (Skapa funktion).

   Du kan också använda [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md), [PowerShell- och Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

2. Under **Function Apps** expanderar du **CleanTextFunctionApp** och väljer **Functions**. I funktionsverktygsfältet väljer du **+ Ny funktion**.

   ![Skapa ny funktion](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. Under **Choose a template below or go to the quickstart** (Välj en mall nedan eller gå till snabbstarten) väljer du funktionsmallen **HttpTrigger - C#**.

   ![Välj funktionsmall](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. Under **Ge funktionen ett namn** skriver du ```RemoveHTMLFunction```. Under **HTTP-utlösarens** > **auktoriseringsnivå** behåller du standardvärdet för **funktionen** och väljer **Skapa**.

   ![Namnge din funktion](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. När redigeringsprogrammet öppnas ersätter du mallkoden med den här koden som tar bort HTML och returnerar resultatet till anroparen:

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. När du är klar väljer du **Spara**. När du vill testa funktionen väljer du **Test** under pilikonen (**<**) i redigeringsprogrammets högra hörn. 

   ![Öppna testfönstret](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. I panelen **Test**, under **Brödtext i begäran**, anger du den här raden och väljer **Kör**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Testa din funktion](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   I fönstret **Utdata** visas det här resultatet från funktionen:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Skapa din logikapp när du har kontrollerat att funktionen fungerar. Trots att den här självstudien visar hur du skapar en funktion som tar bort HTML från e-post har Logic Apps även en **HTML till text**-anslutning.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. Välj **Skapa en resurs** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Skapa en logikapp](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. Under **Skapa en logikapp** anger du informationen om din logikapp så som det visas här. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Ange information om din logikapp](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | LA-ProcessAttachment | Logikappens namn | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Samma Azure-prenumeration som du tidigare använt | 
   | **Resursgrupp** | LA-Tutorial-RG | Samma Azure-resursgrupp som du tidigare använt |
   | **Plats** | Östra USA 2 | Samma region som du tidigare använt | 
   | **Log Analytics** | Av | För den här självstudien behåller du inställningen **Av**. | 
   |||| 

3. När Azure har distribuerat din app öppnas Logic Apps Designer och en sida med en introduktionsvideo och mallar för vanliga logikappar visas. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Lägg sedan till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden som innehåller bilagor. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Övervaka inkommande e-post

1. På designern anger du ”när e-post kommer” i sökrutan. Välj den här utlösaren för din e-postleverantör: **<*your-email-provider*> - När ett nytt e-postmeddelande kommer**, till exempel:

   ![Välj den här utlösaren för e-postleverantör: ”När ett nytt e-postmeddelande kommer”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du Outlook.com. 

2. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så Logic Apps kan ansluta till ditt e-postkonto.

3. Nu anger du villkoret som utlösaren använder för att filtrera ny e-post.

   1. Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden.

      ![Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Inställning | Värde | Beskrivning | 
      | ------- | ----- | ----------- | 
      | **Mapp** | Inkorgen | E-postmappen som ska kontrolleras | 
      | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna | 
      | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller | 
      |  |  |  | 
  
   2. Välj **visa avancerade alternativ** och ange dessa inställningar:

      | Inställning | Värde | Beskrivning | 
      | ------- | ----- | ----------- | 
      | **Has Attachment** (Innehåller bifogad fil) | Ja | Hämta endast e-postmeddelanden med bifogade filer. <p>**Obs!** Utlösaren tar inte bort e-post från ditt konto, kontrollerar endast nya meddelanden och bearbetar endast e-postmeddelanden som matchar filtrets ämne. | 
      | **Inkludera bifogade filer** | Ja | Hämta bilagorna som indata i arbetsflödet istället för att bara söka efter bilagor. | 
      | **Ämnesfilter** | ```Business Analyst 2 #423501``` | Texten att söka efter i e-postämnet | 
      |  |  |  | 

4. Om du vill dölja utlösarinformationen för tillfället klickar du in utlösarens rubriklist.

   ![Minimera form för att dölja information](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

   Logikappen har nu publicerats men gör inget annat än att kolla din e-post. 
   Lägg sedan till ett villkor som anger kriterier för att fortsätta arbetsflödet.

## <a name="check-for-attachments"></a>Sök efter bifogade filer

1. Under utlösaren väljer du **+ Nytt steg** > **Lägg till ett villkor**.

   När villkorsformen visas, visas som standard antingen parameterlistan eller listan med dynamiskt innehåll och visar eventuella parametrar från tidigare steg som du kan inkludera som arbetsflödesindata. 
   Webbläsarens bredd bestämmer vilken lista som visas.

2. Byt namn på villkoret med en bättre beskrivning.

   1. I villkorets rubriklist väljer du **ellipsknappen** (**...**) > **Byt namn**.

      Om webbläsaren till exempel visas i en smal vy:

      ![Byt namn på villkor](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Om din webbläsare är i bred vy och den dynamiska innehållslistan blockerar åtkomsten till ellipsknappen ska du stänga listan genom att välja **Lägg till dynamiskt innehåll** i villkoret. 
      
      ![Stäng listan Dynamiskt innehåll](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Byt namn på villkoret med den här beskrivningen: ```If email has attachments and key subject phrase```

3. Beskriv villkoret genom att tillhandahålla ett uttryck. 

   1. I villkorsformen väljer du **Redigera i avancerat läge**.

      ![Redigera villkor i avancerat läge](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. I textrutan skriver du följande uttryck:

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Det här uttrycket jämför egenskapsvärdet **HasAttachment** från brödtexten i utlösaren, vilket är e-postmeddelandet i den här självstudien med det booleska objektet```True```. 
      Om båda värdena är lika innehåller e-postmeddelandet minst en bifogad fil, villkoret skickas och arbetsflödet fortsätter.

      Villkoret nu ser ut som i det här exemplet:

      ![Villkorsuttryck](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Välj **Redigera standardläge**. Uttrycket löses nu som du ser nedan:

      ![Löst uttryck](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > Om du vill skapa ett uttryck manuellt måste du arbeta i grundläggande läge och ha den dynamiska listan öppen så att du kan arbeta med uttrycksverktyget. Under **Uttryck** kan du välja funktioner. Under **Dynamiskt innehåll** kan du välja parameterfält som ska användas i funktionerna.
      > Den här självstudien visar hur du manuellt skapar uttryck senare.

4. Spara din logikapp.

### <a name="test-your-condition"></a>Testa ditt villkor

Testa nu om villkoret fungerar som det ska:

1. Om din logikapp inte körs redan väljer du **Kör** i designerverktygsfältet.

   Det här steget startar logikappen manuellt utan att vänta tills ditt angivna intervall skickas. 
   Men det händer ingenting förrän test-e-postmeddelandet hamnar i inkorgen. 

2. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne innehåller den text du har angett i utlösarens **ämnesfilter**: ```Business Analyst 2 #423501```

   * Ditt e-postmeddelande har en bifogad fil. 
   Skapa för tillfället en tom textfil och bifoga den i e-postmeddelandet.

   När e-postmeddelandet kommer letar logikappen efter bilagor och den angivna ämnestexten.
   Om villkoret uppfylls utlöser utlösaren Logic Apps-motorn och får den att skapa en logikappinstans och starta arbetsflödet. 

3. Om du vill kontrollera att utlösaren utlöstes och att logikappen kördes väljer du **Översikt** på logikappens meny.

   ![Kontrollera utlösare och körningshistorik](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Om logikappen inte utlöstes eller kördes trots en lyckad utlösare kan du läsa [informationen om att felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Definiera därefter åtgärderna som ska vidtas för grenen **If true** (Om sant). För att spara e-postmeddelandet tillsammans med eventuella bifogade filer tar du bort alla HTML från e-postmeddelandet och skapar blobar i lagringsbehållaren för e-postmeddelandet och de bifogade filerna.

> [!NOTE]
> Logikappen behöver inte göra något för grenen **Om falskt** när ett e-postmeddelande inte innehåller bilagor. Som en extrauppgift efter självstudien kan du lägga till en lämplig åtgärd du vill vidta för grenen **Om falskt**.

## <a name="call-the-removehtmlfunction"></a>Anropa RemoveHTMLFunction

1. Välj **Logic App Designer** på logikappmenyn. I grenen **If true** (Om sant) väljer du **Add an action** (Lägg till en åtgärd).

2. Sök efter ”azure functions” och välj den här åtgärden: **Azure Functions – Välj en Azure-funktion**

   ![Välj åtgärd för ”Azure Functions – Välj en Azure-funktion”](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Välj funktionsappen som du skapade tidigare: **CleanTextFunctionApp**

   ![Välj Azure-funktionsapp](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Välj nu din funktion: **RemoveHTMLFunction**

   ![Välj Azure-funktion](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Byt namn på funktionsformen med den här beskrivningen: ```Call RemoveHTMLFunction to clean email body``` 

6. I funktionsformen anger du indata som ska bearbetas för din funktion. Ange e-postmeddelandets brödtext som visas och beskrivs här:

   ![Ange begärandetexten för funktionen som förväntat](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. Under **Begärandetext** skriver du: 
   
      ```{ "emailBody": ``` 

      Tills du är klar med den här posten i nästa steg visas ett felmeddelande om ogiltigt JSON.
      När du testade funktionen tidigare använde angivna indata för funktionen JavaScript Object Notation (JSON). 
      Så begärandetexten måste använda samma format. 

   2. Välj fältet **Brödtext** under **När ett nytt e-postmeddelande kommer** i listan med dynamiskt innehåll eller i parameterlistan.
   Efter fältet **Brödtext**lägger du till en avslutande klammerparentes: ```}```

      ![Ange begärandetexten som ska skickas till funktionen](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      I logikappens definition visas posten i det här formatet:

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Spara din logikapp.

Lägg sedan till en åtgärd som skapar en blob i lagringsbehållaren för att spara e-postmeddelandets brödtext.

## <a name="create-blob-for-email-body"></a>Skapa blob för e-postmeddelandets brödtext

1. Under Azures funktionsform väljer du **Lägga till en åtgärd**. 

2. Under **Välj en åtgärd** söker du efter ”blob” och väljer den här åtgärden: **Azure Blob Storage – Skapa blob**

   ![Lägga till åtgärd för att skapa blob för e-postmeddelandets brödtext](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Om du inte har någon anslutning till ett Azure-lagringskonto skapar du en anslutning till ditt lagringskonto med inställningarna som visas som beskrivs här. När du är klar väljer du **Skapa**.

   ![Skapa anslutning till lagringskontot](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Anslutningsnamn** | AttachmentStorageConnection | Ett beskrivande namn för anslutningen | 
   | **Lagringskonto** | attachmentstorageacct | Namnet på lagringskontot som du skapade tidigare för att spara bilagor | 
   |||| 

4. Byt namn på åtgärden **Skapa blob**med den här beskrivningen: ```Create blob for email body```

5. I åtgärden **Skapa blob** anger du den här informationen och väljer parametrarna för att skapa bloben som visas och beskrivs:

   ![Ange blobinformation för postmeddelandets brödtext](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Mappsökväg** | /attachments | Sökvägen till och namnet på behållaren som du skapade tidigare. Du kan även bläddra och välja en behållare. | 
   | **Blobnamn** | Fältet **Från** | Skicka e-postmeddelandets avsändarnamn som blobnamn. Välj fältet **Från** under **När ett nytt e-postmeddelande kommer** i listan med dynamiskt innehåll eller i parameterlistan. | 
   | **Blobinnehåll** | **Innehålls**fält | Skicka in e-postmeddelandets HTML-fria brödtext som blobinnehåll. Från listan med dynamiskt innehåll eller i parameterlistan väljer du **Brödtext** under **Call RemoveHTMLFunction to clean email body** (Anropa RemoveHTMLFunction för att rensa e-postmeddelandets brödtext). |
   |||| 

6. Spara din logikapp. 

### <a name="check-attachment-handling"></a>Kontrollera hanteringen av bifogade filer

Testa nu om logikappen hanterar e-postmeddelanden som du angav:

1. Om din logikapp inte körs redan väljer du **Kör** i designerverktygsfältet.

2. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne innehåller den text du har angett i utlösarens **ämnesfilter**: ```Business Analyst 2 #423501```

   * Ditt e-postmeddelande har minst en bifogad fil. 
   Skapa för tillfället en tom textfil och bifoga den i e-postmeddelandet.

   * Ditt e-postmeddelande har testinnehåll i brödtexten, till exempel: 

     ```
     Testing my logic app
     ```

   Om logikappen inte utlöstes eller kördes trots en lyckad utlösare kan du läsa [informationen om att felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

3. Kontrollera om logikappen sparade e-postmeddelandet i rätt lagringsbehållare. 

   1. I Storage Explorer expanderar du **(Lokal och ansluten)** > 
   **Lagringskonton** > **attachmentstorageacct (extern)** > 
   **Blob Containers** (Blob-behållare) > **bifogade filer**.

   2. Titta i behållaren **attachments** efter e-postmeddelandet. 

      Endast e-postmeddelandet visas nu i behållaren eftersom logikappen inte behandlar bilagor ännu.

      ![Kontrollera Storage Explorer efter sparat e-postmeddelande](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. När du är klar tar du bort e-postmeddelandet i Storage Explorer.

4. För att testa grenen **Om falskt**, som inte gör något just nu, kan du även skicka ett e-postmeddelande som inte uppfyller villkoren.

Lägg sedan till en loop för att bearbeta alla bilagor.

## <a name="process-attachments"></a>Bearbeta bilagor

Den här logikappen använder en **for each**-loop (för varje-loop) för att bearbeta varje bilaga i e-postmeddelandet.

1. Under the **Create blob for email body** (Skapa blob för e-postmeddelandets brödtext) väljer du **… Mer** och sedan kommandot: **Lägg till för varje**

   ![Lägg till en "for each"-loop (för varje-loop)](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Byt namn på loopen med den här beskrivningen: ```For each email attachment```

3. Ange nu loopens data för att bearbeta. Klicka i rutan **Välj utdata från föregående steg**. Välj fältet **Bifogade filer** i parameterlistan eller i listan med dynamiskt innehåll. 

   ![Välj ”Bifogade filer”](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Fältet **Bifogade filer** skickar en matris som innehåller alla bifogade filer som finns i ett e-postmeddelande. 
   Loopen **For each** (För varje) upprepar åtgärder för varje objekt som skickats med matrisen.

4. Spara din logikapp.

Lägg sedan till åtgärden som sparar varje bilaga som en blob i lagringsbehållaren **attachments**.

## <a name="create-blobs-for-attachments"></a>Skapa blobar för bifogade filer

1. I loopen **For each** (För varje) väljer du **Lägga till en åtgärd** så du kan ange uppgiften som ska utföras på varje bifogad fil som hittats.

   ![Lägga till åtgärd i loop](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. Under **Välj en åtgärd** söker du efter ”blob” och väljer den här åtgärden: **Azure Blob Storage – Skapa blob**

   ![Lägga till åtgärd för att skapa blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Byt namn på åtgärden **Skapa blob 2**med den här beskrivningen: ```Create blob for each email attachment```

4. I åtgärden **Create blob for each email attachment** (Skapa blob för varje e-postbilaga) anger du den här informationen och väljer parametrarna för att skapa bloben som visas och beskrivs:

   ![Ange blob-information](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Mappsökväg** | /attachments | Sökvägen till och namnet på behållaren som du skapade tidigare. Du kan även bläddra till och välja en behållare. | 
   | **Blobnamn** | Fältet **Namn** | Välj ett **namn** för att skicka in bilagans namn för blobnamnet på parameterlistan eller i listan med dynamiskt innehåll. | 
   | **Blobinnehåll** | **Innehålls**fält | Välj **Innehåll** för att skicka in bilagans innehållsnamn för blobinnehållet på parameterlistan eller i listan med dynamiskt innehåll. |
   |||| 

5. Spara din logikapp. 

### <a name="check-attachment-handling"></a>Kontrollera hanteringen av bifogade filer

Testa nu om logikappen hanterar bilagor som du angav:

1. Om din logikapp inte körs redan väljer du **Kör** i designerverktygsfältet.

2. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne innehåller den text du har angett i utlösarens **ämnesfilter**: ```Business Analyst 2 #423501```

   * Ditt e-postmeddelande har minst två bifogade filer. 
   Skapa för tillfället två tomma textfiler och bifoga dem i e-postmeddelandet.

   Om logikappen inte utlöstes eller kördes trots en lyckad utlösare kan du läsa [informationen om att felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

3. Kontrollera om logikappen sparade e-postmeddelandet och bilagorna i rätt lagringsbehållare. 

   1. I Storage Explorer expanderar du **(Lokal och ansluten)** > 
   **Lagringskonton** > **attachmentstorageacct (extern)** > 
   **Blob Containers** (Blob-behållare) > **bifogade filer**.

   2. Kontrollera både e-postmeddelandet och bilagorna finns i behållaren **attachments**.

      ![Leta efter sparad e-post och bilagor](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. När du är klar tar du bort e-postmeddelandet och bilagorna i Storage Explorer.

Lägg sedan till en åtgärd så att logikappen skickar e-post för att granska de bifogade filerna.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

1. I grenen **if true** (om sant) under loopen **For each email attachment** (För varje e-postbilaga) väljer du **Lägga till en åtgärd**. 

   ![Lägga till åtgärd under ”for each”-loop (för varje-loop)](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. Under **Välj en åtgärd** söker du efter ”skicka e-post” och väljer sedan åtgärden ”skicka e-post” för den e-postprovider du önskar. Om du vill filtrera åtgärdslistan till en specifik tjänst kan du välja anslutningsappen först under **Anslutningar**.

   ![Välj ”Skicka e-post”-åtgärden för din e-postprovider](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du Outlook.com. 

3. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så Logic Apps skapar en anslutning till ditt e-postkonto.

4. Byt namn på åtgärden **Skicka ett e-postmeddelande** med den här beskrivningen: ```Send email for review```

5. Ange informationen för åtgärden och välj fälten du vill ta med i e-postmeddelandet som det visas och beskrivs. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.  

   Om du exempelvis arbetar med listan över dynamiskt innehåll:

   ![Skicka e-postavisering](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Om du inte hittar ett förväntat fält i listan väljer du **See more** (Visa fler) bredvid **När ett nytt e-postmeddelande kommer** i listan över dynamiskt innehåll eller i slutet av parameterlistan.

   | Inställning | Värde | Anteckningar | 
   | ------- | ----- | ----- | 
   | **Till** | <*mottagarens-e-postadress*> | I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne**  | ```ASAP - Review applicant for position: ``` **Ämne** | E-postämnet du vill ha. Välj fältet **Ämne** **När ett nytt e-postmeddelande kommer** från parameterlistan eller i listan med dynamiskt innehåll. | 
   | **Brödtext** | ```Please review new applicant:``` <p>```Applicant name: ``` **Från** <p>```Application file location: ``` **Sökväg** <p>```Application email content: ``` **Brödtext** | Innehållet i e-postmeddelandets brödtext. Välj dessa fält från parameterlistan eller i listan med dynamiskt innehåll: <p>- Fältet **Från** under **När ett nytt e-postmeddelande kommer** </br>- Fältet **Sökväg** under **Skapa blob för e-postmeddelandets brödtext** </br>- Fältet **Brödtext** under **Call RemoveHTMLFunction to clean email body** (Anropa RemoveHTMLFunction för att rensa e-postmeddelandets brödtext) | 
   |||| 

   Om du råkar välja ett fält som innehåller en matris, som **Innehåll**, som är en matris som innehåller bifogade filer, lägger designerprogrammet automatiskt till en "For each"-loop (För varje) omkring åtgärden som refererar till fältet. 
   På så sätt kan din logikappsåtgärd utförs på varje element i matrisen. 
   För att ta bort loopen ska du ta bort matrisens fält, ta bort den refererande åtgärden utanför loopen, välja ellipserna (**...**) på loopens namnlist och välja **Ta bort**.
     
6. Spara din logikapp. 

Nu testar du logikappen, som ser ut som i det här exemplet:

![Färdig logikapp](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne innehåller den text du har angett i utlösarens **ämnesfilter**: ```Business Analyst 2 #423501```

   * E-postmeddelandet innehåller en eller flera bilagor. 
   Du kan återanvända en tom textfil från föregående test. 
   Bifoga en återstartsfil för ett mer realistiskt scenario.

   * I e-postmeddelandet finns den här texten som du kan kopiera och klistra in:

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. Kör logikappen. Om det lyckas skickar din logikapp ett e-postmeddelande som ser ut som i det här exemplet:

   ![E-postmeddelande som skickats av logikapp](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Om du inte får e-post kan du titta i mappen Skräppost. 
   Ditt skräppostfilter kan dirigera om dessa typer av e-post. 
   Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Grattis! Nu har du skapat en och kört en logikapp som automatiserar uppgifter i olika Azure-tjänster och anropar anpassad kod.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när de inte längre behövs. På Azures huvudmeny går du till **Resursgrupper** och väljer resursgruppen för logikappen. Välj **Ta bort resursgrupp**. Ange resursgruppens namn som bekräftelse och välj **Ta bort**.

![Ta bort resursgrupp för logikapp](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du skapat en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions. Lär dig nu mer om andra anslutningsappar som du kan använda för att skapa logikappar.

> [!div class="nextstepaction"]
> [Läs mer om anslutningsappar för Logic Apps](../connectors/apis-list.md)
