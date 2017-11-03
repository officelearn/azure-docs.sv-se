---
title: "Skapa lösningar för hantering i Operations Management Suite (OMS) | Microsoft Docs"
description: "Hanteringslösningar utöka funktionerna i Operations Management Suite (OMS) genom att tillhandahålla paketerade hanteringsscenarier som kunder kan lägga till sina OMS-arbetsyta.  Den här artikeln innehåller information om hur du kan skapa lösningar för hantering som ska användas i din egen miljö eller göras tillgängligt för kunderna."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee3462c13101d18921dc488b08c79e1e4e02ff3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="creating-a-management-solution-file-in-operations-management-suite-oms-preview"></a>Skapa en management lösningsfilen i Operations Management Suite (OMS) (förhandsgranskning)
> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar för hantering i OMS som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.  

Lösningar för hantering i Operations Management Suite (OMS) implementeras som [Resource Manager-mallar](../azure-resource-manager/resource-manager-template-walkthrough.md).  Aktiviteten huvudsakliga Lär dig hur du skapar hanteringslösningar learning så [skapar en mall](../azure-resource-manager/resource-group-authoring-templates.md).  Den här artikeln innehåller unik information om mallar som används för lösningar och hur du konfigurerar vanliga lösning resurser.


## <a name="tools"></a>Verktyg

Du kan använda valfri textredigerare för att arbeta med lösningsfiler, men vi rekommenderar att utnyttja funktionerna i Visual Studio eller Visual Studio Code som beskrivs i följande artiklar.

