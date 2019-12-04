---
title: Schema uppdateringar för för hands versionen av augusti 1-2015
description: Uppdaterad schema version 2015-08-01 – för hands version för Logic app-definitioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792837"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schema uppdateringar för Azure Logic Apps – 1 augusti 2015 för hands version

Den här schema-och API-versionen för Azure Logic Apps innehåller viktiga förbättringar som gör Logic Apps mer pålitliga och enklare att använda:

* Åtgärds typen **utlösarindata apiapp** heter nu [**APIConnection**](#api-connections).
* **Upprepnings** åtgärden heter [**nu.** ](#foreach)
* [API-appen för **http-lyssnare** ](#http-listener) krävs inte längre.
* Anrop av underordnade arbets flöden använder ett [nytt schema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Flytta till API-anslutningar

Den största ändringen är att du inte längre behöver distribuera API Apps till din Azure-prenumeration så att du kan använda API: er. Du kan använda API: er på följande sätt:

* Hanterade API: er
* Dina anpassade webb-API: er

Varje sätt hanteras något annorlunda eftersom deras hanterings-och värd modeller är olika. En fördel med den här modellen är att du inte längre är begränsad till resurser som distribueras i Azures resurs grupp. 

### <a name="managed-apis"></a>Hanterade API: er

Microsoft hanterar vissa API: er för din räkning, till exempel Office 365, Salesforce, Twitter och FTP. Du kan använda vissa hanterade API: er som de är, till exempel Bing-översättning, medan andra kräver konfiguration, även kallat en *anslutning*.

Om du till exempel använder Office 365 måste du skapa en anslutning som innehåller din Office 365-inloggnings-token. Din token lagras på ett säkert sätt och uppdateras så att din Logic app alltid kan anropa API: t för Office 365. Om du vill ansluta till din SQL-eller FTP-server måste du skapa en anslutning som har anslutnings strängen. 

I den här definitionen kallas dessa åtgärder `APIConnection`. Här är ett exempel på en anslutning som anropar Office 365 för att skicka ett e-postmeddelande:

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

`host`-objektet är en del av de indata som är unika för API-anslutningar och innehåller följande delar: `api` och `connection`. `api`-objektet anger körnings-URL: en för den plats där den hanterade API: n finns. Du kan se alla tillgängliga hanterade API: er genom att anropa den här metoden:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

När du använder ett API kan det bero på att API: t eller inte har definierat några *anslutnings parametrar*. Så om API: n inte definierar dessa parametrar, krävs ingen anslutning. Om API: et definierar dessa parametrar måste du skapa en anslutning med ett angivet namn.  
Du refererar sedan till namnet i `connection`-objektet i `host`-objektet. Anropa den här metoden om du vill skapa en anslutning i en resurs grupp:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Med följande text:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Distribuera hanterade API: er i en Azure Resource Manager mall

När interaktiv inloggning inte krävs kan du skapa en fullständig app med hjälp av en Resource Manager-mall.
Om inloggning krävs kan du fortfarande använda en Resource Manager-mall, men du måste godkänna anslutningarna via Azure Portal. 

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

I det här exemplet kan du se att anslutningarna bara är resurser som finns i resurs gruppen. De hänvisar till de hanterade API: er som är tillgängliga i din prenumeration.

### <a name="your-custom-web-apis"></a>Dina anpassade webb-API: er

Om du använder dina egna API: er i stället för Microsoft-hanterade, använder du den inbyggda **http** -åtgärden för att anropa dina API: er. Vi rekommenderar att du anger en Swagger-slutpunkt för ditt API. Den här slut punkten hjälper Logic App Designer att visa dina API: er indata och utdata. Utan en Swagger-slutpunkt kan designern bara Visa indata och utdata som täckande JSON-objekt.

Här är ett exempel som visar den nya `metadata.apiDefinitionUrl`-egenskapen:

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

Om du är värd för ditt webb-API på Azure App Service visas ditt webb-API automatiskt i listan med tillgängliga åtgärder i designern. Om inte, måste du klistra in URL: en direkt. Swagger-slutpunkten måste vara oautentiserad för att kunna användas i Logic App Designer, men du kan skydda själva API: et med de metoder som stöds av Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Anropa distribuerade API-appar med 2015-08-01 – för hands version

Om du tidigare har distribuerat en API-app kan du anropa den appen med **http-** åtgärden.
Om du till exempel använder Dropbox för att lista filer, kan din version av schema version för **2014-12-01-förhands granskning** ha något som liknar:

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

Nu kan du nu bygga en liknande HTTP-åtgärd och lämna Logic Apps-definitionens `parameters` avsnittet oförändrat, till exempel:

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

Flytta igenom dessa egenskaper en och en:

| Åtgärds egenskap | Beskrivning |
| --- | --- |
| `type` | `Http` i stället för `APIapp` |
| `metadata.apiDefinitionUrl` | Om du vill använda den här åtgärden i Logic Apps designer inkluderar du slut punkten för metadata, som är konstruerad från: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Konstruerad från: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | `POST` alltid |
| `inputs.body` | Samma som API app-parametrarna |
| `inputs.authentication` | Samma som API app-autentisering |

Den här metoden bör fungera för alla API app-åtgärder. Kom dock ihåg att dessa tidigare API Apps inte längre stöds. Därför bör du flytta till ett av de två föregående alternativen, ett hanterat API eller en värd för ditt anpassade webb-API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Namnet "REPEAT" har bytt namn till "förväntad"

För den tidigare schema versionen fick vi mycket kundfeedback om att **upprepnings** åtgärdens namn var förvirrande och att den **inte skrevs** in korrekt, var egentligen en for-each-slinga. Vi har bytt namn på `repeat` till `foreach`. Tidigare skrev du den här åtgärden, t. ex. det här exemplet:

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

Nu ska du skriva den här versionen i stället:

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

Den `repeatItem()` funktionen, som refererade till objektet som slingingen bearbetar under den aktuella iterationen, har nu bytt namn `item()`. 

### <a name="reference-outputs-from-foreach"></a>Referens utmatningar från förgrunden

För enkelhetens förenkling omsluts inte längre utdata från `foreach` åtgärder i ett objekt med namnet `repeatItems`. Med dessa ändringar tas även funktionerna `repeatItem()`, `repeatBody()`och `repeatOutputs()` bort.

Med hjälp av föregående `repeat` exempel får du följande utdata:

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

Nu visas dessa utdata i stället:

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

Tidigare, för att hämta `body` från åtgärden när du refererar till dessa utdata:

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

## <a name="native-http-listener"></a>Intern HTTP-lyssnare

HTTP Listener-funktioner är nu inbyggda, så du behöver inte distribuera en HTTP-lyssnare API-app. Mer information finns i så här gör du för att kunna [anropa din](../logic-apps/logic-apps-http-endpoint.md)Logi Kap par-slutpunkt. 

Med dessa ändringar ersätter Logic Apps funktionen `@accessKeys()` med funktionen `@listCallbackURL()` som hämtar slut punkten vid behov. Dessutom måste du nu definiera minst en utlösare i din Logic app. Om du vill `/run` arbets flödet måste du använda någon av följande typer av utlösare: `Manual`, `ApiConnectionWebhook`eller `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Anropa underordnade arbets flöden

Tidigare krävde att underordnade arbets flöden anropade arbets flödet, hämtar åtkomsttoken och klistrar in token i den Logic app-definition där du vill anropa det underordnade arbets flödet. Med det här schemat genererar Logic Apps-motorn automatiskt en SAS vid körning för det underordnade arbets flödet så att du inte behöver klistra in några hemligheter i definitionen. Här är ett exempel:

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

Underordnade arbets flöden får också fullständig åtkomst till den inkommande begäran. Du kan därför skicka parametrar i avsnittet `queries` och i `headers`-objektet. Du kan också fullständigt definiera hela `body` avsnittet.

Slutligen har underordnade arbets flöden nödvändiga ändringar. Även om du tidigare kunde anropa ett underordnat arbets flöde, måste du nu definiera en utlösare slut punkt i arbets flödet för att den överordnade ska kunna anropa. I allmänhet lägger du till en utlösare som har `Manual` typ och använder sedan utlösaren i den överordnade definitionen. Egenskapen `host` har särskilt en `triggerName` eftersom du alltid måste ange den utlösare som du anropar.

## <a name="other-changes"></a>Andra ändringar

### <a name="new-queries-property"></a>Ny egenskap för frågor

Alla åtgärds typer stöder nu en ny Indatatyp som kallas `queries`. Den här indatamängden kan vara ett strukturerat objekt, i stället för att du måste montera strängen manuellt.

### <a name="renamed-parse-function-to-json"></a>Funktionen parse () har bytt namn till JSON ()

Funktionen `parse()` har nu bytt namn till funktionen `json()` för framtida innehålls typer.

## <a name="enterprise-integration-apis"></a>Enterprise-integration-API: er

Det här schemat stöder ännu inte hanterade versioner för Enterprise-integration-API: er, till exempel AS2. Du kan dock använda befintliga distribuerade BizTalk API: er via HTTP-åtgärden. Mer information finns i "använda redan distribuerade API-appar" i [integrerings översikten](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
