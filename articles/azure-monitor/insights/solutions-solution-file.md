---
title: Skapa en hanteringslösningsfil i Azure | Microsoft-dokument
description: Hanteringslösningar tillhandahåller paketerade hanteringsscenarier som kunder kan lägga till i sin Azure-miljö.  Den här artikeln innehåller information om hur du kan skapa hanteringslösningar som ska användas i din egen miljö eller göras tillgängliga för dina kunder.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275183"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Skapa en hanteringslösningsfil i Azure (förhandsversion)
> [!NOTE]
> Detta är preliminär dokumentation för att skapa hanteringslösningar i Azure som för närvarande är i förhandsversion. Alla scheman som beskrivs nedan kan komma att ändras.  

Hanteringslösningar i Azure implementeras som [Resource Manager-mallar](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).  Huvuduppgiften för att lära sig att skapa förvaltningslösningar är att lära sig [att skapa en mall](../../azure-resource-manager/templates/template-syntax.md).  Den här artikeln innehåller unika information om mallar som används för lösningar och hur du konfigurerar typiska lösningsresurser.


## <a name="tools"></a>Verktyg

Du kan använda vilken textredigerare som helst för att arbeta med lösningsfiler, men vi rekommenderar att du utnyttjar funktionerna i Visual Studio eller Visual Studio Code enligt beskrivningen i följande artiklar.

