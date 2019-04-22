---
title: Lägga till scope som kör åtgärder baserat på status för distributionsgrupp – Azure Logic Apps | Microsoft Docs
description: Så här skapar du scope som kör arbetsflödesåtgärder baserat på status för grupp-åtgärd i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 48fb2d14cd4cf99510fff88b25b9ae45814a92a8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882420"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Kör åtgärder baserat på status för distributionsgrupp med omfång i Azure Logic Apps

För att köra åtgärder när en annan grupp av åtgärder lyckas eller misslyckas, gruppera dessa åtgärder i en *omfång*. Den här strukturen är användbart när du vill ordna åtgärder som en logisk grupp, utvärdera gruppens status och utföra åtgärder som baseras på scopets status. När alla åtgärder i ett omfång slutföras, hämtar omfånget även dess egna status. Du kan till exempel använda omfång när du vill implementera [undantag och felhantering](../logic-apps/logic-apps-exception-handling.md#scopes). 

Du kan kontrollera status för ett omfång du kan använda samma villkor som används för att fastställa en logic apps kör status, till exempel ”Succeeded”, ”misslyckades”, ”annullerad” och så vidare. Som standard när alla omfattningar åtgärder lyckas markeras scopets status ”Succeeded”. Men om alla åtgärden misslyckas eller avbryts, scopets status är märkt ”misslyckades”. Gränser för scope för finns i [begränsningar och konfigurationer](../logic-apps/logic-apps-limits-and-config.md). 

Här är till exempel en övergripande logikapp som använder en omfattning för att köra specifika åtgärder och ett villkor för att kontrollera scopets status. Om alla åtgärder i omfånget misslyckas eller avslutas oväntat, omfång har markerats ”misslyckades” eller ”avbrutet” respektive och logikappen skickar ett meddelande med ”omfång misslyckades”. Om alla begränsade åtgärder lyckas skickar logikappen ett ”omfång lyckades”-meddelande.

![Konfigurera utlösaren ”schema – återkommande”](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa exemplet i den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett e-postkonto från valfri e-postleverantör som stöds av Logic Apps. Det här exemplet använder Outlook.com. Om du använder en annan leverantör det allmänna flödet alltid densamma, men Användargränssnittet visas olika.

* En Bing Maps-nyckel. Den här nyckeln finns <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">hämtar en Bing Maps-nyckel</a>.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Skapa exempellogikappen

Börja med att skapa den här exempellogikappen så att du kan lägga till ett scope senare:

![Skapa exempellogikappen](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* En **schema – återkommande** utlösare som kontrollerar Bing Maps-tjänsten med ett intervall som du anger
* En **Bing Maps - Get route** åtgärd som kontrollerar restiden mellan två platser
* En villkorlig sats som kontrollerar om restiden överskrider din angivna restiden
* En åtgärd som skickar e-post den aktuella restiden överskrider din angiven tid

Du kan spara din logikapp när som helst, så spara ditt arbete ofta.

1. Logga in på den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, om du inte redan har gjort. Skapa en tom logikapp.

1. Lägg till den **schema – återkommande** utlösaren med de här inställningarna: **Intervall** = ”1” och **frekvens** = ”Minute”

   ![Konfigurera utlösaren ”schema – återkommande”](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Minimera form för varje åtgärd slutföra de här stegen för att förenkla vyn visuellt och dölja information om varje åtgärd i designern.

1. Lägg till den **Bing Maps - Get route** åtgärd. 

   1. Om du inte redan har en Bing Maps-anslutning, uppmanas du att skapa en anslutning.

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Anslutningsnamn** | BingMapsConnection | Ange ett namn på anslutningen. | 
      | **API-nyckel** | <*your-Bing-Maps-key*> | Ange Bing Maps-nyckeln som du fick tidigare. | 
      ||||  

   1. Konfigurera din **Get route** åtgärd enligt tabellen under den här bilden:

      ![Ställa in ”Bing Maps - Get route” åtgärd](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Mer information om dessa parametrar finns [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Beräkna en resväg).

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Waypoint 1** (Platsmarkör 1) | <*start*> | Ange resvägen. | 
      | **Waypoint 2** (Platsmarkör 2) | <*slutpunkt*> | Ange slutpunkten för resvägen. | 
      | **Avoid** (Undvik) | Ingen | Ange objekt för att undvika vägen, till exempel motorvägar, vägtullar, och så vidare. Möjliga värden finns i [beräkna en väg](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimize** (Optimera) | timeWithTraffic | Välj en parameter för att optimera färdvägen, till exempel avstånd, med information om aktuella trafik och så vidare. Det här exemplet används det här värdet: ”timeWithTraffic” | 
      | **Avståndsenhet** | <*your-preference*> | Ange avståndet enhet för att beräkna din resväg. Det här exemplet används det här värdet: ”Mile” | 
      | **Travel mode** (Färdsätt) | Driving (Bil) | Ange färdmedlet för din resväg. Det här exemplet används det här värdet ”körkort” | 
      | **Transit Date-Time** (Tid/datum för kollektivtrafik) | Ingen | Gäller endast överföring-läge. | 
      | **Överföring Datumtyp typ** | Ingen | Gäller endast överföring-läge. | 
      ||||  

1. [Lägg till ett villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md) som kontrollerar om den aktuella restiden med trafik överskrider en angiven tid. 
   Följ anvisningarna i det här exemplet:

   1. Byt namn på villkoret med den här beskrivningen: **Om tiden för trafik är mer än en angiven tid**

   1. I kolumnen längst till vänster klickar du i den **Välj ett värde** rutan så visas listan med dynamiskt innehåll. Från listan, Välj den **Travel Duration Traffic** fält som är i sekunder. 

      ![Skapa villkor](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. I den mellersta rutan väljer du operatorn: **är större än**

   1. Ange den här Jämförelsevärde, som är i sekunder och motsvarande till 10 minuter i kolumnen längst till höger: **600**

      När du är klar ser villkoret ut som i det här exemplet:

      ![Klar villkor](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. I den **om värdet är true** grenen, lägga till en ”skicka e-post”-åtgärd för din e-postleverantör. 
   Ställ in den här åtgärden genom att följa stegen under den här bilden:

   ![Lägga till ”skicka ett e-postmeddelande” åtgärd i ”om det är SANT” gren](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. I den **till** , ange din e-postadress för testning.

   1. I den **ämne** fältet, anger du den här texten:

      ```Time to leave: Traffic more than 10 minutes```

   1. I den **brödtext** fältet, anger du texten med ett avslutande blanksteg: 

      ```Travel time:```

      Medan du markören visas i den **brödtext** fält, den dynamiska innehållslistan förblir öppen så att du kan välja alla parametrar som är tillgängliga i det här läget.

   1. Välj **Expression** (Uttryck) i listan med dynamiskt innehåll.

   1. Sök efter och välj den **div()** funktion. 
      Placera markören i i funktionens parentes.

   1. Markören är i funktionens parenteser, Välj **dynamiskt innehåll** så att den dynamiska innehållslistan visas. 
   
   1. Från den **Get route** väljer den **trafik varaktighet trafik** fält.

      ![Välj ”trafik varaktighet trafik”](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. När fältet matchar JSON-format, lägga till en **kommatecken** (```,```) följt av numret ```60``` så att du konverterar värdet i **trafik varaktighet trafik** från sekunder till minuter. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Uttrycket nu ser ut som i följande exempel:

      ![Slut uttrycket](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. När du är klar väljer du **OK**.

   <!-- markdownlint-disable MD038 -->
   1. När uttrycket matchar, lägger du till den här texten med ett inledande blanksteg: ``` minutes```
  
       Din **brödtext** fältet ser ut som i det här exemplet:

       ![Klar ”Body”-fält](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Spara din logikapp.

Lägg sedan till ett omfång så att du kan gruppera specifika åtgärder och utvärdera deras status.

## <a name="add-a-scope"></a>Lägg till Omfång

1. Om du inte redan gjort öppna logikappen i Logic App Designer. 

1. Lägga till ett scope på arbetsflödet plats som du vill. Till exempel om du vill lägga till ett scope mellan befintliga stegen i logikapparbetsflöde, Följ dessa steg: 

   1. Flyttar du pekaren över pilen där du vill lägga till området. 
   Välj den **plustecknet** (**+**) > **Lägg till en åtgärd**.

      ![Lägg till Omfång](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. I sökrutan anger du ”omfång” som filter. 
   Välj den **omfång** åtgärd.

## <a name="add-steps-to-scope"></a>Lägg till steg i omfång

1. Nu lägger till stegen eller dra befintliga steg som du vill köra i omfånget. Dra åtgärderna i omfattningen för det här exemplet:
      
   * **Hämta väg**
   * **Om tiden för trafik är mer än en angiven tid**, som innehåller både den **SANT** och **FALSKT** grenar

   Din logikapp nu ser ut som i följande exempel:

   ![Omfång som har lagts till](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Lägg till ett villkor som kontrollerar scopets status under omfånget. Byt namn på villkoret med den här beskrivningen: **Om omfång misslyckades**

   ![Lägg till villkor för att kontrollera status för omfång](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Lägg till dessa uttryck som kontrollerar om scopets status är lika med ”misslyckades” eller ”avbrutet” i villkoret. 

   1. Om du vill lägga till en ny rad, Välj **Lägg till**. 

   1. Klicka i den vänstra rutan så att den dynamiska innehållslistan visas i varje rad. 
   Från den dynamiska innehållslistan, väljer **uttryck**. Ange det här uttrycket i rutan och välj sedan **OK**: 
   
      `result('Scope')[0]['status']`

      ![Lägg till uttryck som kontrollerar scopets status](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Välj för båda rader **är lika med** som operatör. 
   
   1. För jämförelse-värdena i den första raden anger `Failed`. 
   I den andra raden anger `Aborted`. 

      När du är klar ser villkoret ut som i det här exemplet:

      ![Lägg till uttryck som kontrollerar scopets status](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Nu ska du ange villkoret `runAfter` egenskapen så att villkoret kontrollerar status för omfång och kör instruktionen matchande som du definierar i senare steg.

   1. På den **om omfång misslyckades** villkoret, Välj den **ellipsen** (...) knappen och välj sedan **konfigurera körning efter**.

      ![Konfigurera 'runAfter-egenskapen](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Välj alla dessa scope statusar: **lyckas**, **misslyckades**, **hoppas över**, och **har nått sin tidsgräns**

      ![Välj omfattning statusar](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. När du är klar väljer du **Klar**. 
   Villkoret visar nu en ”information”-ikon.

1. I den **om värdet är true** och **om falskt** grenar, lägga till de åtgärder som du vill utföra baserat på varje omfång status, till exempel skicka ett e-post eller ett meddelande.

   ![Lägga till åtgärder ska utföras baserat på status för omfång](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Spara din logikapp.

Din logikapp nu ser ut som i följande exempel:

![Färdig logikapp med omfattning](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testa ditt arbete

I verktygsfältet för appdesignern väljer **kör**. Om alla begränsade åtgärder lyckas får du ett meddelande om ”omfång lyckades”. Om alla begränsade åtgärder inte lyckas får du ett meddelande om ”omfång misslyckades”. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definition

Om du arbetar i kodvy, kan du definiera en scope-struktur i JSON-definition för din logikapp i stället. Här är till exempel JSON-definitionen för utlösare och åtgärder i föregående logikapp:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
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
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
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
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
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
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
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
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
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
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på funktioner och förslag, Besök den [webbplatsen för Azure Logic Apps-Användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
