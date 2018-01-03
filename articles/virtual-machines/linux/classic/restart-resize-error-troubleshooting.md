---
title: "VM att starta om eller ändrar storlek på frågor | Microsoft Docs"
description: "Felsöka klassisk distributionsproblem med att starta om eller ändrar storlek på en befintlig virtuell Linux-dator i Azure"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 73f2672c-602e-4766-8948-2b180115d299
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: required
ms.date: 01/10/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 7f5718a7e1ab2b14902fa61ffb3053910e584ac6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Felsöka klassisk distributionsproblem med att starta om eller ändrar storlek på en befintlig virtuell Linux-dator i Azure
> [!div class="op_single_selector"]
> * [Klassisk](restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

När du försöker starta en stoppad Azure virtuell dator (VM), eller ändra storlek på en befintlig Azure VM är vanligt fel du stöter på ett Allokeringsfel. Det här felet uppstår när det kluster eller den region har inte resurser som är tillgänglig eller har inte stöd för den begärda VM-storleken.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-version finns [här](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Samla in granskningsloggar
Starta felsökning genom att samla in granskningsloggar för att identifiera fel som är associerade med problemet.

I Azure-portalen klickar du på **Bläddra** > **virtuella datorer** > *Linux-dator* > **inställningar** > **granskningsloggar**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fel när du startar en stoppad virtuell dator
Försök att starta en stoppad virtuell dator, men ett fel vid allokering.

### <a name="cause"></a>Orsak
Begäran om att starta stoppad VM måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret har dock inte ledigt utrymme för att slutföra begäran.

### <a name="resolution"></a>Lösning
* Skapa en ny molntjänst och associera den med antingen en region eller en region-baserat virtuellt nätverk, men inte en tillhörighetsgrupp.
* Ta bort stoppad virtuell dator.
* Skapa den virtuella datorn i den nya Molntjänsten med hjälp av diskarna.
* Starta den nya virtuella datorn.

Om du får ett fel vid försök att skapa en ny molntjänst försök vid ett senare tillfälle eller ändra region för Molntjänsten.

> [!IMPORTANT]
> Ny molntjänst får ett nytt namn och VIP, så du behöver ändra den här informationen för alla beroenden som använder denna information för en befintlig molntjänst.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Ett fel uppstod när du ändrar storlek på en befintlig virtuell dator
Försök att ändra storlek på en befintlig virtuell dator men får ett Allokeringsfel.

### <a name="cause"></a>Orsak
Begäran att ändra storlek på den virtuella datorn måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret stöder dock inte den begärda VM-storleken.

### <a name="resolution"></a>Lösning
Minska den begärda VM-storleken och försök ändra storlek på begäran.

* Klicka på **Bläddra igenom alla** > **virtuella datorer (klassisk)** > *din virtuella dator* > **inställningar** > **storlek**. Detaljerade anvisningar finns i [ändra storlek på den virtuella datorn](https://msdn.microsoft.com/library/dn168976.aspx).

Följ dessa steg om det inte är möjligt att minska VM-storlek:

* Skapa en ny molntjänst säkerställer den inte kopplad till en tillhörighetsgrupp och inte är associerad med ett virtuellt nätverk som är kopplad till en tillhörighetsgrupp.
* Skapa en ny, större storlek virtuell dator i den.

Du kan konsolidera alla dina virtuella datorer i samma molntjänst. Om en befintlig molntjänst är associerad med en region-baserat virtuellt nätverk kan du ansluta ny molntjänst till befintligt virtuellt nätverk.

Om en befintlig molntjänst inte är associerad med en region-baserat virtuellt nätverk, har att ta bort de virtuella datorerna i en befintlig molntjänst och återskapa dem i den nya tjänsten molntjänster från sina diskar. Det är dock viktigt att komma ihåg att ny molntjänst får ett nytt namn och VIP, så behöver du uppdatera dessa för de beroenden som för närvarande använder den här informationen för en befintlig molntjänst.

## <a name="next-steps"></a>Nästa steg
Om du får problem när du skapar en ny Linux VM i Azure, se [felsöka distributionsproblem med att skapa en ny virtuell Linux-dator i Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

