---
title: Azure Automation-resurser i hanteringslösningar | Microsoft Docs
description: Hanteringslösningar inkluderar vanligtvis runbooks i Azure Automation för att automatisera processer, till exempel att samla in och bearbetning av övervakningsdata.  Den här artikeln beskriver hur du lägger till runbooks och deras relaterade resurser i en lösning.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7f32cc47c96f6fc8adaedef8f8ea3156ee70115
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887897"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Lägga till Azure Automation-resurser för en lösning (förhandsgranskning)
> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.   


[Hanteringslösningar]( monitoring-solutions.md) inkluderar vanligtvis runbooks i Azure Automation för att automatisera processer, till exempel att samla in och bearbetning av övervakningsdata.  Förutom runbooks, Automation-konton innehåller resurser, t.ex variabler och scheman som stöd för runbooks som används i lösningen.  Den här artikeln beskriver hur du lägger till runbooks och deras relaterade resurser i en lösning.

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure ]( monitoring-solutions-creating.md) 


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med följande information.

- Så här [skapar en lösning för]( monitoring-solutions-creating.md).
- Struktur för en [lösningsfilen]( monitoring-solutions-solution-file.md).
- Så här [skapar Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Automation-konto
Alla resurser i Azure Automation finns i en [Automation-konto](../automation/automation-security-overview.md#automation-account-overview).  Enligt beskrivningen i [logganalys-arbetsytan och Automation-konto]( monitoring-solutions.md#log-analytics-workspace-and-automation-account) Automation-kontot inte finns med i hanteringslösningen men måste finnas innan lösningen är installerad.  Lösning installationen misslyckas om det inte är tillgänglig.

Namnet på varje Automation resursen innehåller namnet på dess Automation-konto.  Detta görs i lösningen med den **accountName** parameter som i följande exempel på en runbook-resurs.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Du bör innehålla alla runbooks som används av lösningen i lösningsfilen så att de skapas när lösningen har installerats.  Du får inte innehålla innehållet i runbooken i mallen, så du bör publicera en runbook på en allmän plats där den kan nås av alla användare som installerar din lösning.

[Azure Automation-runbook](../automation/automation-runbook-types.md) resurser har en typ av **Microsoft.Automation/automationAccounts/runbooks** och har följande struktur. Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 

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


I följande tabell beskrivs egenskaperna för runbooks.

| Egenskap | Beskrivning |
|:--- |:--- |
| runbookType |Anger vilka typer av runbook. <br><br> Skript - PowerShell-skript <br>PowerShell - PowerShell-arbetsflöde <br> GraphPowerShell - grafisk PowerShell-skriptet runbook <br> GraphPowerShellWorkflow - grafisk PowerShell-arbetsflödesrunbook |
| logProgress |Anger om [vidare poster](../automation/automation-runbook-output-and-messages.md) ska genereras för runbook. |
| logVerbose |Anger om [utförliga poster](../automation/automation-runbook-output-and-messages.md) ska genereras för runbook. |
| description |Valfri beskrivning för runbook. |
| publishContentLink |Anger innehållet i runbook. <br><br>URI - Uri för att innehållet i runbook.  Det här är en .ps1-fil för PowerShell-skript och runbooks och en exporterade grafiska runbook-fil för en grafisk runbook.  <br> version - versionen av runbook för egna spårning. |


## <a name="automation-jobs"></a>Automation-jobb
När du startar en runbook i Azure Automation skapas ett automation-jobb.  Du kan lägga till en resurs för automation-jobb din lösning för att automatiskt starta en runbook när hanteringslösningen som är installerad.  Den här metoden används vanligtvis för att starta runbooks som används för inledande konfiguration av lösningen.  Om du vill starta en runbook med jämna mellanrum, skapa en [schema](#schedules) och en [Jobbschema](#job-schedules)

Resurser för jobbet har en typ av **Microsoft.Automation/automationAccounts/jobs** och har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 

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

I följande tabell beskrivs egenskaperna för automation-jobb.

| Egenskap | Beskrivning |
|:--- |:--- |
| runbook |Namn på enkel enhet med namnet på runbook att starta. |
| parameters |Entitet för varje parametervärde som krävs av runbook. |

Jobbet innehåller runbook-namn och ett parametervärde som skickas till runbook.  Jobbet ska [beror på]( monitoring-solutions-solution-file.md#resources) runbook startar sedan runbook måste skapas innan jobbet.  Om du har flera runbooks som ska startas kan du definiera deras inbördes ordning genom att ett jobb är beroende av andra jobb som ska köras första.

Namnet på en resurs för jobbet måste innehålla en GUID som tilldelas vanligtvis av en parameter.  Du kan läsa mer om GUID-parametrar i [att skapa en management lösningsfilen i Azure]( monitoring-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikat
[Azure Automation-certifikat](../automation/automation-certificates.md) har en typ av **Microsoft.Automation/automationAccounts/certificates** och har följande struktur. Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 

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



I följande tabell beskrivs egenskaperna för certifikat resurser.

| Egenskap | Beskrivning |
|:--- |:--- |
| base64Value |Base 64-värde för certifikatet. |
| tumavtrycket |Tumavtryck för certifikatet. |



## <a name="credentials"></a>Autentiseringsuppgifter
[Autentiseringsuppgifter för Azure Automation](../automation/automation-credentials.md) har en typ av **Microsoft.Automation/automationAccounts/credentials** och har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 


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

I följande tabell beskrivs egenskaperna för autentiseringsuppgifter resurser.

| Egenskap | Beskrivning |
|:--- |:--- |
| Användarnamn |Användarnamn för autentiseringsuppgifter. |
| lösenord |Lösenordet för autentiseringsuppgifterna. |


## <a name="schedules"></a>Scheman
[Azure Automation-scheman](../automation/automation-schedules.md) har en typ av **Microsoft.Automation/automationAccounts/schedules** och har de följande struktur. Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 

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

I följande tabell beskrivs egenskaperna för schemalägga resurser.

| Egenskap | Beskrivning |
|:--- |:--- |
| description |Valfri beskrivning för schemat. |
| startTime |Anger starttiden för ett schema som ett DateTime-objekt. En sträng kan tillhandahållas om det kan konverteras till en giltig DateTime. |
| IsEnabled |Anger om schemat har aktiverats. |
| interval |Typ av intervall för schemat.<br><br>dag<br>timme |
| frequency |Frekvensen som schemat ska utlösas i antal dagar eller timmar. |

Måste ha en starttid med ett större värde än den aktuella tiden.  Du kan inte ange det här värdet till en variabel eftersom du har ingen möjlighet att veta när den ska installeras.

Använd någon av följande två strategier när du använder schemalägga resurser i en lösning.

- Använda en parameter för starttiden för schemat.  Detta uppmanar användaren att ange ett värde när de installerar lösningen.  Om du har flera scheman, kan du använda en enda parameter-värdet för fler än ett.
- Skapa schemat med en runbook som startar när lösningen har installerats.  Detta tar bort behovet av användaren som anger en tid, men du kan inte innehålla schemat i din lösning så tas den bort när lösningen tas bort.


### <a name="job-schedules"></a>Jobbscheman
Jobbet schemalägga resurser länkar en runbook med ett schema.  De har en typ av **Microsoft.Automation/automationAccounts/jobSchedules** och har de följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn. 

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


I följande tabell beskrivs egenskaperna för jobbscheman.

| Egenskap | Beskrivning |
|:--- |:--- |
| namn på schema |Enskild **namn** entitet med namnet på schemat. |
| Runbook-namn  |Enskild **namn** entitet med namnet på runbook.  |



## <a name="variables"></a>Variabler
[Azure Automation-variabler](../automation/automation-variables.md) har en typ av **Microsoft.Automation/automationAccounts/variables** och har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn.

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

I följande tabell beskrivs egenskaperna för variabeln resurser.

| Egenskap | Beskrivning |
|:--- |:--- |
| description | Valfri beskrivning för variabeln. |
| isEncrypted | Anger om variabeln ska krypteras. |
| typ | Den här egenskapen har för närvarande ingen effekt.  Datatypen för variabeln bestäms av det ursprungliga värdet. |
| värde | Värdet för variabeln. |

> [!NOTE]
> Den **typen** egenskapen för närvarande har ingen effekt på variabeln som skapas.  Datatypen för variabeln bestäms av värdet.  

Om du anger det initiala värdet för variabeln, måste den konfigureras som rätt datatyp.  Följande tabell innehåller olika datatyper som är tillåtna och deras syntax.  Observera att värdena i JSON förväntas alltid stå inom citattecken med några specialtecken inom citattecken.  Till exempel ett strängvärde skulle anges med citattecken runt strängen (med hjälp av escape-tecken (\\)) när ett numeriskt värde skulle anges med en uppsättning av citattecken.

| Datatyp | Beskrivning | Exempel | Matchar |
|:--|:--|:--|:--|
| sträng   | Värdet omges av dubbla citattecken.  | ”\"Hello world\"” | ”Hello world” |
| numeriskt  | Numeriskt värde med enkla citattecken.| "64" | 64 |
| boolesk  | **SANT** eller **FALSKT** inom citattecken.  Observera att det här värdet måste vara gemener. | ”true” | true |
| datetime | Serialiserad date-värde.<br>Du kan använda cmdleten ConvertTo Json i PowerShell för att generera det här värdet för ett visst datum.<br>Exempel: get-date ”2017-5/24 13:14:57” \| ConvertTo Json | ”\\/Date(1495656897378)\\/” | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduler
Management-lösningen behöver inte definiera [global modul](../automation/automation-integration-modules.md) används av dina runbooks eftersom de alltid är tillgängliga i ditt Automation-konto.  Du behöver innehåller en resurs för alla moduler som används av dina runbooks.

[Integreringsmoduler](../automation/automation-integration-modules.md) har en typ av **Microsoft.Automation/automationAccounts/modules** och har följande struktur.  Detta inkluderar vanliga parametrarna och variablerna så att du kan kopiera och klistra in det här kodstycket i din lösningsfilen och ändra parameternamn.

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


I följande tabell beskrivs egenskaperna för modulen resurser.

| Egenskap | Beskrivning |
|:--- |:--- |
| contentLink |Anger innehållet i modulen. <br><br>URI - Uri för att innehållet i modulen.  Det här är en .ps1-fil för PowerShell-skript och runbooks och en exporterade grafiska runbook-fil för en grafisk runbook.  <br> version - versionen av modulen för egna spårning. |

Runbook ska beror på resursen modulen så att den har skapats innan runbook.

### <a name="updating-modules"></a>Uppdatera moduler
Om du uppdaterar en lösning som innehåller en runbook som använder ett schema, och den nya versionen av din lösning har en ny modul som används av denna runbook, kan runbook använda den gamla versionen av modulen.  Du bör inkludera följande runbooks i din lösning och skapar ett jobb för att köra dem före andra runbooks.  Detta säkerställer att alla moduler som är uppdaterade som krävs innan runbooks har lästs in.

* [Uppdatera ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) säkerställer att alla moduler som används av runbooks i din lösning är den senaste versionen.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) ska registrera om alla resurser som schemat så att runbooks kopplade till dem med använder de senaste modulerna.




## <a name="sample"></a>Exempel
Följande är ett exempel på en lösning som omfattar som innehåller följande resurser:

- Runbook.  Det här är en exempel-runbook som lagras i en offentlig GitHub-databas.
- Automation-jobb som startar runbook när lösningen har installerats.
- Schemat och schema för att starta runbook med jämna mellanrum.
- Certifikat.
- Autentiseringsuppgifter.
- Variabel.
- Modul.  Det här är den [OMSIngestionAPI modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) för att skriva data till logganalys. 

Används [standardlösningen parametrar]( monitoring-solutions-solution-file.md#parameters) variabler som ofta används i en lösning i stället för hardcoding värden i resursdefinitionerna.


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
            "Description": "Start time for shedule."
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
* [Lägga till en vy i lösningen]( monitoring-solutions-resources-views.md) visualisera insamlade data.
