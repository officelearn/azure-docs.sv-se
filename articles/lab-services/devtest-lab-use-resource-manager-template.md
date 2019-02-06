---
title: Visa och använda en virtuell dators Azure Resource Manager-mall | Microsoft Docs
description: Lär dig hur du använder Azure Resource Manager-mall från en virtuell dator för att skapa andra virtuella datorer
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 99c4f838c3c4e4708c3e21ff9c7e63b69a507dbe
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746922"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Skapa virtuella datorer med en Azure Resource Manager-mall 

När du skapar en virtuell dator (VM) i DevTest Labs via den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040), du kan visa Azure Resource Manager-mallen innan du sparar den virtuella datorn. Mallen kan användas som utgångspunkt för att skapa mer labb virtuella datorer med samma inställningar.

Den här artikeln beskriver flera virtuella datorer jämfört med enskild VM Resource Manager-mallar och visar hur du visa och spara en mall när du skapar en virtuell dator.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Flera virtuella datorer jämfört med enskild VM Resource Manager-mallar
Det finns två sätt att skapa virtuella datorer i labb med hjälp av en Resource Manager-mall: etablera Microsoft.DevTestLab/labs/virtualmachines resursen eller etablera Microsoft.Commpute/virtualmachines resursen. Var och en används i olika scenarier och kräver olika behörigheter.

- Resource Manager-mallar som använder en resurstyp för Microsoft.DevTestLab/labs/virtualmachines (såsom anges i egenskapen ”resurser” i mallen) kan etablera enskilda lab virtuella datorer. Varje virtuell dator visas sedan som ett enskilt objekt i listan över virtuella datorer med Labb:

   ![Lista över virtuella datorer som enstaka objekt i listan över virtuella datorer DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Den här typen av Resource Manager-mall kan etableras via Azure PowerShell-kommando **New-AzureRmResourceGroupDeployment** eller via Azure CLI-kommando **az group deployment skapa**. Det krävs administratörsbehörighet, så att användare som är tilldelade till en användarroll för DevTest Labs inte kan utföra distributionen. 

- Resource Manager-mallar som använder en resurstyp för Microsoft.Compute/virtualmachines kan tillhandahålla flera virtuella datorer som en enda miljö i listan över virtuella datorer med Labb:

   ![Lista över virtuella datorer som enstaka objekt i listan över virtuella datorer DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuella datorer i samma miljö kan hanteras tillsammans och har samma livscykel. Användare som är tilldelade till en användarroll för DevTest Labs kan skapa miljöer med hjälp av dessa mallar så länge som administratören har konfigurerat testmiljön sätt.

Resten av den här artikeln beskriver Resource Manager-mallar som använder Microsoft.DevTestLab/labs/virtualmachines. De används av lab administratörer för att automatisera skapandet av lab VM (till exempel tillgängliga virtuella datorer) eller gyllene bilden generation (till exempel bild factory).

[Bästa praxis för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) erbjuder många riktlinjer och förslag för att skapa Azure Resource Manager-mallar som är tillförlitliga och enkelt att använda.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Visa och spara en virtuell dators Resource Manager-mall
1. Följ stegen i [skapa din första virtuella dator i ett labb](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) att börja skapa en virtuell dator.
1. Ange informationen som krävs för den virtuella datorn och Lägg till alla artefakter som du vill använda för den här virtuella datorn.
1. Längst ned i fönstret Konfigurera inställningar väljer **visa ARM-mallen**.

   ![Visa knappen för ARM-mall](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Kopiera och spara Resource Manager-mall för att använda senare för att skapa en annan virtuell dator.

   ![Resource Manager-mall för att spara för senare användning](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

När du har sparat Resource Manager-mall, måste du uppdatera parametrar i mallen innan du kan använda den. Du kan skapa en parameter.json som anpassar bara parametrar, utanför den Resource Manager-mallen. 

![Anpassa parametrar med en JSON-fil](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Resource Manager-mallen är nu redo att använda att [skapa en virtuell dator](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa miljöer för flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* [Distribuera en Resource Manager-mall för att skapa en virtuell dator](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Utforska fler Snabbstart Resource Manager-mallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates).
