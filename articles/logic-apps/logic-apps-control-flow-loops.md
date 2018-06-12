---
title: Lägg till slingor som Upprepa åtgärder eller bearbeta matriser - Azure Logic Apps | Microsoft Docs
description: Så här skapar du slingor som Upprepa arbetsflödesåtgärder eller bearbeta matriser i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 87595eeb0330a2d8210258c097c29b205b628cf4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298193"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Skapa slingor som Upprepa arbetsflödesåtgärder eller bearbeta matriser i Azure Logic Apps

Om du vill gå igenom matriser i din logikapp, kan du använda en [”Foreach” loop](#foreach-loop) eller en [sekventiella ”Foreach” loop](#sequential-foreach-loop). Upprepningar för en standard ”Foreach” loop körs parallellt, medan upprepningar för en sekventiell ”Foreach” loop körs en i taget. Det maximala antalet objekt i matrisen som ”Foreach” slingor kan bearbeta en enkel logikapp som körs, se [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje matris-objekt, kan du *debatch* att matris med de [ **SplitOn** utlösa egenskapen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Du kan upprepa åtgärder tills villkor eller vissa tillstånd har ändrats genom att använda en [”till” loop](#until-loop). Din logikapp först utför alla åtgärder inom loopen och kontrollerar villkoret som det sista steget. Om villkoret är uppfyllt, stoppar loopen. Annars upprepas loopen. Det maximala antalet ”till” loopar i en enkel logikapp som körs, finns i [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>”Foreach” loop

Använda en ”Foreach” loop i logik app arbetsflödet om du vill upprepa åtgärder för varje objekt i en matris. Du kan inkludera flera åtgärder i en slinga ”Foreach” och du kan kapsla ”Foreach” loopar i varandra. Som standard körs cykler i en standard ”Foreach” loop parallellt. Det maximala antalet parallella cykler som ”Foreach” slingor kan köra, finns [gränser och config](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> En loop ”Foreach” fungerar bara med en matris och åtgärder i en slinga använder den `@item()` referens för att bearbeta varje element i matrisen. Om du anger data som inte är i en matris misslyckas logik app arbetsflödet. 

Till exempel skickar den här logikapp en daglig sammanfattning från en webbplats RSS-feed. Appen använder en ”Foreach” loop som skickar ett e-postmeddelande för varje ny hittades.

1. [Skapa det här exemplet logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com eller Office 365 Outlook-konto.

2. Mellan RSS utlösa och skicka e-åtgärd, lägga till en ”Foreach” loop. 

   Om du vill lägga till en loop mellan stegen muspekaren på pilen där du vill lägga till loopen. 
   Välj den **plustecknet** (**+**) som visas, väljer sedan **Lägg till ett för varje**.

   ![Lägg till en ”Foreach” skapas mellan steg](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Nu skapa loopen. Under **Välj utdata från föregående steg** när den **lägga till dynamiskt innehåll** lista visas, väljer du den **Feed länkar** matris som är utdata från RSS-utlösare. 

   ![Välj dynamisk innehåll listan](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Du kan välja *endast* matris matar ut från föregående steg.

   Den valda matrisen nu visas här:

   ![Välj matris](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Om du vill utföra en åtgärd på varje element i matrisen, drar den **skickar ett e-** åtgärd i den **för varje** loop. 

   Din logikapp kan se ut ungefär så det här exemplet:

   ![Lägg till steg i ”Foreach” loop](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Spara din logikapp. Om du vill testa logikappen i verktygsfältet designer manuellt välja **kör**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>”Foreach” loop definition (JSON)

Om du arbetar i kodvy för din logikapp, kan du definiera den `Foreach` loop i din logikapp JSON-definition i stället, till exempel:

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

## <a name="foreach-loop-sequential"></a>”Foreach” slinga: sekventiella

Som standard körs varje cykel i en slinga ”Foreach” parallellt för varje element i matrisen. Om du vill köra varje cykel sekventiellt i **sekventiella** alternativet i ”Foreach”-loop.

1. Välj i Loopens övre högra hörnet **ellipserna** (**...** ) > **Inställningar**.

   ![Välj ”...” på ”Foreach” loop > ”Inställningar”](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Aktivera den **sekventiella** inställningen, Välj **klar**.

   ![Aktivera sekventiella ”Foreach” loop-inställning](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Du kan också ange den **operationOptions** parameter till `Sequential` i din logikapp JSON-definitionen. Exempel:

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

## <a name="until-loop"></a>”Till” loop
  
Om du vill upprepa åtgärder tills villkor eller vissa tillstånd har ändrats, att använda en ”tills” loop i logik app arbetsflödet. Här följer några vanliga användningsområden där du kan använda en ”tills” loop:

* Anropa en slutpunkt förrän du får svar som du vill.
* Skapa en post i en databas, vänta tills ett visst fält i att posten har godkänts och fortsätta bearbetningen. 

Till exempel på 8:00:00 varje dag ökar den här logikapp en variabel tills variabelns värde är lika med 10. Sedan skickar logikappen ett e-postmeddelande bekräftar det aktuella värdet. Även om det här exemplet använder Office 365 Outlook kan du använda valfri e-provider som stöds av Logic Apps ([granska kopplingarna listas här](https://docs.microsoft.com/connectors/)). Om du använder något annat e-postkonto är de övergripande stegen desamma, men användargränssnittet kan skilja sig något. 

1. Skapa en tom logikapp. Sök efter ”återkommande” i logik App Designer och välj den här utlösaren: **schema - upprepning** 

   ![Lägga till ”Schemalägg--återkommande” utlösare](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Ange när utlösaren utlöses genom att ange intervallet, frekvens och timme på dagen. Om du vill ange timme, Välj **visa avancerade alternativ**.

   ![Lägga till ”Schemalägg--återkommande” utlösare](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Egenskap  | Värde |
   | -------- | ----- |
   | **Intervall** | 1 | 
   | **Frekvens** | Dag |
   | **Vid dessa timmar** | 8 |
   ||| 

3. Välj under utlösare, **nytt steg** > **lägga till en åtgärd**. Sök efter ”variabler” och välj sedan den här åtgärden: **variabler - initiera variabeln**

   ![Lägg till ”variabler - initiera variabeln” åtgärd](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Ställ in variabeln med dessa värden:

   ![Ange variabeln egenskaper](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Egenskap  | Värde | Beskrivning |
   | -------- | ----- | ----------- |
   | **Namn** | Gräns | Din variabelnamn | 
   | **Typ** | Integer | Data variabeltyp | 
   | **Värde** | 0 | Variabeln startvärde | 
   |||| 

5. Under den **initiera variabeln** åtgärd, Välj **nytt steg** > **mer**. Välj det här slinga: **lägga till en gör tills**

   ![Lägg till ”göra förrän” loop](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Skapa villkoret för avslutning av Loopens genom att välja den **gränsen** variabeln och **är lika** operator. Ange **10** som Jämförelsevärde.

   ![Skapa villkoret för avslutning av för att stoppa loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. I den här slingan väljer **lägga till en åtgärd**. Sök efter ”variabler” och Lägg sedan till den här åtgärden: **variabler - öka variabeln**

   ![Lägga till åtgärden för att öka variabeln](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. För **namn**, Välj den **gränsen** variabeln. För **värdet**, ange ”1”. 

   ![Räkna upp ”gräns” 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Lägg till en åtgärd som skickar e-postmeddelande under men utanför loopen. Logga in på ditt e-postkonto om du uppmanas att göra det.

   ![Lägg till åtgärd som skickar e-post](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Ange egenskaper för den e-postadress. Lägg till den **gränsen** variabeln till ämnet. På så sätt kan du bekräfta variabelns aktuella värde uppfyller villkoret, till exempel:

    ![Konfigurera egenskaper för e-post](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Egenskap  | Värde | Beskrivning |
    | -------- | ----- | ----------- | 
    | **Till** | *<email-address@domain>* | mottagarens e-postadress. För att testa, använder du e-postadress. | 
    | **Ämne** | Aktuellt värde för ”gräns” är **gräns** | Ange ämnet för e-post. Se till att du inkluderar det här exemplet i **gränsen** variabeln. | 
    | **Brödtext** | <*e-postinnehåll*> | Ange e-postmeddelande innehåll som du vill skicka. För det här exemplet anger du den text som du vill. | 
    |||| 

11. Spara din logikapp. Om du vill testa logikappen i verktygsfältet designer manuellt välja **kör**.

    När din logik börjar köras, kan du få ett e-postmeddelande med det innehåll som du angav:

    ![Mottagen e-post](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Förhindra oändliga slingor

En ”tills” loop har standardgränser som stoppa körning om något av följande villkor:

| Egenskap  | Standardvärde | Beskrivning | 
| -------- | ------------- | ----------- | 
| **Antal** | 60 | Det maximala antalet loopar som körs innan slingan avslutas. Standardvärdet är 60 cykler. | 
| **Timeout** | PT1H | Längsta tid att köra en loop innan slingan avslutas. Standardvärdet är en timme och har angetts i ISO 8601-format. <p>Timeout-värdet utvärderas för varje loop cykel. Om alla åtgärder i en slinga tar längre tid än timeout-gränsen Stoppa inte den aktuella cykeln, men nästa cykel starta inte eftersom gränsen inte är uppfyllt. | 
|||| 

Om du vill ändra dessa standardgränser som väljer **visa avancerade alternativ** i formen loop-åtgärd.

<a name="until-json"></a>

## <a name="until-definition-json"></a>”Till”-definition (JSON)

Om du arbetar i kodvy för din logikapp, kan du definiera en `Until` loop i din logikapp JSON-definition i stället, till exempel:

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

Ett annat exempel är anropar den här ”tills” loop en HTTP-slutpunkt som skapar en resurs och stoppas när HTTP-svarstexten returnerar med statusen ”slutförd”. Om du vill förhindra oändliga slingor, stoppar slingan även om något av följande villkor:

* Slingan har körts 10 gånger som anges av den `count` attribut. Standardvärdet är 60 gånger. 
* Slingan har försökt köra två timmar som anges av den `timeout` attribut i ISO 8601-format. Standardvärdet är en timme.
  
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
* Att skicka eller rösta på funktioner och förslag [Azure Logikappar användare feedbackwebbplats](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Köra steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
