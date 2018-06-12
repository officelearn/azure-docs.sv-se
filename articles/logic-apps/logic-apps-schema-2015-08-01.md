---
title: Schemat uppdateras augusti 1 2015 preview - Azure Logic Apps | Microsoft Docs
description: Skapa JSON-definitioner för Logic Apps i Azure med schemaversionen 2015-08-01-preview
author: stepsic-microsoft-com
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: 736a7cf03c7fe1e9fe976c3bcc80393bff2bada5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299876"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schema-uppdateringar för Logic Apps i Azure - preview 1 augusti 2015

Det här schemat och API-version för Logikappar i Azure innehåller viktiga förbättringar som gör logikappar mer tillförlitlig och enklare att använda:

* Den **APIApp** åtgärdstyp heter nu [ **APIConnection**](#api-connections).
* Den **Upprepa** åtgärd heter nu [ **Foreach**](#foreach).
* Den [ **HTTP-lyssnaren** API-App](#http-listener) krävs inte längre.
* Anropar underordnade arbetsflöden använder en [nya schemat](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Flytta till API-anslutningar

Den största förändringen är att du inte längre behöver distribuera API Apps i Azure-prenumerationen så att du kan använda API: er. Här är det sätt som du kan använda API: er:

* Hanterade API: er
* Dina anpassade webb-API: er

Varje sätt hanteras lite annorlunda eftersom deras hantering och värd modeller är olika. En fördel med den här modellen är du inte längre är begränsad till resurser som har distribuerats i din Azure-resursgrupp. 

### <a name="managed-apis"></a>Hanterade API: er

Microsoft hanterar vissa API: er för din räkning, till exempel Office 365, Salesforce, Twitter och FTP. Du kan använda vissa hanterade API: er som-, till exempel Bing översätta, medan andra kräver konfiguration kallas även en *anslutning*.

När du använder Office 365, måste du skapa en anslutning som innehåller din Office 365-inloggning token. Din token lagras på ett säkert sätt och uppdateras så att din logikapp alltid kan anropa API för Office 365. Om du vill ansluta till SQL- eller FTP-servern måste du skapa en anslutning med anslutningssträngen. 

I den här definitionen åtgärderna kallas `APIConnection`. Här är ett exempel på en anslutning som anropar Office 365 om du vill skicka ett e-postmeddelande:

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

Den `host` objekt är en del av indata som är unik för API-anslutningar och innehåller följande element: `api` och `connection`. Den `api` objektet anger runtime är värd för URL: en för där som hanterar API. Du kan se alla tillgängliga hanterade API: er genom att anropa `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

När du använder ett API som API kanske eller kanske inte har definierat någon *anslutningsparametrar*. Så om API: N inte definierar dessa parametrar, krävs ingen anslutning. Om API: N definierar dessa parametrar, måste du skapa en anslutning med ett angivet namn.  
Sedan kan du referera det namnet i den `connection` objekt i den `host` objekt. Anropa den här metoden om du vill skapa en anslutning i en resursgrupp:

```
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

Du kan skapa en fullständig app i en Azure Resource Manager-mall så länge interaktiv inloggning krävs inte.
Om inloggning krävs, kan du ställa in allt med hjälp av Azure Resource Manager-mallen, men du behöver gå Azure-portalen för att godkänna anslutningar. 

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

Du kan se i det här exemplet att anslutningarna är bara resurser som bor i resursgruppen. De refererar till den hanterade API: er finns i din prenumeration.

### <a name="your-custom-web-apis"></a>Dina anpassade webb-API: er

Om du använder egna API: er, inte Microsoft-hanterad de använda inbyggt **HTTP** åtgärder för att anropa dem. För en perfekt upplevelse bör du exponera en Swagger-slutpunkt för din API. Den här slutpunkten aktiverar logiken App Designer ska renderas indata och utdata för din API. Swagger, kan designern bara visa in- och utgångar som täckande JSON-objekt.

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

Om du är värd för webb-API på Azure App Service, visas Web API automatiskt i listan över åtgärder som är tillgängliga i designern. Om inte, måste du klistra in i URL-Adressen direkt. Swagger-slutpunkten måste vara oautentiserade kan användas i logik App Designer, men du kan skydda API sig själv med de metoder som har stöd för Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Anropa distribuerade API-appar med 2015-08-01-preview

Om du tidigare har distribuerat en API-App kan du anropa appen med den **HTTP** åtgärd.
Om du använder Dropbox att lista filer, till exempel din **2014-12-01-preview** version schemadefinition kan ha något som liknar:

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

Du kan nu kan nu skapa motsvarande HTTP-åtgärden som i följande exempel medan lämnar avsnittet Parametrar för app-definition logik oförändrade:

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

Gå igenom de här egenskaperna i taget:

| Åtgärdsegenskap | Beskrivning |
| --- | --- |
| `type` | `Http` Istället för `APIapp` |
| `metadata.apiDefinitionUrl` | Om du vill använda den här åtgärden i logik App Designer är metadataslutpunkten som har skapats från: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Konstrueras från: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Alltid `POST` |
| `inputs.body` | Identiskt med parametrarna API-App |
| `inputs.authentication` | Identisk med autentiseringen för API-App |

Den här metoden ska fungera för alla åtgärder i API-App. Tänk dock på att dessa tidigare API Apps stöds inte längre. Därför bör du övergå till en av de två andra föregående alternativen, hanterade API eller värd för ditt anpassade webb-API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Byta namn på 'repeat' till 'foreach'

För den föregående schemaversionen vi har tagit emot mycket kundfeedback som den **Upprepa** åtgärdsnamn var förvirrande och inte korrekt avbilda som **Upprepa** verkligen är en för varje slinga. Så vi har fått nytt namn `repeat` till `foreach`. Tidigare skulle du skriva den här åtgärden som det här exemplet:

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

Dessutom den `repeatItem()` funktion som refererar till objektet som slingan bearbetar under den aktuella iterationen nu fått nytt namn `item()`. 

### <a name="reference-outputs-from-foreach"></a>Referens för utdata från 'foreach'

För enkelhetens skull utdata från `foreach` åtgärder är inte längre kapslas in i ett objekt med namnet `repeatItems`. Dessutom med de här ändringarna i `repeatItem()`, `repeatBody()`, och `repeatOutputs()` funktioner har tagits bort.

Det, med hjälp av den tidigare `repeat` exempelvis hämta dessa utdata:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
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
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
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
         "uri": "http://www.example.com",
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
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Inbyggda HTTP-lyssnare

HTTP-lyssnaren funktioner är nu inbyggda i. Så behöver du inte längre distribuera en http-lyssnare API-App. Se [fullständig information om hur du gör logik app slutpunkten callable här](../logic-apps/logic-apps-http-endpoint.md). 

Med de här ändringarna vi tagit bort den `@accessKeys()` funktion, som vi har ersatts med den `@listCallbackURL()` funktionen för att hämta slutpunkten vid behov. Du måste också definiera minst en utlösare i din logikapp nu. Om du vill `/run` arbetsflödet, måste du ha något av dessa utlösare: `manual`, `apiConnectionWebhook`, eller `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Anropa underordnade arbetsflöden

Tidigare måste anropar underordnade arbetsflöden gå till arbetsflödet, få åtkomst-token och klistra in token i logik app definitionen där du vill anropa det underordnade arbetsflödet. Med det nya schemat genererar Logic Apps motorn automatiskt en SAS vid körning för det underordnade arbetsflödet så att du inte klistra in alla hemligheter i definitionen. Här är ett exempel:

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

En andra förbättringar är vi ger underordnade arbetsflöden fullständig åtkomst till den inkommande begäranden. Det innebär att du kan skicka parametrar i den *frågor* avsnittet och i den *huvuden* objektet och att du helt kan definiera hela texten.

Slutligen finns arbetsflöden som ändringar som krävs. När du tidigare kan anropa ett underordnat arbetsflöde direkt, måste nu du definiera en slutpunkt för utlösaren i arbetsflödet för överordnat att anropa. I allmänhet kan du lägga till en utlösare som har `manual` skriver och använder sedan den utlösaren i definitionen för överordnade. Observera den `host` egenskapen specifikt har en `triggerName` eftersom du måste alltid ange vars utlösare du anropar.

## <a name="other-changes"></a>Andra ändringar

### <a name="new-queries-property"></a>Ny 'frågor-egenskap

Alla åtgärdstyper stöder nu en ny inmatning kallas `queries`. Den här indata kan vara en strukturerad objekt, snarare än du behöver samla strängen manuellt.

### <a name="renamed-parse-function-to-json"></a>Har bytt namn till parse() av funktionen 'json()'

Vi lägger till mer innehåll av typen snart, så vi har fått nytt namn i `parse()` för `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Kommer snart: Enterprise Integration-API: er

Vi har inte hanterade versioner än av Enterprise Integration API: erna, t.ex. AS2. Under tiden kan du använda din befintliga distribuerade BizTalk-APIs via HTTP-åtgärden. Mer information, se ”med redan distribuerade API apps” i den [översikt över integrering](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
