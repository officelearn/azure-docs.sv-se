---
title: PowerShell-moduler
titleSuffix: ML Studio (classic) - Azure
description: Använd PowerShell för att skapa och hantera Azure Machine Learning Studio (klassiska) arbets ytor, experiment, webb tjänster och mycket annat.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 04/25/2019
ms.openlocfilehash: 0d465c27621464d840555edb1a0f513fbc195e13
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148121"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>PowerShell-moduler för Azure Machine Learning Studio (klassisk)

Med hjälp av PowerShell-moduler kan du hantera dina Studio (klassiska) resurser och till gångar program mässigt, till exempel arbets ytor, data uppsättningar och webb tjänster.

Du kan interagera med Studio (klassiska) resurser med tre PowerShell-moduler:

* [Azure PowerShell AZ](#az-rm) som släpptes i 2018, innehåller alla funktioner i AzureRM, men med olika cmdlet-namn
* [AzureRM](#az-rm) släpptes i 2016, ersatt av PowerShell AZ
* [Azure Machine Learning PowerShell Classic](#classic) lanseras i 2016

Även om dessa PowerShell-moduler har en del likheter, är var och en utformad för specifika scenarier. Den här artikeln beskriver skillnaderna mellan PowerShell-modulerna och hjälper dig att bestämma vilka du vill välja.  

Se efter i [support tabellen](#support-table) nedan om vilka resurser som stöds av varje modul. 

## <a name="az-rm"></a>Azure PowerShell AZ och AzureRM

AZ är nu den avsedda PowerShell-modulen för att interagera med Azure och innehåller alla tidigare funktioner i AzureRM. AzureRM kommer att fortsätta att ta emot fel korrigeringar, men det får inga nya cmdletar eller funktioner.  AZ och AzureRM båda hanterar lösningar som distribueras med **Azure Resource Manager** distributions modell. Dessa resurser innehåller Studio (klassiska) arbets ytor och Studio (klassiska) "nya" webb tjänster. 

PowerShell Classic kan installeras tillsammans med antingen AZ eller AzureRM för att inkludera både "nya" och "klassiska" resurs typer. Men vi rekommenderar inte att du har AZ och AzureRM installerade samtidigt. För att kunna välja mellan AZ och AzureRM rekommenderar Microsoft AZ för alla framtida distributioner.  Läs mer om AZ jämfört med AzureRM och sökvägen för migrering i [Introduktion till Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Kom igång med AZ genom att följa [installations anvisningarna för Azure AZ](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a>Klassisk PowerShell

Den [klassiska PowerShell-modulen](https://aka.ms/amlps) för Studio (klassisk) gör att du kan hantera resurser som distribueras med den **klassiska distributions modellen**. Dessa resurser inkluderar Studio (klassiska) användar till gångar, "klassiska" webb tjänster och "klassiska" webb tjänst slut punkter.

Microsoft rekommenderar dock att du använder distributions modellen för Resource Manager för alla framtida resurser för att förenkla distributionen och hanteringen av resurser. Om du vill lära dig mer om distributions modellerna kan du läsa artikeln [Azure Resource Manager kontra klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) .

Kom igång med PowerShell Classic genom att ladda ned [versions paketet](https://github.com/hning86/azuremlps/releases) från GitHub och följa [installations anvisningarna](https://github.com/hning86/azuremlps/blob/master/README.md). Anvisningarna beskriver hur du avblockerar den nedladdade/zippade DLL-filen och importerar den sedan till din PowerShell-miljö.

PowerShell Classic kan installeras tillsammans med antingen AZ eller AzureRM för att inkludera både "nya" och "klassiska" resurs typer.

## <a name="support-table"></a>PowerShell-support tabell


| | **AZ** |  **PowerShell – Klassisk** |
| --- | --- | --- |
| Skapa/ta bort arbets ytor | [Resource Manager-mallar](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Hantera åtagande planer för arbets ytan | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Hantera arbets ytans användare |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Hantera webbtjänster | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nya" webb tjänster)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klassiska" webb tjänster) |
| Hantera webb tjänst slut punkter/nycklar |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Hantera användar data uppsättningar/utbildade modeller| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Hantera användar experiment |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Hantera anpassade moduler | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Nästa steg
Läs den fullständiga dokumentationen i PowerShell-modulen:
* [PowerShell – Klassisk](https://aka.ms/amlps)
* [Azure PowerShell AZ](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
