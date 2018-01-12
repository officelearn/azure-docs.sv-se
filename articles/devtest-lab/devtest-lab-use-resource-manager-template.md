---
title: "Visa och använda en virtuell dators Azure Resource Manager-mall | Microsoft Docs"
description: "Lär dig hur du skapar andra virtuella datorer med hjälp av Azure Resource Manager-mall från en virtuell dator"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: v-craic
ms.openlocfilehash: 97822d5fb11c5c106c67aaaab0b8972e1ec8deee
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Skapa virtuella datorer med en Azure Resource Manager-mall 

När du skapar en virtuell dator (VM) i DevTest Labs via den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), du kan visa Azure Resource Manager-mallen innan du sparar den virtuella datorn. Mallen kan användas som grund för att skapa mer labb virtuella datorer med samma inställningar.

Den här artikeln beskriver Multi-VM kontra enskild VM Resource Manager-mallar och visar hur du visa och spara en mall när du skapar en virtuell dator.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Flera Virtuella kontra enskild VM Resource Manager-mallar
Det finns två sätt att skapa virtuella datorer i DevTest Labs med hjälp av en Resource Manager-mall: etablera Microsoft.DevTestLab/labs/virtualmachines resurs eller etablera Microsoft.Commpute/virtualmachines resursen. Var används i olika scenarier och kräver olika behörigheter.

- Resource Manager-mallar som använder en resurstyp i Microsoft.DevTestLab/labs/virtualmachines (som deklarerats i egenskapen ”resurser” i mallen) kan etablera enskilda lab virtuella datorer. Varje virtuell dator visas sedan som ett enstaka objekt i listan DevTest Labs virtuella datorer:

   ![Lista över virtuella datorer som enda objekt i listan över virtuella datorer DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Den här typen av Resource Manager-mall kan etableras via Azure PowerShell-kommandot **ny AzureRmResourceGroupDeployment** eller med kommandot Azure CLI **az distribution skapa**. Det krävs administratörsbehörighet, så att användare som är tilldelade till en DevTest Labs användarroll inte kan utföra distributionen. 

- Resource Manager-mallar som använder en Microsoft.Compute/virtualmachines resurstyp kan tillhandahålla flera virtuella datorer som en enda miljö i listan DevTest Labs virtuella datorer:

   ![Lista över virtuella datorer som enda objekt i listan över virtuella datorer DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuella datorer i samma miljö kan hanteras tillsammans och har samma livscykel. Användare som är tilldelade till en användarroll för DevTest Labs kan skapa miljöer med hjälp av dessa mallar så länge som administratören har konfigurerat labbet sätt.

Resten av den här artikeln beskrivs Resource Manager-mallar som använder Mirosoft.DevTestLab/labs/virtualmachines. Dessa används av lab administratörer att automatisera skapa ett Virtuellt labb (till exempel claimable virtuella datorer) eller generering av gyllene bild (till exempel bild factory).

[Bästa praxis för att skapa mallar för Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) erbjuder många riktlinjer och förslag på hur du skapar Azure Resource Manager-mallar som är tillförlitliga och enkla att använda.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Visa och spara Resource Manager-mall för en virtuell dator
1. Följ stegen i [skapar din första virtuella datorn i ett labb](devtest-lab-create-first-vm.md) du vill skapa en virtuell dator.
1. Ange informationen som krävs för den virtuella datorn och Lägg till alla artefakter som du vill använda för den här virtuella datorn.
1. Längst ned i fönstret för att konfigurera inställningar väljer **visa ARM-mallen**.

   ![Visa knappen för ARM-mall](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Kopiera och spara Resource Manager-mall att använda senare för att skapa en annan virtuell dator.

   ![Resource Manager-mall för att spara för senare användning](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

När du har sparat Resource Manager-mall, måste du uppdatera avsnittet parametrar i mallen innan du kan använda den. Du kan skapa en parameter.json som anpassar bara parametrar, utanför den faktiska Resource Manager-mallen. 

![Anpassa parametrar med en JSON-fil](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Resource Manager-mallen är nu redo att använda att [skapa en virtuell dator](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa flera Virtuella miljöer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* [Distribuera en Resource Manager-mall för att skapa en virtuell dator](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Utforska mer Resource Manager-snabbstartsmallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-repo-](https://github.com/Azure/azure-quickstart-templates).
