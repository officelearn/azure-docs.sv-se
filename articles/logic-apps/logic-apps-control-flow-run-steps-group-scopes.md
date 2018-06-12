---
title: Lägga till scope som kör åtgärder baserat på Gruppstatus - Azure Logic Apps | Microsoft Docs
description: Så här skapar du scope som kör arbetsflödesåtgärder baserat på status för grupp-åtgärd i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 1258175eb3d28d39be8be08498ba8d2e0998aa43
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298822"
---
# <a name="create-scopes-that-run-workflow-actions-based-on-group-status-in-azure-logic-apps"></a>Skapa scope som kör arbetsflödesåtgärder baserat på Gruppstatus för i Azure Logic Apps

Om du vill köra åtgärder när en annan grupp av åtgärder lyckas eller misslyckas, gruppera dessa åtgärder i en *omfång*. Den här strukturen är användbart när du vill ordna åtgärder som en logisk grupp, utvärdera gruppens status och utföra åtgärder som är baserade på scopets status. När alla åtgärder i en omfattning klara så hämtar omfånget också sin egen status. Du kan till exempel använda scope när du vill implementera [undantag och felhantering](../logic-apps/logic-apps-exception-handling.md#scopes). 

Om du vill kontrollera status för ett omfång kan du använda samma villkor som används för att fastställa en logik appar kör status, till exempel ”Succeeded”, ”misslyckades”, ”avbrott” och så vidare. Som standard när alla omfattningar åtgärder lyckas markeras scopets status ”lyckades”. Men om alla åtgärder i omfånget misslyckas eller avbryts, scope har status ”misslyckades”. Gränserna för scope finns [gränser och config](../logic-apps/logic-apps-limits-and-config.md). 

Här är till exempel en övergripande logikappen som använder ett scope för att köra specifika åtgärder och ett villkor för att kontrollera scopets status. Om alla åtgärder i omfånget inte, eller avslutas oväntat omfång har markerats som ”misslyckades” eller ”avbrutet” respektive och logikappen skickar meddelandet ”Scope misslyckades”. Om åtgärderna som omfattas lyckas skickar logikappen meddelandet ”Scope lyckades”.

![Ställ in ”Schemalägg--återkommande” utlösare](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa exemplet i den här artikeln, vad du behöver:

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett e-postkonto från alla e-providern stöds av Logic Apps. Det här exemplet använder Outlook.com. Om du använder en annan provider det allmänna flödet förblir oförändrat, men ditt användargränssnitt visas olika.

* En Bing Maps-nyckel. Den här nyckeln finns <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">få en nyckel för Bing Maps</a>.

* Grundläggande kunskaper om [skapa logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Skapa exempel logikapp

Börja med att skapa det här exemplet logikappen så att du kan lägga till ett scope senare:

![Skapa exempel logikapp](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* En **schema - upprepning** utlösare som kontrollerar Bing Maps-tjänsten vid ett intervall som du anger
* En **Bing Maps - Get-väg** åtgärd som söker resa tiden mellan två platser
* En villkorlig sats som kontrollerar om tid överskrider en angiven tid
* En åtgärd som skickar e-post som aktuell tid är större än den angivna tiden

Du kan spara logikappen när som helst, så du bör spara ditt arbete ofta.

1. Logga in på den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, om du inte redan har gjort. Skapa en tom logikapp.

2. Lägg till den **schema - upprepning** utlösare med dessa inställningar: **intervall** = ”1” och **frekvens** = ”minuter”

   ![Ställ in ”Schemalägg--återkommande” utlösare](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Dölja varje åtgärd form slutföra de här stegen för att förenkla vyn visuellt och dölja information för varje åtgärd i designern.

3. Lägg till den **Bing Maps - Get-väg** åtgärd. 

   1. Om du inte redan har en Bing Maps-anslutning, uppmanas du att skapa en anslutning.

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Anslutningsnamn** | BingMapsConnection | Ange ett namn på anslutningen. | 
      | **API-nyckel** | <*your-Bing-Maps-key*> | Ange Bing Maps-nyckeln som du fick tidigare. | 
      ||||  

   2. Ställ in din **Get väg** åtgärd enligt tabellen nedan avbildningen:

      ![Ställ in ”Bing Maps - Get-väg” åtgärd](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Mer information om dessa parametrar finns [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Beräkna en resväg).

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Waypoint 1** (Platsmarkör 1) | <*Starta*> | Ange din väg ursprung. | 
      | **Waypoint 2** (Platsmarkör 2) | <*End*> | Ange din väg mål. | 
      | **Avoid** (Undvik) | Ingen | Ange objekt för att undvika längs vägen, till exempel väg, vägtullar, och så vidare. Möjliga värden finns i [beräkna en väg](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimize** (Optimera) | timeWithTraffic | Välj en parameter för att optimera din flödet, till exempel avståndet, tid med aktuell trafikinformation om och så vidare. Det här värdet används i det här exemplet: ”timeWithTraffic” | 
      | **Avståndsenhet** | <*your-preference*> | Ange avståndet enhet för att beräkna rutten. Det här värdet används i det här exemplet: ”mil” | 
      | **Travel mode** (Färdsätt) | Driving (Bil) | Ange läget för resa för din vägen. Det här exemplet använder det här värdet ”driva” | 
      | **Transit Date-Time** (Tid/datum för kollektivtrafik) | Ingen | Gäller endast överföring-läge. | 
      | **Överföring datum-typ** | Ingen | Gäller endast överföring-läge. | 
      ||||  

4. Lägg till ett villkor att kontrollera huruvida den aktuella tid med trafik överskrider en angiven tid. Följ stegen i den här avbildningen i det här exemplet:

   ![Skapa villkor](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Byt namn på villkoret med den här beskrivningen: **trafik tid om mer än angiven tid**

   2. I listan, väljer du den **resa varaktighet trafik** fältet, som är i sekunder. 

   3. För jämförelseoperatorn, väljer du den här operatorn: **är större än**

   4. Värdet för jämförelse, ange **600**, vilket är i sekunder och motsvarande till 10 minuter.

5. I villkoret **om värdet är true** Förgrena, lägga till en ”skicka e-post”-åtgärd för e-post-providern. Ställ in den här åtgärden med informationen som visas i tabellen i den här avbildningen:

   ![Lägg till ”skicka ett e-postmeddelande” åtgärd ”om värdet är true” gren](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. För den **till** , ange din e-postadress för testning.

   2. För den **ämne** anger du den här texten:

      ```Time to leave: Traffic more than 10 minutes```

   3. För den **brödtext** anger du den här texten med ett avslutande blanksteg: 

      ```Travel time: ```

      Medan du markören visas i den **brödtext** fältet dynamisk innehållslistan förblir öppen så att du kan välja de parametrar som är tillgängliga nu.

   4. Välj **Expression** (Uttryck) i listan med dynamiskt innehåll.

   5. Leta upp och markera den **div ()** funktion.

   6. Även om markören är i funktionens parenteser, Välj **dynamiskt innehåll** så att du kan lägga till den **trafik varaktighet trafik** parametern nästa.

   7. Under **Get väg** Välj i listan dynamic-parametern i **trafik varaktighet trafik** fältet.

      ![Välj ”trafik varaktighet trafik”](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. När fältet motsvarar JSON-format, lägga till en **kommatecken** (```,```) följt av numret ```60``` så att du konverterar värdet i **trafik varaktighet trafik** från sekunder minuter. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Uttrycket nu ser ut som i det här exemplet:

      ![Slut uttryck](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Se till att du väljer **OK** när du är klar.

  10. När uttrycket matchar, lägger du till den här texten med ett inledande blanksteg: ``` minutes```
  
      Din **brödtext** fältet nu ser ut som i det här exemplet:

      ![Fältet klar ”brödtext”](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Spara din logikapp.

Sedan lägger till ett scope så att du kan gruppera specifika åtgärder och utvärdera deras status.

## <a name="add-a-scope"></a>Lägg till Omfång

1. Om du inte redan gjort öppna logikappen i logik App Designer. 

2. Lägga till ett scope på arbetsflödet plats som du vill. Exempel:

   * Om du vill lägga till ett scope mellan befintliga stegen i logik app arbetsflödet muspekaren på pilen där du vill lägga till området. 
   Välj den **plustecknet** (**+**) > **lägga till ett scope**.

     ![Lägg till Omfång](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     När du vill lägga till ett scope i slutet av arbetsflödet, längst ned i din logikapp, Välj **+ nytt steg** > **... Flera** > **lägga till ett scope**.

3. Nu lägga till stegen eller dra befintliga steg som du vill köra inuti scope. Dra åtgärderna i omfånget för det här exemplet:
      
   * **Hämta väg**
   * **Trafik tid om mer än angiven tid**, vilket omfattar både den **SANT** och **FALSKT** filialer

   Din logikapp nu ser ut som i det här exemplet:

   ![Omfång som har lagts till](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. Lägg till ett villkor som kontrollerar scopets status under omfång. Byt namn på villkoret med den här beskrivningen: **om scope misslyckades**

   ![Lägg till villkor för att kontrollera status för scope](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. Skapa den här uttryck som kontrollerar om scopets status är lika med `Failed` eller `Aborted`.

   ![Lägg till uttryck som kontrollerar scopets status](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   Eller om du vill ange det här uttrycket som text, Välj **redigera i Avancerat läge**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. I den **om värdet är true** och **om falskt** filialer, lägga till de åtgärder som du vill utföra, till exempel skicka e-post eller ett meddelande.

   ![Lägg till uttryck som kontrollerar scopets status](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Spara din logikapp.

Din färdiga logikapp nu ser ut som om det här exemplet med alla former expanderas:

![Klar logikapp med scope](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testa ditt arbete

Välj verktygsfältet designer **kör**. Om åtgärderna som omfattas lyckas visas ett meddelande som ”Scope lyckades”. Om åtgärder som omfattas inte lyckas visas ett meddelande som ”Scope misslyckades”. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definition

Om du arbetar i kodvy, kan du definiera en scope-struktur i din logikapp JSON-definitionen i stället. Här är till exempel JSON-definitionen för utlösare och åtgärder i föregående logikappen:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* För att skicka eller rösta på funktioner och förslag, finns det [Azure Logikappar användare feedbackwebbplats](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Köra steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
