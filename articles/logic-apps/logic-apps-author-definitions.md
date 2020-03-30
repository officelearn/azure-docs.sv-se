---
title: Skapa, redigera eller utöka JSON-arbetsflödesdefinitioner för logikapp
description: Så här skriver, redigerar och utökar du logikappens JSON-arbetsflödesdefinitioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979399"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Skapa, redigera eller utöka JSON för logikapparbetsflödesdefinitioner i Azure Logic Apps

När du skapar företagsintegrationslösningar med automatiserade arbetsflöden i [Azure Logic Apps](../logic-apps/logic-apps-overview.md)använder de underliggande logikappdefinitionerna enkla och deklarativa JavaScript Object Notation (JSON) tillsammans med [WDL-schemat (Workflow Definition Language)](../logic-apps/logic-apps-workflow-definition-language.md) för deras beskrivning och validering. Dessa format gör logikappdefinitioner lättare att läsa och förstå utan att veta mycket om kod.
När du vill automatisera skapandet och distributionen av logikappar kan du inkludera logikappdefinitioner som [Azure-resurser](../azure-resource-manager/management/overview.md) i [Azure Resource Manager-mallar](../azure-resource-manager/templates/overview.md).
Om du vill skapa, hantera och distribuera logikappar kan du sedan använda [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.logicapp) [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)eller AZURE Logic Apps [REST API:er](https://docs.microsoft.com/rest/api/logic/).

Om du vill arbeta med logikappdefinitioner i JSON öppnar du Code View-redigeraren när du arbetar i Azure-portalen eller i Visual Studio, eller kopierar definitionen till valfri redigerare.
Om du inte har tidigare i logikappar kan du läsa [om hur du skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Vissa Azure Logic Apps-funktioner, till exempel definiera parametrar och flera utlösare i logikappdefinitioner, är endast tillgängliga i JSON, inte Logic Apps Designer.
> För dessa uppgifter måste du alltså arbeta i kodvyn eller en annan redigerare.

## <a name="edit-json---azure-portal"></a>Redigera JSON - Azure-portal

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

2. Välj **Alla tjänster**på menyn till vänster .
Leta reda på "logikappar" i sökrutan och välj sedan logikappen i sökrutan.

3. Välj **Logikappkodvy**under **Utvecklingsverktyg**på logikappens meny.

   Code View-redigeraren öppnas och visar logikappdefinitionen i JSON-format.

## <a name="edit-json---visual-studio"></a>Redigera JSON - Visual Studio

Innan du kan arbeta med logikappdefinitionen i Visual Studio kontrollerar du att du har [installerat de verktyg som krävs](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Om du vill skapa en logikapp med Visual Studio läser du [Snabbstart: Automatisera uppgifter och processer med Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

I Visual Studio kan du öppna logikappar som har skapats och distribuerats antingen direkt från Azure-portalen eller som Azure Resource Manager-projekt från Visual Studio.

1. Öppna Visual Studio-lösningen, eller [Azure Resource Group-projektet,](../azure-resource-manager/management/overview.md) som innehåller logikappen.

2. Hitta och öppna logikappens definition, som som standard visas i en [Resource Manager-mall](../azure-resource-manager/templates/overview.md)med namnet **LogicApp.json**.
Du kan använda och anpassa den här mallen för distribution till olika miljöer.

3. Öppna snabbmenyn för logikappdefinitionen och mallen.
Välj **Öppna med Logic App Designer**.

   ![Öppna logikapp i en Visual Studio-lösning](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

4. Längst ned i designern väljer du **Kodvy**.

   Code View-redigeraren öppnas och visar logikappdefinitionen i JSON-format.

5. Om du vill återgå till designervyn väljer du **Design**längst ned i Kodvyredigeraren .

## <a name="parameters"></a>Parametrar

Distributionens livscykel har vanligtvis olika miljöer för utveckling, test, mellanlagring och produktion. När du har värden som du vill återanvända i hela logikappen utan hårdkodning eller som varierar beroende på dina distributionsbehov, kan du skapa en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md) för arbetsflödesdefinitionen så att du också kan automatisera logikappdistributionen.

Följ dessa allmänna steg för att *parameterisera*eller definiera och använda parametrar för dessa värden i stället. Du kan sedan ange värdena i en separat parameterfil som skickar dessa värden till mallen. På så sätt kan du ändra dessa värden enklare utan att behöva uppdatera och distribuera om logikappen. Fullständig information finns i [Översikt: Automatisera distributionen för logikappar med Azure Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. I mallen definierar du mallparametrar och arbetsflödesdefinitionsparametrar för att acceptera de värden som ska användas vid distribution och körning.

   Mallparametrar definieras i ett parameteravsnitt som ligger utanför arbetsflödesdefinitionen, medan arbetsflödesdefinitionsparametrar definieras i ett parameteravsnitt som finns i arbetsflödesdefinitionen.

1. Ersätt de hårdkodade värdena med uttryck som refererar till dessa parametrar. Malluttryck använder syntax som skiljer sig från arbetsflödesdefinitionsuttryck.

   Undvik att komplicera koden genom att inte använda malluttryck, som utvärderas vid distributionen, inuti arbetsflödesdefinitionsuttryck, som utvärderas vid körning. Använd endast malluttryck utanför arbetsflödesdefinitionen. Använd endast arbetsflödesdefinitionsuttryck i arbetsflödesdefinitionen.

   När du anger värdena för parametrarna för arbetsflödesdefinition kan du referera till mallparametrar med hjälp av parameteravsnittet som ligger utanför arbetsflödesdefinitionen men som fortfarande finns i resursdefinitionen för logikappen. På så sätt kan du skicka mallparametervärden till dina arbetsflödesdefinitionsparametrar.

1. Lagra värdena för dina parametrar i en separat [parameterfil](../azure-resource-manager/templates/parameter-files.md) och inkludera filen med distributionen.

## <a name="process-strings-with-functions"></a>Processsträngar med funktioner

Logic Apps har olika funktioner för att arbeta med strängar.
Anta till exempel att du vill skicka ett företagsnamn från en order till ett annat system.
Du är dock inte säker på korrekt hantering för teckenkodning.
Du kan utföra base64-kodning på den här strängen, men för att undvika rymningar i webbadressen kan du ersätta flera tecken i stället. Du behöver också bara en delsträng för företagsnamnet eftersom de första fem tecknen inte används.

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

I de här stegen beskrivs hur det här exemplet bearbetar den här strängen och arbetar från insidan till utsidan:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Hämta [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) för företagsnamnet, så att du får det totala antalet tecken.

2. Om du vill få en `5`kortare sträng subtraherar du .

3. Nu får [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)en .
Börja vid `5`index och gå till resten av strängen.

4. Konvertera den här delsträngen till en [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) sträng.

5. Nu [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alla `+` tecken `-` med tecken.

6. Slutligen [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alla `/` tecken `_` med tecken.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mappa listobjekt till egenskapsvärden och använd sedan kartor som parametrar

Om du vill få olika resultat baserat på en egenskaps värde kan du skapa en karta som matchar varje egenskapsvärde till ett resultat och sedan använda den kartan som en parameter.

Det här arbetsflödet definierar till exempel vissa kategorier som parametrar och en karta som matchar dessa kategorier med en viss URL.
Först får arbetsflödet en lista med artiklar. Sedan använder arbetsflödet kartan för att hitta webbadressen som matchar kategorin för varje artikel.

*   Funktionen [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) kontrollerar om kategorin matchar en känd definierad kategori.

*   När du har hämtat en matchande kategori hämtar exemplet objektet från kartan med hjälp av hakparenteser:`parameters[...]`

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

Om du vill hämta data från en datakälla som inte stöder *utlösare*internt kan du använda datumfunktioner för att arbeta med tider och datum i stället.
Det här uttrycket tar till exempel reda på hur lång tid det här arbetsflödets steg tar och arbetar från insidan till utsidan:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Från `order` åtgärden extraherar `startTime`du .
2. Få aktuell tid `utcNow()`med .
3. Subtrahera en sekund:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Du kan använda andra tidsenheter, gilla `minutes` eller `hours`.

3. Nu kan du jämföra dessa två värden.

   Om det första värdet är mindre än det andra värdet har mer än en sekund gått sedan ordern först placerades.

Om du vill formatera datum kan du använda strängformaterare. Om du till exempel vill hämta RFC1123 använder du [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md).
Läs mer om [datumformatering](../logic-apps/logic-apps-workflow-definition-language.md).

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
* [Kör steg baserat på olika värden (växelsatser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör och upprepa steg (loopar)](../logic-apps/logic-apps-control-flow-loops.md)
* [Kör eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Läs mer om [språkschemat för arbetsflödesdefinition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Läs mer om [arbetsflödesåtgärder och utlösare för Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
