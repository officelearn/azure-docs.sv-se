---
title: Azure Automation-resurser i hanteringslösningar | Microsoft-dokument
description: Hanteringslösningar innehåller vanligtvis runbooks i Azure Automation för att automatisera processer som att samla in och bearbeta övervakningsdata.  I den här artikeln beskrivs hur du inkluderar runbooks och deras relaterade resurser i en lösning.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ef9f27546e9db95d5a41769e1b5bc7bc0c2f851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663070"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Lägga till Azure Automation-resurser i en hanteringslösning (förhandsversion)
> [!NOTE]
> Detta är preliminär dokumentation för att skapa hanteringslösningar som för närvarande förhandsgranskas. Alla scheman som beskrivs nedan kan komma att ändras.   


[Hanteringslösningar]( solutions.md) innehåller vanligtvis runbooks i Azure Automation för att automatisera processer som att samla in och bearbeta övervakningsdata.  Förutom runbooks innehåller Automation-konton tillgångar som variabler och scheman som stöder runbooks som används i lösningen.  I den här artikeln beskrivs hur du inkluderar runbooks och deras relaterade resurser i en lösning.

> [!NOTE]
> I exemplen i den här artikeln används parametrar och variabler som antingen krävs eller är gemensamma för hanteringslösningar och beskrivs i [Design och bygg en hanteringslösning i Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan är bekant med följande information.

- Så här [skapar du en hanteringslösning]( solutions-creating.md).
- Strukturen för en [lösningsfil]( solutions-solution-file.md).
- Så här [skapar du Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>Automation-konto
Alla resurser i Azure Automation finns i ett [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview).  Som beskrivs i [Log Analytics arbetsyta och Automation-konto]( solutions.md#log-analytics-workspace-and-automation-account) ingår inte Automation-kontot i hanteringslösningen utan måste finnas innan lösningen installeras.  Om den inte är tillgänglig misslyckas installationen av lösningen.

Namnet på varje Automation-resurs innehåller namnet på dess Automation-konto.  Detta görs i lösningen med **parametern accountName** som i följande exempel på en runbook-resurs.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Du bör inkludera alla runbooks som används av lösningen i lösningsfilen så att de skapas när lösningen installeras.  Du kan dock inte innehålla runbookens brödtext i mallen, så du bör publicera runbooken på en offentlig plats där du kan komma åt den av alla användare som installerar din lösning.

[Azure Automation-runbook-resurser](../../automation/automation-runbook-types.md) har en typ av **Microsoft.Automation/automationAccounts/runbooks** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Egenskaperna för runbooks beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| runbookType (runbookType) |Anger typerna av runbooken. <br><br> Script - PowerShell-skript <br>PowerShell - PowerShell-arbetsflöde <br> GraphPowerShell - Grafisk PowerShell-skriptkörningsbok <br> GraphPowerShellArbetsflöde - Grafisk PowerShell-arbetsflödeskörningsbok |
| logProgress |Anger om [förloppsposter](../../automation/automation-runbook-output-and-messages.md) ska genereras för runbooken. |
| logVerbose |Anger om [utförliga poster](../../automation/automation-runbook-output-and-messages.md) ska genereras för runbooken. |
| description |Valfri beskrivning för runbooken. |
| publiceraContentLink |Anger innehållet i runbooken. <br><br>uri - Uri till innehållet i runbook.  Detta blir en PS1-fil för PowerShell- och Script-runbooks och en exporterad grafisk runbook-fil för en Graph-runbook.  <br> version - Version av runbook för din egen spårning. |


## <a name="automation-jobs"></a>Automation jobb
När du startar en runbook i Azure Automation skapas ett automatiseringsjobb.  Du kan lägga till en automatiseringsjobbresurs i din lösning för att automatiskt starta en runbook när hanteringslösningen är installerad.  Den här metoden används vanligtvis för att starta runbooks som används för den första konfigurationen av lösningen.  Om du vill starta en runbook med jämna mellanrum skapar du ett [schema](#schedules) och ett [jobbschema](#job-schedules)

Jobbresurser har en typ av **Microsoft.Automation/automationKonton/jobb** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Egenskaperna för automatiseringsjobb beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Runbook |Entiteten med ett namn med namnet på runbooken som ska startas. |
| parameters |Entitet för varje parametervärde som krävs av runbooken. |

Jobbet innehåller runbooknamnet och eventuella parametervärden som ska skickas till runbooken.  Jobbet bör [bero på]( solutions-solution-file.md#resources) den runbook som den startar eftersom runbook måste skapas före jobbet.  Om du har flera runbooks som ska startas kan du definiera deras ordning genom att ha ett jobb beroende av andra jobb som ska köras först.

Namnet på en projektresurs måste innehålla ett GUID som vanligtvis tilldelas av en parameter.  Du kan läsa mer om GUID-parametrar i [Skapa en hanteringslösningsfil i Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikat
[Azure Automation-certifikat](../../automation/automation-certificates.md) har en typ av **Microsoft.Automation/automationAccounts/certifikat** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



Egenskaperna för certifikatresurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| bas64Värde |Bas 64-värde för certifikatet. |
| Stämpel |Tumavtryck för certifikatet. |



## <a name="credentials"></a>Autentiseringsuppgifter
[Azure Automation-autentiseringsuppgifter](../../automation/automation-credentials.md) har en typ av **Microsoft.Automation/automationAccounts/credentials** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

Egenskaperna för Autentiseringsresurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| userName |Användarnamn för autentiseringsuppgifterna. |
| password |Lösenord för autentiseringsuppgifterna. |


## <a name="schedules"></a>Scheman
[Azure Automation-scheman](../../automation/automation-schedules.md) har en typ av **Microsoft.Automation/automationAccounts/scheman** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Egenskaperna för schemaresurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| description |Valfri beskrivning för schemat. |
| startTime |Anger starttiden för ett schema som ett DateTime-objekt. En sträng kan tillhandahållas om den kan konverteras till en giltig DateTime. |
| ärEnabled |Anger om schemat är aktiverat. |
| interval |Typ av intervall för schemat.<br><br>day<br>timme |
| frequency |Frekvens att schemat ska avfyras i antal dagar eller timmar. |

Scheman måste ha en starttid med ett värde som är större än den aktuella tiden.  Du kan inte ange det här värdet med en variabel eftersom du inte skulle ha någon möjlighet att veta när det kommer att installeras.

Använd en av följande två strategier när du använder schemaresurser i en lösning.

- Använd en parameter för schemats starttid.  Detta kommer att uppmana användaren att ange ett värde när de installerar lösningen.  Om du har flera scheman kan du använda ett enda parametervärde för mer än ett av dem.
- Skapa scheman med hjälp av en runbook som startar när lösningen installeras.  Detta tar bort användarens krav på att ange en tid, men du kan inte innehålla schemat i din lösning så att det tas bort när lösningen tas bort.


### <a name="job-schedules"></a>Jobbscheman
Jobbschemaresurser länkar en runbook med ett schema.  De har en typ av **Microsoft.Automation/automationKonto/jobSchedules** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


Egenskaperna för jobbscheman beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| schemanamn |Entiteten med **ett namn** med namnet på schemat. |
| runbook namn  |Entiteten med **ett namn** med namnet på runbooken.  |



## <a name="variables"></a>Variabler
[Azure Automation-variabler](../../automation/automation-variables.md) har en typ av **Microsoft.Automation/automationAccounts/variabler** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

Egenskaperna för variabla resurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| description | Valfri beskrivning för variabeln. |
| ärDekrypterad | Anger om variabeln ska krypteras. |
| typ | Den här egenskapen har för närvarande ingen effekt.  Datatypen för variabeln bestäms av det ursprungliga värdet. |
| värde | Värde för variabeln. |

> [!NOTE]
> **Typegenskapen** har för närvarande ingen effekt på variabeln som skapas.  Datatypen för variabeln bestäms av värdet.  

Om du anger det ursprungliga värdet för variabeln måste det konfigureras som rätt datatyp.  I följande tabell finns de olika datatyper som är tillåtna och deras syntax.  Observera att värden i JSON alltid förväntas omges av citattecken med eventuella specialtecken inom citattecken.  Ett strängvärde anges till exempel av citattecken runt strängen (med escape-tecknet (\\)) medan ett numeriskt värde skulle anges med en uppsättning citattecken.

| Datatyp | Beskrivning | Exempel | Bestämmer åt att |
|:--|:--|:--|:--|
| sträng   | Bifoga värde i dubbla citattecken.  | "\"Hello\"world " | "Hej världen" |
| numeric  | Numeriskt värde med enstaka citattecken.| "64" | 64 |
| boolean  | **sant** eller **falskt** inom citationstecken.  Observera att det här värdet måste vara gemen. | "Sant" | true |
| datetime | Serialiserat datumvärde.<br>Du kan använda cmdleten ConvertTo-Json i PowerShell för att generera det här värdet för ett visst datum.<br>Exempel: get-date "2017-05-24 13:14:57" \| ConvertTo-Json | "\\/Datum(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduler
Din hanteringslösning behöver inte definiera [globala moduler](../../automation/automation-integration-modules.md) som används av dina runbooks eftersom de alltid är tillgängliga i ditt Automation-konto.  Du måste inkludera en resurs för alla andra moduler som används av dina runbooks.

[Integrationsmoduler](../../automation/automation-integration-modules.md) har en typ av **Microsoft.Automation/automationAccounts/modules** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodavsnittet i lösningsfilen och ändra parameternamnen.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


Egenskaperna för modulresurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Contentlink |Anger innehållet i modulen. <br><br>uri - Uri till innehållet i modulen.  Detta blir en PS1-fil för PowerShell- och Script-runbooks och en exporterad grafisk runbook-fil för en Graph-runbook.  <br> version - Version av modulen för din egen spårning. |

Runbooken bör vara beroende av modulresursen för att säkerställa att den skapas före runbooken.

### <a name="updating-modules"></a>Uppdatera moduler
Om du uppdaterar en hanteringslösning som innehåller en runbook som använder ett schema och den nya versionen av din lösning har en ny modul som används av den runbooken, kan runbooken använda den gamla versionen av modulen.  Du bör inkludera följande runbooks i din lösning och skapa ett jobb för att köra dem innan andra runbooks.  Detta säkerställer att alla moduler uppdateras efter behov innan runbooks läses in.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) kommer att se till att alla moduler som används av runbooks i din lösning är den senaste versionen.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) kommer att registrera alla schemaresurser för att säkerställa att runbooks som är länkade till dem med de senaste modulerna.




## <a name="sample"></a>Exempel
Följande är ett exempel på en lösning som innehåller följande resurser:

- Runbook.  Det här är en exempelkörningsbok som lagras i en offentlig GitHub-databas.
- Automation-jobb som startar runbooken när lösningen installeras.
- Schema och jobbschema för att starta runbooken med jämna mellanrum.
- Certifikat.
- Referens.
- Variabel.
- Modul.  Detta är [OMSIngestionAPI-modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) för att skriva data till Log Analytics. 

I exemplet används [standardvariabler för lösningsparametrar]( solutions-solution-file.md#parameters) som ofta används i en lösning i motsats till hårdkodningsvärden i resursdefinitionerna.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Nästa steg
* [Lägg till en vy i din lösning]( solutions-resources-views.md) för att visualisera insamlade data.
