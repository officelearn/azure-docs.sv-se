---
title: "Skapa arbetsflöden för att bearbeta e-postmeddelanden och bifogade filer - Azure Logic Apps | Microsoft Docs"
description: "Den här kursen visar hur du skapar automatiska arbetsflöden för bearbetning av e-postmeddelanden och bifogade filer med Azure Logikappar, Azure Storage och Azure Functions"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 210731ce2e792452650b7a92cfc542c78a0e8014
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Behandla e-postmeddelanden och bifogade filer med en logikapp

Med Azure Logikappar kan du automatisera arbetsflöden och integrera data på Azure-tjänster, Microsoft-tjänster, andra programvara som en tjänst (SaaS)-appar och lokalt system. Den här kursen visar hur du kan skapa en [logikapp](../logic-apps/logic-apps-overview.md) som hanterar inkommande e-post och bifogade filer. Den här logiken app processer innehållet, sparar innehållet till Azure-lagring och skickar meddelanden för granskning av innehållet. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ställ in [Azure storage](../storage/common/storage-introduction.md) och Lagringsutforskaren för att kontrollera sparas e-postmeddelanden och bifogade filer.
> * Skapa en [Azure funktionen](../azure-functions/functions-overview.md) som tar bort HTML från e-postmeddelanden. Den här självstudiekursen innehåller kod som du kan använda för den här funktionen.
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar e-postmeddelanden för bifogade filer.
> * Lägg till ett villkor som kontrollerar om e-postmeddelanden har bifogade filer.
> * Lägg till en åtgärd som anropar funktionen Azure när ett e-postmeddelande har bifogade filer.
> * Lägg till en åtgärd som skapar storage-blobbar för e-postmeddelanden och bifogade filer.
> * Lägg till en åtgärd som skickar e-postmeddelanden.

När du är klar logikappen som ser ut som det här arbetsflödet på en hög nivå:

![Övergripande klar logikapp](./media/tutorial-process-email-attachments-workflow/overview.png)

