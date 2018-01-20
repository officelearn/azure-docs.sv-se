---
title: "Definiera arbetsflöden med JSON - Azure Logic Apps | Microsoft Docs"
description: "Hur du skriver arbetsflödesdefinitioner i JSON för logic apps"
author: jeffhollan
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
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7dde5bc4733af1aba34199f332379d2faf566725
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Skapa arbetsflödesdefinitioner för logic apps med JSON

Du kan skapa arbetsflödesdefinitioner för [Azure Logikappar](logic-apps-overview.md) med enkla, deklarativa JSON-språk. Om du inte redan gjort först granska [hur du skapar din första logikapp med logik App Designer](quickstart-create-first-logic-app-workflow.md). Se även den [fullständig referens för arbetsflödet Definition Language](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Upprepa steg över en lista

Gå igenom en matris som har upp till 10 000 objekt och utföra en åtgärd för varje objekt genom att använda den [foreach typen](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Hantera fel om något går fel

Vanligtvis du vill inkludera en *reparation steg* – vissa logik som kör *endast om* misslyckas för en eller flera av dina anrop. Det här exemplet hämtar data från olika platser, men om anropet misslyckas, vi vill skicka ett meddelande någonstans så att vi kan spåra att fel senare:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Du vill ange att `postToErrorMessageQueue` körs bara `readData` har `Failed`, använda den `runAfter` egenskap, till exempel vill ange en lista över möjliga värden så att `runAfter` kan vara `["Succeeded", "Failed"]`.

Slutligen, eftersom det här exemplet hanterar nu felet, vi inte längre markera kör som- `Failed`. Eftersom vi har lagt till steg för att hantera detta fel i det här exemplet kör har `Succeeded` även om ett steg `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Köra två eller flera steg parallellt

Att köra flera åtgärder parallellt, den `runAfter` egenskapen måste ha samma vid körning. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

I det här exemplet både `branch1` och `branch2` är inställd på att köras `readData`. Därför körs båda grenar parallellt. Tidsstämpel för båda filialer är identiska.

![Parallell](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Ansluta två parallella grenar

Du kan delta i två åtgärder som körs parallellt genom att lägga till objekt i den `runAfter` egenskapen som i föregående exempel.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Parallell](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Mappa objekt till en annan konfiguration

Sedan anta att vi vill hämta olika innehåll baserat på värdet för en egenskap. Vi kan skapa en karta över värden till mål som en parameter:  

```
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

I det här fallet hämta vi först en lista över artiklar. Det andra steget används baserat på den kategori som har definierats som en parameter, en karta för att slå upp URL: en för att hämta innehållet.

Vissa tidpunkter Observera: 

*   Den [ `intersection()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) funktionen kontrollerar om kategorin matchar ett av de angivna kategorierna kända.

*   När vi har fått kategorin hämtar vi objekt från mappningen med hakparenteser:`parameters[...]`

## <a name="process-strings"></a>Process-strängar

Du kan använda olika funktioner för att ändra strängar. Anta exempelvis att vi har en sträng som vi ska skickas till ett system, men vi inte är säker på om korrekt hantering för teckenkodning. Ett alternativ är att base64 koda den här strängen. Men om du vill undvika undantagstecken i URL-adresser ska ersätta några tecken. 

Vi vill även en understräng i den ordning namn eftersom de första fem tecknen inte används.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Arbeta från inuti till utanför:

1. Hämta den [ `length()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) för den orderer namn, så vi få tillbaka det totala antalet tecken.

2. Subtrahera 5 eftersom vi vill att en kortare sträng.

3. Faktiskt, ta den [ `substring()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Vi börjar vid index `5` och gå resten av strängen.

4. Konvertera den här delsträng som en [ `base64()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) sträng.

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)alla de `+` tecken med `-` tecken.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)alla de `/` tecken med `_` tecken.

## <a name="work-with-date-times"></a>Arbeta med datum

Datum kan vara användbart, särskilt när du försöker hämta data från en datakälla som inte stöder naturligt *utlösare*. Du kan också använda datum för att söka efter hur länge olika steg tar.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
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

I det här exemplet vi extrahera den `startTime` från föregående steg. Sedan vi hämta den aktuella tiden och ta bort en sekund:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Du kan använda andra tidsenheter, som `minutes` eller `hours`. Slutligen kan vi Jämför dessa två värden. Om det första värdet är mindre än det andra värdet och sedan mer än en sekund har gått sedan ordern gjordes.

Vi kan använda sträng formaterare för att formatera datum. Till exempel för att få RFC1123 kan vi använda [ `utcnow('r')` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Läs om datumformatering i [språk i arbetsflödesdefinitionen](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## <a name="deployment-parameters-for-different-environments"></a>Distributionsparametrarna för olika miljöer

Distribution livscykler har vanligtvis en utvecklingsmiljö, en mellanlagringsmiljön och en produktionsmiljö. Du kan till exempel använda samma definition i dessa miljöer men använder olika databaser. På samma sätt kan du använda samma definition över olika regioner för hög tillgänglighet, utan varje logik app-instansen tala med den regionen databasen.
Det här scenariot skiljer sig från tar parametrar på *runtime* där i stället du bör använda den `trigger()` fungera som i föregående exempel.

Du kan börja med en grundläggande definition som det här exemplet:

```
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

I den faktiska `PUT` begäran för logic apps kan du ange parametern `uri`. Eftersom det finns inte längre ett standardvärde, kräver den här parametern logik app nyttolasten:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
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

Varje miljö kan du ange ett annat värde för den `connection` parameter. 

Alla de alternativ som du har för att skapa och hantera logic apps finns i [REST API-dokumentation](https://msdn.microsoft.com/library/azure/mt643787.aspx). 
