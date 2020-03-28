---
title: Övervaka ändringar av virtuella datorer – Azure Event Grid & Logic Apps
description: Sök efter ändringar i virtuella datorer med hjälp av Azure Event Grid och Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75982574"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Självstudiekurs: Övervaka ändringar av virtuella datorer med hjälp av Azure Event Grid och Logic Apps

Om du vill övervaka och svara på specifika händelser som inträffar i Azure-resurser eller resurser från tredje part kan du automatisera och köra uppgifter som ett arbetsflöde genom att skapa en [logikapp](../logic-apps/logic-apps-overview.md) som använder minimal kod. Dessa resurser kan publicera händelser i ett [Azure-händelserutnät](../event-grid/overview.md). Händelserutnätet skickar i sin tur händelserna vidare till prenumeranter som har köer, webhooks eller [händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) som slutpunkter. Som prenumerant kan logikappen vänta på att dessa händelser från händelserutnätet körs innan du kör automatiska arbetsflöden för att utföra uppgifter.

Här är exempel på händelser som utgivare kan skicka till prenumeranter via tjänsten Azure Event Grid:

* Händelser som skapar, läser, uppdaterar eller tar bort en resurs. Du kan till exempel övervaka ändringar som kan medföra avgifter i din Azure-prenumeration och som påverkar din faktura.

* Händelser som lägger till eller tar bort en person i en Azure-prenumeration.

* Händelser som är kopplade till en viss åtgärd som utförs av din app.

* Händelser som visar ett nytt meddelande i en kö.

Den här självstudien skapar en logikapp som övervakar ändringar på en virtuell dator och skickar e-postmeddelanden om dessa ändringar. När du skapar en logikapp med en händelseprenumeration för en Azure-resurs flödar händelser från den resursen via ett rutnät till logikappen. Självstudiekursen beskriver steg för steg hur du skapar den här logikappen:

![Översikt – övervaka virtuella datorer med ett händelserutnät och en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en logikapp som övervakar händelser från ett händelserutnät.
> * Lägga till ett villkor som specifikt söker efter ändringar på en virtuell dator.
> * Skicka e-post när den virtuella datorn ändras.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps för att skicka meddelanden, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/).

  Den här självstudien använder ett Office 365 Outlook-konto. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