- [Skapa och distribuera Azure-resursgrupper via Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Arbeta med Azure Resource Manager-mallar i Visual Studio Code](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>struktur
Den grundläggande strukturen i en fil för management-lösning är detsamma som en [Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md#template-format) som är som följer.  Var och en av nedanstående avsnitt beskrivs de översta elementen och och innehållet i en lösning.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametrar
[Parametrarna](../azure-resource-manager/resource-group-authoring-templates.md#parameters) är värden som du behöver från användaren när de installerar hanteringslösningen.  Det finns standardparametrar som har alla lösningar och du kan lägga till ytterligare parametrar som krävs för din lösning.  Hur användare ange parametervärden när de installerar lösningen beror på en viss parameter och hur lösningen installeras.

När en användare installerar din lösning för hantering via den [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) eller [Azure-snabbstartsmallar](operations-management-suite-solutions.md#finding-and-installing-management-solutions) uppmanas de att välja en [OMS-arbetsytan och Automation-kontot](operations-management-suite-solutions.md#oms-workspace-and-automation-account).  Dessa används för att fylla i värdena för var och en av parametrarna som standard.  Användaren uppmanas inte att direkt ange värden för parametrarna standard, men de uppmanas att ange värden för alla ytterligare parametrar.

När användaren installerar lösningen [en annan metod](operations-management-suite-solutions.md#finding-and-installing-management-solutions), måste de ange ett värde för alla parametrar som standard och alla ytterligare parametrar.

En exempel-parametern visas nedan.  

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
| typ |Datatypen för parametern. Inkommande kontrollen visas för användaren beror på datatypen.<br><br>bool - listrutan<br>String - textruta<br>int - textruta<br>SecureString - fält för lösenord<br> |
| category |Valfri kategori för parametern.  Parametrarna i samma kategori grupperas tillsammans. |
| Kontrollen |Ytterligare funktioner för string-parametrar.<br><br>datetime - Datetime kontrollen visas.<br>GUID - Guid-värde genereras automatiskt och parametern visas inte. |
| description |Valfri beskrivning för parametern.  Visas i en information pratbubblor bredvid parametern. |

### <a name="standard-parameters"></a>Standard-parametrar
I följande tabell visas standardparametrar för alla hanteringslösningar för.  Dessa värden har angetts för användaren i stället för att fråga om dem när lösningen har installerats från Azure Marketplace eller snabbstartsmallar.  Användaren måste ange värden för dem om lösningen har installerats med en annan metod.

> [!NOTE]
> Användargränssnittet i Azure Marketplace och snabbstartsmallar förväntas parameternamn i tabellen.  Om du använder olika parameternamn sedan användaren uppmanas att dem och fylls de inte automatiskt.
>
>

| Parameter | Typ | Beskrivning |
|:--- |:--- |:--- |
| Kontonamn |Sträng |Azure Automation-kontonamn. |
| pricingTier |Sträng |Prisnivån för både logganalys-arbetsytan och Azure Automation-konto. |
| regionId |Sträng |Region i Azure Automation-konto. |
| SolutionName |Sträng |Namnet för lösningen.  Om du distribuerar din lösning med snabbstartsmallar bör sedan du definiera solutionName som en parameter där du kan definiera en sträng i stället att användaren måste ange en. |
| workspaceName |Sträng |Log Analytics arbetsytans namn. |
| workspaceRegionId |Sträng |Region i logganalys-arbetsytan. |


Följande är standard parametrar som du kan kopiera och klistra in i din lösningsfilen struktur.  

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


Du refererar till parametervärden i andra element i lösningen med syntaxen **parametrar (parametern name)**.  Till exempel vill komma åt arbetsytans namn måste använda du **parameters('workspaceName')**

## <a name="variables"></a>Variabler
[Variabler](../azure-resource-manager/resource-group-authoring-templates.md#variables) är värden som du ska använda i resten av hanteringslösningen.  Dessa värden exponeras inte för den användare som installerar lösningen.  De är avsedda att ge författaren med en enda plats där de kan hantera värden som kan användas flera gånger i hela lösningen. Du bör placera alla värden specifika till lösningen i variabler i stället för hårda koda dem i den **resurser** element.  Detta gör det lättare att läsa koden och kan du enkelt ändra dessa värden i senare versioner.

Följande är ett exempel på en **variabler** element med vanliga parametrar som används i lösningar.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Du refererar till variabelvärden genom lösningen med syntax **variabler (variabel namn)**.  Till exempel för att komma åt variabeln SolutionName, använder du **variables('SolutionName')**.

Du kan också definiera komplex variabler som flera uppsättningar av värden.  Detta är särskilt användbart i hanteringslösningar där du definierar flera egenskaper för olika typer av resurser.  Du kan till exempel omstrukturera variablerna lösningen ovan följande.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

I så fall måste du referera till variabelvärden genom lösningen med syntax **variables('variable name').property**.  Till exempel vill komma åt variabeln lösningens namn måste använda du **variables('Solution'). Namnet**.

## <a name="resources"></a>Resurser
[Resurser](../azure-resource-manager/resource-group-authoring-templates.md#resources) definierar de olika resurser som din lösning ska installeras och konfigureras.  Det här är den största och mest komplexa delen av mallen.  Du kan hämta den struktur och en fullständig beskrivning av resursen element i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Olika resurser som du vanligtvis definierar beskrivs i andra artiklar i den här dokumentationen. 


### <a name="dependencies"></a>Beroenden
Den **dependsOn** element anger en [beroende](../azure-resource-manager/resource-group-define-dependencies.md) på en annan resurs.  När lösningen har installerats skapas en resurs tills alla dess beroenden har skapats.  Till exempel din lösning kan [startar en runbook](operations-management-suite-solutions-resources-automation.md#runbooks) när den är installerad med hjälp av en [jobbet resurs](operations-management-suite-solutions-resources-automation.md#automation-jobs).  Resursen jobbet är beroende av resursen runbook för att se till att runbooken har skapats innan jobbet har skapats.

### <a name="oms-workspace-and-automation-account"></a>OMS-arbetsytan och Automation-konto
Av hanteringslösningar kräver en [OMS-arbetsytan](../log-analytics/log-analytics-manage-access.md) innehåller vyer och en [Automation-konto](../automation/automation-security-overview.md#automation-account-overview) ska innehålla runbooks och relaterade resurser.  Dessa måste vara tillgängliga innan resurserna i lösningen skapas och ska inte definieras i själva lösningen.  Användaren kommer [ange arbetsytan och kontot](operations-management-suite-solutions.md#oms-workspace-and-automation-account) när de distribuerar din lösning, men som författare bör du överväga följande punkter.

## <a name="solution-resource"></a>Lösning för resurs
Varje lösning kräver att en resurs i den **resurser** element som definierar själva lösningen.  Det har en typ av **Microsoft.OperationsManagement/solutions** och har följande struktur. Detta inkluderar [standardparametrar](#parameters) och [variabler](#variables) som vanligtvis används för att definiera egenskaperna för lösningen.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspacename'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
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
Resursen lösningen måste ha en [beroende](../azure-resource-manager/resource-group-define-dependencies.md) på alla andra resurser i lösningen eftersom de ska finnas innan du kan skapa lösningen.  Det gör du genom att lägga till en post för varje resurs i den **dependsOn** element.

### <a name="properties"></a>Egenskaper
Lösning resursen har egenskaperna i följande tabell.  Detta inkluderar resurser refereras och finns i lösningen som definierar hur resursen hanteras när lösningen har installerats.  Varje resurs i lösningen ska visas i antingen den **referencedResources** eller **containedResources** egenskapen.

| Egenskap | Beskrivning |
|:--- |:--- |
| workspaceResourceId |ID för logganalys-arbetsytan i formuläret  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Arbetsytenamn\>*. |
| referencedResources |Lista över resurser i lösningen som inte tas bort när lösningen tas bort. |
| containedResources |Lista över resurser i lösningen som ska tas bort när lösningen tas bort. |

Exemplet ovan är för en lösning med en runbook ett schema och visa.  Schemat och runbook är *refererade* i den **egenskaper** elementet så att de inte tas bort när lösningen tas bort.  Vy *innehöll* så att den tas bort när lösningen tas bort.

### <a name="plan"></a>Planera
Den **plan** entiteten av lösningen resursen har egenskaper i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| namn |Namnet för lösningen. |
| Version |Version av lösningen som bestäms av författaren. |
| Produkten |Unik sträng som identifierar lösningen. |
| Publisher |Utgivaren av lösningen. |



## <a name="sample"></a>Exempel
Du kan visa prover av lösningsfiler med en resurs på följande platser.

- [Automation-resurser](operations-management-suite-solutions-resources-automation.md#sample)
- [Sök och avisering resurser](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Nästa steg
* [Lägg till sparade sökningar och aviseringar](operations-management-suite-solutions-resources-searches-alerts.md) att din lösning för hantering.
* [Lägga till vyer](operations-management-suite-solutions-resources-views.md) att din lösning för hantering.
* [Lägg till runbooks och andra resurser för Automation](operations-management-suite-solutions-resources-automation.md) att din lösning för hantering.
* Mer information om [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).
* Sök [Azure Quickstart mallar](https://azure.microsoft.com/documentation/templates) exempel på olika Resource Manager-mallar.
