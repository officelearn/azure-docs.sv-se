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
ms.openlocfilehash: d3800fe360a2451bdc39644e713b82ab0608ef12
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="use-a-virtual-machines-azure-resource-manager-template"></a>Använd Azure Resource Manager-mall för en virtuell dator

När du skapar en virtuell dator (VM) i DevTest Labs via den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), du kan visa Azure Resource Manager-mallen innan du sparar den virtuella datorn. Mallen kan användas som grund för att skapa mer labb virtuella datorer med samma inställningar.

Den här artikeln beskriver hur du visar Resource Manager-mallen när du skapar en virtuell dator och hur du distribuerar den senare om du vill automatisera genereringen av samma virtuella dator.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Flera Virtuella kontra enskild VM Resource Manager-mallar
Det finns två sätt att skapa virtuella datorer i DevTest Labs med hjälp av en Resource Manager-mall: etablera Microsoft.DevTestLab/labs/virtualmachines resurs eller etablera Microsoft.Commpute/virtualmachines resursen. Varje används i olika scenarier och kräver olika behörigheter.

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

   ![Resource manager-mall för att spara för senare användning](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

När du har sparat Resource Manager-mall, måste du uppdatera avsnittet parametrar i mallen innan du kan använda den. Du kan skapa en parameter.json som anpassar bara parametrar, utanför den faktiska Resource Manager-mallen. 

![Anpassa parametrar med hjälp av en JSON-fil](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Distribuera en Resource Manager-mall för att skapa en virtuell dator
När du har sparat en Resource Manager-mall och anpassas efter dina behov, kan du använda den till att automatisera genereringen av en virtuell dator. [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) beskriver hur du använder Azure PowerShell med Resource Manager-mallar för att distribuera resurserna till Azure. [Distribuera resurser med Resource Manager-mallar och Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) beskriver hur du använder Azure CLI med Resource Manager-mallar för att distribuera resurserna till Azure.

> [!NOTE]
> Endast en användare med lab ägare behörighet kan skapa virtuella datorer från en Resource Manager-mall med hjälp av Azure PowerShell. Om du vill automatisera genereringen av en virtuell dator med en Resource Manager-mall och du endast har behörighet, kan du använda den [ **az lab vm skapa** i CLI](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa flera Virtuella miljöer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* Utforska mer Snabbkurs Resource Manager-mallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-repo-](https://github.com/Azure/azure-quickstart-templates).
