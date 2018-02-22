---
title: "Övervaka ändringar av virtuell dator - Azure händelse rutnätet & Logic Apps | Microsoft Docs"
description: "Kontrollera config ändringar i virtuella datorer (VM) med hjälp av Azure händelse rutnätet och Logic Apps"
keywords: "logikappar, händelse rutnät, virtuell dator, VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 11/30/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 3d99dabe778b9b9234db9fe130ba503cd8b57834
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Övervaka ändringar av virtuella datorer med Azure händelse rutnätet och Logic Apps

Du kan starta en automatiserad [logik app arbetsflöde](../logic-apps/logic-apps-overview.md) när specifika händelser inträffar i Azure-resurser eller resurser från tredje part. Dessa resurser kan publicera dessa händelser till en [Azure händelse rutnätet](../event-grid/overview.md). I sin tur händelse rutnätet skickar händelser till prenumeranter som har köer, webhooks, eller [händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) som slutpunkter. Som en prenumerant kan din logikapp vänta på dessa händelser från rutnätet händelse innan du kan köra automatiska arbetsflöden för att utföra uppgifter - utan att du skriva någon kod.

Här är till exempel vissa händelser som utgivare kan skicka till prenumeranter via tjänsten Azure händelse rutnätet:

* Skapa, läsa, uppdatera eller ta bort en resurs. Du kan till exempel övervaka ändringar som kan avgifter på din Azure-prenumeration och påverkar fakturan. 
* Lägg till eller ta bort personer från en Azure-prenumeration.
* Din app utför en viss åtgärd.
* Ett nytt meddelande visas i en kö.

Den här guiden skapar en logikapp som övervakar ändringar till en virtuell dator och skickar e-postmeddelanden om dessa ändringar. När du skapar en logikapp med en händelseprenumeration för en Azure-resurs flöda händelser från den här resursen via ett rutnät för händelsen till logikappen. Kursen vägleder dig genom att skapa den här logikapp:

