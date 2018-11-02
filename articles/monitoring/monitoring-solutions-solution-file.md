---
title: Skapa en fil för lösningen i Azure | Microsoft Docs
description: Lösningar för hantering ger paketerade hanteringsscenarier som kunder kan lägga till deras Azure-miljön.  Den här artikeln innehåller information om hur du kan skapa lösningar för hantering som ska användas i din egen miljö eller göras tillgängligt för dina kunder.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 13da68f826f7077acec9a64d1aa0ea18c66be6ff
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914251"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Skapa en fil för lösningen i Azure (förhandsversion)
> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering i Azure som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.  

Lösningar för hantering i Azure implementeras som [Resource Manager-mallar](../azure-resource-manager/resource-manager-template-walkthrough.md).  Viktigaste uppgiften i lära dig hur du skapar lösningar för hantering av learning så [skapar en mall](../azure-resource-manager/resource-group-authoring-templates.md).  Den här artikeln innehåller unika detaljer mallar som används för lösningar och hur du konfigurerar vanliga lösningsresurser.


## <a name="tools"></a>Verktyg

Du kan använda valfri textredigerare för att arbeta med lösningsfiler, men vi rekommenderar att utnyttja funktionerna i Visual Studio eller Visual Studio Code, enligt beskrivningen i följande artiklar.

