---
title: Lägg till loopar som Upprepa åtgärder eller bearbeta matriser – Azure Logic Apps | Microsoft Docs
description: Hur du skapar loopar som Upprepa arbetsflödesåtgärder eller bearbeta matriser i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 5ba5e5abef4ebdc58c44cbe7f5ba584efe8abfc7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233114"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Skapa loopar som Upprepa arbetsflödesåtgärder eller bearbeta matriser i Azure Logic Apps

Du kan använda för att gå igenom matriser i din logikapp, en [”Foreach”-loop](#foreach-loop) eller en [sekventiella ”Foreach”-loop](#sequential-foreach-loop). Iterationer för en standard ”Foreach”-loop körs samtidigt, medan iterationer för en sekventiell ”Foreach”-loop körs ett i taget. Det maximala antalet matrisobjekt som ”Foreach” slingor kan bearbeta i en enda logikappskörning, se [gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje objekt i matrisen kan du *debatch* matrisen med den [ **SplitOn** utlösa egenskapen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Du kan upprepa åtgärder tills villkor eller vissa tillstånd har ändrats genom att använda en [”Until”-loop](#until-loop). Logikappen först utför alla åtgärder i den här slingan och därefter utvärderas villkoret som det sista steget. Om villkoret är uppfyllt, stoppar loopen. I annat fall upprepas loopen. Det maximala antalet ”till” loopar i en enkel logikapp som körs, finns i [gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>”Foreach”-loop

Om du vill upprepa åtgärder för varje objekt i en matris, använder du en ”Foreach”-loop i logikappens arbetsflöde. Du kan inkludera flera åtgärder i en ”Foreach”-loop och du kan kapsla ”Foreach” loopar i varandra. Som standard köras cykler i en standard ”Foreach”-loop parallellt. Det maximala antalet parallella cykler som ”Foreach” slingor kan köra, finns i [begränsningar och konfigurationer](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> En ”Foreach”-loop fungerar bara med en matris och åtgärder i loopen använder den `@item()` referens för att bearbeta varje objekt i matrisen. Om du anger data som inte är i en matris, misslyckas logikappens arbetsflöde. 

Exempelvis kan skickar den här logikappen en daglig sammanfattning från en webbplats RSS-feed. Appen använder en ”Foreach”-loop som skickar ett e-postmeddelande varje nytt objekt hittades.

1. [Skapa den här exempellogikappen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

2. Mellan RSS utlösa och e-poståtgärd, lägga till en ”Foreach”-loop. 

   Om du vill lägga till en loop mellan stegen, flytta du pekaren över pilen där du vill lägga till loopen. 
   Välj den **plustecknet** (**+**) som visas, välj sedan **Lägg till en för varje**.

   ![Lägg till en ”Foreach”-loop mellan steg](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Nu bygga loopen. Under **Välj utdata från föregående steg** när den **Lägg till dynamiskt innehåll** lista visas väljer du den **Flödeslänkar** matris, vilket är utdata från RSS-utlösaren. 

   ![Välj från listan med dynamiskt innehåll](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Du kan välja *endast* matris utdata från föregående steg.

   Den valda matrisen visas nu finns här:

   ![Välj matris](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Om du vill utföra en åtgärd på varje matrisen, dra den **skicka ett e-postmeddelande** åtgärd i den **för varje** loop. 

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
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>”Foreach”-loop: sekventiella

Som standard körs varje cykel i en ”Foreach”-loop parallellt för varje objekt i matrisen. Om du vill köra varje cykel sekventiellt i **sekventiella** alternativet i ”Foreach”-loop.

1. I Loopens övre högra hörnet, väljer **ellipserna** (**...** ) > **Inställningar**.

   ![På ”Foreach”-loop, väljer du ”...” > ”Inställningar”](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Aktivera den **sekventiella** inställningen, välj sedan **klar**.

   ![Aktivera ”Foreach”-loop sekventiella inställning](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Du kan också ange den **operationoptions har ställts** parameter `Sequential` i JSON-definition för din logikapp. Exempel:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>”Until”-loop
  
Du kan upprepa åtgärder tills villkor eller vissa tillstånd har ändrats genom att använda en ”tills”-loop i logikappens arbetsflöde. Här följer några vanliga användningsområden där du kan använda en ”tills”-loop:

* Anropa en slutpunkt tills du får ett svar som du vill.
* Skapa en post i en databas, vänta tills ett visst fält i den post har godkänts och fortsätta bearbetningen. 

Till exempel kl 8:00 varje dag inkrementerar den här logikappen en variabel tills variabelns värde är lika med 10. Sedan skickar logikappen ett e-postmeddelande som bekräftar det aktuella värdet. Även om det här exemplet använder Office 365 Outlook kan du använda valfri e-postleverantör som stöds av Logic Apps ([granska listan med anslutningsappar här](https://docs.microsoft.com/connectors/)). Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något. 

1. Skapa en tom logikapp. I Logic App Designer söker du efter ”återkommande” och välj den här utlösaren: **schema – återkommande** 

   ![Lägg till ”schema – återkommande” utlösare](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Ange när utlösaren aktiveras genom att ange intervall, frekvens och timme på dagen. Om du vill ange timmen, Välj **visa avancerade alternativ**.

   ![Lägg till ”schema – återkommande” utlösare](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Egenskap  | Värde |
   | -------- | ----- |
   | **Intervall** | 1 | 
   | **Frekvens** | Dag |
   | **Vid dessa timmar** | 8 |
   ||| 

3. Under utlösaren väljer **nytt steg** > **Lägg till en åtgärd**. Sök efter ”variabler” och välj sedan den här åtgärden: **variabler – initiera variabel**

   ![Lägg till ”variabler – initiera variabel” åtgärd](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Ställ in variabeln med följande värden:

   ![Ange egenskaper för variabeln](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Egenskap  | Värde | Beskrivning |
   | -------- | ----- | ----------- |
   | **Namn** | Gräns | Ditt namn | 
   | **Typ** | Integer | Data variabeltyp | 
   | **Värde** | 0 | Startvärde för variabeln | 
   |||| 

5. Under den **initieras variabeln** åtgärd, Välj **nytt steg** > **mer**. Välj den här loopen: **lägga till en gör tills**

   ![Lägg till ”do until”-loop](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Skapa Loopens avsluta-villkor genom att välja den **gränsen** variabeln och **är lika med** operator. Ange **10** som Jämförelsevärde.

   ![Skapa avsluta-villkor för att stoppa loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. I den här slingan väljer **Lägg till en åtgärd**. Sök efter ”variabler” och Lägg sedan till den här åtgärden: **variabler - inkrementera variabeln**

   ![Lägg till åtgärd för att öka variabeln](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. För **namn**väljer den **gränsen** variabeln. För **värdet**, ange ”1”. 

   ![Öka ”gräns” med 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Under men utanför loopen, lägger du till en åtgärd som skickar e-post. Logga in på ditt e-postkonto om du uppmanas att göra det.

   ![Lägg till åtgärd som skickar e-postmeddelande](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Ange e-postmeddelandets egenskaper. Lägg till den **gränsen** variabeln till ämnet. På så sätt kan du bekräfta variabelns aktuella värde uppfyller dina angivna villkor, till exempel:

    ![Konfigurera postegenskaper för e](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Egenskap  | Värde | Beskrivning |
    | -------- | ----- | ----------- | 
    | **Till** | *<email-address@domain>* | mottagarens e-postadress. För att testa, använda din egen e-postadress. | 
    | **Ämne** | Aktuellt värde för ”gräns” är **gräns** | Ange postämnet för e. Det här exemplet, se till att du inkluderar den **gränsen** variabeln. | 
    | **Brödtext** | <*e-postinnehåll*> | Ange e-meddelandeinnehåll som du vill skicka. För det här exemplet anger du den text som du vill. | 
    |||| 

11. Spara din logikapp. För att manuellt testa din logikapp på verktygsfältet för appdesignern väljer **kör**.

    När din logik börjar köras, kan du få ett e-postmeddelande med innehåll som du angav:

    ![Mottagen e-post](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Förhindra oändliga slingor

En ”tills”-loop har standardgränser som stoppa körning om något av följande villkor:

| Egenskap  | Standardvärde | Beskrivning | 
| -------- | ------------- | ----------- | 
| **Antal** | 60 | Det maximala antalet loopar som körs innan slingan avslutas. Standardvärdet är 60 cykler. | 
| **Timeout** | PT1H | Längsta tid att köra en loop innan loopen avslutas. Standardvärdet är en timme och har angetts i ISO 8601-format. <p>Värdet för tidsgränsen ska utvärderas för varje loop cykel. Om något i loopen tar längre tid än tidsgränsen, den aktuella cykeln Stoppa inte, men nästa cykel starta inte eftersom gränsen villkoret inte uppfylls. | 
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
        },
    }
},
```

Ett annat exempel är anropar den här ”tills”-loop en HTTP-slutpunkt som skapar en resurs och stoppas när HTTP-svarstext returnerar ”slutförd” status. Om du vill förhindra oändliga slingor, stoppas även loopen om något av följande villkor:

* Loopen har körts 10 gånger som angetts av den `count` attribut. Standardvärdet är 60 gånger. 
* Loopen har försökt att köras i två timmar som den anges av den `timeout` attribut i ISO 8601-format. Standardvärdet är en timme.
  
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
},
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Skicka eller rösta på funktioner och förslag, [webbplatsen för Azure Logic Apps-Användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
