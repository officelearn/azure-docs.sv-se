---
title: Övervaka ändringar av virtuella datorer – Azure Event Grid & Logic Apps
description: Sök efter ändringar på virtuella datorer med hjälp av Azure Event Grid och Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982574"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Självstudie: övervaka ändringar av virtuella datorer med hjälp av Azure Event Grid och Logic Apps

Om du vill övervaka och svara på vissa händelser som inträffar i Azure-resurser eller resurser från tredje part, kan du automatisera och köra uppgifter som ett arbets flöde genom att skapa en [Logic-app](../logic-apps/logic-apps-overview.md) som använder minimal kod. Dessa resurser kan publicera händelser till ett [Azure Event-rutnät](../event-grid/overview.md). Händelserutnätet skickar i sin tur händelserna vidare till prenumeranter som har köer, webhooks eller [händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) som slutpunkter. Som prenumerant kan din Logi Kap par vänta på dessa händelser från händelse rutnätet innan automatiserade arbets flöden körs för att utföra åtgärder.

Här är exempel på händelser som utgivare kan skicka till prenumeranter via tjänsten Azure Event Grid:

* Händelser som skapar, läser, uppdaterar eller tar bort en resurs. Du kan till exempel övervaka ändringar som kan medföra avgifter i din Azure-prenumeration och som påverkar din faktura.

* Händelser som lägger till eller tar bort en person i en Azure-prenumeration.

* Händelser som är kopplade till en viss åtgärd som utförs av din app.

* Händelser som visar ett nytt meddelande i en kö.

Den här självstudien skapar en Logic-app som övervakar ändringar på en virtuell dator och skickar e-postmeddelanden om dessa ändringar. När du skapar en logikapp med en händelseprenumeration för en Azure-resurs flödar händelser från den resursen via ett rutnät till logikappen. Självstudiekursen beskriver steg för steg hur du skapar den här logikappen:

![Översikt – övervaka virtuella datorer med ett händelserutnät och en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en logikapp som övervakar händelser från ett händelserutnät.
> * Lägga till ett villkor som specifikt söker efter ändringar på en virtuell dator.
> * Skicka e-post när den virtuella datorn ändras.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps för att skicka meddelanden, till exempel Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/).

  I den här självstudien används ett Office 365 Outlook-konto. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

