---
title: "Skapa eller ändra labs automatiskt med Azure Resource Manager-mallar med PowerShell | Microsoft Docs"
description: "Lär dig hur du använder Azure Resource Manager-mallar med PowerShell för att skapa eller ändra labs automatiskt i ett DevTest-labb"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
ms.openlocfilehash: c9f293c7c96633cd0116dc3c19189085d24048b4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Skapa eller ändra labs automatiskt med Azure Resource Manager-mallar och PowerShell

DevTest Labs innehåller mallar för Azure Resource Manager och PowerShell-skript som kan hjälpa dig att snabbt och automatiskt skapa nya övningar eller ändra befintliga labs och sedan distribuera dessa resurser.

Den här artikeln får du stegvisa anvisningar genom att använda dessa mallar och skript för att automatisera skapas, ändras eller distribution av ditt labb. Den här artikeln visar också var du hittar mer information om hur du använder PowerShell för att utföra vissa vanliga aktiviteter i DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Steg 1: Samla in dina mallar och skript
Du kan hitta färdiga [Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) och [PowerShell-skript](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) på vårt offentliga [Github-lagringsplatsen](https://github.com/Azure/azure-devtestlab). Använda dem som-är, eller anpassa dem efter dina behov och lagra dem i din egen [privata Git repo](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Steg 2: Ändra Azure Resource Manager-mall
Du kan följa stegen i [skapa din första Azure Resource Manager-mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) om du aldrig har skapat en mall innan.

Dessutom [bästa praxis för att skapa mallar för Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) erbjuder många riktlinjer och förslag på hur du skapar Azure Resource Manager-mallar som är tillförlitliga och enkla att använda. Du kommer normalt använda en variant av en av de metoder eller exempel tillhandahålls och ändra mallen för dina behov.

## <a name="step-3-deploy-resources-with-powershell"></a>Steg 3: Distribuera resurser med PowerShell
När du har anpassat dina mallar och skript, följer du stegen för att [distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Artikeln innehåller allmän information om hur du använder Azure PowerShell med Azure Resource Manager-mallar för att distribuera resurserna till Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Vanliga uppgifter som du kan utföra i DevTest labs med hjälp av PowerShell
Det finns många andra vanliga aktiviteter som du kan automatisera med hjälp av PowerShell. I dokumentationen till följande avsnitt beskriver de steg som krävs för att utföra dessa uppgifter.

* [Skapa en anpassad avbildning från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Överföra VHD-filen till labblagringskontot med hjälp av PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Lägg till en extern användare i ett testlabb med hjälp av PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Skapa en anpassad labb-roll med hjälp av PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar en [privata Git-lagringsplats](devtest-lab-add-artifact-repo.md) där du vill lagra din anpassade mallar eller skript.
* Utforska den [Azure Resource Manager-mallar från Azure Quickstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates).
