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
ms.date: 01/12/2019
ms.openlocfilehash: e735c9773971a4c594c32e9ae29eeb295c32810c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824814"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Självstudie: Övervaka ändringar på virtuella maskiner med Azure Event Grid och Logic Apps

Du kan starta ett automatiserat [arbetsflöde för en logikapp](../logic-apps/logic-apps-overview.md) när specifika händelser inträffar i Azure-resurser eller i resurser från tredje part. Dessa resurser kan publicera händelserna till ett [Azure-händelserutnät](../event-grid/overview.md). Händelserutnätet skickar i sin tur händelserna vidare till prenumeranter som har köer, webhooks eller [händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md) som slutpunkter. Som prenumerant kan din logikapp vänta på dessa händelser från händelserutnätet innan den kör automatiserade arbetsflöden för att utföra uppgifter – utan att du behöver skriva någon kod.

Här är exempel på händelser som utgivare kan skicka till prenumeranter via tjänsten Azure Event Grid:

* Händelser som skapar, läser, uppdaterar eller tar bort en resurs. Du kan till exempel övervaka ändringar som kan medföra avgifter i din Azure-prenumeration och som påverkar din faktura. 
* Händelser som lägger till eller tar bort en person i en Azure-prenumeration.
* Händelser som är kopplade till en viss åtgärd som utförs av din app.
* Händelser som visar ett nytt meddelande i en kö.

I den här självstudiekursen skapas en logikapp som övervakar ändringar på en virtuell dator och som skickar e-postmeddelanden om dessa ändringar. När du skapar en logikapp med en händelseprenumeration för en Azure-resurs flödar händelser från den resursen via ett rutnät till logikappen. Självstudiekursen beskriver steg för steg hur du skapar den här logikappen:

