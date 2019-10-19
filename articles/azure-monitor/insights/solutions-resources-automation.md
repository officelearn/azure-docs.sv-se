---
title: Azure Automation resurser i hanterings lösningar | Microsoft Docs
description: Hanterings lösningar innehåller vanligt vis Runbooks i Azure Automation för att automatisera processer som insamling och bearbetning av övervaknings data.  I den här artikeln beskrivs hur du inkluderar Runbooks och deras relaterade resurser i en lösning.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e09bacd1ce70f05f04798f092d3eb4b3e36ab5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555239"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Lägga till Azure Automation resurser i en hanterings lösning (för hands version)
> [!NOTE]
> Det här är en preliminär dokumentation för att skapa hanterings lösningar som för närvarande finns i för hands version. Alla scheman som beskrivs nedan kan komma att ändras.   


[Hanterings lösningar]( solutions.md) innehåller vanligt vis runbooks i Azure Automation för att automatisera processer som insamling och bearbetning av övervaknings data.  Förutom Runbooks innehåller Automation-konton till gångar, till exempel variabler och scheman som stöder Runbooks som används i lösningen.  I den här artikeln beskrivs hur du inkluderar Runbooks och deras relaterade resurser i en lösning.

> [!NOTE]
> I exemplen i den här artikeln används parametrar och variabler som antingen är obligatoriska eller vanliga för hanterings lösningar och som beskrivs i [utforma och skapa en hanterings lösning i Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan är bekant med följande information.

- Så här [skapar du en hanterings lösning]( solutions-creating.md).
- Strukturen för en [lösnings fil]( solutions-solution-file.md).
- Så här [skapar du Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Automation-konto
Alla resurser i Azure Automation finns i ett [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview).  Som det beskrivs i [Log Analytics arbets yta och Automation-konto]( solutions.md#log-analytics-workspace-and-automation-account) ingår inte Automation-kontot i hanterings lösningen, men det måste finnas innan lösningen installeras.  Om den inte är tillgänglig kommer lösningen inte att installeras.

Namnet på varje Automation-resurs innehåller namnet på Automation-kontot.  Detta görs i lösningen med parametern **accountName** som i följande exempel för en Runbook-resurs.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Du bör inkludera alla Runbooks som används av lösningen i lösnings filen så att de skapas när lösningen installeras.  Du kan inte ta med bröd texten i Runbook i mallen, så du bör publicera runbooken på en offentlig plats där den kan nås av alla användare som installerar lösningen.

[Azure Automation Runbook](../../automation/automation-runbook-types.md) -resurser har en typ av **Microsoft. Automation/automationAccounts/Runbooks** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen. 

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


Egenskaperna för Runbooks beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| runbookType |Anger typerna av Runbook. <br><br> Skript – PowerShell-skript <br>PowerShell – PowerShell-arbetsflöde <br> GraphPowerShell – grafisk PowerShell-skript Runbook <br> GraphPowerShellWorkflow – grafisk PowerShell Workflow-Runbook |
| logProgress |Anger om [status poster](../../automation/automation-runbook-output-and-messages.md) ska genereras för runbooken. |
| logVerbose |Anger om [utförliga poster](../../automation/automation-runbook-output-and-messages.md) ska genereras för runbooken. |
| beskrivning |Valfri beskrivning för runbooken. |
| publishContentLink |Anger Runbook-innehållet. <br><br>URI-URI till Runbook-innehållet.  Detta är en. ps1-fil för PowerShell-och skript-Runbooks och en exporterad grafisk Runbook-fil för en graf-Runbook.  <br> version – Runbook-versionen för din egen spårning. |


## <a name="automation-jobs"></a>Automation-jobb
När du startar en Runbook i Azure Automation skapas ett Automation-jobb.  Du kan lägga till en automatiserings jobb resurs till din lösning för att automatiskt starta en runbook när hanterings lösningen är installerad.  Den här metoden används vanligt vis för att starta Runbooks som används för inledande konfiguration av lösningen.  Om du vill starta en Runbook med jämna mellanrum skapar du ett [schema](#schedules) och ett [jobb schema](#job-schedules)

Jobb resurser har en typ av **Microsoft. Automation/automationAccounts/Jobs** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen. 

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

Egenskaperna för Automation-jobb beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| runbooken |Entitet med enskilt namn med namnet på den Runbook som ska startas. |
| parameters |Entitet för varje parameter värde som krävs av runbooken. |

Jobbet innehåller Runbook-namn och eventuella parameter värden som ska skickas till runbooken.  Jobbet bör [vara beroende]( solutions-solution-file.md#resources) av den Runbook som det startar sedan runbooken måste skapas innan jobbet.  Om du har flera Runbooks som ska startas kan du definiera deras ordning genom att låta ett jobb vara beroende av andra jobb som ska köras först.

Namnet på en jobb resurs måste innehålla ett GUID som vanligt vis tilldelas av en parameter.  Du kan läsa mer om GUID-parametrar i [skapa en hanterings lösnings fil i Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certifikat
[Azure Automation certifikat](../../automation/automation-certificates.md) har typen **Microsoft. Automation/automationAccounts/certificates** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen. 

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



Egenskaperna för certifikat resurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| base64Value |Bas 64-värde för certifikatet. |
| begäran |Tumavtryck för certifikatet. |



## <a name="credentials"></a>Autentiseringsuppgifter
[Azure Automation autentiseringsuppgifter](../../automation/automation-credentials.md) har en typ av **Microsoft. Automation/automationAccounts/credentials** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen. 


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

Egenskaperna för resurser för autentiseringsuppgifter beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Användar |Användar namn för autentiseringsuppgiften. |
| lösenord |Lösen ord för autentiseringsuppgiften. |


## <a name="schedules"></a>Scheman
[Azure Automation scheman](../../automation/automation-schedules.md) har en typ av **Microsoft. Automation/automationAccounts/-scheman** och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen. 

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

Egenskaperna för schema resurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| beskrivning |Valfri beskrivning av schemat. |
| startTime |Anger start tiden för ett schema som ett DateTime-objekt. En sträng kan anges om den kan konverteras till ett giltigt DateTime-värde. |
| isEnabled |Anger om schemat är aktiverat. |
| interval |Typ av intervall för schemat.<br><br>dagen<br>timme |
| frequency |Hur ofta schemat ska utlösas i antal dagar eller timmar. |

Scheman måste ha en start tid med ett värde som är större än den aktuella tiden.  Du kan inte ange det här värdet med en variabel eftersom du inte skulle kunna veta när det ska installeras.

Använd någon av följande två strategier när du använder schema resurser i en lösning.

- Använd en parameter för start tiden för schemat.  Detta kommer att uppmana användaren att ange ett värde när de installerar lösningen.  Om du har flera scheman kan du använda ett enda parameter värde för fler än en av dem.
- Skapa scheman med en Runbook som startar när lösningen installeras.  Detta tar bort kravet på att användaren ska ange en tid, men du kan inte ange schemat i lösningen så att det tas bort när lösningen tas bort.


### <a name="job-schedules"></a>Jobbscheman
Jobb schema resurser länka en Runbook med ett schema.  De har en typ av **Microsoft. Automation/automationAccounts/jobSchedules** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen. 

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


Egenskaperna för jobb scheman beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Schema namn |Entitet med enskilt **namn** med namnet på schemat. |
| Runbook-namn  |Entitet med enskilt **namn** med namnet på runbooken.  |



## <a name="variables"></a>Variabler
[Azure Automation variabler](../../automation/automation-variables.md) har en typ av **Microsoft. Automation/automationAccounts/variabler** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen.

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
| beskrivning | Valfri beskrivning för variabeln. |
| isEncrypted | Anger om variabeln ska krypteras. |
| typ | Den här egenskapen har för närvarande ingen påverkan.  Data typen för variabeln bestäms av det initiala värdet. |
| värde | Värde för variabeln. |

> [!NOTE]
> Egenskapen **Type** har för närvarande ingen inverkan på variabeln som skapas.  Data typen för variabeln bestäms av värdet.  

Om du anger det ursprungliga värdet för variabeln måste det konfigureras som rätt datatyp.  I följande tabell visas de olika data typerna som är tillåtna och deras syntax.  Observera att värden i JSON förväntas alltid stå inom citat tecken med specialtecken inom citat tecken.  Ett sträng värde skulle till exempel anges med citat tecken runt strängen (med escape-tecken (\\)) medan ett numeriskt värde anges med en uppsättning citat tecken.

| Datatyp | Beskrivning | Exempel | Matchar |
|:--|:--|:--|:--|
| sträng   | Omge värde med dubbla citat tecken.  | "\"Hello World \"" | "Hello World" |
| nummer  | Numeriskt värde med enkla citat tecken.| "64" | 64 |
| boolesk  | **Sant** eller **falskt** inom citat tecken.  Observera att det här värdet måste vara gemener. | värdet | sant |
| datetime | Serialiserat datum värde.<br>Du kan använda cmdleten ConvertTo-JSON i PowerShell för att generera det här värdet för ett visst datum.<br>Exempel: get-date "5/24/2017 13:14:57" \| ConvertTo-JSON | "\\/Date (1495656897378) \\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Moduler
Din hanterings lösning behöver inte definiera [globala moduler](../../automation/automation-integration-modules.md) som används av dina runbooks eftersom de alltid kommer att vara tillgängliga i ditt Automation-konto.  Du måste inkludera en resurs för alla andra moduler som används av dina runbooks.

[Integrerings moduler](../../automation/automation-integration-modules.md) har en typ av **Microsoft. Automation/automationAccounts/modules** och har följande struktur.  Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in kodfragmentet i lösnings filen och ändra parameter namnen.

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


Egenskaperna för module-resurser beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| contentLink |Anger innehållet i modulen. <br><br>URI-URI till innehållet i modulen.  Detta är en. ps1-fil för PowerShell-och skript-Runbooks och en exporterad grafisk Runbook-fil för en graf-Runbook.  <br> version-versionen av modulen för din egen spårning. |

Runbooken bör vara beroende av modulens resurs för att säkerställa att den skapas före runbooken.

### <a name="updating-modules"></a>Uppdaterar moduler
Om du uppdaterar en hanterings lösning som innehåller en Runbook som använder ett schema och den nya versionen av lösningen har en ny modul som används av denna Runbook, kan runbooken använda den gamla versionen av modulen.  Du bör inkludera följande Runbooks i lösningen och skapa ett jobb för att köra dem före andra Runbooks.  På så sätt kan du se till att alla moduler uppdateras efter behov innan Runbooks läses in.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) ser till att alla moduler som används av Runbooks i din lösning är den senaste versionen.  
* [ReRegisterAutomationSchedule-MS-MGMT](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) omregistrerar alla schema resurser för att säkerställa att de Runbooks som är kopplade till dem med använder de senaste modulerna.




## <a name="sample"></a>Exempel
Följande är ett exempel på en lösning som omfattar följande resurser:

- Runbooken.  Det här är ett exempel på en Runbook som lagras i en offentlig GitHub-lagringsplats.
- Automation-jobb som startar runbooken när lösningen installeras.
- Schemalägg och jobb schema för att starta runbooken med jämna mellanrum.
- Certifikatmallens.
- Certifiering.
- Variabel.
- Modulen.  Detta är [OMSIngestionAPI-modulen](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) för att skriva data till Log Analytics. 

Exemplet använder variabler för [standardlösnings parametrar]( solutions-solution-file.md#parameters) som vanligt vis används i en lösning i stället för hårdkoda-värden i resurs definitionerna.


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
* [Lägg till en vy till din lösning]( solutions-resources-views.md) för att visualisera insamlade data.
