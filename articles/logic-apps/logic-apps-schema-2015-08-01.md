---
title: Schemauppdateringar för augusti-1-2015 preview – Azure Logic Apps | Microsoft Docs
description: Uppdaterade schemaversionen 2015-08-01-preview för logikappsdefinitioner i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: 92f522c72f69218e55b1ee4cfff74511a30288b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904549"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schemauppdateringar för Azure Logic Apps - 1 augusti 2015 preview

Det här schemat och API-version för Azure Logic Apps innehåller viktiga förbättringar som gör logikappar mer tillförlitlig och enklare att använda:

* Den **APIApp** åtgärdstyp heter nu [ **APIConnection**](#api-connections).
* Den **Upprepa** åtgärd heter nu [ **Foreach**](#foreach).
* Den [ **HTTP-lyssnare** API-App](#http-listener) inte längre behövs.
* Anropa underordnade arbetsflöden använder en [nya schemat](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Flytta till API-anslutningar

Den största förändringen är att du inte längre behöver distribuera API-appar i din Azure-prenumeration så att du kan använda API: er. Här är hur du kan använda API: er:

* Hanterade API: er
* Dina anpassade webb-API: er

Varje sätt hanteras lite annorlunda eftersom deras hantering och som är värd för modeller är olika. En fördel med den här modellen är du inte längre är begränsad till resurser som distribueras i Azure-resursgruppen. 

### <a name="managed-apis"></a>Hanterade API: er

Microsoft hanterar vissa API: er för din räkning, till exempel Office 365, Salesforce, Twitter och FTP. Du kan använda vissa hanterade API: er som –, till exempel Bing översätta, medan andra kräver konfiguration kallas även en *anslutning*.

När du använder Office 365, måste du skapa en anslutning som innehåller din Office 365-inloggningen token. Din token lagras på ett säkert sätt och uppdateras så att logikappen alltid kan anropa API: et för Office 365. Om du vill ansluta till SQL- eller FTP-servern måste du skapa en anslutning med anslutningssträngen. 

I den här definitionen åtgärderna kallas `APIConnection`. Här är ett exempel på en anslutning som anropar Office 365 för att skicka ett e-postmeddelande:

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

Den `host` objektet är en del av indata som är unik för API-anslutningar och innehåller dessa delar: `api` och `connection`. Den `api` objektet anger runtime som är värd för URL: en för där som hanterad API. Du kan se alla tillgängliga hanterade API: erna genom att anropa den här metoden:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

När du använder ett API som API: et kan eller inte har definierat någon *anslutningsparametrar*. Så om API: et inte definierar dessa parametrar, krävs ingen anslutning. Om API: et definierar dessa parametrar, måste du skapa en anslutning med ett visst angivet namn.  
Du sedan referera till det här namnet i den `connection` objekt i den `host` objekt. Anropa den här metoden om du vill skapa en anslutning i en resursgrupp:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Distribuera hanterade API: er i en Azure Resource Manager-mall

När interaktiv inloggning krävs inte, kan du skapa en fullständig app med hjälp av Resource Manager-mall.
Om inloggning krävs, du kan fortfarande använda Resource Manager-mall, men du måste godkänna anslutningar via Azure portal. 

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

Du kan se i det här exemplet att anslutningarna är bara resurser som bor i resursgruppen. De refererar till den hanterade API: er tillgängliga för dig i din prenumeration.

### <a name="your-custom-web-apis"></a>Dina anpassade webb-API: er

Om du använder egna API: er i stället för Microsoft-hanterade som kan använda inbyggda **HTTP** åtgärden att anropa API: er. Helst bör du ange en Swagger-slutpunkt för ditt API. Den här slutpunkten kan visa in- och utdata för dina API: er för Logic App Designer. En Swagger-slutpunkt, kan designern bara visa indata och utdata som täckande JSON-objekt.

Här är ett exempel som visar den nya `metadata.apiDefinitionUrl` egenskapen:

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

Om du vara värd för ditt webb-API på Azure App Service, visas ditt webb-API automatiskt i listan över tillgängliga i designern. Annars kan du behöva klistra in URL: en direkt. Swagger-slutpunkten måste vara oautentiserade kan användas i Logic App Designer, men du kan skydda själva API med de metoder som har stöd för Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Anropa distribuerade API-appar med 2015-08-01-preview

Om du tidigare har distribuerat en API-App som du kan anropa appen med den **HTTP** åtgärd.
Om du använder Dropbox för att lista filer, till exempel din **2014-12-01-preview** version schemadefinitionen kan ha något som liknar:

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

Nu kan du nu skapa en liknande HTTP-åtgärd och låta logikappsdefinitionen `parameters` avsnittet oförändrade, till exempel:

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

Gå igenom de här egenskaperna en i taget:

| Egenskapen för åtgärden | Beskrivning |
| --- | --- |
| `type` | `Http` Istället för `APIapp` |
| `metadata.apiDefinitionUrl` | Om du vill använda den här åtgärden i Logic App Designer är metadataslutpunkt som konstrueras utifrån: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Konstrueras från: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Alltid `POST` |
| `inputs.body` | Samma som parametrar för API-App |
| `inputs.authentication` | Samma som API-App-autentisering |

Den här metoden bör fungera för alla åtgärder som API-App. Kom dock ihåg att dessa API-appar som tidigare inte längre stöds. Så bör du flytta till en av de två andra föregående alternativen, en hanterad API eller som är värd för dina anpassade webb-API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Namn 'Upprepa ”till” foreach ”

För den föregående schemaversionen vi fått mycket feedback från kunder som den **Upprepa** åtgärdsnamn var förvirrande och korrekt att avläsa som **Upprepa** verkligen är en för varje slinga. Så vi har fått nytt namn `repeat` till `foreach`. Tidigare skriva du den här åtgärden som i följande exempel:

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

Nu kan du skriva den här versionen i stället:

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

Dessutom den `repeatItem()` som refererar till det objekt som loopen bearbetas under den aktuella iterationen nu fått nytt namn `item()`. 

### <a name="reference-outputs-from-foreach"></a>Referens för utdata från ”foreach”

För enkelhetens skull utdata från `foreach` åtgärder är inte längre och är inneslutna i ett objekt med namnet `repeatItems`. Dessutom med de här ändringarna i `repeatItem()`, `repeatBody()`, och `repeatOutputs()` funktioner tas bort.

Det, med hjälp av den tidigare `repeat` exempel hämta dessa utdata:

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

Nu får du dessa utdata i stället:

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

Tidigare att hämta den `body` från åtgärden när du refererar till dessa utdata:

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

HTTP-lyssnaren-funktioner är nu inbyggda, så att du inte behöver att distribuera en HTTP-lyssnaren API-App. Mer information lär du dig hur du [gör logic app slutpunkten anropningsbara](../logic-apps/logic-apps-http-endpoint.md). 

Med dessa ändringar, Logic Apps ersätter den `@accessKeys()` fungerar med den `@listCallbackURL()` funktion som hämtar slutpunkten när det behövs. Dessutom måste nu du definiera minst en utlösare i din logikapp. Om du vill `/run` arbetsflödet, som du behöver använda någon av följande typer av utlösare: `Manual`, `ApiConnectionWebhook`, eller `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Anropa underordnade arbetsflöden

Tidigare tvungna anropa underordnade arbetsflöden att arbetsflödet, hämta åtkomsttoken och klistra in token i logikappens definition där du vill anropa det underordnade arbetsflödet. Med det här schemat genererar Logic Apps-motorn automatiskt en SAS vid körning för det underordnade arbetsflödet så att du inte behöver att klistra in hemligheter i definitionen. Här är ett exempel:

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

Underordnade arbetsflöden får även fullständig åtkomst till den inkommande begäranden. Så du kan skicka parametrar i den `queries` avsnittet och i den `headers` objekt. Du kan också helt definiera hela `body` avsnittet.

Slutligen har underordnade arbetsflöden dessa nödvändiga ändringar. Även om du kunde tidigare och direkt anropar ett underordnat arbetsflöde, måste du definiera en slutpunkt för utlösaren i arbetsflödet för överordnat att anropa nu. I allmänhet bör du skulle lägga till en utlösare som har `Manual` skriver och använder sedan den utlösaren i definitionen av överordnad. Den `host` egenskapen specifikt har en `triggerName` eftersom du måste alltid ange utlösaren anropas.

## <a name="other-changes"></a>Andra ändringar

### <a name="new-queries-property"></a>Ny 'frågor, egenskap

En ny indata som kallas nu stöd för alla åtgärdstyper `queries`. Denna indata kan vara ett strukturobjekt snarare än du behöver samla ihop strängen manuellt.

### <a name="renamed-parse-function-to-json"></a>Omdöpt parse() funktionen 'json()'

Den `parse()` funktionen nu fått nytt namn i `json()` funktionen för framtida innehållstyper.

## <a name="enterprise-integration-apis"></a>API: er för Enterprise-Integration

Det här schemat stöder inte ännu hanterade versioner för Enterprise-Integration API: er, till exempel AS2. Du kan dock använda befintliga distribuerade BizTalk APIs via HTTP-åtgärden. Mer information finns i ”använda dina redan distribuerade API-appar” i den [integrering översikten](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
