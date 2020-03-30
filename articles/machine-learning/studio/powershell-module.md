---
title: PowerShell-moduler
titleSuffix: ML Studio (classic) - Azure
description: Använd PowerShell för att skapa och hantera Azure Machine Learning Studio (klassiska) arbetsytor, experiment, webbtjänster med mera.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204298"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>PowerShell-moduler för Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Med PowerShell-moduler kan du programmässigt hantera dina Studio-resurser (klassiska) resurser som arbetsytor, datauppsättningar och webbtjänster.

Du kan interagera med Studio -resurser (klassiska) med tre Powershell-moduler:

* [Azure PowerShell Az](#az-rm) släpptes 2018, innehåller alla funktioner i AzureRM, men med olika cmdletnamn
* [AzureRM](#az-rm) släpptes 2016, ersatt av PowerShell Az
* [Azure Machine Learning PowerShell-klassiker](#classic) släpptes 2016

Även om dessa PowerShell-moduler har vissa likheter, är var och en utformad för särskilda scenarier. I den här artikeln beskrivs skillnaderna mellan PowerShell-modulerna och du kan bestämma vilka som ska väljas.  

Kontrollera [supporttabellen](#support-table) nedan för att se vilka resurser som stöds av varje modul. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a>Azure PowerShell Az och AzureRM

Az är nu den avsedda PowerShell-modulen för att interagera med Azure och innehåller alla tidigare funktioner i AzureRM. AzureRM fortsätter att ta emot buggfixar, men det får inga nya cmdlets eller funktioner.  Både Az och AzureRM hanterar lösningar som distribueras med hjälp av distributionsmodellen **för Azure Resource Manager.** Dessa resurser inkluderar Studio (klassiska) arbetsytor och Studio (klassiska) "Nya" webbtjänster. 

PowerShell classic kan installeras tillsammans med antingen Az eller AzureRM för att täcka både "nya" och "klassiska" resurstyper. Det rekommenderas dock inte att az och AzureRM installeras samtidigt. För att välja mellan Az och AzureRM rekommenderar Microsoft Az för alla framtida distributioner.  Läs mer om Az mot AzureRM och migreringsvägen i [introduktionen till Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

För att komma igång med Az, följ [installationsinstruktionerna för Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a>PowerShell-klassiker

Med [den klassiska powershell-modulen Studio](https://aka.ms/amlps) (klassisk) kan du hantera resurser som distribueras med den klassiska **distributionsmodellen**. Dessa resurser inkluderar Studio (klassiska) användartillgångar, "klassiska" webbtjänster och "klassiska" slutpunkter för webbtjänsten.

Microsoft rekommenderar dock att du använder resurshanterarens distributionsmodell för alla framtida resurser för att förenkla distributionen och hanteringen av resurser. Om du vill veta mer om distributionsmodellerna läser du artikeln [med Azure Resource Manager jämfört med klassisk distribution.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)

För att komma igång med PowerShell classic, ladda ner [utgivningspaketet](https://github.com/hning86/azuremlps/releases) från GitHub och följ [instruktionerna för installation](https://github.com/hning86/azuremlps/blob/master/README.md). Instruktionerna förklarar hur du avblockerar den nedladdade/uppackade DLL-filen och sedan importerar den till din PowerShell-miljö.

PowerShell classic kan installeras tillsammans med antingen Az eller AzureRM för att täcka både "nya" och "klassiska" resurstyper.

## <a name="powershell-support-table"></a><a name="support-table"></a>Supporttabell för PowerShell


| | **Az** |  **PowerShell-klassiker** |
| --- | --- | --- |
| Skapa/ta bort arbetsytor | [Resource Manager-mallar](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Hantera planer för arbetsyteåtagande | [Ny-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Hantera användare av arbetsytan |  | [Lägg till-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Hantera webbtjänster | [Ny-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nya" webbtjänster)|| [Ny-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klassiska" webbtjänster) |
| Hantera slutpunkter/nycklar för webbtjänsten |  [Hämta AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Tillägg till AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Hantera användardatauppsättningar/tränade modeller| | [Hämta-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Hantera användarexperiment |  | [Start-AmlUtperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Hantera anpassade moduler | | [Ny-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Nästa steg
Läs den fullständiga dokumentationen av PowerShell-modulen:
* [PowerShell-klassiker](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
