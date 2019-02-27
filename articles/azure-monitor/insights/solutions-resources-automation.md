---
title: Azure Automation-resurser i lösningar för hantering av | Microsoft Docs
description: Lösningar för hantering av inkluderar vanligtvis runbooks i Azure Automation för att automatisera processer, till exempel samla in och bearbetning av övervakningsdata.  Den här artikeln beskriver hur du inkluderar runbooks och deras relaterade resurser i en lösning.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c24d8bd382dc57a8a354b61252d7a5e16640436f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864659"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Att lägga till Azure Automation-resurser i en lösning för hantering (förhandsversion)
> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.   


[Lösningar för hantering av]( solutions.md) inkluderar vanligtvis runbooks i Azure Automation för att automatisera processer, till exempel samla in och bearbetning av övervakningsdata.  Automation-konton innehåller förutom runbooks, tillgångar som variabler och scheman som har stöd för runbooks som används i lösningen.  Den här artikeln beskriver hur du inkluderar runbooks och deras relaterade resurser i en lösning.

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med följande information.

- Så här [skapa en lösning för]( solutions-creating.md).
- Strukturen för en [lösningsfilen]( solutions-solution-file.md).
- Så här [skapar Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Automation-konto
Alla resurser i Azure Automation finns i en [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview).  Mer information finns i [Log Analytics-arbetsytan och Automation-kontot]( solutions.md#log-analytics-workspace-and-automation-account) Automation-kontot ingår inte i hanteringslösningen men måste finnas innan lösningen är installerad.  Lösningen installationen misslyckas om den inte är tillgänglig.

Namnet på varje Automation-resursen innehåller namnet på dess Automation-konto.  Detta görs i lösningen med den **accountName** parameter som i följande exempel för en runbook-resurs.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Du bör innehålla alla runbooks som används av lösningen i lösningsfilen så att de skapas när lösningen har installerats.  Du får inte innehålla brödtexten i en runbook i mallen, så att du ska publicera runbook på en allmän plats där den kan nås av alla användare som installerar din lösning.

[Azure Automation-runbook](../../automation/automation-runbook-types.md) resurser har en typ av **Microsoft.Automation/automationAccounts/runbooks** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen. 

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

| Egenskap  | Beskrivning |
|:--- |:--- |
| runbookType |Anger vilka typer av runbook. <br><br> Skript - PowerShell-skript <br>PowerShell – PowerShell-arbetsflöde <br> GraphPowerShell - grafiska PowerShell script-runbook <br> GraphPowerShellWorkflow - grafiskt PowerShell-Arbetsflödesbaserade runbook |
| logProgress |Anger om [vidare poster](../../automation/automation-runbook-output-and-messages.md) ska genereras för runbook. |
| logVerbose |Anger om [utförliga poster](../../automation/automation-runbook-output-and-messages.md) ska genereras för runbook. |
| beskrivning |Valfri beskrivning för runbook. |
| publishContentLink |Anger innehållet i runbooken. <br><br>URI - Uri för att innehållet i runbooken.  Det här är en .ps1-fil för runbooks med PowerShell och skript och en exporterade grafiska runbook-fil för en runbook i diagrammet.  <br> version - versionen av runbooken för dina egna spårning. |


## <a name="automation-jobs"></a>Automation-jobb
När du startar en runbook i Azure Automation, skapar ett automation-jobb.  Du kan lägga till en resurs för automation-jobb i lösningen att automatiskt starta en runbook när lösningen installeras.  Den här metoden används vanligtvis för att starta runbooks som används för inledande konfiguration av lösningen.  Om du vill starta en runbook med jämna mellanrum, skapa en [schema](#schedules) och en [Jobbschema](#job-schedules)

Jobbresurser har en typ av **Microsoft.Automation/automationAccounts/jobs** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen. 

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

| Egenskap  | Beskrivning |
|:--- |:--- |
| Runbook |Namn på enskild entitet med namnet på runbook att starta. |
| parameters |Entitet för varje parametervärde som krävs av runbook. |

Jobbet innehåller namn på runbook och alla parametervärden som ska skickas till runbooken.  Jobbet ska [beror på]( solutions-solution-file.md#resources) runbooken som startar sedan runbook måste skapas innan jobbet.  Om du har flera runbooks som ska startas kan du definiera deras inbördes ordning genom att använda ett jobb som är beroende av andra jobb som ska köras första.

Namnet på en resurs för jobbet måste innehålla ett GUID som tilldelas vanligtvis av en parameter.  Du kan läsa mer om GUID-parametrar i [skapar en fil för lösningen i Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikat
[Azure Automation-certifikat](../../automation/automation-certificates.md) har en typ av **Microsoft.Automation/automationAccounts/certificates** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen. 

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



Egenskaper för certifikat resurser beskrivs i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| base64Value |Base 64-värde för certifikatet. |
| thumbprint |Tumavtryck för certifikatet. |



## <a name="credentials"></a>Autentiseringsuppgifter
[Autentiseringsuppgifter för Azure Automation](../../automation/automation-credentials.md) har en typ av **Microsoft.Automation/automationAccounts/credentials** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen. 


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

Egenskaper för autentiseringsuppgifter resurser beskrivs i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Användarnamn |Användarnamn för autentiseringsuppgifter. |
| lösenord |Lösenordet för autentiseringsuppgifterna. |


## <a name="schedules"></a>Scheman
[Azure Automation-scheman](../../automation/automation-schedules.md) har en typ av **Microsoft.Automation/automationAccounts/schedules** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen. 

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

Egenskaper för schema resurser beskrivs i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| beskrivning |Valfri beskrivning för schemat. |
| startTime |Anger starttiden för ett schema som ett datum/tid-objekt. En sträng kan anges om den kan konverteras till en giltig DateTime. |
| isEnabled |Anger om schemat är aktiverad. |
| interval |Typ av intervall för schemat.<br><br>dag<br>timme |
| frequency |Frekvensen som schemat ska utlösas i antal dagar eller timmar. |

Måste ha en starttid med ett värde som är större än den aktuella tiden.  Du kan inte ange det här värdet med en variabel eftersom du behöver inget sätt att veta när det kommer att installeras.

Använd någon av följande två strategier när du använder schemat resurser i en lösning.

- Använd en parameter för starttiden för schemat.  Detta uppmanar användaren att ange ett värde när de installerar lösningen.  Om du har flera scheman, kan du använda en enda parameter-värdet för mer än en av dem.
- Skapa scheman med hjälp av en runbook som startar när lösningen är installerad.  Detta eliminerar behovet av användaren som anger en tid, men du får inte innehålla schemat i din lösning så att den tas bort när lösningen har tagits bort.


### <a name="job-schedules"></a>Jobbscheman
Schema för jobbresurser länkar en runbook med ett schema.  De har en typ av **Microsoft.Automation/automationAccounts/jobSchedules** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen. 

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


I följande tabell beskrivs egenskaperna för scheman för datalagerjobb.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Namn på schema |Enkel **namn** entitet med namnet på schemat. |
| runbook-namn  |Enkel **namn** entitet med namnet på runbooken.  |



## <a name="variables"></a>Variabler
[Azure Automation-variabler](../../automation/automation-variables.md) har en typ av **Microsoft.Automation/automationAccounts/variables** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen.

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

| Egenskap  | Beskrivning |
|:--- |:--- |
| beskrivning | Valfri beskrivning för variabeln. |
| isEncrypted | Anger om variabeln ska vara krypterat. |
| typ | Den här egenskapen har för närvarande ingen effekt.  Datatypen för variabeln bestäms av det inledande värdet. |
| värde | Värdet för variabeln. |

> [!NOTE]
> Den **typ** egenskapen har ingen effekt på variabeln som skapas.  Datatypen för variabeln bestäms av värdet.  

Om du ställer in det initiala värdet för variabeln måste den konfigureras som rätt datatyp.  Följande tabell innehåller de olika datatyperna som är tillåtna och deras syntax.  Observera att värdena i JSON förväntas alltid stå inom citattecken med några specialtecken inom citattecken.  Till exempel ett strängvärde skulle anges med citattecken runt strängen (med hjälp av escape-tecknet (\\)) när ett numeriskt värde anges med en uppsättning citattecken.

| Datatyp | Beskrivning | Exempel | Matchar |
|:--|:--|:--|:--|
| sträng   | Ange värdet inom dubbla citattecken.  | ”\"Hello world\"” | ”Hello world” |
| numeriskt  | Numeriskt värde med enkla citattecken.| "64" | 64 |
| boolesk  | **SANT** eller **FALSKT** inom citattecken.  Observera att det här värdet måste vara gemener. | ”true” | true |
| datetime | Serialiserade datumvärdet.<br>Du kan använda cmdleten ConvertTo-Json i PowerShell för att skapa det här värdet för ett visst datum.<br>Exempel: get-date ”5/24/2017 13:14:57” \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduler
Din lösning inte behöver definiera [globala modulerna](../../automation/automation-integration-modules.md) används av dina runbooks eftersom de kommer alltid att vara tillgängliga i ditt Automation-konto.  Du behöver att inkludera en resurs för alla moduler som används av dina runbooks.

[Integreringsmoduler](../../automation/automation-integration-modules.md) har en typ av **Microsoft.Automation/automationAccounts/modules** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen.

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

| Egenskap  | Beskrivning |
|:--- |:--- |
| contentLink |Anger innehållet i modulen. <br><br>URI - Uri för att innehållet i modulen.  Det här är en .ps1-fil för runbooks med PowerShell och skript och en exporterade grafiska runbook-fil för en runbook i diagrammet.  <br> version - versionen av modulen för dina egna spårning. |

Runbook bör vara beroende av resursen modulen så att den har skapats innan runbook.

### <a name="updating-modules"></a>Uppdatera moduler
Om du uppdaterar en lösning som innehåller en runbook som använder ett schema, och den nya versionen av din lösning har en ny modul som används av runbook, kan runbook använda den gamla versionen av modulen.  Du bör inkludera följande runbooks i din lösning och skapa ett jobb för att köra dem innan andra runbooks.  Detta säkerställer att uppdateras alla moduler som krävs innan runbooks har lästs in.

* [Uppdatera ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) säkerställer att alla moduler som används av runbooks i din lösning är den senaste versionen.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) ska registrera om alla resurser schema så att runbooks som är kopplad till dem till användning de senaste modulerna.




## <a name="sample"></a>Exempel
Nedan följer ett exempel på en lösning som omfattar som innehåller följande resurser:

- Runbook.  Det här är en exempel-runbook som lagras i en offentlig GitHub-lagringsplats.
- Automation-jobb som startar en runbook när lösningen är installerad.
- Schemat och schema för att starta runbooken med jämna mellanrum.
- certifikat.
- Autentiseringsuppgifter.
- Variabeln.
- Modul.  Det här är den [OMSIngestionAPI modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) för att skriva data till Log Analytics. 

I exemplet används [standardlösning parametrar]( solutions-solution-file.md#parameters) variabler som ofta används i en lösning till skillnad från hardcoding värden i resursdefinitionerna.


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
* [Lägga till en vy i lösningen]( solutions-resources-views.md) att visualisera insamlade data.
