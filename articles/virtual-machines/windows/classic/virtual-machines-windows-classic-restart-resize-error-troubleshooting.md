---
title: Virtuell dator startar om eller ändra storlek på problem med | Microsoft Docs
description: Felsök problem med att starta om eller ändra storlek på en befintlig Windows virtuell dator i Azure i klassiska distributionen
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 06/15/2018
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 400b20e474257650a22e04c89ddde581bf0552f4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35978214"
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Felsök problem med att starta om eller ändra storlek på en befintlig Windows virtuell dator i Azure i klassiska distributionen
> [!div class="op_single_selector"]
> * [Klassisk](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

När du försöker starta en stoppad Azure virtuell dator (VM), eller ändra storlek på en befintlig Azure VM är vanligt fel du stöter på ett Allokeringsfel. Det här felet uppstår när det kluster eller den region antingen inte har resurser som är tillgängliga eller har inte stöd för den begärda VM-storleken.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Samla in granskningsloggar
Starta felsökning genom att samla in granskningsloggar för att identifiera felet som är associerade med problemet.

I Azure-portalen klickar du på **Bläddra** > **virtuella datorer** > *din Windows-dator*  >   **Inställningar för** > **granskningsloggar**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problem: Fel när du startar en stoppad virtuell dator
Försök att starta en stoppad virtuell dator men får ett Allokeringsfel.

### <a name="cause"></a>Orsak
Begäran om att starta stoppad virtuell dator måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret har dock inte ledigt utrymme för att slutföra begäran.

### <a name="resolution"></a>Lösning
* Skapa en ny molntjänst och associera den med antingen en region eller en region-baserat virtuellt nätverk, men inte en tillhörighetsgrupp.
* Ta bort stoppad virtuell dator.
* Återskapa den virtuella datorn i den nya Molntjänsten med hjälp av diskarna.
* Starta den nya virtuella datorn.

Om du får ett felmeddelande när du försöker skapa en ny molntjänst, försök igen senare eller ändra region för Molntjänsten.

> [!IMPORTANT]
> Ny molntjänst får ett nytt namn och VIP, så behöver du ändra den här informationen för alla beroenden som använder denna information för den befintliga Molntjänsten.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problem: Ett fel inträffade när du ändrar storlek på en befintlig virtuell dator
Försök att ändra storlek på en befintlig virtuell dator men får ett Allokeringsfel.

### <a name="cause"></a>Orsak
Förfrågan om att ändra storlek på den virtuella datorn måste göras i det ursprungliga klustret som är värd för Molntjänsten. Klustret stöder dock inte den begärda VM-storleken.

### <a name="resolution"></a>Lösning
Minska den begärda VM-storleken och försöka storleksändring.

* Klicka på **Bläddra igenom alla** > **virtuella datorer (klassiska)** > *din virtuella dator* > **inställningar**  >  **Storlek**. Detaljerade anvisningar finns i [ändra storlek på den virtuella datorn](https://msdn.microsoft.com/library/dn168976.aspx).

Om det inte går att minska virtuella datorstorlek, gör du följande:

* Skapa en ny molntjänst, säkerställer att de är inte länkad till en tillhörighetsgrupp och inte är associerade med ett virtuellt nätverk som är länkad till en tillhörighetsgrupp.
* Skapa en ny, större virtuell dator i den.

Du kan konsolidera alla dina virtuella datorer i samma molntjänst. Om din befintliga molntjänst är associerad med en region-baserat virtuellt nätverk, kan du ansluta ny molntjänst till befintligt virtuellt nätverk.

Om den befintliga Molntjänsten inte är kopplad till en region-baserat virtuellt nätverk har att ta bort de virtuella datorerna i den befintliga Molntjänsten och återskapa dem i den nya Molntjänsten från deras diskar. Det är dock viktigt att komma ihåg att den nya Molntjänsten har ett nytt namn och VIP, så du måste uppdatera dessa för alla beroenden som för närvarande använder den här informationen för den befintliga Molntjänsten.

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du skapar en virtuell Windows-dator i Azure finns i [felsöka distributionsproblem med att skapa en virtuell Windows-dator i Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

