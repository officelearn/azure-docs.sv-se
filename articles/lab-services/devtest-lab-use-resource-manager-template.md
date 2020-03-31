---
title: Visa och använda en virtuell dators Azure Resource Manager-mall
description: Lär dig hur du använder Azure Resource Manager-mallen från en virtuell dator för att skapa andra virtuella datorer
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169120"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Skapa virtuella datorer med hjälp av en Azure Resource Manager-mall 

När du skapar en virtuell dator (VM) i DevTest Labs via [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040)kan du visa Azure Resource Manager-mallen innan du sparar den virtuella datorn. Mallen kan sedan användas som grund för att skapa fler virtuella labb-datorer med samma inställningar.

I den här artikeln beskrivs mallar för resurshanteraren för flera virtuella datorer jämfört med en virtuell dator och hur du visar och sparar en mall när du skapar en virtuell dator.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Resurshanterarens mallar för flera virtuella datorer jämfört med en virtuell dator
Det finns två sätt att skapa virtuella datorer i DevTest Labs med hjälp av en Resource Manager-mall: etablera microsoft.DevTestLab/labs/virtualmachines-resursen eller etablera Microsoft.Compute/virtualmachines-resursen. Var och en används i olika scenarier och kräver olika behörigheter.

- Resource Manager-mallar som använder en Microsoft.DevTestLab/labs/virtualmachines-resurstyp (som deklarerats i egenskapen "resurs" i mallen) kan etablera enskilda virtuella labb-datorer. Varje virtuell dator visas sedan som ett enda objekt i listan DevTest Labs virtuella datorer:

   ![Lista över virtuella datorer som enskilda objekt i listan Virtuella Datorer i DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Den här typen av Resource Manager-mall kan etableras via Azure **PowerShell-kommandot New-AzResourceGroupDeployment** eller via Azure CLI-kommandot **az group deployment create**. Det kräver administratörsbehörigheter, så att användare som har tilldelats med en DevTest Labs-användarroll inte kan utföra distributionen. 

- Resource Manager-mallar som använder en Microsoft.Compute/virtualmachines-resurstyp kan etablera flera virtuella datorer som en enda miljö i listan DevTest Labs virtuella datorer:

   ![Lista över virtuella datorer som enskilda objekt i listan Virtuella Datorer i DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuella datorer i samma miljö kan hanteras tillsammans och dela samma livscykel. Användare som har tilldelats med en DevTest Labs-användarroll kan skapa miljöer med hjälp av dessa mallar så länge administratören har konfigurerat labbet på det sättet.

I resten av den här artikeln beskrivs Resource Manager-mallar som använder Microsoft.DevTestLab/labs/virtualmachines. Dessa används av labbadministratörer för att automatisera skapande av virtuella labb (till exempel anspråksbara virtuella datorer) eller gyllene avbildningsgenerering (till exempel bildfabrik).

[Metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) innehåller många riktlinjer och förslag som hjälper dig att skapa Azure Resource Manager-mallar som är tillförlitliga och enkla att använda.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Visa och spara en resurshanterares mall för en virtuell dator
1. Följ stegen på [Skapa din första virtuella dator i ett labb](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) för att börja skapa en virtuell dator.
1. Ange den information som krävs för den virtuella datorn och lägg till de artefakter du vill ha för den här virtuella datorn.
1. Swtich till fliken **Avancerade inställningar.** 
1. Längst ned i fönstret Konfigurera inställningar väljer du **Visa ARM-mall**.
1. Kopiera och spara resource manager-mallen som ska användas senare för att skapa en annan virtuell dator.

   ![Resource Manager-mall som ska sparas för senare användning](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

När du har sparat resource manager-mallen måste du uppdatera parameteravsnittet i mallen innan du kan använda den. Du kan skapa en parameter.json som bara anpassar parametrarna, utanför den faktiska Resource Manager-mallen. 

![Anpassa parametrar med hjälp av en JSON-fil](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Resource Manager-mallen är nu klar att användas för att [skapa en virtuell dator](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Ange utgångsdatum
I scenarier som utbildning, demonstrationer och utvärderingsversioner kanske du vill skapa virtuella datorer och ta bort dem automatiskt efter en fast varaktighet så att du inte ådrar dig onödiga kostnader. Du kan skapa en virtuell labbdator med ett utgångsdatum genom att ange egenskapen **expirationDate** för den virtuella datorn. Kolla in samma Resource Manager-mall i [vårt GitHub-arkiv](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar miljöer med flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* [Distribuera en Resource Manager-mall för att skapa en virtuell dator](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Utforska fler snabbstarts resource manager-mallar för DevTest Labs automatisering från den [offentliga DevTest Labs GitHub repo](https://github.com/Azure/azure-quickstart-templates).