* En [virtuell dator](https://azure.microsoft.com/services/virtual-machines) som är ensam i sin egen Azure-resursgrupp. Om du inte redan har gjort det skapar du en virtuell dator via [självstudiekursen Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md). [Du behöver inte göra något mer](../event-grid/overview.md) för att den virtuella datorn ska publicera händelser.

## <a name="create-blank-logic-app"></a>Skapa en tom logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. På Huvudmenyn i Azure väljer du Skapa en > **resursintegrationslogikapp** > **Logic App**. **Create a resource**

   ![Skapa en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Under **Logic App**anger du information om logikappresursen. När du är klar väljer du **Skapa**.

   ![Tillhandahålla information om logikappar](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*logik-app-namn*> | Ange ett unikt namn för logikappen. |
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Välj samma Azure-prenumeration för alla tjänster i den här självstudien. |
   | **Resursgrupp** | Ja | <*Azure-resurs-grupp*> | Azure-resursgruppsnamnet för logikappen, som du kan välja för alla tjänster i den här självstudien. |
   | **Location** | Ja | <*Azure-region*> | Välj samma region för alla tjänster i den här självstudiekursen. |
   |||

1. När Azure har distribuerat din logikapp visar Logic Apps Designer en sida med en introduktionsvideo och vanliga utlösare. Rulla förbi videon och utlösarna.

1. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj logikappmall](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visar nu de [*utlösare*](../logic-apps/logic-apps-overview.md#logic-app-concepts) som du kan använda för att starta logikappen. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps en arbetsflödesinstans som kör din logikapp.

## <a name="add-an-event-grid-trigger"></a>Lägga till en utlösare för händelserutnät

Lägg nu till utlösaren för händelserutnät, som du använder för att övervaka resursgruppen för den virtuella datorn.

1. Ange `event grid` som filter i sökrutan i designern. Välj utlösaren **När en resurshändelse inträffar i** listan utlösare i listan utlösare.

   ![Välj den här utlösaren: "På en resurshändelse"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Logga in på Azure Event Grid med dina Azure-kontouppgifter när du uppmanas att göra det. I listan **Klient,** som visar Azure Active Directory-klienten som är associerad med din Azure-prenumeration, kontrollerar du att rätt klient visas, till exempel:

   ![Logga in med dina autentiseringsuppgifter för Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Om du är inloggad med ett personligt Microsoft-konto som @outlook.com eller @hotmail.com kanske Event Grid-utlösaren inte visas korrekt. Som en lösning väljer du [Anslut med tjänsthuvudnamn](../active-directory/develop/howto-create-service-principal-portal.md)eller autentiserar som medlem i Azure Active Directory som är associerad med din *Azure-prenumeration,*@emailoutlook.onmicrosoft.comtill exempel användarnamn .

1. Prenumerera nu din logikapp på händelser från utgivaren. Ange information om din evenemangsprenumeration enligt beskrivningen i följande tabell, till exempel:

   ![Ange information om händelseprenumeration](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Prenumeration** | Ja | <*händelseutgivare-Azure-prenumeration-namn*> | Välj namnet på den Azure-prenumeration som är associerad med *händelseutgivaren*. För den här självstudien väljer du Azure-prenumerationsnamnet för din virtuella dator. |
   | **Resurstyp** | Ja | <*händelseutgivare-Azure-resurs-typ*> | Välj Azure-resurstyp för händelseutgivaren. Mer information om Azure-resurstyper finns i [Azure-resursleverantörer och -typer](../azure-resource-manager/management/resource-providers-and-types.md). För den här `Microsoft.Resources.ResourceGroups` självstudien väljer du värdet för att övervaka Azure-resursgrupper. |
   | **Resursnamn** |  Ja | <*händelseutgivare-Azure-resurs-namn*> | Välj Azure-resursnamnet för händelseutgivaren. Den här listan varierar beroende på vilken resurstyp du har valt. För den här självstudien väljer du namnet på Azure-resursgruppen som innehåller din virtuella dator. |
   | **Objekt för händelsetyp** |  Inga | <*händelsetyper*> | Välj en eller flera specifika händelsetyper som du vill filtrera och skicka till händelserutnätet. Du kan till exempel lägga till dessa händelsetyper för att identifiera när resurser ändras eller tas bort: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Mer information finns i de här ämnena: <p><p>- [Azure Event Grid-händelseschema för resursgrupper](../event-grid/event-schema-resource-groups.md) <br>- [Förstå händelsefiltrering](../event-grid/event-filtering.md) <br>- [Filtrera händelser för händelserutnät](../event-grid/how-to-filter-events.md) |
   | Om du vill lägga till valfria egenskaper väljer du **Lägg till ny parameter**och väljer önskade egenskaper. | Inga | {se beskrivningar} | * **Prefixfilter:** Lämna den här egenskapen tom för den här självstudien. Standardbeteendet matchar alla värden. Du kan dock ange en prefixsträng som ett filter, till exempel en sökväg och en parameter för en specifik resurs. <p>* **Suffixfilter**: Lämna den här egenskapen tom för den här självstudien. Standardbeteendet matchar alla värden. Du kan dock ange en suffixsträng som ett filter, till exempel ett filnamnstillägg, om du bara vill använda specifika filtyper. <p>* **Prenumerationsnamn:** För den här självstudien kan du ange ett unikt namn för din evenemangsprenumeration. |
   |||

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet . Om du vill dölja och dölja informationen om en åtgärd i logikappen markerar du åtgärdens namnlist.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   När du sparar logikappen med en utlösare för händelserutnät skapar Azure automatiskt en händelseprenumeration för logikappen för den resurs du valt. När resursen sedan publicerar en händelse i händelserutnätet, skickar händelserutnätet automatiskt händelsen till logikappen. Den här händelsen utlöser logikappen och skapar och kör sedan en instans av arbetsflödet som du definierar i följande steg.

Nu är logikappen aktiv och lyssnar på händelser från händelserutnätet, men den gör inget förrän du lägger till åtgärder i arbetsflödet.

## <a name="add-a-condition"></a>Lägg till ett villkor

Om du vill att logikappen bara ska köras när en viss `Microsoft.Compute/virtualMachines/write` händelse eller åtgärd inträffar lägger du till ett villkor som söker efter åtgärden. När det här villkoret är sant skickar logikappen ett e-postmeddelande till dig med information om den uppdaterade virtuella datorn.

1. Välj **Nytt steg**under utlösaren för händelserutnät i Logic App Designer.

   ![Välj "Nytt steg"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Ange `condition` som filter i sökrutan under **Välj en åtgärd.** Välj åtgärden **Villkor** i åtgärdslistan.

   ![Lägg till ett villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic Apps-designern lägger till ett tomt villkor till arbetsflödet, inklusive åtgärdssökvägar som ska följas beroende på om villkoret är sant eller falskt.

   ![Ett tomt villkor visas](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Byt namn på `If a virtual machine in your resource group has changed`villkorsrubriken till . I villkorets namnlist markerar du knappen ellipser (**...**) och väljer **Byt namn**.

   ![Byta namn på villkoret](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Skapa ett villkor som `body` kontrollerar händelsen för ett `data` objekt där egenskapen `operationName` är lika med operationen. `Microsoft.Compute/virtualMachines/write` Lär dig mer om [händelsescheman i Event Grid](../event-grid/event-schema.md).

   1. På den första raden under **And** (och) klickar du i den vänstra rutan. Välj **Uttryck**i listan med dynamiskt innehåll som visas .

      ![Välj "Uttryck" för att öppna uttrycksredigeraren](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. I uttrycksredigeraren anger du det här uttrycket, som returnerar operationsnamnet från utlösaren, och väljer **OK:**

      `triggerBody()?['data']['operationName']`

      Ett exempel:

      ![Ange uttryck för att extrahera operationsnamn](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. I den mellersta rutan behåller du operatorn **is equal to** (är lika med).

   1. I den högra rutan anger du det här värdet, vilket är den specifika åtgärd som du ska övervaka:

      `Microsoft.Compute/virtualMachines/write`

   Ditt färdiga tillstånd ser nu ut så här:

   ![Slutfört villkor som jämför operationen](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Om du växlar från designvyn till kodvyn och tillbaka till designvyn, matchas uttrycket som du angav i villkoret till **data.operationName-token:**

   ![Lösta token i villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Spara din logikapp.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

Lägg nu till en [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) så att du kan få ett e-postmeddelande när det angivna villkoret är sant.

1. I rutan **Om sant** i villkoret väljer du Lägg till **en åtgärd**.

   ![Lägg till en åtgärd för när villkoret är sant](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Ange `send an email` som filter i sökrutan under **Välj en åtgärd.** Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Ett exempel:

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook.

   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com.

   * För Gmail-konton väljer du Gmail-anslutningsappen.

   Den här självstudien fortsätter med Office 365 Outlook-anslutningen. Om du använder en annan leverantör förblir stegen desamma, men användargränssnittet kan se lite annorlunda ut.

   ![Välj åtgärden ”send email” (skicka e-post)](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Om du inte redan är ansluten till din e-postleverantör loggar du in på ditt e-postkonto när du uppmanas att autentisera.

1. Byt namn på åtgärden skicka e-post till den här titeln:`Send email when virtual machine updated`

1. Ange information om e-postmeddelandet enligt följande tabell:

   ![Ange information om e-poståtgärd](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Om du vill välja utdata från föregående steg i arbetsflödet klickar du i en redigeringsruta så att listan med dynamiskt innehåll visas eller väljer **Lägg till dynamiskt innehåll**. Om du vill ha fler resultat väljer du **Visa mer** för varje avsnitt i listan. Om du vill stänga listan med dynamiskt innehåll väljer du **Lägg till dynamiskt innehåll** igen.

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Att** | Ja | <*mottagande\@domän*> | Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. |
   | **Subjekt** | Ja | `Resource updated:` **Subjekt** | Ange innehållet för e-postmeddelandets ämne. För den här självstudien anger du den **Subject** angivna texten och väljer händelsens ämnesfält. Här innehåller e-postmeddelandets ämne namnet på den uppdaterade resursen (virtuell dator). |
   | **Brödtext** | Ja | `Resource:` **Hjälpavsnitt** <p>`Event type:`**Händelsetyp**<p>`Event ID:` **ID**<p>`Time:`**Händelsetid** | Ange innehållet för e-postmeddelandets ämne. För den här självstudien anger du den angivna texten och väljer händelsens **fält ämne,** **händelsetyp**, **ID**och **Händelsetid** så att e-postmeddelandet innehåller den resurs som avfyrade händelsen, händelsetypen, händelsetidsstämpeln och händelse-ID för uppdateringen. För den här självstudien är resursen den Azure-resursgrupp som valts i utlösaren. <p>Tryck på Skift+Retur om du vill lägga till tomma rader i innehållet. |
   ||||

   > [!NOTE]
   > Om du väljer ett fält som representerar en matris lägger designverktyget automatiskt till en **For each**-loop runt åtgärden som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

   Nu bör din e-poståtgärd se ut ungefär som i det här exemplet:

   ![Välj utdata som ska tas med i e-postmeddelandet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Och den färdiga logikappen bör se ut ungefär som i det här exemplet:

   ![Färdig logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Spara din logikapp. Om du vill dölja och dölja informationen om varje åtgärd i logikappen markerar du åtgärdens namnlist.

   Nu är logikappen aktiv, men den väntar på att den virtuella datorn ska ändras innan den gör något. Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="test-your-logic-app-workflow"></a>Testa logikappens arbetsflöde

1. Kontrollera att logikappen tar emot de angivna händelserna genom att uppdatera den virtuella datorn.

   Du kan till exempel ändra storlek på den virtuella datorn på Azure Portal eller [ändra storlek på den virtuella datorn med Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Du bör få ett e-postmeddelande efter en liten stund. Ett exempel:

   ![E-postmeddelande om uppdatering av den virtuella datorn](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Om du vill granska körningar och utlösarhistorik för logikappen väljer du **Översikt**på logikappmenyn . Om du vill visa mer information om en körning markerar du raden för den körningen.

   ![Logikappens körningshistorik](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Expandera det steg som du vill granska för att visa indata och utdata för varje steg. Den här informationen kan hjälpa dig att diagnostisera och felsöka problem i din logikapp.

   ![Information om logikappens körningshistorik](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Grattis, du har skapat och kört en logikapp som övervakar resurshändelser via ett händelserutnät och som skickar e-postmeddelanden när dessa händelser inträffar. Du har också lärt dig hur du enkelt kan skapa arbetsflöden som automatiserar processer och integrerar system och molntjänster.

Du kan övervaka andra konfigurationsändringar med händelserutnät och logikappar, till exempel:

* En virtuell dator tilldelas RBAC-rättigheter (rollbaserad åtkomstkontroll).
* Ändringar görs i en nätverkssäkerhetsgrupp (NSG) för ett nätverkskort (NIC).
* Diskar för en virtuell dator läggs till eller tas bort.
* Nätverkskortet för en virtuell dator tilldelas en offentlig IP-adress.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien använder resurser och utför åtgärder som medför avgifter i din Azure-prenumeration. När du är klar med självstudiekursen och testningen bör du därför inaktivera eller ta bort resurser som du inte vill betala för.

* Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. På logikappmenyn väljer du **Översikt**. Välj **Inaktivera**i verktygsfältet .

  ![Stänga av din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

* Om du vill ta bort logikappen permanent väljer du **Översikt**på logikappmenyn . Välj **Ta bort**i verktygsfältet . Bekräfta att du vill ta bort logikappen och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Skapa och dirigera anpassade händelser med Event Grid](../event-grid/custom-event-quickstart.md)
