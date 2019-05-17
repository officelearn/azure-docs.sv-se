---
title: Övervaka ändringar på virtuella datorer – Azure Event Grid och Logic Apps | Microsoft Docs
description: Övervaka konfigurationsändringar på virtuella datorer (VM) med hjälp av Azure Event Grid och Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 33634773b436114f4a5f2942028710ae50e0e703
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801114"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Självstudie: Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps

Du kan starta ett automatiserat [arbetsflöde för en logikapp](../logic-apps/logic-apps-overview.md) när specifika händelser inträffar i Azure-resurser eller i resurser från tredje part. Dessa resurser kan publicera händelserna till ett [Azure-händelserutnät](../event-grid/overview.md). Händelserutnätet skickar i sin tur händelserna vidare till prenumeranter som har köer, webhooks eller [händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) som slutpunkter. Som prenumerant kan din logikapp vänta på dessa händelser från händelserutnätet innan den kör automatiserade arbetsflöden för att utföra uppgifter – utan att du behöver skriva någon kod.

Här är exempel på händelser som utgivare kan skicka till prenumeranter via tjänsten Azure Event Grid:

* Händelser som skapar, läser, uppdaterar eller tar bort en resurs. Du kan till exempel övervaka ändringar som kan medföra avgifter i din Azure-prenumeration och som påverkar din faktura. 
* Händelser som lägger till eller tar bort en person i en Azure-prenumeration.
* Händelser som är kopplade till en viss åtgärd som utförs av din app.
* Händelser som visar ett nytt meddelande i en kö.

Den här självstudiekursen skapar en logikapp som övervakar ändringar till en virtuell dator och skickar e-postmeddelanden om dessa ändringar. När du skapar en logikapp med en händelseprenumeration för en Azure-resurs flödar händelser från den resursen via ett rutnät till logikappen. Självstudiekursen beskriver steg för steg hur du skapar den här logikappen:

![Översikt – övervaka virtuella datorer med ett händelserutnät och en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en logikapp som övervakar händelser från ett händelserutnät.
> * Lägga till ett villkor som specifikt söker efter ändringar på en virtuell dator.
> * Skicka e-post när den virtuella datorn ändras.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps för att skicka meddelanden, som Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/). 

  Den här självstudien används ett Office 365 Outlook-konto. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