- [Skapa och distribuera Azure-resursgrupper via Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Arbeta med Azure Resource Manager-mallar i Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktur
Den grundläggande strukturen för en hanteringslösningsfil är samma som en [Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md#template-format), vilket är följande.  I avsnitten nedan beskrivs de översta elementen och deras innehåll i en lösning.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametrar
[Parametrar](../../azure-resource-manager/templates/template-syntax.md#parameters) är värden som du behöver från användaren när de installerar hanteringslösningen.  Det finns standardparametrar som alla lösningar kommer att ha, och du kan lägga till ytterligare parametrar som krävs för just din lösning.  Hur användare kommer att tillhandahålla parametervärden när de installerar din lösning beror på den specifika parametern och hur lösningen installeras.

När en användare [installerar din hanteringslösning](solutions.md#install-a-monitoring-solution) via Azure Marketplace- eller Azure QuickStart-mallarna uppmanas de att välja en [Log Analytics-arbetsyta och Automation-konto](solutions.md#log-analytics-workspace-and-automation-account).  Dessa används för att fylla i värdena för var och en av standardparametrarna.  Användaren uppmanas inte att direkt ange värden för standardparametrarna, men de uppmanas att ange värden för eventuella ytterligare parametrar.


En exempelparameter visas nedan.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

I följande tabell beskrivs attributen för en parameter.

| Attribut | Beskrivning |
|:--- |:--- |
| typ |Datatyp för parametern. Indatakontrollen som visas för användaren beror på datatypen.<br><br>bool - Rullgardinsmenyn<br>sträng - Textruta<br>int - Textruta<br>securestring - Fältet Lösenord<br> |
| category |Valfri kategori för parametern.  Parametrar i samma kategori grupperas tillsammans. |
| control |Ytterligare funktioner för strängparametrar.<br><br>datetime - Datetime-kontrollen visas.<br>guid - Guid-värdet genereras automatiskt och parametern visas inte. |
| description |Valfri beskrivning för parametern.  Visas i en informationsballong bredvid parametern. |

### <a name="standard-parameters"></a>Standardparametrar
I följande tabell visas standardparametrarna för alla hanteringslösningar.  Dessa värden fylls i för användaren i stället för att fråga efter dem när din lösning installeras från Azure Marketplace- eller Snabbstartsmallarna.  Användaren måste ange värden för dem om lösningen installeras med en annan metod.

> [!NOTE]
> Användargränssnittet i mallarna Azure Marketplace och Snabbstart förväntar sig parameternamnen i tabellen.  Om du använder olika parameternamn kommer användaren att uppmanas för dem, och de kommer inte att fyllas i automatiskt.
>
>

| Parameter | Typ | Beskrivning |
|:--- |:--- |:--- |
| accountName |sträng |Azure Automation-kontonamn. |
| priserTier |sträng |Prisnivå för både Log Analytics-arbetsytan och Azure Automation-kontot. |
| regionId |sträng |Region för Azure Automation-kontot. |
| solutionName (solutionName) |sträng |Lösningens namn.  Om du distribuerar lösningen via Snabbstartsmallar bör du definiera solutionName som en parameter så att du kan definiera en sträng i stället som kräver att användaren anger en. |
| workspaceName |sträng |Namnet på logganalysarbetsyta. |
| arbetsytaRegionId |sträng |Region på arbetsytan Logganalys. |


Följande är strukturen på de standardparametrar som du kan kopiera och klistra in i lösningsfilen.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Du refererar till parametervärden i andra element i lösningen med **syntaxparametrarna("parameternamn")**.  Om du till exempel vill komma åt arbetsytans namn använder du **parametrar ("workspaceName")**

## <a name="variables"></a>Variabler
[Variabler](../../azure-resource-manager/templates/template-syntax.md#variables) är värden som du ska använda i resten av hanteringslösningen.  Dessa värden visas inte för användaren som installerar lösningen.  De är avsedda att ge författaren en enda plats där de kan hantera värden som kan användas flera gånger i hela lösningen. Du bör placera alla värden som är specifika för din lösning **resources** i variabler i motsats till hård kodning dem i resurselementet.  Detta gör koden mer lättläst och gör att du enkelt kan ändra dessa värden i senare versioner.

Följande är ett exempel på ett **variableelement** med typiska parametrar som används i lösningar.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Du refererar till variabelvärden via lösningen med **syntaxvariablerna("variabelnamn")**.  Om du till exempel vill komma åt solutionname-variabeln använder du **variabler("SolutionName")**.

Du kan också definiera komplexa variabler som flera uppsättningar värden.  Dessa är särskilt användbara i hanteringslösningar där du definierar flera egenskaper för olika typer av resurser.  Du kan till exempel omstrukturera lösningsvariablerna som visas ovan till följande.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

I det här fallet refererar du till variabelvärden via lösningen med **syntaxvariablerna("variabelnamn").**  Om du till exempel vill komma åt variabeln Lösningsnamn använder du **variabler("Lösning"). Namn**.

## <a name="resources"></a>Resurser
[Resurser](../../azure-resource-manager/templates/template-syntax.md#resources) definierar de olika resurser som hanteringslösningen ska installera och konfigurera.  Detta kommer att vara den största och mest komplexa delen av mallen.  Du kan hämta strukturen och fullständig beskrivning av resurselement i [Författare Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md#resources).  Olika resurser som du vanligtvis definierar beskrivs i andra artiklar i den här dokumentationen. 


### <a name="dependencies"></a>Beroenden
Elementet **dependsOn** anger ett [beroende av](../../azure-resource-manager/templates/define-resource-dependency.md) en annan resurs.  När lösningen har installerats skapas inte en resurs förrän alla dess beroenden har skapats.  Lösningen kan till exempel [starta en runbook](solutions-resources-automation.md#runbooks) när den installeras med hjälp av en [jobbresurs](solutions-resources-automation.md#automation-jobs).  Jobbresursen skulle vara beroende av runbook-resursen för att se till att runbooken skapas innan jobbet skapas.

### <a name="log-analytics-workspace-and-automation-account"></a>Logganalysarbetsyta och Automation-konto
Hanteringslösningar kräver en [Log Analytics-arbetsyta](../../azure-monitor/platform/manage-access.md) för att innehålla vyer och ett [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview) som innehåller runbooks och relaterade resurser.  Dessa måste vara tillgängliga innan resurserna i lösningen skapas och bör inte definieras i själva lösningen.  Användaren [anger en arbetsyta och ett konto](solutions.md#log-analytics-workspace-and-automation-account) när de distribuerar din lösning, men som författare bör du tänka på följande punkter.


## <a name="solution-resource"></a>Lösningsresurs
Varje lösning kräver en resurspost i **resurselementet** som definierar själva lösningen.  Detta kommer att ha en typ av **Microsoft.OperationsManagement/lösningar** och har följande struktur. Detta inkluderar [standardparametrar](#parameters) och [variabler](#variables) som vanligtvis används för att definiera lösningens egenskaper.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Beroenden
Lösningsresursen måste vara beroende [av](../../azure-resource-manager/templates/define-resource-dependency.md) alla andra resurser i lösningen eftersom de måste finnas innan lösningen kan skapas.  Du gör detta genom att lägga till en post för varje resurs i **elementet dependsOn.**

### <a name="properties"></a>Egenskaper
Lösningsresursen har egenskaperna i följande tabell.  Detta inkluderar de resurser som refereras och finns i lösningen som definierar hur resursen hanteras när lösningen har installerats.  Varje resurs i lösningen ska visas i antingen **den refererade Resurser** eller egenskapen **containedResources.**

| Egenskap | Beskrivning |
|:--- |:--- |
| workspaceResourceId |ID för log analytics-arbetsytan i formuläret * \<Resursgrupp-ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>*. |
| refereradeResurser |Lista över resurser i lösningen som inte ska tas bort när lösningen tas bort. |
| innehöllResurser |Lista över resurser i lösningen som ska tas bort när lösningen tas bort. |

Exemplet ovan är för en lösning med en runbook, ett schema och en vy.  Schema och runbook refereras i *egenskapselementet* så att de inte tas bort när lösningen tas bort. **properties**  Vyn *finns* så att den tas bort när lösningen tas bort.

### <a name="plan"></a>Planera
**Planentiteten** för lösningsresursen har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| namn |Lösningens namn. |
| version |Version av lösningen som bestäms av författaren. |
| produkt |Unik sträng för att identifiera lösningen. |
| utgivare |Utgivare av lösningen. |



## <a name="next-steps"></a>Nästa steg
* [Lägg till sparade sökningar och aviseringar](solutions-resources-searches-alerts.md) i hanteringslösningen.
* [Lägg till vyer](solutions-resources-views.md) i hanteringslösningen.
* [Lägg till runbooks och andra Automation-resurser](solutions-resources-automation.md) i hanteringslösningen.
* Lär dig mer om [hur du skapar Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md).
* Sök i [Azure Quickstart-mallar](https://azure.microsoft.com/documentation/templates) efter exempel på olika Resource Manager-mallar.
