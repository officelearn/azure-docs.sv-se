---
title: Schemat uppdateras augusti 1 2015 preview - Azure Logic Apps | Microsoft Docs
description: "Skapa JSON-definitioner för Logic Apps i Azure med schemaversionen 2015-08-01-preview"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: LADocs; stepsic
ms.openlocfilehash: 35d7a56d5607dcc18a4407c65b92962d3d0dcd1d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schema-uppdateringar för Logic Apps i Azure - preview 1 augusti 2015

Den här nya schemat och API-version för Logikappar i Azure innehåller viktiga förbättringar som gör logikappar mer tillförlitlig och enklare att använda:

*   Den **APIApp** åtgärdstyp uppdateras till en ny [ **APIConnection** ](#api-connections) åtgärdstyp.
*   **Upprepa** ändras till [ **Foreach**](#foreach).
*   Den [ **HTTP-lyssnaren** API-App](#http-listener) krävs inte längre.
*   Anropar underordnade arbetsflöden använder en [nya schemat](#child-workflows).

<a name="api-connections"></a>
## <a name="move-to-api-connections"></a>Flytta till API-anslutningar

Den största förändringen är att du inte längre behöver distribuera API Apps i Azure-prenumerationen så att du kan använda API: er. Här är det sätt som du kan använda API: er:

* Hanterade API: er
* Dina anpassade webb-API: er

Varje sätt hanteras lite annorlunda eftersom deras hantering och värd modeller är olika. En fördel med den här modellen är du inte längre är begränsad till resurser som har distribuerats i din Azure-resursgrupp. 

### <a name="managed-apis"></a>Hanterade API: er

Microsoft hanterar vissa API: er för din räkning, till exempel Office 365, Salesforce, Twitter och FTP. Du kan använda vissa hanterade API: er som-är, till exempel Bing översätta, medan andra kräver konfiguration. Den här konfigurationen kallas en *anslutning*.

När du använder Office 365, måste du skapa en anslutning som innehåller din Office 365-inloggning token. Denna token lagras på ett säkert sätt och uppdateras så att din logikapp alltid kan anropa API för Office 365. Om du vill ansluta till SQL- eller FTP-server måste du skapa en anslutning med anslutningssträngen. 

I den här definitionen åtgärderna kallas `APIConnection`. Här är ett exempel på en anslutning som anropar Office 365 om du vill skicka ett e-postmeddelande:

```
{
    "actions": {
        "Send_Email": {
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
                "method": "post",
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

Den `host` objekt som är del av indata som är unik för API-anslutningar och innehåller dra delar: `api` och `connection`.

Den `api` har körningsmiljön URL där som hanterad API finns. Du kan se alla tillgängliga hanterade API: er genom att anropa `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

När du använder ett API API: et kanske eller kanske inte har någon *anslutningsparametrar* definieras. Om API: T inte är ingen *anslutning* krävs. Om API: et matchar, måste du skapa en anslutning. Den skapade anslutningen har namn som du väljer. Sedan kan du referera namn i den `connection` objekt i den `host` objekt. Anropa för att skapa en anslutning i en resursgrupp:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Med följande text:

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Distribuera hanterade API: er i en Azure Resource Manager-mall

Du kan skapa en fullständig program i en Azure Resource Manager-mall så länge interaktiv inloggning krävs inte.
Om inloggning krävs, kan du ställa in allt med hjälp av Azure Resource Manager-mallen, men du fortfarande behöver Besök portalen för att godkänna anslutningar. 

```
    "resources": [{
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
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
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
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
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
            }
        }
    }]
```

Du kan se i det här exemplet att anslutningarna är bara resurser som bor i resursgruppen. De refererar till den hanterade API: er finns i din prenumeration.

### <a name="your-custom-web-apis"></a>Dina anpassade webb-API: er

Om du använder egna API: er, inte Microsoft-hanterad de använda inbyggt **HTTP** åtgärder för att anropa dem. För en perfekt upplevelse bör du exponera en Swagger-slutpunkt för din API. Den här slutpunkten aktiverar logiken App Designer ska renderas indata och utdata för din API. Swagger, kan designern bara visa in- och utgångar som täckande JSON-objekt.

Här är ett exempel som visar den nya `metadata.apiDefinitionUrl` egenskapen:

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Om du är värd för webb-API på Azure App Service, visas Web API automatiskt i listan över åtgärder som är tillgängliga i designern. Om inte, måste du klistra in i URL-Adressen direkt. Swagger-slutpunkten måste vara oautentiserade kan användas i logik App Designer, men du kan skydda API sig själv med de metoder som har stöd för Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Anropa distribuerade API-appar med 2015-08-01-preview

Om du tidigare har distribuerat en API-App kan du anropa appen med den **HTTP** åtgärd.

Om du använder Dropbox att lista filer, till exempel din **2014-12-01-preview** version schemadefinition kan ha något som liknar:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
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
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Du kan skapa motsvarande HTTP-åtgärden som exempel, när avsnittet parametrar av logik app definition förblir oförändrad:

```
{
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
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Gå igenom de här egenskaperna i taget:

| Åtgärdsegenskap | Beskrivning |
| --- | --- |
| `type` |`Http`Istället för`APIapp` |
| `metadata.apiDefinitionUrl` |Om du vill använda den här åtgärden i logik App Designer är metadataslutpunkten som har skapats från:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Konstrueras från:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Alltid`POST` |
| `inputs.body` |Identiskt med parametrarna API-App |
| `inputs.authentication` |Identisk med autentiseringen för API-App |

Den här metoden ska fungera för alla åtgärder i API-App. Tänk dock på att dessa tidigare API Apps stöds inte längre. Därför bör du övergå till en av de två andra föregående alternativen, hanterade API eller värd för ditt anpassade webb-API.

<a name="foreach"></a>
## <a name="renamed-repeat-to-foreach"></a>Byta namn på 'repeat' till 'foreach'

För den föregående schemaversionen vi har tagit emot mycket kundfeedback som **Upprepa** var förvirrande och inte korrekt avbilda som **Upprepa** verkligen är en för varje slinga. Därför har vi bytt namn `repeat` till `foreach`. Till exempel tidigare du skulle kunna skriva:

```
{
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
}
```

Nu skulle du skriva:

```
{
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
}
```

Funktionen `@repeatItem()` användes tidigare för att referera till det aktuella objektet som hävdade över. Den här funktionen förenklas nu till `@item()`. 

### <a name="reference-outputs-from-foreach"></a>Referens för utdata från 'foreach'

För enkelhetens skull utdata från `foreach` åtgärder som inte är omslutna i ett objekt som kallas `repeatItems`. När utdata från den tidigare `repeat` exempel var:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Nu är dessa utdata:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Tidigare få till i brödtexten för åtgärden när du refererar till dessa utdata:

```
{
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
}
```

Nu kan du göra i stället:

```
{
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
}
```

Med de här ändringarna, funktionerna `@repeatItem()`, `@repeatBody()`, och `@repeatOutputs()` tas bort.

<a name="http-listener"></a>
## <a name="native-http-listener"></a>Inbyggda HTTP-lyssnare

HTTP-lyssnaren funktioner är nu inbyggda i. Så behöver du inte längre distribuera en http-lyssnare API-App. Se [fullständig information om hur du gör logik app slutpunkten callable här](../logic-apps/logic-apps-http-endpoint.md). 

Med de här ändringarna vi tagit bort den `@accessKeys()` funktion, som vi har ersatts med den `@listCallbackURL()` funktionen för att hämta slutpunkten vid behov. Du måste också definiera minst en utlösare i din logikapp nu. Om du vill `/run` arbetsflödet, måste du ha något av dessa utlösare: `manual`, `apiConnectionWebhook`, eller `httpWebhook`.

<a name="child-workflows"></a>
## <a name="call-child-workflows"></a>Anropa underordnade arbetsflöden

Tidigare måste anropar underordnade arbetsflöden gå till arbetsflödet, få åtkomst-token och klistra in token i logik app definitionen där du vill anropa det underordnade arbetsflödet. Med det nya schemat genererar Logic Apps motorn automatiskt en SAS vid körning för det underordnade arbetsflödet så att du inte klistra in alla hemligheter i definitionen. Här är ett exempel:

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
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