* En [virtuell dator](https://azure.microsoft.com/services/virtual-machines). Om du inte redan gjort det, skapar du en virtuell dator via den [skapa en VM-självstudie](../virtual-machines/windows/quick-create-portal.md). [Du behöver inte göra något mer](../event-grid/overview.md) för att den virtuella datorn ska publicera händelser.

## <a name="create-blank-logic-app"></a>Skapa en tom logikapp

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto. 

1. Välj den Azure-huvudmenyn **skapa en resurs** > **integrering** > **Logikapp**.

   ![Skapa en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Under **Logikapp**, ange information om din logikapp. När du är klar väljer du **Skapa**.

   ![Tillhandahålla information om logikappar](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Egenskap  | Föreslaget värde | Beskrivning |
   | -------- | --------------- | ----------- |
   | **Namn** | <*logic-app-name*> | Ange ett unikt namn för din logikapp. |
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Välj samma Azure-prenumeration för alla tjänster i den här självstudiekursen. |
   | **Resursgrupp** | <*Azure-resource-group*> | Välj samma Azure-resursgrupp för alla tjänster i den här självstudiekursen. |
   | **Plats** | <*Azure-datacenter-region*> | Välj samma region för alla tjänster i den här självstudiekursen. |
   |||

   Du har nu skapat en Azure-resurs för din logikapp. 

1. När Azure har distribuerat din logikapp, Logic Apps Designer visar en sida med en introduktionsvideo video och vanliga utlösare. Rulla förbi videon och utlösarna. 

1. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja mall för logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visar dig nu [ *utlösare* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) som du kan använda för att starta logikappen. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. 
   Varje gång utlösaren utlöses skapar Azure Logic Apps skapar en arbetsflödesinstans som körs din logikapp.

## <a name="add-event-grid-trigger"></a>Lägg till Event Grid-utlösare 

Lägg nu till Event Grid-utlösare som övervakar resursgruppen för den virtuella datorn. 

1. På designern i sökrutan anger du ”event grid” som filter. Välj den här utlösaren från listan över utlösare: **När en resurshändelse inträffar – Azure Event Grid**

   ![Välj den här utlösaren: ”På en resurshändelse”](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. När du uppmanas logga in Azure Event Grid med dina Azure-autentiseringsuppgifter. I den **klient** lista som visar Azure Active Directory-klient som är associerat med din Azure-prenumeration, kontrollera att rätt klient visas.

   ![Logga in med dina autentiseringsuppgifter för Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Om du är inloggad med ett personligt Microsoft-konto som @outlook.com eller @hotmail.com kanske Event Grid-utlösaren inte visas korrekt. Du kan komma runt det här problemet genom att välja [Anslut med tjänstens huvudnamn](../active-directory/develop/howto-create-service-principal-portal.md) eller genom att autentisera som en Azure Active Directory-medlem som är associerad med din Azure-prenumeration, t.ex. *user-name*@emailoutlook.onmicrosoft.com.

1. Registrera logikappen för händelser från utgivaren. Ange information om händelseprenumerationen enligt följande tabell:

   ![Ange information om händelseprenumeration](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Egenskap  | Krävs | Value | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Prenumeration** | Ja | <*event-publisher-Azure-subscription-name*> | Välj namnet på den Azure-prenumeration som är associerade med händelseutfärdaren. Välj Azure-prenumeration-namnet för den virtuella datorn i den här självstudien. |
   | **Resurstyp** | Ja | <*event-publisher-Azure-resource-type*> | Välj Azure-resurs-typ för händelseutfärdaren. Välj det här värdet för att övervaka Azure-resursgrupper i den här självstudien: <p><p>**Microsoft.Resources.ResourceGroups** |
   | **Resursnamn** |  Ja | <*event-publisher-Azure-resource-name*> | Välj namnet på Azure-resursen för händelseutfärdaren. Den här listan varierar beroende på den resurstyp som du har valt. Välj namnet på Azure-resursgrupp för den virtuella datorn i den här självstudien. |
   | **Händelsen typobjekt** |  Nej | <*typer av händelser*> | Välj en eller flera specifika händelsetyper att filtrera och skicka till event grid. Du kan exempelvis kan du lägga till dessa händelsetyper att upptäcka när resurser har ändrats eller tagits bort: <p><p>- **Microsoft.Resources.ResourceActionSuccess** <br>- **Microsoft.Resources.ResourceDeleteSuccess** <br>- **Microsoft.Resources.ResourceWriteSuccess** <p>Mer information finns i följande avsnitt: <p><p>- [Förstå händelsefiltrering](../event-grid/event-filtering.md) <br>- [Filtrera händelser för Event Grid](../event-grid/how-to-filter-events.md) <br>- [Azure Event Grid-Händelseschema för resursgrupper](../event-grid/event-schema-resource-groups.md) |
   | **Prenumerationsnamn** | Nej | <*event-subscription-name*> | Ange ett unikt namn för din händelseprenumeration. |
   | Valfria inställningar, Välj **Lägg till ny parameter**. | Nej | {Se beskrivningar} | * **Prefixfilter**: Lämna den här egenskapen tomt i den här självstudien. Standardbeteendet matchar alla värden. Du kan dock ange en prefixsträng som ett filter, till exempel en sökväg och en parameter för en specifik resurs. <p>* **Suffixfilter**: Lämna den här egenskapen tomt i den här självstudien. Standardbeteendet matchar alla värden. Du kan dock ange en suffixsträng som ett filter, till exempel ett filnamnstillägg, om du bara vill använda specifika filtyper. |
   |||

   När du är klar Event Grid-utlösare kan se ut som i följande exempel:

   ![Information om exempel Event Grid-utlösare](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Spara din logikapp. Välj **Spara** i designerverktygsfältet. Om du vill komprimera och dölja informationen om en åtgärd i logikappen väljer du åtgärdens namnlist.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   När du sparar logikappen med en utlösare för händelserutnät skapar Azure automatiskt en händelseprenumeration för logikappen för den resurs du valt. När resursen sedan publicerar en händelse i händelserutnätet, skickar händelserutnätet automatiskt händelsen till logikappen. Den här händelsen utlöser logikappen och skapar och kör sedan en instans av arbetsflödet som du definierar i följande steg.

Nu är logikappen aktiv och lyssnar på händelser från händelserutnätet, men den gör inget förrän du lägger till åtgärder i arbetsflödet. 

## <a name="add-condition"></a>Lägg till villkor

Om du bara vill köra logikappens arbetsflöde när en viss händelse inträffar, lägger du till ett villkor som letar efter ”write”-åtgärder på den virtuella datorn. När det här villkoret är sant skickar logikappen ett e-postmeddelande till dig med information om den uppdaterade virtuella datorn.

1. I Logic App Designer under event grid-utlösare väljer **nytt steg**.

   ![Välj ”nytt steg”](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. I sökrutan anger du ”villkor” som filter. Välj den här åtgärden från åtgärdslistan över: **villkor**

   ![Lägg till ett villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic Apps-designern lägger till ett tomt villkor till arbetsflödet, inklusive åtgärdssökvägar som ska följas beroende på om villkoret är sant eller falskt.

   ![Tomt villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Byt namn på villkoret rubriken till `If a virtual machine in your resource group has changed`. På den villkorets rubriklist väljer du ellipserna (**...** ) och välj **Byt namn på**.

   ![Byt namn på villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Skapa ett villkor som kontrollerar händelsen `body` för en `data` objekt där den `operationName` egenskapen är lika med den `Microsoft.Compute/virtualMachines/write` igen. Lär dig mer om [händelsescheman i Event Grid](../event-grid/event-schema.md).

   1. På den första raden under **And** (och) klickar du i den vänstra rutan. I den dynamiska innehållslistan som visas, väljer **uttryck**.

      ![Välj ”uttryck”](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Ange det här uttrycket i uttrycksredigeraren och välj **OK**: 

      `triggerBody()?['data']['operationName']`

      Exempel:

      ![Välj ”uttryck”](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. I den mellersta rutan behåller du operatorn **is equal to** (är lika med).

   1. I den högra rutan anger du det här värdet:

      `Microsoft.Compute/virtualMachines/write`

   Klar villkoret nu ser ut som i följande exempel:

   ![Slutförda villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Om du växlar från designvyn och visa och tillbaka till designvyn det uttryck som du angav i villkoret som motsvarar den **data.operationName** token:

   ![Löst villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Spara din logikapp.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

Nu ska du lägga till en [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) så att du får ett e-postmeddelanden när det angivna villkoret är sant.

1. Välj **Lägg till en åtgärd** i rutan **Om sant** för villkoret.

   ![Lägg till en åtgärd för när villkoret är sant](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. I sökrutan anger du ”skicka ett e-postmeddelande” som filter. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Exempel: 

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook. 

   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com. 

   * För Gmail-konton väljer du Gmail-anslutningsappen. 

   Den här självstudien fortsätter med Office 365 Outlook-anslutningen. 
   Om du använder en annan leverantör förblir stegen desamma, men Användargränssnittet kan vara lite annorlunda. 

   ![Välj åtgärden ”send email” (skicka e-post)](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Om du inte redan är ansluten till din e-postleverantör loggar du in på ditt e-postkonto när du uppmanas att autentisera.

1. Byt namn på rubriken skicka e-post till den här rubriken: `Send email when virtual machine updated`

1. Ange e-postinformationen som i tabellen nedan:

   ![Tom e-poståtgärd](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Om du vill markera resultaten från föregående steg i arbetsflödet, klickar du på i en redigeringsruta så att den dynamiska innehållslistan visas, eller välj **Lägg till dynamiskt innehåll**. Fler resultat väljer **mer** för varje avsnitt i listan. Välj för att stänga den dynamiska innehållslistan **Lägg till dynamiskt innehåll** igen.

   | Egenskap  | Krävs | Value | Beskrivning |
   | -------- | -------- | ----- | ----------- |
   | **Till** | Ja | <*mottagaren\@domän*> | Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. |
   | **Ämne** | Ja | Uppdaterad resurs: **Ämne** | Ange innehållet för e-postmeddelandets ämne. Den här självstudien anger du den angivna texten och väljer du händelsen **ämne** fält. Här innehåller e-postmeddelandets ämne namnet på den uppdaterade resursen (virtuell dator). |
   | **Brödtext** | Ja | Resurs: **Avsnittet** <p>Händelsetyp: **Händelsetyp**<p>Händelse-ID: **ID**<p>Tid: **Händelsetid** | Ange innehållet för e-postmeddelandets ämne. För den här självstudiekursen anger du den angivna texten och väljer du händelsen **avsnittet**, **händelsetyp**, **ID**, och **Händelsetid** fält så att din e-postmeddelandet innehåller den resurs som utlöst händelsen, händelsetyp, händelsetidsstämpel och händelse-ID för uppdateringen. Den här självstudien är resursen Azure-resursgrupp som valts i utlösaren. <p>Tryck på Skift+Retur om du vill lägga till tomma rader i innehållet. |
   ||||

   > [!NOTE]
   > Om du väljer ett fält som representerar en matris lägger designverktyget automatiskt till en **For each**-loop runt åtgärden som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

   Nu bör din e-poståtgärd se ut ungefär som i det här exemplet:

   ![Välj utdata som ska tas med i e-postmeddelandet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Och den färdiga logikappen bör se ut ungefär som i det här exemplet:

   ![Färdig logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Spara din logikapp. Om du vill komprimera och dölja informationen om en åtgärd i logikappen väljer du åtgärdens namnlist.

   Nu är logikappen aktiv, men den väntar på att den virtuella datorn ska ändras innan den gör något. 
   Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="test-your-logic-app-workflow"></a>Testa logikappens arbetsflöde

1. Kontrollera att logikappen tar emot de angivna händelserna genom att uppdatera den virtuella datorn.

   Du kan till exempel ändra storlek på den virtuella datorn på Azure Portal eller [ändra storlek på den virtuella datorn med Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Du bör få ett e-postmeddelande efter en liten stund. Exempel:

   ![E-postmeddelande om uppdatering av den virtuella datorn](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Om du vill granska körningar och utlösningshistorik för din logikapp på logikappmenyn, Välj **översikt**. Om du vill visa mer information om en körning väljer du körningens rad.

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

* Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. På logikappmenyn, väljer **översikt**. I verktygsfältet väljer du **Inaktivera**.

  ![Stänga av din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

* För att permanent ta bort din logikapp på logikappens meny, Välj **översikt**. I verktygsfältet väljer du **Ta bort**. Bekräfta att du vill ta bort din logikapp och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Skapa och dirigera anpassade händelser med Event Grid](../event-grid/custom-event-quickstart.md)
