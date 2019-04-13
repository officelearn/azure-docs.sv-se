---
title: Lägg till loopar som Upprepa åtgärder eller bearbeta matriser – Azure Logic Apps | Microsoft Docs
description: Hur du skapar loopar som Upprepa arbetsflödesåtgärder eller bearbeta matriser i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: 339d4270dc1803879607663e9e2db4a86591ec76
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523009"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Skapa loopar som Upprepa arbetsflödesåtgärder eller bearbeta matriser i Azure Logic Apps

Om du vill bearbeta en matris i din logikapp, kan du skapa en [”Foreach”-loop](#foreach-loop). Den här loopen upprepas en eller flera åtgärder för varje objekt i matrisen. Gränser för antalet matrisobjekt som ”Foreach” loopar kan bearbeta finns i [gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md). 

Om du vill upprepa åtgärder tills ett villkor som hämtar uppfyllt eller ett tillstånd ändras, kan du skapa en [”Until”-loop](#until-loop). Logikappen kan du först kör alla åtgärder i den här slingan, och därefter utvärderas villkoret eller tillstånd. Om villkoret är uppfyllt, stoppar loopen. I annat fall upprepas loopen. Gränser för antalet ”till” loopar i en logikappskörning, finns i [gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje objekt i matrisen kan du *debatch* matrisen med den [ **SplitOn** utlösa egenskapen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>”Foreach”-loop

En ”Foreach-loop” upprepas en eller flera åtgärder för varje objekt i matrisen och fungerar bara i matriser. Iterationer i en ”Foreach”-loop som körs parallellt. Du kan dock köra iterationer en i taget genom att ställa in en [sekventiella ”Foreach”-loop](#sequential-foreach-loop). 

Följande är några saker när du använder slingor ”Foreach”:

* I kapslade loopar körs iterationer alltid sekventiellt, inte parallellt. Om du vill köra åtgärder parallellt för objekt i en kapslad loop, skapa och [anropa en underordnad logikapp](../logic-apps/logic-apps-http-endpoint.md).

* Om du vill ha förutsägbara resultat från åtgärder på variabler under varje iteration av loopen, körs dessa slingor sekventiellt. Exempelvis returnera när samtidigt körs loopa upphör, öka, minska och lägga till i variabeln operations förutsägbara resultat. Dessa åtgärder kan dock under varje iteration som körs samtidigt loopen returnera oväntade resultat. 

* Åtgärder i en ”Foreach” loopa används den [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
uttryck som refererar till och bearbeta varje objekt i matrisen. Om du anger data som inte är i en matris, misslyckas logikappens arbetsflöde. 

Det här exemplet logikappen skickar en daglig sammanfattning för en webbplats RSS-flöde. Appen använder en ”Foreach”-loop som skickar ett e-postmeddelande för varje nytt objekt.

1. [Skapa den här exempellogikappen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

2. Mellan RSS utlösa och e-poståtgärd, lägga till en ”Foreach”-loop. 

   1. Om du vill lägga till en loop mellan stegen, flyttar du pekaren över pilen mellan de här stegen. 
   Välj den **plustecknet** (**+**) som visas, välj sedan **Lägg till en åtgärd**.

      ![Välj ”Lägg till en åtgärd”](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Under sökrutan väljer du **Alla**. I sökrutan skriver du ”för var och en” som filter. Välj den här åtgärden från åtgärdslistan över: **För varje - kontroll**

      ![Lägg till ”för var och en” loop](media/logic-apps-control-flow-loops/select-for-each.png)

3. Nu bygga loopen. Under **Välj utdata från föregående steg** när den **Lägg till dynamiskt innehåll** lista visas väljer du den **Flödeslänkar** matris, vilket är utdata från RSS-utlösaren. 

   ![Välj från listan med dynamiskt innehåll](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Du kan välja *endast* matris utdata från föregående steg.

   Den valda matrisen visas nu finns här:

   ![Välj matris](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Om du vill köra en åtgärd på varje matrisen, dra den **skicka ett e-postmeddelande** åtgärd i loopen. 

   Logikappen kan se ut ungefär som i följande exempel:

   ![Lägg till steg i ”Foreach”-loop](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Spara din logikapp. För att manuellt testa din logikapp på verktygsfältet för appdesignern väljer **kör**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definitionen för ”Foreach”-loop (JSON)

Om du arbetar i kodvyn för din logikapp, kan du definiera den `Foreach` loop i JSON-definition för din logikapp i stället, till exempel:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>”Foreach” slinga: Sekventiell

Som standard köras cykler i en ”Foreach”-loop parallellt. Ange om du vill köra varje cykel sekventiellt Loopens **sekventiella** alternativet. ”Foreach” slingor måste köra sekventiellt när du har kapslade loopar eller variabler i slingor där du förväntar dig förutsägbara resultat. 

1. I Loopens övre högra hörnet, väljer **ellipserna** (**...** ) > **Inställningar**.

   ![På ”Foreach”-loop, väljer du ”...” > ”Inställningar”](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Under **samtidighetskontroll**, aktivera den **samtidighetskontroll** att ställa in **på**. Flytta den **grad av parallellitet** skjutreglaget till **1**, och välj **klar**.

   ![Aktivera samtidighetskontroll](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Om du arbetar med din logikapp JSON-definition, kan du använda den `Sequential` alternativet genom att lägga till den `operationOptions` parameter, till exempel:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>”Until”-loop
  
Placera dessa åtgärder i en ”tills”-loop för att köra och upprepa åtgärder tills ett villkor som hämtar uppfyllt eller ett tillstånd ändras. Logikappen kan du först kör alla åtgärder i den här slingan, och därefter utvärderas villkoret eller tillstånd. Om villkoret är uppfyllt, stoppar loopen. I annat fall upprepas loopen.

Här följer några vanliga scenarier där du kan använda en ”tills”-loop:

* Anropa en slutpunkt tills du får svar som du vill ha.

* Skapa en post i en databas. Vänta tills ett visst fält i den post har godkänts. Fortsätta bearbetningen. 

Kl. 8:00:00 varje dag, inkrementerar den här exempellogikapp en variabel tills variabelns värde är lika med 10. Sedan skickar logikappen ett e-postmeddelande som bekräftar det aktuella värdet. 

> [!NOTE]
> De här stegen används Office 365 Outlook, men du kan använda valfri e-postleverantör som har stöd för Logic Apps. 
> [Kontrollera listan med anslutningsappar här](https://docs.microsoft.com/connectors/). Om du använder en annan e-postkonto, de allmänna stegen desamma, men Användargränssnittet kan skilja. 

1. Skapa en tom logikapp. I Logic App Designer under sökrutan väljer **alla**. Sök efter ”återkommande”. 
   Välj den här utlösaren från listan över utlösare: **Upprepning - schema**

   ![Add "Recurrence - Schedule" trigger](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Ange när utlösaren aktiveras genom att ange intervall, frekvens och timme på dagen. Om du vill ange timmen, Välj **visa avancerade alternativ**.

   ![Konfigurera upprepningsschemat](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Egenskap  | Värde |
   | -------- | ----- |
   | **Intervall** | 1 | 
   | **Frekvens** | Dag |
   | **Vid dessa timmar** | 8 |
   ||| 

1. Under utlösaren väljer **nytt steg**. 
   Sök efter ”variabler” och välj den här åtgärden: **Initiera variabel - variabler**

   ![Lägg till ”initiera variabel - variabler” åtgärd](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Ställ in variabeln med följande värden:

   ![Ange egenskaper för variabeln](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Egenskap  | Värde | Beskrivning |
   | -------- | ----- | ----------- |
   | **Namn** | Gräns | Ditt namn | 
   | **Typ** | Integer | Data variabeltyp | 
   | **Värde** | 0 | Startvärde för variabeln | 
   |||| 

1. Under den **initieras variabeln** åtgärd, Välj **nytt steg**. 

1. Under sökrutan väljer du **Alla**. Sök efter ”till” och välj den här åtgärden: **Tills - kontroll**

   ![Lägg till ”Until”-loop](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Skapa Loopens avsluta-villkor genom att välja den **gränsen** variabeln och **är lika med** operator. 
   Ange **10** som Jämförelsevärde.

   ![Skapa avsluta-villkor för att stoppa loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. I den här slingan väljer **Lägg till en åtgärd**. 

1. Under sökrutan väljer du **Alla**. Sök efter ”variabler” och välj den här åtgärden: **Öka variabeln - variabler**

   ![Lägg till åtgärd för att öka variabeln](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. För **namn**väljer den **gränsen** variabeln. För **värdet**, ange ”1”. 

     ![Öka ”gräns” med 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Utanför och under loopen, välja **nytt steg**. 

1. Under sökrutan väljer du **Alla**. 
     Hitta och Lägg till en åtgärd som skickar e-post, till exempel: 

     ![Lägg till åtgärd som skickar e-postmeddelande](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Logga in på ditt e-postkonto om du uppmanas att göra det.

1. Ange e-postmeddelandet åtgärdens egenskaper. Lägg till den **gränsen** variabeln till ämnet. På så sätt kan du bekräfta variabelns aktuella värde uppfyller dina angivna villkor, till exempel:

      ![Konfigurera postegenskaper för e](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Egenskap  | Värde | Beskrivning |
      | -------- | ----- | ----------- | 
      | **Till** | *\<email-address\@domain>* | mottagarens e-postadress. För att testa, använda din egen e-postadress. | 
      | **Ämne** | Aktuellt värde för ”gräns” är **gräns** | Ange postämnet för e. Det här exemplet, se till att du inkluderar den **gränsen** variabeln. | 
      | **Brödtext** | <*email-content*> | Ange e-meddelandeinnehåll som du vill skicka. För det här exemplet anger du den text som du vill. | 
      |||| 

1. Spara din logikapp. För att manuellt testa din logikapp på verktygsfältet för appdesignern väljer **kör**.

      När din logik börjar köras, kan du få ett e-postmeddelande med innehåll som du angav:

      ![Mottagen e-post](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Förhindra oändliga slingor

En ”tills”-loop har standardgränser som stoppa körning om något av följande villkor:

| Egenskap  | Standardvärde | Beskrivning | 
| -------- | ------------- | ----------- | 
| **Antal** | 60 | Det högsta antalet loopar som körs innan slingan avslutas. Standardvärdet är 60 cykler. | 
| **Timeout** | PT1H | De flesta lång tid att köra en loop innan loopen avslutas. Standardvärdet är en timme och har angetts i ISO 8601-format. <p>Värdet för tidsgränsen ska utvärderas för varje loop cykel. Om något i loopen tar längre tid än tidsgränsen, slutar inte den aktuella cykeln. Dock starta nästa cykel inte eftersom gränsen villkoret inte uppfylls. | 
|||| 

Om du vill ändra dessa standardgränser, Välj **visa avancerade alternativ** i formen slinga åtgärd.

<a name="until-json"></a>

## <a name="until-definition-json"></a>”Till” definition (JSON)

Om du arbetar i kodvyn för din logikapp, kan du definiera en `Until` loop i JSON-definition för din logikapp i stället, till exempel:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Det här exemplet ”förrän” loop anropar en HTTP-slutpunkt, vilket skapar en resurs. Loopen stoppas när HTTP-svarstext returnerar med `Completed` status. Om du vill förhindra oändliga slingor, stoppas även loopen om något av följande villkor:

* Loopen körde 10 gånger som angetts av den `count` attribut. Standardvärdet är 60 gånger. 

* Loopen kördes i två timmar som den anges av den `timeout` attribut i ISO 8601-format. Standardvärdet är en timme.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Skicka eller rösta på funktioner och förslag, [webbplatsen för Azure Logic Apps-Användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
