---
title: Gruppera och köra åtgärder efter omfattning
description: Skapa scoped-åtgärder som körs baserat på gruppstatus i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74791485"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Köra åtgärder baserat på gruppstatus med hjälp av scope i Azure Logic Apps

Om du bara vill köra åtgärder efter att en annan grupp åtgärder har lyckats eller misslyckats grupperar du dessa åtgärder i ett *scope*. Den här strukturen är användbar när du vill organisera åtgärder som en logisk grupp, utvärdera gruppens status och utföra åtgärder som baseras på scopets status. När alla åtgärder i ett scope har körts får scopet också sin egen status. Du kan till exempel använda scope när du vill implementera [undantags- och felhantering](../logic-apps/logic-apps-exception-handling.md#scopes). 

Om du vill kontrollera ett scopes status kan du använda samma villkor som du använder för att fastställa en logikapps körningsstatus, till exempel "Lyckades", "Misslyckades", "Annullerad" och så vidare. När alla scopeåtgärder lyckas markeras som standard scopets status som "lyckades". Men när en åtgärd i scopet misslyckas eller avbryts markeras scopens status "Misslyckades". Begränsningar för scope finns i [Gränser och config](../logic-apps/logic-apps-limits-and-config.md). 

Här finns till exempel en logikapp på hög nivå som använder ett scope för att köra specifika åtgärder och ett villkor för att kontrollera scopets status. Om några åtgärder i scopet misslyckas eller slutar oväntat markeras scopet "Misslyckades" respektive "Avbruten" och logikappen skickar meddelandet "Scope failed". Om alla begränsade åtgärder lyckas skickar logikappen meddelandet "Scope lyckades".

![Ställ in utlösaren "Schema - återkommande"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Krav

Om du vill följa exemplet i den här artikeln behöver du följande:

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett e-postkonto från alla e-postleverantörer som stöds av Logic Apps. I det här exemplet används Outlook.com. Om du använder en annan provider förblir det allmänna flödet detsamma, men användargränssnittet ser annorlunda ut.

* En Bing Maps-nyckel. Mer om du vill hämta den här nyckeln finns i <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Hämta en Bing Maps-nyckel</a>.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Skapa exempellogikapp

Skapa först den här exempellogiken så att du kan lägga till ett scope senare:

![Skapa exempellogikapp](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* En **schema - återkommande** utlösare som kontrollerar tjänsten Bing Maps med ett intervall som du anger
* A **Bing Maps - Få ruttåtgärd** som kontrollerar restiden mellan två platser
* En villkorsförklaring som kontrollerar om restiden överskrider din angivna restid
* En åtgärd som skickar e-post till dig som den aktuella restiden överskrider din angivna tid

Du kan spara logikappen när som helst, så spara ditt arbete ofta.

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>om du inte redan har gjort det. Skapa en tom logikapp.

1. Lägg till **schema - återkommande** utlösare med dessa inställningar: **Intervall** = "1" och **Frekvens** = "Minut"

   ![Ställ in utlösaren "Schema - återkommande"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Om du vill förenkla vyn visuellt och dölja informationen om varje åtgärd i designern komprimerar du varje åtgärds form när du går igenom de här stegen.

1. Lägg till **Bing-kartorna - Hämta** vägåtgärden. 

   1. Om du inte redan har en Bing Maps-anslutning uppmanas du att skapa en anslutning.

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Anslutningsnamn** | BingMapsConnection | Ange ett namn på anslutningen. | 
      | **API-nyckel** | <*your-Bing-Maps-nyckel*> | Ange Bing Maps-nyckeln som du fick tidigare. | 
      ||||  

   1. Ställ in åtgärden **Hämta rutt** som visas i tabellen under den här bilden:

      ![Ställ in åtgärden "Bing Kartor - Hämta rutt"](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Mer information om dessa parametrar finns [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Beräkna en resväg).

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Waypoint 1** (Platsmarkör 1) | <*Börja*> | Ange ruttens ursprung. | 
      | **Waypoint 2** (Platsmarkör 2) | <*Slutet*> | Ange ruttens destination. | 
      | **Avoid** (Undvik) | Inget | Ange objekt att undvika på rutten, till exempel motorvägar, vägtullar och så vidare. Möjliga värden finns i [Beräkna en rutt](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimera** | timeWithTraffic | Välj en parameter för att optimera rutten, till exempel avstånd, tid med aktuell trafikinformation och så vidare. I det här exemplet används det här värdet: "timeWithTraffic" | 
      | **Avståndsenhet** | <*dina önskemål*> | Ange den sträcka för att beräkna rutten. I det här exemplet används det här värdet: "Mile" | 
      | **Travel mode** (Färdsätt) | Driving (Bil) | Ange färdsätt för din rutt. I det här exemplet används det här värdet "Körning" | 
      | **Transit Date-Time** (Tid/datum för kollektivtrafik) | Inget | Gäller endast för transitläge. | 
      | **Typ av transitdatumtyp** | Inget | Gäller endast för transitläge. | 
      ||||  

1. [Lägg till ett villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md) som kontrollerar om den aktuella restiden med trafik överskrider en angiven tid. 
   Gör så här:

   1. Byt namn på villkoret med den här beskrivningen: **Om trafiktiden är mer än angiven tid**

   1. I kolumnen längst till vänster klickar du i rutan **Välj ett värde** så att listan med dynamiskt innehåll visas. Välj fältet Trafik **för resor i** listan, som är i sekunder. 

      ![Skapa villkor](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. I mittenrutan väljer du den här operatorn: **är större än**

   1. I kolumnen längst till höger anger du det här jämförelsevärdet, som är i sekunder och motsvarar 10 minuter: **600**

      När du är klar ser villkoret ut som i det här exemplet:

      ![Villkoret Färdigt](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. I grenen **Om sant** lägger du till en åtgärd för "skicka e-post" för din e-postleverantör. 
   Konfigurera den här åtgärden genom att följa stegen under den här bilden:

   ![Lägg till åtgärden Skicka ett e-postmeddelande i grenen "Om sant"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Ange din e-postadress i fältet **Till** för testning.

   1. I fältet **Ämne** anger du den här texten:

      ```Time to leave: Traffic more than 10 minutes```

   1. I fältet **Brödtext** anger du den här texten med ett avslutande blanksteg: 

      ```Travel time:```

      Medan markören visas i fältet **Brödtext** förblir listan med dynamiskt innehåll öppen så att du kan välja alla parametrar som är tillgängliga just nu.

   1. Välj **Expression** (Uttryck) i listan med dynamiskt innehåll.

   1. Sök efter och välj funktionen **div().** 
      Placera markören i funktionens parenteser.

   1. Medan markören finns inom funktionens parentes väljer du **Dynamiskt innehåll** så att listan med dynamiskt innehåll visas. 
   
   1. Välj fältet **Trafikvaraktig trafik i** avsnittet **Hämta rutt.**

      ![Välj "Trafikvaraktig trafik"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. När fältet har lösts till JSON-format```,```lägger du ```60``` till ett **kommatecken** ( ) följt av numret så att du konverterar värdet i **Trafikvaraktig trafik** från sekunder till minuter. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Ditt uttryck ser nu ut så här:

      ![Avsluta uttryck](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. När du är klar väljer du **OK**.

   <!-- markdownlint-disable MD038 -->
   1. När uttrycket har lösts lägger du till den här texten med ett inledande blanksteg:``` minutes```
  
       Fältet **Body** ser nu ut så här:

       ![Fältet Färdigt "Brödtext"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Spara din logikapp.

Lägg sedan till ett scope så att du kan gruppera specifika åtgärder och utvärdera deras status.

## <a name="add-a-scope"></a>Lägga till ett scope

1. Om du inte redan har gjort det öppnar du logikappen i Logic App Designer. 

1. Lägg till ett scope på den arbetsflödesplats som du vill använda. Om du till exempel vill lägga till ett scope mellan befintliga steg i logikapparbetsflödet gör du så här: 

   1. Flytta pekaren över pilen där du vill lägga till scopet. 
   Välj **plustecknet** **+**( ) > **Lägg till en åtgärd**.

      ![Lägga till ett scope](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Ange "scope" som filter i sökrutan. 
   Välj åtgärden **Omfattning.**

## <a name="add-steps-to-scope"></a>Lägga till steg i scope

1. Lägg nu till stegen eller dra befintliga steg som du vill köra inuti scopet. Dra i det här exemplet dessa åtgärder till omfånget:
      
   * **Hämta rutt**
   * **Om trafiktiden är mer än angiven tid**, vilket inkluderar både **de sanna** och **falska** grenarna

   Logikappen ser nu ut så här:

   ![Tillagd omfattning](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Lägg till ett villkor som kontrollerar scopets status under scopet. Byt namn på villkoret med den här beskrivningen: **Om scopet misslyckades**

   ![Lägg till villkor för att kontrollera scopestatus](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. I villkoret lägger du till dessa uttryck som kontrollerar om scopets status är lika med "Misslyckades" eller "Avbruten". 

   1. Om du vill lägga till en annan rad väljer du **Lägg till**. 

   1. På varje rad klickar du i den vänstra rutan så att listan med dynamiskt innehåll visas. 
   Välj **Uttryck**i listan med dynamiskt innehåll . Ange det här uttrycket i redigeringsrutan och välj sedan **OK:** 
   
      `result('Scope')[0]['status']`

      ![Lägga till uttryck som kontrollerar scopets status](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. För båda raderna är markeringen **lika** med som operator. 
   
   1. För jämförelsevärdena anger du `Failed`i den första raden . 
   I den andra `Aborted`raden anger du . 

      När du är klar ser villkoret ut som i det här exemplet:

      ![Lägga till uttryck som kontrollerar scopets status](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Ange nu villkorets `runAfter` egenskap så att villkoret kontrollerar scopestatusen och kör den matchande åtgärden som du definierar i senare steg.

   1. På **villkoret Om omfånget misslyckades** väljer du knappen **ellips** (...) och väljer sedan **Konfigurera körning efter**.

      ![Konfigurera egenskapen RunAfter](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Markera alla dessa scopestatusar: **lyckas**, **misslyckades**, **hoppas över**och **har timest out**

      ![Välj scopestatus](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. När du är klar väljer du **Klar**. 
   Villkoret visar nu en "information" ikon.

1. I **om true-** och **If-falska** grenar lägger du till de åtgärder som du vill utföra baserat på varje scopestatus, till exempel skicka ett e-postmeddelande eller meddelande.

   ![Lägga till åtgärder som ska vidtas baserat på scopestatus](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Spara din logikapp.

Din färdiga logikapp ser nu ut så här:

![Avslutad logikapp med omfattning](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testa ditt arbete

Välj **Kör**i designerverktygsfältet . Om alla begränsade åtgärder lyckas får du meddelandet "Scope lyckades". Om några begränsade åtgärder inte lyckas visas meddelandet "Scope failed". 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definition

Om du arbetar i kodvyn kan du definiera en scopestruktur i logikappens JSON-definition i stället. Här är till exempel JSON-definitionen för utlösare och åtgärder i den tidigare logikappen:

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
* Om du vill skicka in eller rösta om funktioner och förslag besöker du [webbplatsen för användarfeedback för Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (växelsatser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (loopar)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
