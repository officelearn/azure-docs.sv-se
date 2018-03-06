---
title: "Bygger på logiken app definitioner med JSON - Azure Logic Apps | Microsoft Docs"
description: "Lägg till parametrar, bearbeta strängar, skapa parametern maps och hämta data med datumfunktioner"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 01/31/2018
ms.author: LADocs; estfan
ms.openlocfilehash: d05f7e34cbe670db6733c199e3420c810c304a84
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="build-on-your-logic-app-definition-with-json"></a>Skapa på logiken app definition med JSON

För mer avancerade aktiviteter med [Azure Logikappar](../logic-apps/logic-apps-overview.md), du kan använda kodvyn för att redigera din logik app definition, som använder enkla, deklarativa JSON-språk. Om du inte redan gjort först granska [hur du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se även den [fullständig referens för arbetsflödet Definition Language](http://aka.ms/logicappsdocs).

> [!NOTE]
> Vissa funktioner som parametrar, Azure Logic Apps är bara tillgängliga när du arbetar i kodvy för definition av din logikapp. Parametrar kan du återanvända värden i hela din logikapp. Om du vill använda samma e-postadress över flera åtgärder du till exempel definiera den e-postadressen som en parameter.

## <a name="view-and-edit-your-logic-app-definitions-in-json"></a>Visa och redigera dina logic app definition i JSON

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal").

2. Välj den vänstra menyn **fler tjänster**. Välj **Logikappar** under **Enterprise-integration**. Välj din logikapp.

3. Logik för app-menyn under **utvecklingsverktyg**, Välj **logik App kodvy**.

   Fönstret Visa kod öppnas och visar din app logik-definition.

## <a name="parameters"></a>Parametrar

Parametrar kan du återanvända värden i hela din logikapp och är bra för att ersätta värdena som du kan ändra ofta. Om du har en e-postadress som du vill använda på flera platser, bör du till exempel definiera den e-postadressen som en parameter. 

Parametrar är också användbara när du vill åsidosätta parametrar i olika miljöer, Lär dig mer om [parametrar för distribution av](#deployment-parameters) och [REST API för Logikappar i Azure-dokumentation](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parametrar är bara tillgängliga i kodvy.

I den [första exempel logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), du har skapat ett arbetsflöde som skickar e-postmeddelanden när nya poster visas i en webbplats RSS-feed. Feed-URL: en är hårdkodad, så det här exemplet illustrerar hur du ersätter värdet frågan med en parameter så att du kan ändra feed-URL: en enklare.

1. I kodvyn, hitta den `parameters : {}` objekt och lägger till en `currentFeedUrl` objekt:

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. I den `When_a_feed-item_is_published` åtgärd, hitta den `queries` avsnittet och Ersätt värdet för frågan med `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **Innan du**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **Efter**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   För att ansluta två eller flera strängar kan du också använda den `concat` funktion. 
   Till exempel `"@concat('#',parameters('currentFeedUrl'))"` fungerar på samma sätt som i föregående exempel.

3.  När du är klar väljer du **Spara**. 

Nu kan du ändra webbplatsens RSS-feed genom att ange en annan URL till den `currentFeedURL` objekt.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Distributionsparametrarna för olika miljöer

Vanligtvis har distribution livscykler miljöer för utveckling, mellanlagring och produktion. Du kan till exempel använda samma logik app definition i dessa miljöer men använder olika databaser. På samma sätt kan du använda samma definition över olika regioner för hög tillgänglighet, utan varje logik app-instansen som använder den regionen databas. 

> [!NOTE] 
> Det här scenariot skiljer sig från tar parametrar på *runtime* där du ska använda den `trigger()` fungerar i stället.

Här är en grundläggande definition:

``` json
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
I den faktiska `PUT` begäran för logic apps kan du ange parametern `uri`. Varje miljö kan du ange ett annat värde för den `connection` parameter. Eftersom det finns inte längre ett standardvärde, kräver den här parametern logik app nyttolasten:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Mer information finns i [REST API för Logikappar i Azure-dokumentation](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Processen strängar med funktioner

Logic Apps har olika funktioner för att arbeta med strängar. Anta att du vill skicka ett företagsnamn från en order till ett annat system. Men du inte vet om korrekt hantering för teckenkodning. Du kan utföra base64-kodning på den här strängen, men för att undvika visar i URL: en kan du ersätta flera tecken i stället. Dessutom behöver du bara en understräng för företagets namn eftersom de första fem tecknen inte används. 

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Här beskrivs hur det här exemplet bearbetar denna sträng kan arbeta från insidan till utsidan:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Hämta den [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) för företagets namn, så du får det totala antalet tecken.

2. För att få en kortare sträng, subtrahera `5`.

3. Nu att få en [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Startar vid index `5`, och gå till resten av strängen.

4. Konvertera den här delsträng som en [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) sträng.

5. Nu [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) alla de `+` tecken med `-` tecken.

6. Slutligen [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) alla de `/` tecken med `_` tecken.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mappa listobjekt till egenskapsvärden och sedan använda maps som parametrar

Om du vill ha olika resultat baserat ett egenskapsvärde, du kan skapa en karta som matchar varje egenskapsvärde till ett resultat och sedan använda som mappar som en parameter. 

Det här arbetsflödet definierar till exempel vissa kategorier som parametrar och en karta som matchar de kategorierna med en specifik URL. Först hämtar arbetsflödet en lista över artiklar. Arbetsflödet använder sedan kartan för att hitta URL-Adressen matchar kategorin för varje artikel.

*   Den [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) funktionen kontrollerar om kategorin matchar en definierad känd kategori.

*   När en matchande kategori, hämtar exemplet objekt från mappningen med hakparenteser: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Hämta data med datumfunktioner

Att hämta data från en datakälla som inte har inbyggt stöd *utlösare*, kan du använda datum fungerar för att arbeta med tid och datum i stället. Det här uttrycket hittar till exempel hur länge det här arbetsflödet steg tar, fungerar inifrån på utsidan:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Från den `order` åtgärd, extrahera det `startTime`. 
2. Hämta den aktuella tiden med `utcNow()`.
3. Ta bort en sekund:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Du kan använda andra tidsenheter, som `minutes` eller `hours`. 

3. Nu kan du jämföra två gånger. 

   Om det första värdet är mindre än det andra värdet och sedan mer än en sekund har gått sedan ordern gjordes.

Om du vill formatera datum, kan du använda string-formaterare. Exempelvis kan använda för att få RFC1123 [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Lär dig mer om [datum formatering](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>Nästa steg

* [Köra steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Lär dig mer om den [språk i Arbetsflödesdefinitionen för schemat för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Lär dig mer om [arbetsflödesåtgärder och utlösare för Logic Apps i Azure](../logic-apps/logic-apps-workflow-actions-triggers.md)