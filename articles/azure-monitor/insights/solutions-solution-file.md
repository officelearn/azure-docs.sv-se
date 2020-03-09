---
title: Skapa en hanterings lösnings fil i Azure | Microsoft Docs
description: Hanterings lösningar tillhandahåller paketlösningar för hantering av paket som kunder kan lägga till i sin Azure-miljö.  Den här artikeln innehåller information om hur du kan skapa hanterings lösningar som ska användas i din egen miljö eller som görs tillgängliga för dina kunder.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395622"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Skapa en hanterings lösnings fil i Azure (för hands version)
> [!NOTE]
> Det här är en preliminär dokumentation för att skapa hanterings lösningar i Azure som för närvarande är en för hands version. Alla scheman som beskrivs nedan kan komma att ändras.  

Hanterings lösningar i Azure implementeras som [Resource Manager-mallar](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).  Den viktigaste uppgiften i hur du skapar hanterings lösningar är att lära dig hur du [skapar en mall](../../azure-resource-manager/templates/template-syntax.md).  Den här artikeln innehåller unik information om mallar som används för lösningar och hur du konfigurerar typiska lösnings resurser.


## <a name="tools"></a>Formateringsverktyg

Du kan använda valfri text redigerare för att arbeta med lösningsfiler, men vi rekommenderar att du använder de funktioner som finns i Visual Studio eller Visual Studio Code enligt beskrivningen i följande artiklar.

