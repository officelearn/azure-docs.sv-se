---
title: PowerShell-moduler för Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Skapa och hantera Azure Machine Learning Studio-arbetsytor, experiment, webbtjänster och annat med hjälp av PowerShell.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: e3c2587fceed265c9768b6ea6f2ecf3b9a8b7b1a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011360"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>PowerShell-moduler för Azure Machine Learning Studio

Med PowerShell-moduler kan hantera du programmässigt dina Studio resurser och resurser, till exempel arbetsytor, datauppsättningar och webbtjänster.

Du kan interagera med Studio resurser med hjälp av tre Powershell-moduler:

* [Azure PowerShell-Az](#az-rm) utgivet 2018, innehåller alla funktioner i AzureRM, men med olika cmdlet-namnen
* [AzureRM](#az-rm) i 2016, ersatts av PowerShell-Az
* [Klassisk Azure Machine Learning PowerShell](#classic) i 2016

Även om dessa PowerShell-moduler har vissa likheter kan är varje utformad för särskilda scenarier. Den här artikeln beskrivs skillnaderna mellan PowerShell-moduler och hjälper dig att bestämma vilka som du väljer.  

Kontrollera den [support tabell](#support-table) nedan för att se vilka resurser som stöds av varje modul. 

## <a name="az-rm"></a> Azure PowerShell-Az- och AzureRM

AZ är nu avsedda PowerShell-modulen för att interagera med Azure och innehåller alla funktioner som tidigare i AzureRM. AzureRM fortsätter att ta emot felkorrigeringar, men den får inga nya cmdletar eller funktioner.  AZ- och AzureRM både hantera lösningar som distribueras med den **Azure Resource Manager** distributionsmodell. Dessa resurser inkluderar Studio-arbetsytor och ”New” Studio-webbtjänster. 

Klassisk PowerShell kan installeras tillsammans med Az eller AzureRM för att täcka både ”nytt” och ”klassiska” resurstyper. Det rekommenderas dock inte ha Az- och AzureRM installerad på samma gång. Om du vill välja mellan Az- och AzureRM, rekommenderar Microsoft Az för alla framtida distributioner.  Läs mer om Az jämfört med AzureRM- och migreringsprocessen i [introduktion till Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Kom igång med Az genom att följa den [Installationsinstruktioner för Azure-Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell – klassisk

Studio [klassiska PowerShell-modulen](https://aka.ms/amlps) kan du hantera resurser som distribueras med den **klassiska distributionsmodellen**. Dessa resurser inkluderar Studio användaren tillgångar, ”klassiska” webbtjänster och slutpunkter för ”klassiska” webbtjänster.

Microsoft rekommenderar dock att du använder Resource Manager-distributionsmodellen för alla framtida resurser för att förenkla distribution och hantering av resurser. Om du vill ha mer information om distributionsmodellerna finns i den [Azure Resource Manager och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artikeln.

Kom igång med klassisk PowerShell genom att ladda ned den [versionspaketet](https://github.com/hning86/azuremlps/releases) från GitHub och följer de [anvisningar för installation](https://github.com/hning86/azuremlps/blob/master/README.md). Instruktionerna förklarar hur du avblockera den nedladdade/ouppackade DLL-filen och sedan importera den till din PowerShell-miljö.

Klassisk PowerShell kan installeras tillsammans med Az eller AzureRM för att täcka både ”nytt” och ”klassiska” resurstyper.

## <a name="support-table"></a> PowerShell stöd tabell


| | **Az** |  **PowerShell – Klassisk** |
| --- | --- | --- |
| Skapa/ta bort arbetsytor | [Resource Manager-mallar](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Hantera arbetsytan åtagandeprenumerationer | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Hantera arbetsyteanvändare |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Hantera webbtjänster | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>(”nytt” webbtjänster)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(”klassiska” web services) |
| Hantera web service slutpunkter/nycklar |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Hantera användare datauppsättningar/utbildade modeller| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Hantera användare experiment |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Hantera anpassade moduler | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Nästa steg
I den fullständiga dokumentationen dessa PowerShell-modulen:
* [PowerShell – Klassisk](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