Om du inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a> innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* Ett e-postkonto från en e-provider som stöds av Logikappar, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra leverantörer [granska kopplingarna listas här](https://docs.microsoft.com/connectors/).

  Den här logikapp använder ett Office 365 Outlook-konto. 
  Om du använder en annan e-postkonto allmänna steg förblir densamma, men ditt användargränssnitt visas något annorlunda.

* Hämta och installera den <a href="http://storageexplorer.com/" target="_blank">kostnadsfri Microsoft Azure Lagringsutforskaren</a>. Det här verktyget kan du kontrollera att din lagringsbehållaren är korrekt inställt.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> med dina Azure-autentiseringsuppgifter.

## <a name="set-up-storage-to-save-attachments"></a>Konfigurera lagring för att spara bifogade filer

Du kan spara inkommande e-postmeddelanden och bifogade filer som blobar i en [Azure storage-behållare](../storage/common/storage-introduction.md). 

1. Innan du kan skapa en lagringsbehållare [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) med dessa inställningar:

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | attachmentstorageacct | Namn för ditt lagringskonto | 
   | **Distributionsmodell** | Resurshanterare | Den [distributionsmodell](../azure-resource-manager/resource-manager-deployment-model.md) för att hantera resurs distribution | 
   | **Typ av konto** | Generellt syfte | Den [lagringskontotypen](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Prestanda** | Standard | Den här inställningen anger datatyper som stöds och media för att lagra data. Se [typer av lagringskonton](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Replikering** | Lokalt redundant lagring (LRS) | Den här inställningen anger hur dina data kopieras, lagras, hanteras och synkroniseras. Se [replikering](../storage/common/storage-introduction.md#replication). | 
   | **Säker överföring krävs** | Disabled | Den här inställningen anger den säkerhet som krävs för begäranden från anslutningar. Se [kräver säker överföring](../storage/common/storage-require-secure-transfer.md). | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Namn för din Azure-prenumeration | 
   | **Resursgrupp** | LA-Tutorial-RG | Namnet på den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) används för att organisera och hantera relaterade resurser. <p>**Obs:** en resursgrupp som finns i en viss region. Även om objekt som den här kursen inte kanske är tillgänglig i alla regioner, kan du försöka använda samma region när det är möjligt. | 
   | **Plats** | Östra USA 2 | Regionen där att lagra information om ditt lagringskonto | 
   | **Konfigurera virtuella nätverk** | Disabled | Den här självstudiekursen hålla den **inaktiverad** inställningen. | 
   |||| 

   Du kan också använda [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) eller [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. När Azure distribuerar ditt lagringskonto, hämta åtkomstnyckeln för ditt lagringskonto:

   1. I ditt lager konto menyn under **inställningar**, Välj **åtkomstnycklar**. 
   2. Hitta **key1** under **standard nycklar** och namnet på ditt lagringskonto.

      ![Kopiera och spara lagringskontonamn och nyckel](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Du kan också använda [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) eller [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Skapa en lagringsbehållare för e-postbilagor.
   
   1. På storage-konto-menyn på den **översikt** fönstret Välj **Blobbar** under **Services**, Välj **+ behållare**.

   2. Ange ”bilagor” behållarens namn. Under **offentliga åtkomstnivå**väljer **behållare (anonym läsbehörighet för behållare och blobbar)**, och välj **OK**.

   Du kan också använda [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), eller [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   När du är klar hittar din lagringsbehållaren i ditt lagringskonto här i Azure-portalen:

   ![Klar lagringsbehållare](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

Anslut sedan Lagringsutforskaren ditt lagringskonto.

## <a name="set-up-storage-explorer"></a>Ställ in Lagringsutforskaren

Nu kan ansluta Lagringsutforskaren till ditt lagringskonto så att du kan bekräfta att din logikapp korrekt sparar bifogade filer som blobar i storage-behållare.

1. Öppna Lagringsutforskaren för Microsoft Azure. När Lagringsutforskaren uppmanar dig för en anslutning till Azure storage, välja **använder ett lagringskontonamn och nyckel** > **nästa**.
Om inga uppmanas att välja **Lägg till konto** explorer-verktygsfältet.

2. Under **bifoga med hjälp av namn och nyckel**, ange namnet på ditt lagringskonto och åtkomstnyckel som du sparat tidigare. Välj **nästa** > **ansluta**.

3. Kontrollera att ditt lagringskonto och behållaren visas korrekt i Lagringsutforskaren:

   1. Under **Explorer**, expandera **(lokala och bifogad)** > 
    **Lagringskonton** > **attachmentstorageaccount** > 
    **Blob-behållare**.

   2. Bekräfta att behållaren ”bilagor” nu visas. 
   Exempel:

      ![Lagringsutforskaren - bekräfta lagringsbehållare](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Skapa sedan en [Azure funktionen](../azure-functions/functions-overview.md) som tar bort HTML från inkommande e-post.

## <a name="create-a-function-to-clean-html"></a>Skapa en funktion för att rensa HTML

Nu kan du använda kodfragmentet som tillhandahålls av de här stegen för att skapa en Azure-funktion som tar bort HTML från varje inkommande e-post. På så sätt kan innehållet i e-post är tydligare och lättare att processen. Sedan kan du anropa den här funktionen från din logikapp.

1. Innan du kan skapa en funktion, [skapa en funktionsapp](../azure-functions/functions-create-function-app-portal.md) med dessa inställningar:

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Appens namn** | CleanTextFunctionApp | Ett globalt unikt och beskrivande namn för din funktionsapp | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Samma Azure-prenumerationen som du tidigare använt | 
   | **Resursgrupp** | LA-Tutorial-RG | Samma Azure resursgrupp som du tidigare använt | 
   | **Värd för planen** | Förbrukningsplan | Den här inställningen avgör hur du allokerar och skala resurser, till exempel datorkraft för att köra funktionen appen. Se [värd planer jämförelse](../azure-functions/functions-scale.md). | 
   | **Plats** | Östra USA 2 | Samma region som du tidigare använt | 
   | **Storage** | cleantextfunctionstorageacct | Skapa ett lagringskonto för din funktionsapp. Använd endast gemener och siffror. <p>**Obs:** det här lagringskontot innehåller funktionen-appar och skiljer sig från din tidigare skapade lagringskontot för e-postbilagor. | 
   | **Application Insights** | Av | Aktiverar övervakning av program med [Programinsikter](../application-insights/app-insights-overview.md), men den här kursen behålla den **av** inställningen. | 
   |||| 

   Om funktionen appen inte automatiskt öppnar efter distributionen kan hitta din app i den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>. Välj på Azure Huvudmeny, **Apptjänster**, och välj appen funktion.

   ![Skapade funktionsapp](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Om **Apptjänster** inte visas på menyn Azure, gå till **fler tjänster** i stället. I sökrutan, hitta och välja **funktionen appar**. Mer information finns i [skapa din funktion](../azure-functions/functions-create-first-azure-function.md).

   Du kan också använda [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md), eller [PowerShell och Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

2. Under **funktionen appar**, expandera **CleanTextFunctionApp**, och välj **funktioner**. Välj verktygsfältet funktioner **+ nya funktionen**.

   ![Skapa ny funktion](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. Under **välja en mall för nedan eller gå till Snabbstart**, Välj den **HttpTrigger - C#** funktionen mallen.

   ![Välj funktionen mall](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. Under **namnge din funktion**, ange ```RemoveHTMLFunction```. Under **HTTP-utlösaren** > **åtkomstnivå**, behåller du standardvärdet **funktionen** värdet och välj **skapa**.

   ![Ge funktionen ett namn](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. När du öppnar Redigeraren, ersätter du mallkoden med den här koden tar bort HTML och returnerar resultaten till anroparen:

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

6. När du är klar väljer **spara**. Om du vill testa funktionen väljer **testa** under på pilen (**<**) längst redigerarens högra kant. 

   ![Öppna fönstret ”Test”](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. I den **Test** rutan under **brödtext i begäran**, anger du den här raden och väljer **kör**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Testa din funktion](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   Den **utdata** fönstret visas resultatet från funktionen:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Skapa din logikapp när du har kontrollerat att funktionen fungerar. Även om den här kursen visar hur du skapar en funktion som tar bort HTML från e-postmeddelanden, Logic Apps har också en **HTML till Text** koppling.

## <a name="create-your-logic-app"></a>Skapa din logikapp

1. Välj på Azure Huvudmeny, **ny** > **Enterprise Integration** > **Logikapp**.

   ![Skapa en logikapp](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. Under **skapa logikapp**, ger den här informationen om din logikapp som visas och beskrivs. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Ange information om logik appen](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | LA-ProcessAttachment | Namn för din logikapp | 
   | **Prenumeration** | <*your-Azure-subscription-name*> | Samma Azure-prenumerationen som du tidigare använt | 
   | **Resursgrupp** | LA-Tutorial-RG | Samma Azure resursgrupp som du tidigare använt |
   | **Plats** | Östra USA 2 | Samma region som du tidigare använt | 
   | **Log Analytics** | Av | Den här självstudiekursen hålla den **av** inställningen. | 
   |||| 

3. När Azure distribuerar appen, Designer för Logic Apps öppnas och visar en sida med en video introduktion och mallar för vanliga logik app mönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Lägg till en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden med bifogade filer. Varje logikapp måste börja med en utlösare som utlöses när en viss händelse inträffar eller när nya data uppfyller ett visst villkor. Mer information finns i [skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Övervaka inkommande e-post

1. Ange ”när e-postmeddelandet” i sökrutan på designern. Välj den här utlösaren för din e-postleverantör:  **< *din e-post providern*> – när en ny e-postmeddelandet**, till exempel:

   ![Välj den här utlösaren för e-provider: ”när en ny e-postmeddelandet”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du Outlook.com. 

2. Om du tillfrågas om autentiseringsuppgifter, logga in på ditt e-postkonto så att Logic Apps kan ansluta till ditt e-postkonto.

3. Nu ange villkor som utlösaren använder för att filtrera ny e-post.

   1. Ange mappen, intervall och frekvens för att kontrollera e-postmeddelanden.

      ![Ange mappen, intervall och frekvens för att kontrollera e-post.](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Inställning | Värde | Beskrivning | 
      | ------- | ----- | ----------- | 
      | **Mappen** | Inkorg | E-mappen för att kontrollera | 
      | **Intervall** | 1 | Antalet intervall som ska förflyta mellan kontroller | 
      | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontrollerar | 
      |  |  |  | 
  
   2. Välj **visa avancerade alternativ** och anger dessa inställningar:

      | Inställning | Värde | Beskrivning | 
      | ------- | ----- | ----------- | 
      | **Har bifogad fil** | Ja | Hämta endast e-postmeddelanden med bifogade filer. <p>**Obs:** utlösaren inte ta bort e-post från ditt konto, kontrollerar endast nya meddelanden och bearbetning av endast e-postmeddelanden som matchar filtret ämne. | 
      | **Inkludera bifogade filer** | Ja | Hämta bilagor som indata för arbetsflödet, i stället för att bara söka efter bifogade filer. | 
      | **Ämne Filter** | ```Business Analyst 2 #423501``` | Texten som ska söka efter i ämnet för e-post | 
      |  |  |  | 

4. Om du vill dölja utlösarens information för tillfället, klicka i utlösarens namnlist.

   ![Dölj form för att dölja detaljer](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

   Din logikapp har nu live men inte göra någonting annat Kontrollera din e-post. 
   Sedan lägger till ett villkor som specificerar kriterier för att fortsätta arbetsflödet.

## <a name="check-for-attachments"></a>Sök efter bifogade filer

1. Välj under utlösare, **+ nytt steg** > **Lägg till ett villkor**.

   När villkorsformen visas som standard listan parametrar eller dynamiska innehållslistan visas och visar alla parametrar från föregående steg som du kan inkludera som arbetsflödesindata. 
   Din webbläsarbredd anger vilken lista visas.

2. Byt namn på villkoret med en bättre beskrivning.

   1. Välj på det villkoret namnlisten **ellipserna** (**...** ) knappen > **Byt namn på**.

      Till exempel om webbläsaren visas i smala vy:

      ![Byt namn på villkor](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Om din webbläsare är bred vy och dynamiska innehållslistan blockerar åtkomsten till på puknappen, stänger du listan genom att välja **lägga till dynamiskt innehåll** i villkoret. 
      
      ![Stäng dynamisk innehåll lista](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Byt namn på villkoret med den här beskrivningen:```If email has attachments and key subject phrase```

3. Beskriv villkoret genom att tillhandahålla ett uttryck. 

   1. I villkorsformen, Välj **redigera i Avancerat läge**.

      ![Redigera villkor i Avancerat läge](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. I textrutan anger du det här uttrycket:

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Det här uttrycket jämför den **harbifogadfil** egenskapsvärde från brödtexten utlösare som är e-postmeddelandet i den här självstudien med booleska objektet ```True```. 
      Om båda värdena är lika e-postmeddelandet har minst en bifogad fil, villkor-överför och arbetsflödet fortsätter.

      Villkoret nu ser ut som i det här exemplet:

      ![Villkorsuttrycket](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Välj **redigera i grundläggande läget**. Uttrycket nu löser som visas här:

      ![Matcha uttrycket](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > För att manuellt skapa ett uttryck, måste du grundläggande läge och har den dynamiska listan Öppna så att du kan arbeta med expression-verktyg. Under **uttryck**, kan du välja funktioner. Under **dynamiskt innehåll**, du kan välja Parameterfält som ska användas i dessa funktioner.
      > Den här kursen visar hur du manuellt skapar uttryck senare.

4. Spara din logikapp.

### <a name="test-your-condition"></a>Testa dina villkor

Nu testa om villkoret fungerar korrekt:

1. Om din logikapp inte körs redan, Välj **kör** i verktygsfältet designer.

   Det här steget startar manuellt logikappen utan att behöva vänta tills skickar din angivet intervall. 
   Men händer ingenting tills test e-postmeddelandet i Inkorgen. 

2. Skicka dig ett e-postmeddelande som uppfyller kriterierna:

   * Din e-postmeddelandets ämne innehåller den text som du angav i utlösarens **ämne filter**:```Business Analyst 2 #423501```

   * Din e-post har en bifogad fil. 
   Just nu skapa en tom textfil och bifoga den här filen till din e-post.

   Din logikapp kontrollerar för bifogade filer och ämnestexten angivna för e-postmeddelandet.
   Om villkoret skickar utlösaren utlöses och orsakar Logic Apps-motorn att skapa en logik app-instansen och starta arbetsflödet. 

3. Om du vill kontrollera att åtgärden utlösts och logikappen, har körts på menyn logiken i appen väljer **översikt**.

   ![Utlösare och kör historiken](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Om din logikapp inte utlösa eller kör trots en lyckad utlösare, se [felsöka din logikapp](../logic-apps/logic-apps-diagnosing-failures.md).

Därefter definiera åtgärder som vidtas för den **om värdet är true** grenen. För att spara e-postmeddelandet tillsammans med eventuella bifogade filer, ta bort alla HTML från i e-postmeddelandet och skapa BLOB storage-behållare för e-post och bifogade filer.

> [!NOTE]
> Din logikapp behöver inte göra något den **om falskt** Förgrena när ett e-postmeddelande saknar bifogade filer. Som en rätta Övning när du har slutfört den här självstudiekursen kommer du kan lägga till lämpliga åtgärder som du vill ta den **om falskt** grenen.

## <a name="call-the-removehtmlfunction"></a>Anropa RemoveHTMLFunction

1. Välj på menyn logik app **logik App Designer**. I den **om värdet är true** Förgrena, Välj **lägga till en åtgärd**.

2. Sök efter ”azure functions” och markera den här åtgärden: **Azure Functions – Välj en Azure-funktion**

   ![Välj åtgärd för ”Azure Functions - Välj en Azure-funktion”](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Välj din tidigare skapade funktionsapp: **CleanTextFunctionApp**

   ![Välj en funktionsapp i Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Nu välja din funktion: **RemoveHTMLFunction**

   ![Välj din Azure-funktion](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Byt namn på din funktionsform med beskrivningen:```Call RemoveHTMLFunction to clean email body``` 

6. Ange indata för att funktionen ska bearbeta i formen funktion. Ange e-postmeddelandets brödtext som visas och beskrivs här:

   ![Ange begärandetexten för funktionen som förväntat](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. Under **brödtext i begäran**, ange den här texten: 
   
      ```{ "emailBody": ``` 

      Tills du är klar med den här posten i nästa steg, visas ett felmeddelande om ogiltigt JSON.
      När du tidigare har testat den här funktionen används indata som angetts för den här funktionen JavaScript Object Notation (JSON). 
      Begärandetexten måste därför använda samma format för. 

   2. Markera parameterlistan eller dynamiska innehållslistan den **brödtext** fältet under **när en ny e-postmeddelandet**.
   Efter den **brödtext** fältet, lägga till den avslutande klammerparentesen:```}```

      ![Ange begärandetexten för överföring till funktionen](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      I logik app definition visas den här posten i det här formatet:

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Spara din logikapp.

Sedan lägger till en åtgärd som skapar en blobb i storage-behållare för att spara e-postmeddelandets brödtext.

## <a name="create-blob-for-email-body"></a>Skapa blob för e-postmeddelandets brödtext

1. Välj under formen Azure funktionen **lägga till en åtgärd**. 

2. Under **välja en åtgärd**, söka efter ”blob” och väljer den här åtgärden: **Azure Blob Storage – skapa blob**

   ![Lägga till åtgärden för att skapa blob för e-postmeddelandets brödtext](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Om du inte har en anslutning till ett Azure storage-konto, skapa en anslutning till ditt lagringskonto med de här inställningarna som visas som beskrivs här. När du är klar väljer **skapa**.

   ![Skapa anslutning till lagringskontot](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Anslutningsnamn** | AttachmentStorageConnection | Ett beskrivande namn för anslutningen | 
   | **Storage-konto** | attachmentstorageacct | Namnet för lagringskontot som du skapade tidigare för att spara bifogade filer | 
   |||| 

4. Byt namn på den **skapa blob** åtgärden med den här beskrivningen:```Create blob for email body```

5. I den **skapa blob** åtgärd, ange den här informationen och välj dessa parametrar för att skapa blob som visas och beskrivs:

   ![Ange information om blob för e-postmeddelandets brödtext](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Mappsökväg** | /Attachments | Sökvägen och namnet för behållaren som du skapade tidigare. Du kan också bläddra och välj en behållare. | 
   | **Blobbnamnet** | **Från** fält | Skicka in avsändarens e-namn som blobbnamnet på. Markera parameterlista eller dynamiska innehållslistan **från** under **när en ny e-postmeddelandet**. | 
   | **BLOB-innehåll** | **Innehåll** fält | Skicka in utan HTML-e-postmeddelandets brödtext som blob-innehåll. Markera parameterlista eller dynamiska innehållslistan **brödtext** under **anropa RemoveHTMLFunction att rensa e-postmeddelandets brödtext**. |
   |||| 

6. Spara din logikapp. 

### <a name="check-attachment-handling"></a>Kontrollera hanteringen av bifogade filer

Nu testa om din logikapp hanterar e-postmeddelanden på sätt som du angav:

1. Om din logikapp inte körs redan, Välj **kör** i verktygsfältet designer.

2. Skicka dig ett e-postmeddelande som uppfyller kriterierna:

   * Din e-postmeddelandets ämne innehåller den text som du angav i utlösarens **ämne filter**:```Business Analyst 2 #423501```

   * Din e-post har minst en bifogad fil. 
   Just nu skapa en tom textfil och bifoga den här filen till din e-post.

   * Din e-post har Testinnehåll i själva brödtexten, till exempel: 

     ```
     Testing my logic app
     ```

   Om din logikapp inte utlösa eller kör trots en lyckad utlösare, se [felsöka din logikapp](../logic-apps/logic-apps-diagnosing-failures.md).

3. Kontrollera att din logikapp sparas e-postmeddelandet till lagringsbehållaren korrekta. 

   1. I Lagringsutforskaren, expanderar **(lokala och bifogad)** > 
    **Lagringskonton** > **attachmentstorageacct (externa)** > 
    **Blobbbehållare** > **bilagor**.

   2. Kontrollera den **bilagor** behållare för e-postmeddelandet. 

      Endast e-postmeddelandet visas nu i behållaren eftersom logikappen inte behandla bilagor ännu.

      ![Kontrollera Lagringsutforskaren för sparade e-post](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. När du är klar, ta bort e-post i Lagringsutforskaren.

4. Du kan också testa den **om värdet är FALSKT** gren, vilka har inget just nu, kan du skicka ett e-postmeddelande som inte uppfyller kriterierna.

Lägg till en slinga för att bearbeta e-postbilagor.

## <a name="process-attachments"></a>Processen för bifogade filer

Den här logikapp använder en **för varje** slinga för att bearbeta bifogad i e-postmeddelandet.

1. Under den **skapa blob för e-postmeddelandets brödtext** form, Välj **... Flera**, och välj det här kommandot: **Lägg till ett för varje**

   ![Lägg till ”för var och en” loop](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Byt namn på din loopen med beskrivningen:```For each email attachment```

3. Nu ska du ange data för loop att bearbeta. Klicka i den **Välj utdata från föregående steg** rutan. Markera parameterlistan eller dynamiska innehållslistan **bilagor**. 

   ![Välj ”bilagor”](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Den **bilagor** fältet skickar en matris som innehåller alla bifogade filer i ett e-postmeddelande. 
   Den **för varje** loop upprepas åtgärder på varje objekt som har skickats in med matrisen.

4. Spara din logikapp.

Lägg sedan till den åtgärd som sparar bifogad fil som en blobb i din **bilagor** lagringsbehållaren.

## <a name="create-blobs-for-attachments"></a>Skapa BLOB för bifogade filer

1. I den **för varje** slinga, Välj **lägga till en åtgärd** så att du kan ange aktiviteten för att utföra på bifogad fil hittades.

   ![Lägg till åtgärd ska upprepas](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. Under **välja en åtgärd**, söka efter ”blob” och välj sedan den här åtgärden: **Azure Blob Storage – skapa blob**

   ![Lägga till åtgärden för att skapa blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Byt namn på den **skapa blob 2** åtgärden med den här beskrivningen:```Create blob for each email attachment```

4. I den **skapa blob för varje e-postmeddelande** åtgärd, ange den här informationen och välj parametrar för att skapa varje blob som visas och beskrivs:

   ![Ange blob-information](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Mappsökväg** | /Attachments | Sökväg och namn för behållaren du skapade tidigare. Du kan även bläddra till och välj en behållare. | 
   | **Blobbnamnet** | **Namnet** fält | Markera parameterlista eller dynamiska innehållslistan **namn** att skicka in namnet på bifogade för blob-namnet. | 
   | **BLOB-innehåll** | **Innehåll** fält | Markera parameterlista eller dynamiska innehållslistan **innehåll** att skicka innehållet för den bifogade filen för blobinnehåll. |
   |||| 

5. Spara din logikapp. 

### <a name="check-attachment-handling"></a>Kontrollera hanteringen av bifogade filer

Sedan testa om din logikapp hanterar bilagor på sätt som du angav:

1. Om din logikapp inte körs redan, Välj **kör** i verktygsfältet designer.

2. Skicka dig ett e-postmeddelande som uppfyller kriterierna:

   * Din e-postmeddelandets ämne innehåller den text som du angav i utlösarens **ämne filter**:```Business Analyst 2 #423501```

   * Din e-post har minst två bifogade filer. 
   Just nu skapa två tomma filer och bifoga filerna till din e-post.

   Om din logikapp inte utlösa eller kör trots en lyckad utlösare, se [felsöka din logikapp](../logic-apps/logic-apps-diagnosing-failures.md).

3. Kontrollera att din logikapp sparas e-post och bifogade filer till lagringsbehållaren korrekta. 

   1. I Lagringsutforskaren, expanderar **(lokala och bifogad)** > 
    **Lagringskonton** > **attachmentstorageacct (externa)** > 
    **Blobbbehållare** > **bilagor**.

   2. Kontrollera den **bilagor** behållare för både den e-posten och bifogade filer.

      ![Sök efter sparade e-post och bifogade filer](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. När du är klar, ta bort e-post och bifogade filer i Lagringsutforskaren.

Sedan lägger till en åtgärd så att din logikapp skickar e-postmeddelande för att granska de bifogade filerna.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

1. I den **om värdet är true** Förgrena under den **för varje e-postmeddelande** slinga, Välj **lägga till en åtgärd**. 

   ![Lägg till åtgärd under ”för var och en” loop](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. Under **välja en åtgärd**, söker du efter ”skicka e-post” och välj åtgärden ”Skicka e-post” för den e-provider som du vill. Om du vill filtrera listan åtgärder för en specifik tjänst, du kan välja kopplingen först **kopplingar**.

   ![Välj ”Skicka e-post” åtgärd för din e-provider](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du Outlook.com. 

3. Om du tillfrågas om autentiseringsuppgifter, logga in på ditt e-postkonto så att Logic Apps skapar en anslutning till ditt e-postkonto.

4. Byt namn på den **skickar ett e-** åtgärden med den här beskrivningen:```Send email for review```

5. Ange information för den här åtgärden och välj de fält som du vill ska ingå i e-post som visas och beskrivs. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.  

   Till exempel om du arbetar med listan över dynamiskt innehåll:

   ![Skicka e-postavisering](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Om du inte hittar en förväntade fält i listan, Välj **se mer** bredvid **när en ny e-postmeddelandet** i listan för dynamiskt innehåll eller i slutet av listan över parametrar.

   | Inställning | Värde | Anteckningar | 
   | ------- | ----- | ----- | 
   | **Till** | <*recipient-email-address*> | I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne**  | ```ASAP - Review applicant for position: ```**Ämne** | Ämne för e-post som du vill ha. Parameterlista eller dynamiskt innehåll listan, Välj den **ämne** fältet under **när en ny e-postmeddelandet**. | 
   | **Brödtext** | ```Please review new applicant:``` <p>```Applicant name: ```**Från** <p>```Application file location: ```**Sökväg** <p>```Application email content: ```**Brödtext** | Innehållet i e-postmeddelandets brödtext. Välj fälten från parameterlista eller dynamisk innehåll lista: <p>- **Från** fältet under **när en ny e-postmeddelandet** </br>- **Sökväg** fältet under **skapa blob för e-postmeddelandets brödtext** </br>- **Brödtext** fältet under **anropa RemoveHTMLFunction att rensa e-postmeddelandets brödtext** | 
   |||| 

   Om du markerar ett fält som innehåller en matris som **innehåll**, vilket är en matris som innehåller bifogade filer, designern lägger automatiskt till en ”för var och en” loop runt den åtgärd som refererar till fältet. 
   På så sätt kan din logikappsåtgärd utförs på varje element i matrisen. 
   Ta bort loopen genom att ta bort fältet för matrisen, flytta åtgärden refererande utanför loopen, Välj ellipserna (**...** ) på Loopens titel och välj **ta bort**.
     
6. Spara din logikapp. 

Sedan testa logikappen som nu ser ut som i det här exemplet:

![Klar logikapp](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka dig ett e-postmeddelande som uppfyller kriterierna:

   * Din e-postmeddelandets ämne innehåller den text som du angav i utlösarens **ämne filter**:```Business Analyst 2 #423501```

   * Din e-post har på en eller flera bilagor. 
   Du kan återanvända en tom textfil från föregående testet. 
   Bifoga en resume-fil för ett mer realistisk scenario.

   * I e-postmeddelandet har den här texten som du kan kopiera och klistra in:

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

2. Kör logikappen. Om det lyckas, skickar din logikapp ett e-postmeddelande som ser ut som i det här exemplet:

   ![E-postmeddelanden som skickas av logikapp](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Om du inte får e-post, kontrollera din e-post skräppost mappen. 
   E-skräppost filtret kan dirigera om dessa typer av e-post. 
   Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Grattis, du har nu skapats och köra en logikapp som automatiserar uppgifter mellan olika Azure-tjänster och anropar anpassad kod.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller din logikapp och relaterade resurser när de inte längre behövs. På Azure Huvudmeny, gå till **resursgrupper**, och markera resursgruppen för din logikapp. Välj **ta bort resursgruppen**. Anger resursgruppens namn som bekräftelse och väljer **ta bort**.

![Ta bort resursgruppen för logik app](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions i den här självstudiekursen. Lär dig mer om andra kopplingar som du kan använda för att skapa logikappar nu.

> [!div class="nextstepaction"]
> [Läs mer om kopplingar för Logic Apps](../connectors/apis-list.md)