- [Skapa och Distribuera Azure-resurs grupper via Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Arbeta med Azure Resource Manager mallar i Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Hierarkistruktur
Den grundläggande strukturen i en hanterings lösnings fil är samma som en [Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md#template-format), vilket är följande.  I följande avsnitt beskrivs de översta elementen och deras innehåll i en lösning.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Komponentparametrar
[Parametrar](../../azure-resource-manager/templates/template-syntax.md#parameters) är värden som du behöver från användaren när de installerar hanterings lösningen.  Det finns standard parametrar som alla lösningar kommer att ha och du kan lägga till ytterligare parametrar som krävs för din specifika lösning.  Hur användarna kommer att ange parameter värden när de installerar lösningen beror på den specifika parametern och hur lösningen installeras.

När en användare [installerar din hanterings lösning](solutions.md#install-a-monitoring-solution) via Azure Marketplace eller Azures snabb starts mallar uppmanas de att välja en [Log Analytics arbets yta och ett Automation-konto](solutions.md#log-analytics-workspace-and-automation-account).  Dessa används för att fylla i värdena för var och en av standard parametrarna.  Användaren uppmanas inte att ange värden för standard parametrarna direkt, men de uppmanas att ange värden för eventuella ytterligare parametrar.


En exempel parameter visas nedan.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

I följande tabell beskrivs attributen för en parameter.

| Basattributet | Beteckning |
|:--- |:--- |
| bastyp |Parameterns datatyp. Den inmatnings kontroll som visas för användaren beror på data typen.<br><br>bool-List rutan<br>sträng – text ruta<br>int-text ruta<br>SecureString – lösen ords fält<br> |
| kategori |Valfri kategori för parametern.  Parametrar i samma kategori grupperas tillsammans. |
| reglering |Ytterligare funktioner för sträng parametrar.<br><br>datetime-datetime-kontrollen visas.<br>GUID-GUID-värdet genereras automatiskt och parametern visas inte. |
| Beteckning |Valfri beskrivning för parametern.  Visas i en informations prat bubbla bredvid parametern. |

### <a name="standard-parameters"></a>Standard parametrar
I följande tabell visas standard parametrarna för alla hanterings lösningar.  Dessa värden fylls i i stället för att fråga efter dem när din lösning installeras från Azure Marketplace eller snabb starts mallar.  Användaren måste ange värden för dem om lösningen installeras med en annan metod.

> [!NOTE]
> Användar gränssnittet i Azure Marketplace och snabb starts mal vägar förväntar sig parameter namnen i tabellen.  Om du använder olika parameter namn kommer användaren att uppmanas att ange dem och de fylls inte i automatiskt.
>
>

| ProfileServiceApplicationProxy | Bastyp | Beteckning |
|:--- |:--- |:--- |
| Konto |Nollängd |Azure Automation konto namn. |
| pricingTier |Nollängd |Pris nivå för både Log Analytics arbets yta och Azure Automation konto. |
| regionId |Nollängd |Azure Automation kontots region. |
| solutionName |Nollängd |Lösningens namn.  Om du distribuerar din lösning via snabb starts mallar bör du definiera solutionName som en parameter så att du kan definiera en sträng som kräver att användaren anger en sträng. |
| workspaceName |Nollängd |Log Analytics arbets ytans namn. |
| workspaceRegionId |Nollängd |Region i Log Analytics-arbetsytan. |


Följande är strukturen för de standard parametrar som du kan kopiera och klistra in i lösnings filen.  

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


Du refererar till parameter värden i andra element i lösningen med hjälp av syntaxen **Parameters (parameter namn)** .  Om du till exempel vill komma åt arbets ytans namn använder du **parametrar (' workspaceName ')**

## <a name="variables"></a>användarvariabler
[Variabler](../../azure-resource-manager/templates/template-syntax.md#variables) är värden som du kommer att använda i resten av hanterings lösningen.  Dessa värden exponeras inte för den användare som installerar lösningen.  De är avsedda att ge upphovs mannen en enda plats där de kan hantera värden som kan användas flera gånger i lösningen. Du bör ange värden som är unika för din lösning i variabler i stället för att hårdkoda dem i **resurs** elementet.  Detta gör koden mer lättläst och gör att du enkelt kan ändra dessa värden i senare versioner.

Följande är ett exempel på ett **variabel** element med typiska parametrar som används i lösningar.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Du refererar till variabel värden via lösningen med syntax **-variablerna ("variabel namn")** .  Om du till exempel vill komma åt variabeln SolutionName använder du **variabler (' SolutionName ')** .

Du kan också definiera komplexa variabler som flera uppsättningar med värden.  Dessa är särskilt användbara i hanterings lösningar där du definierar flera egenskaper för olika typer av resurser.  Du kan till exempel strukturera om de lösnings variabler som visas ovan till följande.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

I det här fallet refererar du till variabel värden genom lösningen med syntax **-variablerna (variabel namn). egenskap**.  Om du till exempel vill komma åt variabeln lösnings namn använder du **variabler ("lösning"). Namn**.

## <a name="resources"></a>resurser
[Resurser](../../azure-resource-manager/templates/template-syntax.md#resources) definierar de olika resurser som din hanterings lösning kommer att installera och konfigurera.  Detta är den största och mest komplexa delen av mallen.  Du kan hämta strukturen och fullständig beskrivning av resurs element i [redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md#resources).  Olika resurser som du definierar ofta beskrivs i andra artiklar i den här dokumentationen. 


### <a name="dependencies"></a>relation
**DependsOn** -elementet anger ett [beroende](../../azure-resource-manager/templates/define-resource-dependency.md) för en annan resurs.  När lösningen har installerats skapas ingen resurs förrän alla dess beroenden har skapats.  Din lösning kan till exempel [starta en Runbook](solutions-resources-automation.md#runbooks) när den installeras med en [jobb resurs](solutions-resources-automation.md#automation-jobs).  Jobb resursen är beroende av Runbook-resursen för att se till att runbooken skapas innan jobbet skapas.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics arbets yta och Automation-konto
Hanterings lösningar kräver att en [Log Analytics arbets yta](../../azure-monitor/platform/manage-access.md) innehåller vyer och ett [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview) för att innehålla Runbooks och relaterade resurser.  Dessa måste vara tillgängliga innan resurserna i lösningen skapas och ska inte definieras i själva lösningen.  Användaren [anger en arbets yta och ett konto](solutions.md#log-analytics-workspace-and-automation-account) när de distribuerar din lösning, men som författare bör du tänka på följande saker.


## <a name="solution-resource"></a>Lösnings resurs
Varje lösning kräver en resurs post i resurs **elementet som** definierar själva lösningen.  Detta kommer att ha en typ av **Microsoft. OperationsManagement/-lösningar** och ha följande struktur. Detta inkluderar [standard parametrar](#parameters) och [variabler](#variables) som vanligt vis används för att definiera egenskaper för lösningen.


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




### <a name="dependencies"></a>relation
Lösnings resursen måste ha ett [beroende](../../azure-resource-manager/templates/define-resource-dependency.md) på alla andra resurser i lösningen eftersom de måste finnas innan lösningen kan skapas.  Du gör detta genom att lägga till en post för varje resurs i **dependsOn** -elementet.

### <a name="properties"></a>Egenskaperna
Lösnings resursen har egenskaperna i följande tabell.  Detta inkluderar de resurser som refereras och ingår i lösningen som definierar hur resursen hanteras när lösningen har installerats.  Varje resurs i lösningen ska visas i antingen **referencedResources** eller egenskapen **containedResources** .

| Immaterialrätt | Beteckning |
|:--- |:--- |
| workspaceResourceId |ID för arbets ytan Log Analytics i formatet *\<resurs grupps-id >/providers/Microsoft.OperationalInsights/workspaces/\<arbets ytans namn\>* . |
| referencedResources |Lista över resurser i lösningen som inte ska tas bort när lösningen tas bort. |
| containedResources |Lista med resurser i lösningen som ska tas bort när lösningen tas bort. |

Exemplet ovan är för en lösning med en Runbook, ett schema och en vy.  Schemat och Runbook-flödet *refereras* till i **Properties** -elementet så att de inte tas bort när lösningen tas bort.  Vyn *finns så att* den tas bort när lösningen tas bort.

### <a name="plan"></a>projektplan
**Plan** -entiteten för lösnings resursen har egenskaperna i följande tabell.

| Immaterialrätt | Beteckning |
|:--- |:--- |
| Namn |Lösningens namn. |
| 2\.0.1 |Version av lösningen som definieras av författaren. |
| momsproduktbokföringsmallar |Unik sträng för att identifiera lösningen. |
| Förläggare |Utgivare av lösningen. |



## <a name="next-steps"></a>Nästa steg
* [Lägg till sparade sökningar och aviseringar](solutions-resources-searches-alerts.md) i hanterings lösningen.
* [Lägg till vyer](solutions-resources-views.md) i hanterings lösningen.
* [Lägg till runbooks och andra Automation-resurser](solutions-resources-automation.md) i hanterings lösningen.
* Läs mer om hur du [redigerar Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).
* Sök i [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates) efter exempel på olika Resource Manager-mallar.