![Översikt – övervaka virtuella datorn med händelsen rutnätet och logik app](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en logikapp som övervakar händelser från ett rutnät för händelsen.
> * Lägg till ett villkor som specifikt söker efter ändringar av virtuell dator.
> * Skicka e-post när den virtuella datorn ändras.

## <a name="prerequisites"></a>Förutsättningar

* Ett e-postkonto från [valfri e-provider som stöds av Azure Logikappar](../connectors/apis-list.md), till exempel Office 365 Outlook, Outlook.com eller Gmail för att skicka meddelanden. Den här självstudien använder Office 365 Outlook.

* En [virtuella](https://azure.microsoft.com/services/virtual-machines). Om du inte redan gjort det, skapar du en virtuell dator via en [skapa en VM-kursen](https://docs.microsoft.com/azure/virtual-machines/). Att göra den virtuella datorn publicera händelser du [behöver inte göra något annat](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Skapa en logikapp som övervakar händelser från ett rutnät för händelse

Skapa en logikapp och lägga till en händelseutlösare rutnät som övervakar resursgruppen för den virtuella datorn först. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Välj det övre vänstra hörnet i Azure huvudmenyn **skapar du en resurs** > **Enterprise Integration** > **Logikapp**.

   ![Skapa en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Skapa din logikapp med de inställningar som anges i följande tabell:

   ![Tillhandahålla information om logikappar](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *{your-logic-app-name}* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *{your-Azure-subscription}* | Välj samma Azure-prenumerationen för alla tjänster i den här kursen. | 
   | **Resursgrupp** | *{your-Azure-resource-group}* | Välj samma Azure resursgrupp för alla tjänster i den här kursen. | 
   | **Plats** | *{your-Azure-region}* | Välj samma region för alla tjänster i den här kursen. | 
   | | | 

4. När du är klar, Välj **fäst på instrumentpanelen**, och välj **skapa**.

   Du har nu skapat en Azure-resurs för din logikapp. 
   När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE] 
   > När du väljer **fäst på instrumentpanelen**, logikappen öppnas automatiskt i Logic Apps Designer. I annat fall kan du manuellt hitta och öppna logikappen.

5. Nu välja en mall för logik-app. Under **mallar**, Välj **tom Logikapp** så att du kan bygga din logikapp från början.

   ![Välja mall för logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visas nu [ *kopplingar* ](../connectors/apis-list.md) och [ *utlösare* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) som du kan använda för att starta logikapp och även åtgärder som att du kan lägga till efter en utlösare för att utföra uppgifter. En utlösare är en händelse som skapar en logik app-instansen och startar logik app arbetsflödet. 
   Din logikapp måste en utlösare som det första objektet.

6. I sökrutan anger du ”händelse rutnätet” som filter. Välj den här utlösaren: **Azure händelse rutnät - på en resurs-händelse**

   ![Välj den här utlösaren: ”Azure händelse rutnät - på en resurs händelse”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. När du uppmanas logga in Azure händelse rutnät med dina autentiseringsuppgifter för Azure.

   ![Logga in med dina autentiseringsuppgifter för Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Om du har loggat in med ett personligt microsoftkonto som @outlook.com eller @hotmail.com, händelse rutnätet utlösaren kanske inte visas korrekt. Som en tillfällig lösning kan välja [Anslut med tjänstens huvudnamn](../azure-resource-manager/resource-group-create-service-principal-portal.md), eller som en medlem i Azure Active Directory som är kopplad till ditt Azure-prenumeration, till exempel autentisera *användarnamn*@emailoutlook.onmicrosoft.com.

8. Nu prenumerera logikappen publisher händelser. Ange detaljer för din händelseprenumeration som anges i följande tabell:

   ![Ange detaljer för händelseprenumerationen](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Prenumeration** | *{virtual-machine-Azure-subscription}* | Välj den händelseutfärdare Azure-prenumeration. Välj Azure-prenumeration för den virtuella datorn för den här självstudiekursen. | 
   | **Resurstyp** | Microsoft.Resources.resourceGroups | Välj den händelseutfärdare resurstypen. Välj det angivna värdet för den här självstudiekursen så logikappen övervakar enbart resursgrupper. | 
   | **Resursnamn** | *{virtual-machine-resource-group-name}* | Välj utgivarens resursnamnet. Välj namnet på resursgruppen för den virtuella datorn för den här självstudiekursen. | 
   | Valfria inställningar, väljer **visa avancerade alternativ**. | *{finns beskrivningar}* | * **Prefixet Filter**: den här kursen kan lämna inställningen tom. Standardbeteendet matchar alla värden. Du kan dock ange prefix-strängen som ett filter, till exempel en sökväg och en parameter för en viss resurs. <p>* **Suffix Filter**: den här kursen kan lämna inställningen tom. Standardbeteendet matchar alla värden. Du kan dock ange en suffixsträng som ett filter, till exempel ett filnamnstillägg, när du vill att endast specifika filtyper.<p>* **Prenumerationsnamn**: Ange ett unikt namn för din händelseprenumeration. |
   | | | 

   När du är klar kan din händelseutlösare rutnätet se ut det här exemplet:
   
   ![Exempel händelseinformationen rutnätet utlösare](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Spara din logikapp. Välj **Spara** i designerverktygsfältet. För att komprimera och Dölj detaljer för en åtgärd i din logikapp, Välj åtgärdens namnlist.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   När du sparar din logikapp med en rutnätet händelseutlösare skapar Azure automatiskt en händelseprenumeration för din logikapp till den markerade resursen. Så när resursen publicerar en händelse i rutnätet för händelsen, skickar som händelsen rutnätet automatiskt händelsen till din logikapp. Den här händelsen utlöser din logikapp, och sedan skapar och kör en instans av arbetsflödet som du definierar i dessa nästa steg.

Din logikapp har publicerats och lyssnar på händelser från rutnätet händelse, men göra inte något tills du lägger till åtgärder i arbetsflödet. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Lägg till ett villkor som söker efter ändringar av virtuell dator

Om du vill köra arbetsflödet logik app bara när en viss händelse inträffar, lägger du till ett villkor som kontrollerar för den virtuella datorn ”skrivåtgärder”. När det här villkoret är sant, skickar din logikapp du e-post med information om den uppdaterade virtuella datorn.

1. I logik App Designer under rutnätet händelseutlösare väljer **nytt steg** > **Lägg till ett villkor**.

   ![Lägg till ett villkor i din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Logik App Designer lägger till en tom villkor i arbetsflödet, inklusive åtgärd sökvägar till följer baserat om villkoret är SANT eller FALSKT.

   ![Tom villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. I den **villkoret** väljer **redigera i Avancerat läge**.
Ange det här uttrycket:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Villkoret nu ser ut som i det här exemplet:

   ![Tom villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Det här uttrycket kontrollerar händelsen `body` för en `data` objekt där den `operationName` egenskapen är den `Microsoft.Compute/virtualMachines/write` igen. 
   Lär dig mer om [händelse rutnätet Händelseschema](../event-grid/event-schema.md).

3. Om du vill ange en beskrivning för villkoret, Välj den **ellipserna** (**...** ) knappen på villkorsformen och välj sedan **Byt namn på**.

   > [!NOTE] 
   > Senare exemplen i den här kursen finns också beskrivningar stegen i logik app arbetsflödet.

4. Nu välja **redigera i grundläggande läget** så att uttrycket löser automatiskt enligt:

   ![Logik app villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Spara din logikapp.

## <a name="send-email-when-your-virtual-machine-changes"></a>Skicka e-post när den virtuella datorn ändras

Lägg nu till en [ *åtgärd* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) så att du får ett e-postmeddelande när det angivna villkoret är sant.

1. I villkoret **om värdet är true** väljer **lägga till en åtgärd**.

   ![Lägga till åtgärden för när villkoret är SANT](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. I sökrutan anger du ”e-post” som filter. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Exempel: 

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com. 
   * För Gmail-konton väljer du Gmail-anslutningsappen. 

   Vi kommer att fortsätta med anslutingsappen Office 365 Outlook. 
   Om du använder en annan leverantör förblir stegen desamma, men användargränssnittet kan vara annorlunda. 

   ![Välj åtgärden ”Skicka e-post”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Om du inte redan har en anslutning för e-post-providern, logga in på ditt e-postkonto när du uppmanas för autentisering.

4. Ange information för e-post som anges i följande tabell:

   ![Tomt e-åtgärd](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Välj i fält som är tillgängliga i arbetsflödet, klicka i en redigeringsruta så som den **dynamiskt innehåll** lista öppnas, eller välj **lägga till dynamiskt innehåll**. Fler fält, Välj **se mer** för varje avsnitt i listan. Stäng den **dynamiskt innehåll** Välj **lägga till dynamiskt innehåll**.

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Till** | *{recipient-email-address}* |Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | Resursen uppdateras: **ämne**| Ange innehållet för e-postmeddelandets ämne. Ange den föreslagna texten för den här kursen och väljer du händelsen **ämne** fältet. Här är innehåller din e-postmeddelandets ämne namnet på för den uppdaterade resursen (virtuell dator). | 
   | **Brödtext** | Resursgrupp: **avsnittet** <p>Händelsetyp: **händelsetyp**<p>Händelse-ID: **ID**<p>Tid: **Händelsetid** | Ange innehållet för e-postmeddelandets ämne. Ange den föreslagna texten för den här kursen och väljer du händelsen **avsnittet**, **händelsetyp**, **ID**, och **Händelsetid** fält så att din e-post innehåller resursgruppens namn, typ, händelse tidsstämpel och händelse-ID för uppdateringen. <p>Tryck på SKIFT + RETUR för att lägga till tomma rader i ditt innehåll. | 
   | | | 

   > [!NOTE] 
   > Om du väljer ett fält som representerar en matris, designern lägger automatiskt till en **för varje** cirkel runt den åtgärd som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

   Din e-åtgärd kan nu se ut det här exemplet:

   ![Välj utdata ska ingå i e-post](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Och klar logikappen kan se ut som i det här exemplet:

   ![Färdig logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Spara din logikapp. För att komprimera och dölja information för varje åtgärd i din logikapp, Välj åtgärdens namnlist.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   Din logikapp nu är aktiv, men väntar på ändringar av den virtuella datorn innan du börjar. 
   Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="test-your-logic-app-workflow"></a>Testa arbetsflödet logik app

1. Uppdatera den virtuella datorn för att kontrollera att din logikapp är får de angivna händelserna. 

   Du kan till exempel ändra storlek den virtuella datorn i Azure-portalen eller [ändra storlek på den virtuella datorn med Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Du bör få ett e-postmeddelande efter en liten stund. Exempel:

   ![E-post om uppdateringen av den virtuella datorn](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Om du vill granska körs och utlösa historik för din logikapp på logiken app-menyn väljer **översikt**. Om du vill visa mer information om en körning väljer du körningens rad.

   ![Logikapp körs historik](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Expandera det steg som du vill granska för att visa indata och utdata för varje steg. Den här informationen kan hjälpa dig att diagnostisera och felsöka problem i din logikapp.
 
   ![Kör historikinformation logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Grattis, du skapat och kör en logikapp som övervakar resurs händelser via en händelse rutnätet och e-postmeddelanden när dessa händelser inträffar. Du också lära dig hur enkelt du kan skapa arbetsflöden som automatisera processer och integrera system och molntjänster.

Du kan övervaka andra ändringar i konfigurationen med händelsen rutnät och logikappar, till exempel:

* En virtuell dator hämtar rollbaserad åtkomst (RBAC) behörighet.
* Ändringar har gjorts i en nätverkssäkerhetsgrupp (NSG) för ett nätverksgränssnitt (NIC).
* Diskar för en virtuell dator läggs till eller tas bort.
* En offentlig IP-adress har tilldelats virtuella nätverkskort.

## <a name="clean-up-resources"></a>Rensa resurser

Den här kursen använder resurser och utför åtgärder som avgifter på din Azure-prenumeration. Så när du är klar med guiden och testning, ska du se till att du inaktiverar eller ta bort resurser där du inte vill avgifter.

* Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. På logikappmenyn väljer du **Översikt**. I verktygsfältet väljer du **Inaktivera**.

  ![Stänga av din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

* Om du vill ta bort din logikapp på menyn logiken i appen permanent väljer **översikt**. I verktygsfältet väljer du **Ta bort**. Bekräfta att du vill ta bort din logikapp och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Skapa och dirigera anpassade händelser med händelse rutnätet](../event-grid/custom-event-quickstart.md)
