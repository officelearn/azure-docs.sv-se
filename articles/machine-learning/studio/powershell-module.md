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
ms.date: 01/25/2019
ms.openlocfilehash: bd3a82f326cdf7f51e8842e45333ff2bd647c260
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092760"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>PowerShell-moduler för Azure Machine Learning Studio

Med PowerShell-moduler kan hantera du programmässigt dina Studio resurser och resurser, till exempel arbetsytor, datauppsättningar och webbtjänster.

Du kan interagera med Studio resurser med hjälp av tre Powershell-moduler:

* [Azure PowerShell-Az](#az-rm) utgivet 2018, innehåller alla funktioner i AzureRM, men med olika cmdlet-namnen
* [AzureRM](#az-rm) i 2016
* [Klassisk Azure Machine Learning PowerShell](#classic) i 2016

Även om dessa moduler har vissa likheter kan är varje utformad för särskilda scenarier. Den här artikeln beskrivs skillnaderna mellan PowerShell-moduler och hjälper dig att bestämma vilka som du väljer.

## <a name="choosing-modules"></a> Välja moduler

Välja mellan de tillgängliga PowerShell-modulerna beror på vilken typ av resurser som du hanterar.

Kontrollera den [support tabell](#support-table) nedan för att se vilka resurser som stöds av varje modul. Eftersom klassisk PowerShell kan installeras parallellt med Az eller AzureRM, kan du installera två moduler och täcker alla resurstyper (klassiska med Az eller klassiska med AzureRM)

Det rekommenderas dock inte ha Az- och AzureRM installerad på samma gång. Om du vill välja mellan Az- och AzureRM, rekommenderar Microsoft Az för alla framtida distributioner. Använd AzureRm bara om det finns särskilda omständigheter i din miljö som kräver detta.

Läs mer om skillnaderna mellan Az- och AzureRM samt vår angivna migreringssökväg i vår [introduktion till Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Azure PowerShell-Az- och AzureRM

AZ- och AzureRM både hantera lösningar som distribueras med den **Azure Resource Manager** distributionsmodell. Dessa resurser inkluderar Studio-arbetsytor och Studio nya webbtjänster. För att hantera resurser som distribueras med den klassiska distributionsmodellen, bör du använda den klassiska PowerShell-modulen. Om du vill ha mer information om distributionsmodellerna finns i den [Azure Resource Manager och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artikeln.

AZ är nu avsedda PowerShell-modulen för att interagera med Azure och innehåller alla funktioner som tidigare i AzureRM. AzureRM fortsätter att ta emot felkorrigeringar, men den får inga nya cmdletar eller funktioner. Eftersom det uppgradering från AzureRM, om det uppstår problem med Az när du arbetar med Studio kan du rapportera problemet och återgår till att använda AzureRM.

Kom igång med Az genom att följa den [Installationsinstruktioner för Azure-Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell – klassisk

Studio [klassiska PowerShell-modulen](https://aka.ms/amlps) kan du hantera resurser som distribueras med den **klassiska distributionsmodellen**. Dessa resurser inkluderar Studio användaren tillgångar, klassiska webbtjänster och slutpunkter för klassiska webbtjänster.

Microsoft rekommenderar dock att du använder Resource Manager-distributionsmodellen för alla nya resurser för att förenkla distribution och hantering av resurser. Om du vill ha mer information om distributionsmodellerna finns i den [Azure Resource Manager och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) artikeln.

Kom igång med klassisk PowerShell genom att ladda ned den [versionspaketet](https://github.com/hning86/azuremlps/releases) från GitHub och följer de [anvisningar för installation](https://github.com/hning86/azuremlps/blob/master/README.md). Instruktionerna förklarar hur du avblockera den nedladdade/ouppackade DLL-filen och sedan importera den till din PowerShell-miljö.

## <a name="support-table"></a> PowerShell stöd tabell

 **Studio-arbetsytor** | **Az** |  **AzureRM** | **PowerShell – Klassisk** |
| --- | --- | --- | --- |
| Skapa/ta bort arbetsytor | [Resource Manager-mallar](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Resource Manager-mallar](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Hantera arbetsyteanvändare |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Hantera åtagandeprenumerationer | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Webbtjänster** | **Az** | **AzureRM** | **PowerShell – Klassisk** |
| Hantera webbtjänster | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Nya webbtjänster) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Nya webbtjänster) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (klassiska webbtjänster) |
| Hantera slutpunkter/nycklar |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Nya webbtjänster) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Nya webbtjänster) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (klassiska webbtjänster) |
|||
| **Användaren tillgångar** | **Az** | **AzureRM** | **PowerShell – Klassisk** |
| Hantera datauppsättningar/utbildade modeller |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Hantera experiment |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Hantera anpassade moduler |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Nästa steg
Du kan följa dessa länkar för fullständig dokumentation för PowerShell-moduler:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell – Klassisk](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