- [Skapa och distribuera Azure-resursgrupper via Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Arbeta med Azure Resource Manager-mallar i Visual Studio Code](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>struktur
Den grundläggande strukturen i en fil för lösningen är samma som en [Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md#template-format), som ser ut så.  Vart och ett av avsnitten nedan beskrivs de översta elementen och deras innehåll i en lösning.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametrar
[Parametrar](../azure-resource-manager/resource-group-authoring-templates.md#parameters) är värden som du behöver från användaren när de installerar hanteringslösningen.  Det finns standardparametrar som har alla lösningar och du kan lägga till ytterligare parametrar som krävs för din lösning.  Hur användare ange parametervärden när de installerar din lösning beror på en viss parameter och hur lösningen installeras.

När en användare [installerar din lösning](monitoring-solutions.md#install-a-management-solution) via Azure Marketplace eller Azure QuickStart-mallar uppmanas de att välja en [Log Analytics-arbetsytan och Automation-kontot](monitoring-solutions.md#log-analytics-workspace-and-automation-account).  De används för att fylla i värdena för var och en av parametrarna standard.  Användaren behöver inte ange direkt ange värden för parametrarna standard, men de uppmanas att ange värden för alla ytterligare parametrar.


En exempel-parameter visas nedan.  

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
| typ |Datatypen för parametern. Den indatakontroll som visas för användaren är beroende av datatypen.<br><br>bool - listrutan<br>sträng - textrutan<br>int - textrutan<br>SecureString - lösenordsfältet<br> |
| category |Valfri kategori för parametern.  Parametrarna i samma kategori grupperas tillsammans. |
| Kontroll |Ytterligare funktioner för strängparametrar.<br><br>datetime - Datetime kontroll visas.<br>GUID – Guid-värde genereras automatiskt och parametern visas inte. |
| beskrivning |Valfri beskrivning för parametern.  Visas i en information pratbubblor bredvid parametern. |

### <a name="standard-parameters"></a>Standardparametrar
I följande tabell visas standardparametrar för alla lösningar för hantering.  Dessa värden är ifyllda i stället för att fråga om dem när lösningen har installerats från Azure Marketplace eller Quickstart-mallar.  Användaren måste ange värden för dem om lösningen är installerat med någon annan metod.

> [!NOTE]
> Användargränssnittet i Azure Marketplace och snabbstartsmallar förväntar sig parameternamnen i tabellen.  Om du använder olika parameternamn sedan användaren uppmanas att dem och de är inte automatiskt ifyllda.
>
>

| Parameter | Typ | Beskrivning |
|:--- |:--- |:--- |
| Kontonamn |sträng |Azure Automation-kontonamn. |
| pricingTier |sträng |Prisnivån för både Log Analytics-arbetsytan och Azure Automation-konto. |
| regionId |sträng |Region för Azure Automation-kontot. |
| solutionName |sträng |Namnet på lösningen.  Om du distribuerar din lösning med Quickstart-mallar, bör sedan du definiera solutionName som en parameter så att du kan definiera en sträng i stället eftersom användaren måste ange en. |
| workspaceName |sträng |Log Analytics-Arbetsytenamn. |
| workspaceRegionId |sträng |Region för Log Analytics-arbetsytan. |


Nedan följer strukturen för standardparametrar som du kan kopiera och klistra in i din lösningsfilen.  

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


Du refererar till parametervärden i andra element i lösningen med syntaxen **parametrar (parametern name)**.  Till exempel för att komma åt arbetsytans namn om du använder **parameters('workspaceName')**

## <a name="variables"></a>Variabler
[Variabler](../azure-resource-manager/resource-group-authoring-templates.md#variables) är värden som du ska använda i resten av hanteringslösningen.  Dessa värden exponeras inte för den användare som installerar lösningen.  De är avsedda att ge författaren med en enda plats där de kan hantera värden som kan användas flera gånger i hela lösningen. Du bör placera värden specifika i din lösning i variabler istället för hårdkoda dem i den **resurser** element.  Detta gör koden lättare att läsa och gör att du kan enkelt ändra dessa värden i senare versioner.

Följande är ett exempel på en **variabler** element med vanliga parametrar som används i lösningar.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Du refererar till variabelvärden genom lösningen med syntaxen **variabler (variabeln namn)**.  Till exempel för att komma åt SolutionName variabeln, använder du **variables('SolutionName')**.

Du kan också definiera komplex variabler som flera uppsättningar med värden.  Det här är särskilt användbart i lösningar för hantering av där du definierar flera egenskaper för olika typer av resurser.  Du kan till exempel omstrukturera lösningen variablerna som visas ovan för att följande.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

I det här fallet du hänvisa till variabeln genom lösningen med syntaxen **variables('variable name').property**.  Till exempel för att komma åt lösningsnamn variabeln, använder du **variables('Solution'). Namn på**.

## <a name="resources"></a>Resurser
[Resurser](../azure-resource-manager/resource-group-authoring-templates.md#resources) definierar de olika resurser som din lösning ska installeras och konfigureras.  Det här är den största och mest avancerade delen av mallen.  Du kan hämta struktur och fullständig beskrivning av resursen element i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Olika resurser som du vanligtvis definierar beskrivs i andra artiklar i den här dokumentationen. 


### <a name="dependencies"></a>Beroenden
Den **dependsOn** elementet anger en [beroende](../azure-resource-manager/resource-group-define-dependencies.md) på en annan resurs.  När lösningen har installerats kan en resurs skapas inte förrän alla dess beroenden har skapats.  Till exempel din lösning kan [startar en runbook](monitoring-solutions-resources-automation.md#runbooks) när den är installerad med hjälp av en [jobbet resource](monitoring-solutions-resources-automation.md#automation-jobs).  Jobb-resursen skulle vara beroende av runbook-resursen för att se till att runbooken har skapats innan jobbet har skapats.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-arbetsytan och Automation-konto
Lösningar för hantering av kräver en [Log Analytics-arbetsyta](../log-analytics/log-analytics-manage-access.md) innehålla vyer och en [Automation-konto](../automation/automation-security-overview.md#automation-account-overview) som innehåller runbooks och relaterade resurser.  Dessa måste vara tillgängliga innan resurserna i lösningen skapas och ska inte definieras i själva lösningen.  Användaren kommer att [ange en arbetsyta och konto](monitoring-solutions.md#log-analytics-workspace-and-automation-account) när de distribuerar din lösning, men som författare bör du överväga följande punkter.


## <a name="solution-resource"></a>Lösningen resurs
Varje lösning kräver att en resurs i den **resurser** -element som definierar själva lösningen.  Detta har en typ av **Microsoft.OperationsManagement/solutions** och har följande struktur. Detta inkluderar [standardparametrar](#parameters) och [variabler](#variables) som används vanligtvis för att definiera egenskaperna för lösningen.


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
Lösningen resursen måste ha en [beroende](../azure-resource-manager/resource-group-define-dependencies.md) på alla andra resurser i lösningen eftersom de måste finnas innan du kan skapa lösningen.  Du gör detta genom att lägga till en post för varje resurs i den **dependsOn** element.

### <a name="properties"></a>Egenskaper
Lösningen-resursen har egenskaperna i följande tabell.  Detta omfattar de resurser som refererar till och ingår i lösningen som definierar hur resursen hanteras när lösningen har installerats.  Varje resurs i lösningen bör visas i antingen den **referencedResources** eller **containedResources** egenskapen.

| Egenskap  | Beskrivning |
|:--- |:--- |
| workspaceResourceId |ID för Log Analytics-arbetsytan i formuläret  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Arbetsytenamn\>*. |
| referencedResources |Listan över resurser i den lösning som inte ska tas bort när lösningen har tagits bort. |
| containedResources |Lista över resurser i lösningen som ska tas bort när lösningen har tagits bort. |

I exemplet ovan är för en lösning med en runbook, ett schema och visa.  Schemat och runbook är *refererade* i den **egenskaper** element så att de inte tas bort när lösningen har tagits bort.  Vyn är *innehöll* så tas den bort när lösningen har tagits bort.

### <a name="plan"></a>Planera
Den **plan** entitet av lösningen resursen har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| namn |Namnet på lösningen. |
| version |Version av lösningen systemets författaren. |
| produkt |Unik sträng att identifiera lösningen. |
| utgivare |Utgivare av lösningen. |



## <a name="sample"></a>Exempel
Du kan visa exempel av lösningsfiler med en lösning resurs på följande platser.

- [Automation-resurser](monitoring-solutions-resources-automation.md#sample)
- [Resurser för sökning och aviseringar](monitoring-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Nästa steg
* [Lägg till sparade sökningar och aviseringar](monitoring-solutions-resources-searches-alerts.md) till din lösning.
* [Lägga till vyer](monitoring-solutions-resources-views.md) till din lösning.
* [Lägg till runbooks och andra Automation-resurser](monitoring-solutions-resources-automation.md) till din lösning.
* Mer information om [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).
* Sök [Azure-Snabbstartsmallar](https://azure.microsoft.com/documentation/templates) exempel på olika Resource Manager-mallar.