![Översikt – övervaka virtuella datorer med ett händelserutnät och en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en logikapp som övervakar händelser från ett händelserutnät.
> * Lägga till ett villkor som specifikt söker efter ändringar på en virtuell dator.
> * Skicka e-post när den virtuella datorn ändras.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett e-postkonto från [valfri e-postleverantör som stöds av Azure Logic Apps](../connectors/apis-list.md), till exempel Office 365 Outlook, Outlook.com eller Gmail, för att skicka meddelanden. Den här självstudien använder Office 365 Outlook.

* En [virtuell dator](https://azure.microsoft.com/services/virtual-machines). Om du inte redan gjort det skapar du en virtuell dator. Följ bara anvisningarna i en självstudiekurs [som beskriver hur du skapar en virtuell dator](https://docs.microsoft.com/azure/virtual-machines/). [Du behöver inte göra något mer](../event-grid/overview.md) för att den virtuella datorn ska publicera händelser.

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Skapa en logikapp som övervakar händelser från ett händelserutnät

Börja med att skapa en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn. 

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Längst upp till vänster på huvudmenyn i Azure väljer du **Skapa en resurs** > **Enterprise-integration** > **Logikapp**.

   ![Skapa en logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Skapa logikappen med inställningarna som anges i följande tabell:

   ![Tillhandahålla information om logikappar](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *{logikappens-namn}* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *{din-Azure-prenumeration}* | Välj samma Azure-prenumeration för alla tjänster i den här självstudiekursen. | 
   | **Resursgrupp** | *{din-Azure-resursgrupp}* | Välj samma Azure-resursgrupp för alla tjänster i den här självstudiekursen. | 
   | **Plats** | *{din-Azure-region}* | Välj samma region för alla tjänster i den här självstudiekursen. | 
   | | | 

4. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

   Du har nu skapat en Azure-resurs för din logikapp. 
   När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE] 
   > När du väljer **Fäst på instrumentpanelen** öppnas logikappen automatiskt i Logic Apps-designern. Om inte kan du leta upp och öppna logikappen manuellt.

5. Välj en mall för logikappar. Under **Mallar** väljer du **Tom logikapp**, så att du kan skapa logikappen från grunden.

   ![Välja mall för logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Nu visas [*anslutningsprogram*](../connectors/apis-list.md) och [*utlösare*](../logic-apps/logic-apps-overview.md#logic-app-concepts) i Logic Apps-designern som du kan använda för att börja skapa logikappen, samt åtgärder som du kan lägga till efter en utlösare för att utföra åtgärder. En utlösare är en händelse som skapar en instans av en logikapp och som startar logikappens arbetsflöde. 
   Din logikapp måste ha en utlösare som det första objektet.

6. Skriv ”event grid” (händelserutnät) som filter i sökrutan. Välj den här utlösaren: **Azure Event Grid - On a resource event** (vid en resurshändelse)

   ![Välj den här utlösaren: "Azure Event Grid - On a resource event" (vid en resurshändelse)](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. När du uppmanas att göra det loggar du in i Azure Event Grid med dina autentiseringsuppgifter för Azure.

   ![Logga in med dina autentiseringsuppgifter för Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Om du är inloggad med ett personligt Microsoft-konto som @outlook.com eller @hotmail.com kanske Event Grid-utlösaren inte visas korrekt. Du kan komma runt det här problemet genom att välja [Anslut med tjänstens huvudnamn](../active-directory/develop/howto-create-service-principal-portal.md) eller genom att autentisera som en Azure Active Directory-medlem som är associerad med din Azure-prenumeration, t.ex. *user-name*@emailoutlook.onmicrosoft.com.

8. Registrera logikappen för händelser från utgivaren. Ange information om händelseprenumerationen enligt följande tabell:

   ![Ange information om händelseprenumeration](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Prenumeration** | *{prenumeration-på-virtuell-Azure-dator}* | Välj händelseutfärdarens Azure-prenumeration. I den här självstudiekursen väljer du Azure-prenumerationen för din virtuella dator. | 
   | **Resurstyp** | Microsoft.Resources.resourceGroups | Välj händelseutfärdarens resurstyp. I den här självstudiekursen väljer du det angivna värdet så att logikappen endast övervakar resursgrupper. | 
   | **Resursnamn** | *{namn-på-resursgrupp-för-virtuell-dator}* | Välj utgivarens resursnamn. I den här självstudiekursen väljer du namnet på resursgruppen för din virtuella dator. | 
   | Välj **Visa avancerade alternativ** om du vill visa valfria inställningar. | *{se beskrivningar}* | * **Prefixfilter**: I den här självstudien lämnar du den här inställningen tom. Standardbeteendet matchar alla värden. Du kan dock ange en prefixsträng som ett filter, till exempel en sökväg och en parameter för en specifik resurs. <p>* **Suffixfilter**: I den här självstudien lämnar du den här inställningen tom. Standardbeteendet matchar alla värden. Du kan dock ange en suffixsträng som ett filter, till exempel ett filnamnstillägg, om du bara vill använda specifika filtyper.<p>* **Prenumerationsnamn**: Ange ett unikt namn för din händelseprenumeration. |
   | | | 

   När du är klar bör utlösaren för händelserutnätet se ut ungefär som i det här exemplet:
   
   ![Information om exempelutlösare för händelserutnät](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Spara din logikapp. Välj **Spara** i designerverktygsfältet. Om du vill komprimera och dölja informationen om en åtgärd i logikappen väljer du åtgärdens namnlist.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   När du sparar logikappen med en utlösare för händelserutnät skapar Azure automatiskt en händelseprenumeration för logikappen för den resurs du valt. När resursen sedan publicerar en händelse i händelserutnätet, skickar händelserutnätet automatiskt händelsen till logikappen. Den här händelsen utlöser logikappen och skapar och kör sedan en instans av arbetsflödet som du definierar i följande steg.

Nu är logikappen aktiv och lyssnar på händelser från händelserutnätet, men den gör inget förrän du lägger till åtgärder i arbetsflödet. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Lägga till ett villkor som söker efter ändringar på en virtuell dator

Om du bara vill köra logikappens arbetsflöde när en viss händelse inträffar, lägger du till ett villkor som letar efter ”write”-åtgärder på den virtuella datorn. När det här villkoret är sant skickar logikappen ett e-postmeddelande till dig med information om den uppdaterade virtuella datorn.

1. Välj **Nytt steg** > **Lägg till ett villkor** under utlösaren för händelserutnätet i Logic Apps-designern.

   ![Lägg till ett villkor till logikappen](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Logic Apps-designern lägger till ett tomt villkor till arbetsflödet, inklusive åtgärdssökvägar som ska följas beroende på om villkoret är sant eller falskt.

   ![Tomt villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. Välj **Redigera i avancerat läge** i rutan **Villkor**.
Ange det här uttrycket:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Villkoret nu ser ut som i det här exemplet:

   ![Tomt villkor](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Det här uttrycket söker efter ett `data`-objekt i händelsen `body`, där egenskapen `operationName` är åtgärden `Microsoft.Compute/virtualMachines/write`. 
   Lär dig mer om [händelsescheman i Event Grid](../event-grid/event-schema.md).

3. Om du vill lägga till en beskrivning av villkoret väljer du **ellipsen** (knappen **...** ) i villkorsformen och väljer sedan **Byt namn**.

   > [!NOTE] 
   > Exemplen längre fram i den här självstudiekursen innehåller även beskrivningar av steg i logikappens arbetsflöde.

4. Välj **Redigera standardläge** så att uttrycket körs som i bilden nedan:

   ![Villkor för logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Spara din logikapp.

## <a name="send-email-when-your-virtual-machine-changes"></a>Skicka ett e-postmeddelande när den virtuella datorn ändras

Nu ska du lägga till en [*åtgärd*](../logic-apps/logic-apps-overview.md#logic-app-concepts) så att du får ett e-postmeddelanden när det angivna villkoret är sant.

1. Välj **Lägg till en åtgärd** i rutan **Om sant** för villkoret.

   ![Lägg till en åtgärd för när villkoret är sant](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Skriv ”email” (e-post) som filter i sökrutan. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Exempel: 

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com. 
   * För Gmail-konton väljer du Gmail-anslutningsappen. 

   Vi kommer att fortsätta med anslutingsappen Office 365 Outlook. 
   Om du använder en annan leverantör förblir stegen desamma, men användargränssnittet kan vara annorlunda. 

   ![Välj åtgärden ”send email” (skicka e-post)](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Om du inte redan är ansluten till din e-postleverantör loggar du in på ditt e-postkonto när du uppmanas att autentisera.

4. Ange e-postinformationen som i tabellen nedan:

   ![Tom e-poståtgärd](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Välj bland de tillgängliga fälten i arbetsflödet genom att klicka i en redigeringsruta så att listan **Dynamiskt innehåll** öppnas eller välj **Lägg till dynamiskt innehåll**. Om du vill visa fler fält väljer du **Visa mer** för varje avsnitt i listan. Om du vill stänga listan **Dynamiskt innehåll** väljer du **Lägg till dynamiskt innehåll**.

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Till** | *{mottagarens-e-postadress}* |Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | Uppdaterad resurs: **Ämne**| Ange innehållet för e-postmeddelandets ämne. I den här självstudiekursen lägger du till den föreslagna texten och väljer fältet **Ämne** för händelsen. Här innehåller e-postmeddelandets ämne namnet på den uppdaterade resursen (virtuell dator). | 
   | **Brödtext** | Resursgrupp: **Ämne** <p>Händelsetyp: **Händelsetyp**<p>Händelse-ID: **ID**<p>Tid: **Händelsetid** | Ange innehållet för e-postmeddelandets ämne. I den här självstudiekursen lägger du till den föreslagna texten och väljer fälten **Ämne**, **Händelsetyp**, **ID**, och **Händelsetid** för händelsen så att resursgruppens namn, händelsetypen, händelsens tidsstämpel och händelse-ID:t för uppdateringen tas med i e-postmeddelandet. <p>Tryck på Skift+Retur om du vill lägga till tomma rader i innehållet. | 
   | | | 

   > [!NOTE] 
   > Om du väljer ett fält som representerar en matris lägger designverktyget automatiskt till en **For each**-loop runt åtgärden som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

   Nu bör din e-poståtgärd se ut ungefär som i det här exemplet:

   ![Välj utdata som ska tas med i e-postmeddelandet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Och den färdiga logikappen bör se ut ungefär som i det här exemplet:

   ![Färdig logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Spara din logikapp. Om du vill komprimera och dölja informationen om en åtgärd i logikappen väljer du åtgärdens namnlist.

   ![Spara din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   Nu är logikappen aktiv, men den väntar på att den virtuella datorn ska ändras innan den gör något. 
   Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="test-your-logic-app-workflow"></a>Testa logikappens arbetsflöde

1. Kontrollera att logikappen tar emot de angivna händelserna genom att uppdatera den virtuella datorn. 

   Du kan till exempel ändra storlek på den virtuella datorn på Azure Portal eller [ändra storlek på den virtuella datorn med Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Du bör få ett e-postmeddelande efter en liten stund. Exempel:

   ![E-postmeddelande om uppdatering av den virtuella datorn](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Om du vill granska körningarna och visa historik för logikappen väljer du **Översikt** på logikappens meny. Om du vill visa mer information om en körning väljer du körningens rad.

   ![Logikappens körningshistorik](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Expandera det steg som du vill granska för att visa indata och utdata för varje steg. Den här informationen kan hjälpa dig att diagnostisera och felsöka problem i din logikapp.
 
   ![Information om logikappens körningshistorik](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Grattis, du har skapat och kört en logikapp som övervakar resurshändelser via ett händelserutnät och som skickar e-postmeddelanden när dessa händelser inträffar. Du har också lärt dig hur du enkelt kan skapa arbetsflöden som automatiserar processer och integrerar system och molntjänster.

Du kan övervaka andra konfigurationsändringar med händelserutnät och logikappar, till exempel:

* En virtuell dator tilldelas RBAC-rättigheter (rollbaserad åtkomstkontroll).
* Ändringar görs i en nätverkssäkerhetsgrupp (NSG) för ett nätverkskort (NIC).
* Diskar för en virtuell dator läggs till eller tas bort.
* Nätverkskortet för en virtuell dator tilldelas en offentlig IP-adress.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien använder resurser och utför åtgärder som medför avgifter i din Azure-prenumeration. När du är klar med självstudiekursen och testningen bör du därför inaktivera eller ta bort resurser som du inte vill betala för.

* Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. På logikappmenyn väljer du **Översikt**. I verktygsfältet väljer du **Inaktivera**.

  ![Stänga av din logikapp](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

* Om du vill ta bort logikappen permanent börjar du med att välja **Översikt** på logikappens meny. I verktygsfältet väljer du **Ta bort**. Bekräfta att du vill ta bort din logikapp och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Skapa och dirigera anpassade händelser med Event Grid](../event-grid/custom-event-quickstart.md)