* En [virtuell dator](https://azure.microsoft.com/services/virtual-machines) som är fristående i sin egen Azure-resurs grupp. Om du inte redan har gjort det skapar du en virtuell dator via [själv studie kursen skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md). [Du behöver inte göra något mer](../event-grid/overview.md) för att den virtuella datorn ska publicera händelser.

## <a name="create-blank-logic-app"></a>Skapa en tom logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med dina Azure-kontoautentiseringsuppgifter.

1. Från huvud menyn i Azure väljer du **skapa en resurs** > **integration** > **Logic app**.

   ![Skapa en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Ange information om din Logic app-resurs under **Logic app**. När du är klar väljer du **Skapa**.

   ![Tillhandahålla information om logikappar](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Ja | <*logic-app-name*> | Ange ett unikt namn för din Logic app. |
   | **Prenumeration** | Ja | <*Azure-prenumerationsnamn*> | Välj samma Azure-prenumeration för alla tjänster i den här självstudien. |
   | **Resursgrupp** | Ja | <*Azure-resource-group*> | Namnet på Azure-resurs gruppen för din Logic app, som du kan välja för alla tjänster i den här självstudien. |
   | **Plats** | Ja | <*Azure-region*> | Välj samma region för alla tjänster i den här självstudiekursen. |
   |||

1. När Azure har distribuerat din Logic app visas en sida med en introduktions video och ofta använda utlösare i Logic Apps designer. Rulla förbi videon och utlösarna.

1. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj Logic app-mall](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visar nu de [*utlösare*](../logic-apps/logic-apps-overview.md#logic-app-concepts) som du kan använda för att starta din Logic app. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Azure Logic Apps en arbets flödes instans som kör din Logic app.

## <a name="add-an-event-grid-trigger"></a>Lägg till en Event Grid-utlösare

Lägg nu till den Event Grid utlösare som du använder för att övervaka resurs gruppen för den virtuella datorn.

1. I rutan Sök i designern anger du `event grid` som ditt filter. I listan utlösare väljer du **när en resurs händelse inträffar** som utlösare.

   ![Välj den här utlösaren: "på en resurs händelse"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. När du uppmanas till det loggar du in på Azure Event Grid med dina autentiseringsuppgifter för Azure-kontot. I listan **klient organisation** , som visar Azure Active Directory klient som är associerad med din Azure-prenumeration, kontrollerar du att rätt klient visas, till exempel:

   ![Logga in med dina autentiseringsuppgifter för Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Om du är inloggad med ett personligt Microsoft-konto som @outlook.com eller @hotmail.com kanske Event Grid-utlösaren inte visas korrekt. Som en lösning väljer du [Anslut med tjänstens huvud namn](../active-directory/develop/howto-create-service-principal-portal.md)eller autentisera som en medlem i den Azure Active Directory som är associerad med din Azure-prenumeration, till exempel *användar namn*@emailoutlook.onmicrosoft.com.

1. Prenumerera nu på din Logic app till händelser från utgivaren. Ange information om händelse prenumerationen enligt beskrivningen i följande tabell, till exempel:

   ![Ange information om händelseprenumeration](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Prenumeration** | Ja | <*event-publisher-Azure-subscription-name*> | Välj namnet på den Azure-prenumeration som är associerad med *händelse utgivaren*. I den här självstudien väljer du namnet på Azure-prenumerationen för den virtuella datorn. |
   | **Resurstyp** | Ja | <*event-publisher-Azure-resource-type*> | Välj resurs typen Azure för händelse utgivaren. Mer information om resurs typer i Azure finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md). I den här självstudien väljer du `Microsoft.Resources.ResourceGroups` värde för att övervaka Azures resurs grupper. |
   | **Resursnamn** |  Ja | <*event-publisher-Azure-resource-name*> | Välj Azure-resursens namn för händelse utgivaren. Den här listan varierar beroende på vilken resurs typ du har valt. I den här självstudien väljer du namnet på den Azure-resurs grupp som innehåller den virtuella datorn. |
   | **Objekt i händelse typ** |  Inga | > för <*händelse typer* | Välj en eller flera speciella händelse typer som ska filtreras och skickas till Event Grid. Du kan till exempel lägga till dessa händelse typer för att upptäcka när resurser ändras eller tas bort: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Mer information finns i de här ämnena: <p><p>- [Azure Event Grid händelse schema för resurs grupper](../event-grid/event-schema-resource-groups.md) <br>- [förstå händelse filtrering](../event-grid/event-filtering.md) <br>- [Filtrera händelser för Event Grid](../event-grid/how-to-filter-events.md) |
   | Om du vill lägga till valfria egenskaper väljer du **Lägg till ny parameter**och väljer sedan de egenskaper som du vill använda. | Inga | {Se beskrivningar} | * **prefix filter**: för den här självstudien lämnar du den här egenskapen tom. Standardbeteendet matchar alla värden. Du kan dock ange en prefixsträng som ett filter, till exempel en sökväg och en parameter för en specifik resurs. <p>* **suffixs filter**: i den här självstudien lämnar du den här egenskapen tom. Standardbeteendet matchar alla värden. Du kan dock ange en suffixsträng som ett filter, till exempel ett filnamnstillägg, om du bara vill använda specifika filtyper. <p>* **prenumerations namn**: i den här självstudien kan du ange ett unikt namn för din händelse prenumeration. |
   |||

1. Spara din logikapp. I verktygsfältet designer väljer du **Spara**. Om du vill komprimera och dölja en åtgärds information i din Logic app väljer du åtgärdens namn List.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   När du sparar logikappen med en utlösare för händelserutnät skapar Azure automatiskt en händelseprenumeration för logikappen för den resurs du valt. När resursen sedan publicerar en händelse i händelserutnätet, skickar händelserutnätet automatiskt händelsen till logikappen. Den här händelsen utlöser logikappen och skapar och kör sedan en instans av arbetsflödet som du definierar i följande steg.

Nu är logikappen aktiv och lyssnar på händelser från händelserutnätet, men den gör inget förrän du lägger till åtgärder i arbetsflödet.

## <a name="add-a-condition"></a>Lägg till ett villkor

Om du vill att din Logi Kap par endast ska köras när en händelse eller åtgärd sker, lägger du till ett villkor som kontrollerar `Microsoft.Compute/virtualMachines/write` åtgärden. När det här villkoret är sant skickar logikappen ett e-postmeddelande till dig med information om den uppdaterade virtuella datorn.

1. I Logic App Designer går du till händelse rutnäts utlösaren och väljer **nytt steg**.

   ![Välj "nytt steg"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Under **Välj en åtgärd**går du till rutan Sök och anger `condition` som ditt filter. I listan åtgärder väljer du **villkors** åtgärden.

   ![Lägg till ett villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic Apps-designern lägger till ett tomt villkor till arbetsflödet, inklusive åtgärdssökvägar som ska följas beroende på om villkoret är sant eller falskt.

   ![Ett tomt villkor visas](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Byt namn på villkors rubriken till `If a virtual machine in your resource group has changed`. I villkorets namn List väljer du knappen med punkter ( **...** ) och sedan **Byt namn**.

   ![Byt namn på villkoret](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Skapa ett villkor som kontrollerar händelse `body` för ett `data`-objekt där `operationName`-egenskapen är lika med `Microsoft.Compute/virtualMachines/write` åtgärden. Lär dig mer om [händelsescheman i Event Grid](../event-grid/event-schema.md).

   1. På den första raden under **And** (och) klickar du i den vänstra rutan. I listan med dynamiskt innehåll som visas väljer du **uttryck**.

      ![Öppna uttrycks redigeraren genom att välja "uttryck"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. I uttrycks redigeraren anger du det här uttrycket, som returnerar åtgärds namnet från utlösaren och väljer **OK**:

      `triggerBody()?['data']['operationName']`

      Ett exempel:

      ![Ange uttryck för att extrahera åtgärds namn](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. I den mellersta rutan behåller du operatorn **is equal to** (är lika med).

   1. I den högra rutan anger du det här värdet, vilket är den åtgärd som du vill övervaka:

      `Microsoft.Compute/virtualMachines/write`

   Ditt slutförda villkor ser nu ut som i det här exemplet:

   ![Slutfört villkor som jämför åtgärden](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Om du växlar från designvyn till kodvyn och tillbaka till designvyn, matchas det uttryck som du angav i villkoret med **data. operationName** -token:

   ![Lösta token i villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Spara din logikapp.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

Lägg nu till en [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) så att du kan få ett e-postmeddelande när det angivna villkoret är sant.

1. I rutan villkoret **om sant** väljer du **Lägg till en åtgärd**.

   ![Lägg till en åtgärd för när villkoret är sant](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Under **Välj en åtgärd**går du till rutan Sök och anger `send an email` som ditt filter. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Ett exempel:

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook.

   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com.

   * För Gmail-konton väljer du Gmail-anslutningsappen.

   Den här självstudien fortsätter med Office 365 Outlook Connector. Om du använder en annan provider förblir stegen desamma, men användar gränssnittet kan verka något annorlunda.

   ![Välj åtgärden ”send email” (skicka e-post)](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Om du inte redan är ansluten till din e-postleverantör loggar du in på ditt e-postkonto när du uppmanas att autentisera.

1. Byt namn på åtgärden skicka e-post till den här rubriken: `Send email when virtual machine updated`

1. Ange information om e-postmeddelandet som anges i följande tabell:

   ![Ange information om e-poståtgärd](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Om du vill välja utdata från föregående steg i arbets flödet klickar du i en redigerings ruta så att listan med dynamiskt innehåll visas eller väljer **Lägg till dynamiskt innehåll**. För fler resultat väljer du **Mer information** för varje avsnitt i listan. Stäng listan med dynamiskt innehåll genom att välja **Lägg till dynamiskt innehåll** igen.

   | Egenskap | Krävs | Värde | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Till** | Ja | <*mottagare\@domän*> | Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. |
   | **Ämne** | Ja | `Resource updated:` **ämne** | Ange innehållet för e-postmeddelandets ämne. I den här självstudien anger du den angivna texten och väljer händelsens **ämnes** fält. Här innehåller e-postmeddelandets ämne namnet på den uppdaterade resursen (virtuell dator). |
   | **Brödtext** | Ja | `Resource:` **ämne** <p>**händelse typ** för `Event type:`<p>`Event ID:` **-ID**<p>Tid för `Time:` **händelse** | Ange innehållet för e-postmeddelandets ämne. I den här självstudien anger du den angivna texten och väljer händelsens **ämne**, **händelse typ**, **ID**och **tids** fält för händelsen så att din e-post innehåller den resurs som utlöste händelsen, händelse typen, händelsens tidstämpel och händelse-ID för uppdateringen. I den här självstudien är resursen den Azure-resurs grupp som valts i utlösaren. <p>Tryck på Skift+Retur om du vill lägga till tomma rader i innehållet. |
   ||||

   > [!NOTE]
   > Om du väljer ett fält som representerar en matris lägger designverktyget automatiskt till en **For each**-loop runt åtgärden som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

   Nu bör din e-poståtgärd se ut ungefär som i det här exemplet:

   ![Välj utdata som ska tas med i e-postmeddelandet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Och den färdiga logikappen bör se ut ungefär som i det här exemplet:

   ![Färdig logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Spara din logikapp. Om du vill dölja och dölja varje åtgärds information i din Logic app väljer du åtgärdens namn List.

   Nu är logikappen aktiv, men den väntar på att den virtuella datorn ska ändras innan den gör något. Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="test-your-logic-app-workflow"></a>Testa logikappens arbetsflöde

1. Kontrollera att logikappen tar emot de angivna händelserna genom att uppdatera den virtuella datorn.

   Du kan till exempel ändra storlek på den virtuella datorn på Azure Portal eller [ändra storlek på den virtuella datorn med Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Du bör få ett e-postmeddelande efter en liten stund. Ett exempel:

   ![E-postmeddelande om uppdatering av den virtuella datorn](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Om du vill granska körnings-och utlösnings historiken för din Logic app väljer du **Översikt**på din Logic app-meny. Om du vill visa mer information om en körning väljer du raden för den körningen.

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

* Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. På din Logic app-meny väljer du **Översikt**. Välj **inaktivera**i verktygsfältet.

  ![Stänga av din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

* Om du vill ta bort din Logic app permanent går du till menyn Logic app och väljer **Översikt**. Välj **ta bort**i verktygsfältet. Bekräfta att du vill ta bort din Logic app och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Skapa och dirigera anpassade händelser med Event Grid](../event-grid/custom-event-quickstart.md)
