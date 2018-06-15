---
title: Felsöka Windows VM distribution-klassisk | Microsoft Docs
description: Felsökning av problem med klassisk distribution när du skapar en ny Windows virtuell dator i Azure
services: virtual-machines-windows
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f01d237-ba39-4c32-b72d-18f5f505d43a
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 5a1ad33bd8c287910458d26a96858e85c689fb16
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
ms.locfileid: "24056669"
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Felsöka klassisk distributionsproblem med att skapa en ny Windows virtuell dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-version av den här artikeln finns [här](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Samla in granskningsloggar
Starta felsökning genom att samla in granskningsloggar för att identifiera fel som är associerade med problemet.

I Azure-portalen klickar du på **Bläddra** > **virtuella datorer** > *din Windows-dator* > **inställningar** > **granskningsloggar**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** om Operativsystemet är Windows generaliserad, och det är upp och/eller avbildas med inställningen generaliserad och det inte finns några fel. På samma sätt om Operativsystemet är Windows specialiserade, och den är upp och/eller avbildas med inställningen för särskilda och det inte finns några fel.

**Överför fel:**

**N<sup>1</sup>:** om Operativsystemet är Windows generaliserad och det överförs som specialiserade, du får ett etablering timeout-fel med den virtuella datorn som fastnat på skärmen OOBE.

**N<sup>2</sup>:** om Operativsystemet är Windows specialiserade och det överförs som generaliserad, du får felet etablering fel med den virtuella datorn som fastnat på skärmen OOBE eftersom den nya virtuella datorn körs med det ursprungliga datornamnet, användarnamn och lösenord.

**Lösning:**

Överför den ursprungliga virtuella Hårddisken tillgänglig lokalt, med samma inställning som för OS (generaliserad/specialiserade) för att lösa båda dessa fel. Kom ihåg att köra sysprep först om du vill överföra som generaliserad. Se [skapa och ladda upp en Windows Server VHD till Azure](createupload-vhd.md) för mer information.

**Avbilda fel:**

**N<sup>3</sup>:** om Operativsystemet är Windows generaliserad och avbildas som specialiserade, du får en allokering tidsgränsfel eftersom den ursprungliga virtuella datorn inte kan användas eftersom den har markerats som generaliserad.

**N<sup>4</sup>:** om Operativsystemet är Windows specialiserade och avbildningen som generaliserad, får du en allokering felmeddelande eftersom den nya virtuella datorn körs med ursprungliga datornamn, användarnamn och lösenord. Dessutom ursprungligt virtuella datorn kan inte användas eftersom den har markerats som särskilda.

**Lösning:**

Ta bort den nuvarande avbildningen från portalen för att lösa båda dessa fel och [avbilda från de aktuella virtuella hårddiskarna](capture-image.md) med samma inställning som för OS (generaliserad/specialiserade).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: Anpassad / galleriet / marketplace-avbildning; Allokeringsfel
Det här felet uppstår i situationer när den nya VM-begäranden skickas till ett kluster som inte har ledigt utrymme för begäran eller stöder inte den begärda VM-storlek. Det går inte att blanda olika antal virtuella datorer i samma molntjänst. Så om du vill skapa en ny virtuell dator i en annan storlek än vad din molntjänst kan stödja att beräknings-begäran misslyckas.

Beroende på begränsningar av molntjänster som du använder för att skapa den nya virtuella datorn, kan det uppstå ett fel som orsakats av något av två situationer.

**Orsak 1:** Molntjänsten är fäst på ett specifikt kluster eller den är kopplad till en tillhörighetsgrupp och därför fäst på ett specifikt kluster avsiktligt. Det nya beräkningsresurser begäranden i den tillhörighetsgrupp testas i samma kluster där de befintliga resurserna finns. Men kan samma kluster antingen inte stöder den begärda VM-storleken eller har inte tillräckligt ledigt utrymme, vilket resulterar i ett Allokeringsfel. Detta är SANT om nya resurser som har skapats via en ny molntjänst eller en befintlig molntjänst.

**Lösning 1:**

* Skapa en ny molntjänst och koppla den till en region eller en region-baserat virtuellt nätverk.
* Skapa en ny virtuell dator i ny molntjänst.
  Om du får ett fel vid försök att skapa en ny molntjänst försök vid ett senare tillfälle eller ändra region för Molntjänsten.

> [!IMPORTANT]
> Om du försöker skapa en ny virtuell dator i en befintlig molntjänst men det gick inte att och var tvungen att skapa en ny molntjänst för den nya virtuella datorn, kan du välja att konsolidera alla dina virtuella datorer i samma molntjänst. Om du vill göra det, ta bort de virtuella datorerna i en befintlig molntjänst och avbilda dem från sina diskar i en ny molntjänst. Det är dock viktigt att komma ihåg att ny molntjänst får ett nytt namn och VIP, så behöver du uppdatera dessa för de beroenden som för närvarande använder den här informationen för en befintlig molntjänst.
> 
> 

**Orsak 2:** Molntjänsten är associerad med ett virtuellt nätverk som är kopplad till en tillhörighetsgrupp, så den är fäst på ett specifikt kluster avsiktligt. Alla nya beräkningsresurser begäranden i den tillhörighetsgrupp testas därför i samma kluster där de befintliga resurserna finns. Men kan samma kluster antingen inte stöder den begärda VM-storleken eller har inte tillräckligt ledigt utrymme, vilket resulterar i ett Allokeringsfel. Detta är SANT om nya resurser som har skapats via en ny molntjänst eller en befintlig molntjänst.

**Lösning 2:**

* Skapa en ny regionalt virtuellt nätverk.
* Skapa den nya virtuella datorn i ett nytt virtuellt nätverk.
* [Ansluta befintliga virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) till ett nytt virtuellt nätverk. Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Du kan också [migrera tillhörighet-grupp-baserat virtuellt nätverk till ett regionalt virtuellt nätverk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), och sedan skapa den nya virtuella datorn.

## <a name="next-steps"></a>Nästa steg
Om du får problem när du startar en stoppad virtuell Windows-dator eller ändra storlek på en befintlig Windows virtuell dator i Azure, se [felsöka klassisk distributionsproblem med att starta om eller ändrar storlek på en befintlig Windows virtuell dator i Azure](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md).

