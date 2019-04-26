---
title: Skapa, redigera eller utöka JSON för logic app-definitioner – Azure Logic Apps | Microsoft Docs
description: Skapa och utöka JSON för logic app-definitioner i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: daeb900abc3f24a408fc1b5f6e989e5181f2a463
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427061"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Skapa, redigera eller utöka JSON för logic app-definitioner i Azure Logic Apps

När du skapar enterprise lösningar för dataintegrering med automatiserade arbetsflöden i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), de underliggande logikappsdefinitioner använda enkel och deklarativ JavaScript Object Notation (JSON) tillsammans med den [ Workflow Definition Language (WDL) schemat](../logic-apps/logic-apps-workflow-definition-language.md) för sina beskrivning och verifiering. Dessa format gör logic app-definitioner enklare att läsa och förstå utan att känna till kunskaper om kod. När du vill automatisera skapa och distribuera logikappar kan du inkludera logikappsdefinitioner som [Azure-resurser](../azure-resource-manager/resource-group-overview.md) inuti [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-overview.md#template-deployment). Att skapa, hantera och distribuera logic apps kan du använda [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md), eller [Azure Logic Apps REST API: er](https://docs.microsoft.com/rest/api/logic/).

Öppna Redigeraren för kodvy när du arbetar i Azure portal eller i Visual Studio för att fungera med logikappsdefinitioner i JSON, eller kopiera definitionen till valfri redigerare som du vill. Om du är nybörjare till logic apps, granska [hur du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Vissa Azure Logic Apps-funktioner, till exempel definiera parametrar och flera utlösare i logic app-definitioner är endast tillgängliga i JSON, inte Logic Apps Designer.
> Så du måste arbeta i kodvyn eller någon annan redigerare för dessa aktiviteter.

## <a name="edit-json---azure-portal"></a>Redigera JSON - Azure-portalen

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. I den vänstra menyn, Välj **alla tjänster**. Hitta ”logic apps” i sökrutan och välj sedan din logikapp från resultatet.

3. På din logikapp-menyn, under **utvecklingsverktyg**väljer **Logic App kodvy**.

   Kodvy redigeraren öppnas och visar sina logikapp-definitioner i JSON-format.

## <a name="edit-json---visual-studio"></a>Redigera JSON - Visual Studio

Innan du kan arbeta med sina logikapp-definitioner i Visual Studio, se till att du har [installerat nödvändiga verktyg](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Om du vill skapa en logikapp med Visual Studio, granska [Snabbstart: Automatisera uppgifter och processer med Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

I Visual Studio kan du öppna logikappar som har skapats och distribuerats antingen direkt från Azure portal eller Azure Resource Manager-projekt från Visual Studio.

1. Öppna Visual Studio-lösningen eller [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) projekt som innehåller din logikapp.

2. Leta upp och öppna din logikapp-definition som standard visas i en [Resource Manager-mall](../azure-resource-manager/resource-group-overview.md#template-deployment), namngiven **LogicApp.json**. Du kan använda och anpassa den här mallen för distribution till olika miljöer.

3. Öppna snabbmenyn för din logikapp-definitioner och mall. Välj **Öppna med Logic App Designer**.

   ![Öppna logikappen i en Visual Studio-lösning](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. Längst ned i designern väljer **kodvy**. 

   Kodvy redigeraren öppnas och visar sina logikapp-definitioner i JSON-format.

5. Om du vill gå tillbaka till designverktygsvyn längst ned i redigeraren kodvy väljer **Design**.

## <a name="parameters"></a>Parametrar

Parametrar kan du återanvända värden i hela din logikapp och är bra för ersättning av värden som du kan ändra ofta. Om du har en e-postadress som du vill använda på flera platser bör du till exempel definiera den e-postadressen som en parameter.

Parametrar är också användbara när du vill åsidosätta parametrar i olika miljöer, Lär dig mer om [parametrar för distribution av](#deployment-parameters) och [REST API för Azure Logic Apps-dokumentation](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parametrar är endast tillgängliga i kodvy.

I den [första exempellogikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), du har skapat ett arbetsflöde som skickar e-postmeddelanden när nya poster visas i en webbplats RSS-feed. Feed-URL: en är hårdkodad, så det här exemplet visar hur du ersätter värdet för frågan med en parameter så att du kan enkelt ändra feedens URL: en.

1. I kodvyn hitta den `parameters : {}` objekt och lägga till en `currentFeedUrl` objekt:

   ``` json
   "currentFeedUrl" : {
      "type" : "string",
      "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. I den `When_a_feed-item_is_published` åtgärd, hitta den `queries` avsnitt och Ersätt värdet för frågan med `"feedUrl": "#@{parameters('currentFeedUrl')}"`.

   **Innan du**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },
   ```

   **När du har**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },
   ```

   Om du vill ansluta till två eller flera strängar, du kan också använda den `concat` funktion. 
   Till exempel `"@concat('#',parameters('currentFeedUrl'))"` fungerar på samma sätt som i föregående exempel.

3.  När du är klar väljer du **Spara**.

Nu kan du ändra webbplatsens RSS-flöde genom att skicka en annan URL via den `currentFeedURL` objekt.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Distributionsparametrarna för olika miljöer

Livscykler för distribution har vanligtvis, miljöer för utveckling, mellanlagring och produktion. Du kan till exempel använda samma logikappsdefinitionen i dessa miljöer men använder olika databaser. På samma sätt kan du använda samma definition över olika regioner för hög tillgänglighet men vill varje logic app-instansen du använder den regionen database.

> [!NOTE]
> Det här scenariot skiljer sig från att ta parametrar på *runtime* där du ska använda den `trigger()` i stället.

Här är en grundläggande definition:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
I den faktiska `PUT` begära för logic apps kan du ange parametern `uri`. I varje miljö kan du ange ett annat värde för den `connection` parametern. Eftersom det finns inte längre ett standardvärde, kräver den här parametern i nyttolasten logic app:

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

Mer information finns i den [REST API för Azure Logic Apps-dokumentation](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Processen strängar med functions

Logic Apps har olika funktioner för att arbeta med strängar. Anta exempelvis att du vill skicka ett företagsnamn från en order till ett annat system. Men vet du inte om korrekt hantering för teckenkodning. Du kan utföra base64-kodning på den här strängen, men för att undvika visar i URL: en kan du ersätta flera tecken i stället. Dessutom behöver du bara en understräng för företagets namn eftersom de första fem tecknen inte används.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Dessa steg beskriver hur det här exemplet bearbetar denna sträng kan arbeta från insidan på utsidan:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Hämta den [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) för företagets namn, så du får det totala antalet tecken.

2. För att få en kortare sträng, subtrahera `5`.

3. Nu få en [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Starta vid indexet `5`, och gå till resten av strängen.

4. Konvertera den här delsträng som en [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) sträng.

5. Nu [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) alla de `+` tecken långt med `-` tecken.

6. Slutligen [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) alla de `/` tecken långt med `_` tecken.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mappa listobjekt egenskapsvärden och sedan använda maps som parametrar

För att få olika resultat baserat ett egenskapsvärde, du kan skapa en karta som matchar varje egenskapsvärde till ett resultat och sedan använda som mappar som en parameter.

Exempelvis definierar det här arbetsflödet vissa kategorier som parametrar och en karta som matchar de kategorierna med en specifik URL. Först hämtar arbetsflödet en lista över artiklar. Arbetsflödet använder sedan kartan för att hitta den URL som matchar kategorin för varje artikel.

*   Den [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) funktionen kontrollerar om kategorin matchar en känd definierade kategori.

*   När du har fått en matchande kategori hämtar i exempel objekt från mappningen med hakparenteser: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "science": "https://www.nasa.gov",
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
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
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

Att hämta data från en datakälla som inte har inbyggt stöd *utlösare*, du kan använda datum-funktioner för att arbeta med gånger och datum i stället. Till exempel det här uttrycket söker efter hur lång tid det här arbetsflödet steg tar, arbetar från insidan på utsidan:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Från den `order` åtgärd, extrahera det `startTime`.
2. Hämta den aktuella tiden med `utcNow()`.
3. Ta bort en sekund:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Du kan använda andra tidsenheter, som `minutes` eller `hours`.

3. Nu kan du jämföra dessa två värden. 

   Om det första värdet är mindre än det andra värdet och sedan mer än en sekund har passerat sedan ordern har gjorts först.

Om du vill formatera datum, kan du använda sträng-formaterare. Till exempel för att få RFC1123 kan använda [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Läs mer om [datum formatering](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
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
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
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

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (switch-satser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör- eller merge-parallella åtgärder (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperade Åtgärdsstatus (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Läs mer om den [Definitionsspråk för arbetsflödet schemat för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Läs mer om [arbetsflödesåtgärder och utlösare för Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
