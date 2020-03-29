---
title: Skapa eller ändra labb med Azure Resource Manager-mallar
description: Lär dig hur du använder Azure Resource Manager-mallar med PowerShell för att skapa eller ändra labb automatiskt i ett DevTest-labb
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 180081829387afeb7e63defe7a22378aa8d417d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170297"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Skapa eller ändra labb automatiskt med Azure Resource Manager-mallar och PowerShell

DevTest Labs innehåller många Azure Resource Manager-mallar och PowerShell-skript som kan hjälpa dig att snabbt och automatiskt skapa nya labb eller ändra befintliga labb och sedan distribuera dessa resurser.

Den här artikeln hjälper dig att använda dessa mallar och skript för att automatisera skapandet, ändringen och distributionen av dina labb. I den här artikeln visas också var du kan hitta mer information om hur du använder PowerShell för att utföra några vanliga uppgifter i DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Steg 1: Samla in mallar och skript
Du hittar färdiga [Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) och [PowerShell-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) i vår offentliga [GitHub-databas](https://github.com/Azure/azure-devtestlab). Använd dem som de är, eller anpassa dem för dina behov och lagra dem i din egen [privata Git repo.](devtest-lab-add-artifact-repo.md)

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Steg 2: Ändra din Azure Resource Manager-mall
Du kan följa stegen på [Skapa din första Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) om du aldrig har skapat en mall tidigare.

Dessutom innehåller [metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) många riktlinjer och förslag som hjälper dig att skapa Azure Resource Manager-mallar som är tillförlitliga och enkla att använda. Vanligtvis använder du en variant av en av de metoder eller exempel som tillhandahålls och ändrar mallen för dina behov.

## <a name="step-3-deploy-resources-with-powershell"></a>Steg 3: Distribuera resurser med PowerShell
När du har anpassat mallarna och skripten följer du stegen som krävs för att [distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Artikeln innehåller allmän information om hur du använder Azure PowerShell med Azure Resource Manager-mallar för att distribuera dina resurser till Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Vanliga uppgifter som du kan utföra i DevTest-övningar med PowerShell
Det finns många andra vanliga uppgifter som du kan automatisera med PowerShell. I följande avsnitt i dokumentationen beskrivs de steg som krävs för att utföra dessa uppgifter.

* [Skapa en anpassad avbildning från en VHD-fil med PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Ladda upp VHD-fil till labbets lagringskonto med PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Lägga till en extern användare i ett labb med PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Skapa en anpassad labbroll med PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar en [privat Git-databas](devtest-lab-add-artifact-repo.md) där du ska lagra dina anpassade mallar eller skript.
* Utforska [Azure Resource Manager-mallarna från Azure Quickstart-mallgalleriet](https://github.com/Azure/azure-quickstart-templates).
