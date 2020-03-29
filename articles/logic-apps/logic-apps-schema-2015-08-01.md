---
title: Schemauppdateringar för förhandsversionen av augusti-1-2015
description: Uppdaterad schemaversion 2015-08-01-preview för logikappdefinitioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792837"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schemauppdateringar för Azure Logic Apps – förhandsversionen av 1 augusti 2015

Den här schema- och API-versionen för Azure Logic Apps innehåller viktiga förbättringar som gör logikappar mer tillförlitliga och enklare att använda:

* **APIApp-åtgärdstypen** heter nu [**APIConnection**](#api-connections).
* Åtgärden **Upprepa** heter nu [**Foreach**](#foreach).
* [ **API-appen för HTTP-lyssnaren** ](#http-listener) behövs inte längre.
* Anropa underordnade arbetsflöden använder ett [nytt schema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Flytta till API-anslutningar

Den största förändringen är att du inte längre behöver distribuera API-appar till din Azure-prenumeration så att du kan använda API:er. Här är de sätt som du kan använda API:er:

* Hanterade API:er
* Dina anpassade webb-API:er

Varje sätt hanteras något annorlunda eftersom deras förvaltning och hosting modeller är olika. En fördel med den här modellen är att du inte längre är begränsad till resurser som distribueras i din Azure-resursgrupp. 

### <a name="managed-apis"></a>Hanterade API:er

Microsoft hanterar vissa API:er för din räkning, till exempel Office 365, Salesforce, Twitter och FTP. Du kan använda vissa hanterade API:er som de är, till exempel Bing Translate, medan andra kräver konfiguration, även kallad *anslutning*.

När du till exempel använder Office 365 måste du skapa en anslutning som innehåller inloggningstoken för Office 365. Din token lagras och uppdateras på ett säkert sätt så att logikappen alltid kan anropa Office 365-API:et. Om du vill ansluta till SQL- eller FTP-servern måste du skapa en anslutning som har anslutningssträngen. 

I den här definitionen `APIConnection`kallas dessa åtgärder . Här är ett exempel på en anslutning som anropar Office 365 för att skicka ett e-postmeddelande:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

Objektet `host` är en del av de indata som är unika för `api` API-anslutningar och innehåller dessa delar: och `connection`. Objektet `api` anger körnings-URL:en för var det hanterade API:et finns. Du kan se alla tillgängliga hanterade API:er genom att anropa den här metoden:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

När du använder ett API kanske det API:et har definierat några *anslutningsparametrar*eller inte . Så om API:et inte definierar dessa parametrar krävs ingen anslutning. Om API:et definierar dessa parametrar måste du skapa en anslutning med ett angivet namn.  
Du refererar sedan `connection` till namnet `host` i objektet i objektet. Om du vill skapa en anslutning i en resursgrupp anropar du den här metoden:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Med följande kropp:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Distribuera hanterade API:er i en Azure Resource Manager-mall

När interaktiv inloggning inte krävs kan du skapa en fullständig app med hjälp av en Resource Manager-mall.
Om inloggning krävs kan du fortfarande använda en Resource Manager-mall, men du måste godkänna anslutningarna via Azure-portalen. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

I det här exemplet kan du se att anslutningarna bara är resurser som finns i resursgruppen. De refererar till de hanterade API:er som är tillgängliga för dig i din prenumeration.

### <a name="your-custom-web-apis"></a>Dina anpassade webb-API:er

Om du använder dina egna API:er i stället för Microsoft-hanterade användare använder du den inbyggda **HTTP-åtgärden** för att anropa dina API:er. Helst bör du ange en Swagger-slutpunkt för ditt API. Den här slutpunkten hjälper Logic App Designer att visa ditt API:s indata och utdata. Utan en Swagger-slutpunkt kan designern bara visa indata och utdata som ogenomskinliga JSON-objekt.

Här är ett exempel `metadata.apiDefinitionUrl` som visar den nya egenskapen:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Om du är värd för ditt webb-API på Azure App Service visas webb-API:et automatiskt i listan över åtgärder som är tillgängliga i designern. Om inte, måste du klistra in webbadressen direkt. Swagger-slutpunkten måste vara oautentiseras för att kunna användas i Logic App Designer, även om du kan skydda själva API:et med vilka metoder som Swagger stöder.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Anropa distribuerade API-appar med förhandsversionen av 2015-08-01

Om du tidigare har distribuerat en API-app kan du anropa den appen med **HTTP-åtgärden.**
Om du till exempel använder Dropbox för att lista filer kan schemaversionsdefinitionen **för 2014-12-01-förhandsgranskning** ha något i stil med:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Nu kan du nu skapa en liknande HTTP-åtgärd `parameters` och lämna avsnittet om logikappdefinitionen oförändrad, till exempel:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Gå igenom dessa egenskaper en efter en:

| Egenskapen Åtgärd | Beskrivning |
| --- | --- |
| `type` | `Http`Istället för`APIapp` |
| `metadata.apiDefinitionUrl` | Om du vill använda den här åtgärden i Logic App Designer inkluderar du slutpunkten för metadata, som är konstruerad från:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Tillverkad av:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Alltid`POST` |
| `inputs.body` | Samma som API-appparametrarna |
| `inputs.authentication` | Samma som API-appautentiseringen |

Den här metoden bör fungera för alla API App-åtgärder. Kom dock ihåg att dessa tidigare API-appar inte längre stöds. Så du bör flytta till ett av de två andra tidigare alternativen, ett hanterat API eller värd för ditt anpassade webb-API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Omdöpt "upprepa" till "foreach"

För den tidigare schemaversionen fick vi mycket feedback från kunder om att namnet **upprepa** åtgärd var förvirrande och inte fångade **repeat-programmet** korrekt var en för varje loop. Så bytte vi `repeat` `foreach`namn till . Tidigare skulle du skriva den här åtgärden så här exemplet:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Nu skulle du skriva den här versionen istället:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

`repeatItem()` Funktionen, som refererade till objektet som loopen bearbetar under den `item()`aktuella iterationen, har nu bytt namn . 

### <a name="reference-outputs-from-foreach"></a>Referensutgångar från "foreach"

För förenkling är utdata från `foreach` åtgärder inte längre `repeatItems`insvepta i ett objekt med namnet . Med dessa ändringar tas `repeatItem()` `repeatBody()`också `repeatOutputs()` funktionerna och bort.

Så, med `repeat` hjälp av föregående exempel, får du dessa utgångar:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Nu får du dessa utgångar istället:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Tidigare, för `body` att få från åtgärden när refererar till dessa utgångar:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Nu kan du använda den här versionen i stället:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Inbyggd HTTP-lyssnare

HTTP-lyssnarfunktioner är nu inbyggda, så du behöver inte distribuera en HTTP Listener API-app. Mer information finns i hur du [gör din logikappslutpunkt samtalsbar](../logic-apps/logic-apps-http-endpoint.md). 

Med dessa ändringar ersätter `@accessKeys()` Logic Apps `@listCallbackURL()` funktionen med funktionen, som hämtar slutpunkten vid behov. Nu måste du också definiera minst en utlösare i logikappen. Om du `/run` vill arbetsflödet måste du använda någon `Manual`av `ApiConnectionWebhook`dessa utlösartyper: , eller`HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Ringa underordnade arbetsflöden

Tidigare krävde anropande underordnade arbetsflöden att gå till arbetsflödet, hämta åtkomsttoken och klistra in token i logikappdefinitionen där du vill anropa det underordnade arbetsflödet. Med det här schemat genererar Logic Apps-motorn automatiskt en SAS vid körning för det underordnade arbetsflödet så att du inte behöver klistra in några hemligheter i definitionen. Här är ett exempel:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Underordnade arbetsflöden får också fullständig åtkomst till den inkommande begäran. Så kan du skicka `queries` parametrar i `headers` avsnittet och i objektet. Du kan också helt `body` definiera hela avsnittet.

Slutligen har underordnade arbetsflöden dessa nödvändiga ändringar. Du kan tidigare och direkt anropa ett underordnat arbetsflöde, men du måste nu definiera en utlösarslutpunkt i arbetsflödet som den överordnade ska anropa. I allmänhet lägger du `Manual` till en utlösare som har typ och använder sedan utlösaren i den överordnade definitionen. Egenskapen `host` har specifikt `triggerName` en eftersom du alltid måste ange den utlösare du ringer.

## <a name="other-changes"></a>Andra ändringar

### <a name="new-queries-property"></a>Ny egenskap för frågor

Alla åtgärdstyper stöder nu `queries`en ny indata som kallas . Den här indata kan vara ett strukturerat objekt, i stället för att du måste montera strängen för hand.

### <a name="renamed-parse-function-to-json"></a>Omdöpt till funktionen "parsa()" till "json()"

Funktionen `parse()` har nu bytt `json()` namn till funktionen för framtida innehållstyper.

## <a name="enterprise-integration-apis"></a>API:er för företagsintegrering

Det här schemat har ännu inte stöd för hanterade versioner för API:er för företagsintegrering, till exempel AS2. Du kan dock använda befintliga distribuerade BizTalk API:er via HTTP-åtgärden. Mer information finns i "Använda dina redan distribuerade API-appar" i [integrationsöversikten](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
