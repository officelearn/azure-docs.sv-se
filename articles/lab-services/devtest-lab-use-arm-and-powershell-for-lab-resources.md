---
title: Skapa eller ändra labb automatiskt med Azure Resource Manager-mallar med PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure Resource Manager-mallar med PowerShell för att skapa eller ändra labb automatiskt i ett labb
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622465"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Skapa eller ändra labb automatiskt med Azure Resource Manager-mallar och PowerShell

DevTest Labs innehåller många Azure Resource Manager-mallar och PowerShell-skript som kan hjälpa dig att snabbt och automatiskt skapa nya övningar eller ändra befintliga labb och distribuerar dessa resurser.

Den här artikeln hjälper till att guida dig genom processen för att använda dessa mallar och skript för att automatisera den skapas, ändras eller distribution av dina labb. Den här artikeln visar även där du hittar mer information om hur du använder PowerShell för att utföra vissa vanliga uppgifter i DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Steg 1: Samla in dina mallar och skript
Du kan hitta färdiga [Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) och [PowerShell-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) på vårt offentliga [GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab). Använda dem som – är, eller anpassa dem efter dina behov och lagra dem i din egen [privata Git-lagringsplats](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Steg 2: Ändra din Azure Resource Manager-mall
Du kan följa stegen i [skapa din första Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) om du aldrig har skapat en mall innan.

Dessutom [bästa praxis för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) erbjuder många riktlinjer och förslag för att skapa Azure Resource Manager-mallar som är tillförlitliga och enkelt att använda. Normalt kan du använda en variant av en av metoderna eller exempel som tillhandahålls och ändra mallen för dina behov.

## <a name="step-3-deploy-resources-with-powershell"></a>Steg 3: Distribuera resurser med PowerShell
När du har anpassat dina mallar och skript, följer du stegen för att [distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Artikeln innehåller allmän information om hur du använder Azure PowerShell med Azure Resource Manager-mallar för att distribuera dina resurser till Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Vanliga uppgifter som du kan utföra i labb med hjälp av PowerShell
Det finns många andra vanliga aktiviteter som du kan automatisera med hjälp av PowerShell. I följande avsnitt av dokumentationen beskriver de steg som krävs för att utföra dessa uppgifter.

* [Skapa en anpassad avbildning från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Överför VHD-filen till övningen storage-konto med hjälp av PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Lägga till en extern användare i ett labb med hjälp av PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Skapa en anpassad roll för labb med hjälp av PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar en [privata Git-lagringsplats](devtest-lab-add-artifact-repo.md) där du vill lagra dina anpassade mallar eller skript.
* Utforska den [Azure Resource Manager-mallar från Azure Quickstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates).
