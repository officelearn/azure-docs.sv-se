---
title: "Felsöka Linux VM distribution-klassisk | Microsoft Docs"
description: "Felsökning av problem med klassisk distribution när du skapar en ny virtuell Linux-dator i Azure"
services: virtual-machines-linux
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 581fbaa477bd603fea5fdc0ef77c6ef7498b7897
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Felsöka klassisk distributionsproblem med att skapa en ny virtuell Linux-dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-version av den här artikeln finns [här](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Samla in granskningsloggar
Starta felsökning genom att samla in granskningsloggar för att identifiera fel som är associerade med problemet.

I Azure-portalen klickar du på **Bläddra** > **virtuella datorer** > *din Windows-dator* > **inställningar** > **granskningsloggar**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** om Operativsystemet är Linux generaliserad, och det är upp och/eller avbildas med inställningen generaliserad och det inte finns några fel. På samma sätt om Operativsystemet Linux specialiserade, den är upp och/eller avbildas med inställningen specialiserade och det inte finns några fel.

**Överför fel:**

**N<sup>1</sup>:** om Operativsystemet är Linux generaliserad och det överförs som specialiserade, du får en allokering tidsgränsfel eftersom den virtuella datorn har fastnat på läget etablering.

**N<sup>2</sup>:** om Operativsystemet Linux specialiserade och det överförs som generaliserad du får felet etablering misslyckades eftersom den nya virtuella datorn körs med ursprungliga datornamn, användarnamn och lösenord.

**Lösning:**

Överför den ursprungliga virtuella Hårddisken tillgänglig lokalt, med samma inställning som för OS (generaliserad/specialiserade) för att lösa båda dessa fel. Om du vill överföra som generaliserad ihåg att köra - ta bort etableringen först. Se [skapa och ladda upp en virtuell hårddisk som innehåller Linux-operativsystem](create-upload-vhd-classic.md) för mer information.

**Avbilda fel:**

**N<sup>3</sup>:** om Operativsystemet är Linux generaliserad och avbildas som specialiserade, du får en allokering tidsgränsfel eftersom den ursprungliga virtuella datorn inte kan användas eftersom den har markerats som generaliserad.

**N<sup>4</sup>:** om Operativsystemet Linux specialiserade och avbildningen som generaliserad du får felet etablering misslyckades eftersom den nya virtuella datorn körs med ursprungliga datornamn, användarnamn och lösenord. Dessutom ursprungligt virtuella datorn kan inte användas eftersom den har markerats som särskilda.

**Lösning:**

Ta bort den nuvarande avbildningen från portalen för att lösa båda dessa fel och [avbilda från de aktuella virtuella hårddiskarna](capture-image-classic.md) med samma inställning som för OS (generaliserad/specialiserade).

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
Om du får problem när du startar en stoppad Linux VM eller ändra storlek på en befintlig Linux VM i Azure, se [felsöka klassisk distributionsproblem med att starta om eller ändrar storlek på en befintlig virtuell Linux-dator i Azure](restart-resize-error-troubleshooting.md).

