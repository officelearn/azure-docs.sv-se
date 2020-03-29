---
title: Felsöka tilldelningsfel för Azure VM i klassisk distributionsmodell| Microsoft-dokument
description: Felsöka allokeringsfel när du skapar, startar om eller ändrar storlek på en klassisk virtuell dator i Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913386"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Specifika felsökningssteg för scenarier med allokeringsfel i den klassiska distributionsmodellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Följande är vanliga allokeringsscenarier som gör att en allokeringsbegäran fästs. Vi kommer att dyka in i varje scenario senare i den här artikeln.

- Ändra storlek på en virtuell dator eller lägga till virtuella datorer eller rollinstanser i en befintlig molntjänst
- Starta om delvis stoppade (frigjorda) virtuella datorer
- Starta om fullständigt stoppade (frigjorda) virtuella datorer
- Mellanlagring och produktionsdistributioner (endast plattform som tjänst)
- Tillhörighetsgrupp (virtuell dator eller servicenärhet)
- Tillhörighet-gruppbaserat virtuellt nätverk

När du får ett allokeringsfel kontrollerar du om något av de scenarier som visas gäller för felet. Använd allokeringsfelet som returneras av Azure-plattformen för att identifiera motsvarande scenario. Om din begäran är fäst tar du bort några av fästbegränsningarna för att öppna din begäran för fler kluster, vilket ökar risken för att allokeringen lyckas.
Om felet inte anger att "den begärda vm-storleken inte stöds" kan du alltid försöka igen vid ett senare tillfälle. Detta beror på att tillräckligt med resurser kan ha frigjorts i klustret för att tillgodose din begäran. Om problemet är att den begärda vm-storleken inte stöds provar du en annan vm-storlek. Annars är det enda alternativet att ta bort fästvillkoret.

Två vanliga felscenarier är relaterade till tillhörighetsgrupper. Tidigare användes en tillhörighetsgrupp för att ge närhet till virtuella datorer och tjänstinstanser, eller så användes den för att skapa ett virtuellt nätverk. I och med införandet av regionala virtuella nätverk krävs inte längre tillhörighetsgrupper för att skapa ett virtuellt nätverk. Med minskningen av nätverksfördröjningen i Azure-infrastruktur har rekommendationen att använda tillhörighetsgrupper för virtuella datorer eller tjänstnärhet ändrats.

I följande diagram visas taxonomin för (fästa) allokeringsscenarier. 

![Fäst fördelningstaxonomi](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Ändra storlek på en virtuell dator eller lägga till virtuella datorer eller rollinstanser i en befintlig molntjänst
**Fel**

Upgrade_VMSizeNotSupported eller GeneralError

**Orsak till klusternålning**

En begäran om att ändra storlek på en virtuell dator eller lägga till en virtuell dator eller en rollinstans till en befintlig molntjänst måste göras på det ursprungliga klustret som är värd för den befintliga molntjänsten. Genom att skapa en ny molntjänst kan Azure-plattformen hitta ett annat kluster som har frigöra resurser eller stöder den virtuella datorns storlek som du begärde.

**Workaround**

Om felet är Upgrade_VMSizeNotSupported*, prova en annan vm-storlek. Om det inte är ett alternativ att använda en annan vm-storlek, men om det är acceptabelt att använda en annan virtuell IP-adress (VIP), skapar du en ny molntjänst som är värd för den nya virtuella datorn och lägger till den nya molntjänsten i det regionala virtuella nätverket där de befintliga virtuella datorerna körs. Om din befintliga molntjänst inte använder ett regionalt virtuellt nätverk kan du fortfarande skapa ett nytt virtuellt nätverk för den nya molntjänsten och sedan ansluta det [befintliga virtuella nätverket till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Om felet är GeneralError*, är det troligt att resurstypen (till exempel en viss vm-storlek) stöds av klustret, men klustret har inte frigöra resurser för tillfället. I likhet med ovanstående scenario lägger du till önskad beräkningsresurs genom att skapa en ny molntjänst (observera att den nya molntjänsten måste använda en annan VIP) och använda ett regionalt virtuellt nätverk för att ansluta dina molntjänster.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppade (frigjorda) virtuella datorer
**Fel**

GeneralError*

**Orsak till klusternålning**

Partiell deallocation innebär att du har stoppat (deallocated) en eller flera, men inte alla, virtuella datorer i en molntjänst. När du stoppar (deallocate) en virtuell dator frigörs de associerade resurserna. Starta om den stoppade (deallocated) VM är därför en ny allokeringsbegäran. Att starta om virtuella datorer i en delvis dislokalerad molntjänst motsvarar att lägga till virtuella datorer i en befintlig molntjänst. Allokeringsbegäran måste göras på det ursprungliga klustret som är värd för den befintliga molntjänsten. Genom att skapa en annan molntjänst kan Azure-plattformen hitta ett annat kluster som har en kostnadsfri resurs eller stöder den virtuella datorns storlek som du begärde.

**Workaround**

Om det är acceptabelt att använda en annan VIP tar du bort de stoppade (deallocated) virtuella datorerna (men behåller de associerade diskarna) och lägger till de virtuella datorerna tillbaka via en annan molntjänst. Använd ett regionalt virtuellt nätverk för att ansluta dina molntjänster:

* Om din befintliga molntjänst använder ett regionalt virtuellt nätverk lägger du bara till den nya molntjänsten i samma virtuella nätverk.
* Om din befintliga molntjänst inte använder ett regionalt virtuellt nätverk skapar du ett nytt virtuellt nätverk för den nya molntjänsten och ansluter sedan [det befintliga virtuella nätverket till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppade (frigjorda) virtuella datorer
**Fel**

GeneralError*

**Orsak till klusternålning**

Fullständig deallocation innebär att du stoppade (deallocated) alla virtuella datorer från en molntjänst. Allokeringsbegäranden för att starta om dessa virtuella datorer måste göras på det ursprungliga klustret som är värd för molntjänsten. Genom att skapa en ny molntjänst kan Azure-plattformen hitta ett annat kluster som har frigöra resurser eller stöder den virtuella datorns storlek som du begärde.

**Workaround**

Om det är acceptabelt att använda en annan VIP tar du bort de ursprungliga stoppade (deallocated) virtuella datorerna (men behåller de associerade diskarna) och tar bort motsvarande molntjänst (de associerade beräkningsresurserna släpptes redan när du stoppade (deallocated) de virtuella datorerna). Skapa en ny molntjänst för att lägga till de virtuella datorerna tillbaka.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Mellanlagrings-/produktionsdistributioner (endast plattform som tjänst)
**Fel**

New_General* eller New_VMSizeNotSupported*

**Orsak till klusternålning**

Mellanlagringsdistributionen och produktionsdistributionen av en molntjänst finns i samma kluster. När du lägger till den andra distributionen görs motsvarande allokeringsbegäran i samma kluster som är värd för den första distributionen.

**Workaround**

Ta bort den första distributionen och den ursprungliga molntjänsten och distribuera om molntjänsten. Den här åtgärden kan landa den första distributionen i ett kluster som har tillräckligt med lediga resurser för att passa både distributioner eller i ett kluster som stöder den virtuella datorns storlekar som du begärde.

## <a name="affinity-group-vmservice-proximity"></a>Tillhörighetsgrupp (VM/servicenärhet)
**Fel**

New_General* eller New_VMSizeNotSupported*

**Orsak till klusternålning**

Alla beräkningsresurser som tilldelas en tillhörighetsgrupp är kopplade till ett kluster. Nya beräkningsresursbegäranden i den tillhörighetsgruppen görs i samma kluster där de befintliga resurserna finns. Detta gäller oavsett om de nya resurserna skapas via en ny molntjänst eller via en befintlig molntjänst.

**Workaround**

Om en intressegrupp inte är nödvändig ska du inte använda en tillhörighetsgrupp eller gruppera beräkningsresurserna i flera tillhörighetsgrupper.

## <a name="affinity-group-based-virtual-network"></a>Tillhörighetsgruppsbaserat virtuellt nätverk
**Fel**

New_General* eller New_VMSizeNotSupported*

**Orsak till klusternålning**

Innan regionala virtuella nätverk introducerades var du tvungen att associera ett virtuellt nätverk med en tillhörighetsgrupp. Som ett resultat är beräkningsresurser som placeras i en tillhörighetsgrupp bundna av samma begränsningar som beskrivs i avsnittet "Allokeringsscenario: tillhörighetsgrupp (VM/tjänst närhet)" ovan. Beräkningsresurserna är knutna till ett kluster.

**Workaround**

Om du inte behöver en tillhörighetsgrupp skapar du ett nytt regionalt virtuellt nätverk för de nya resurser som du lägger till och ansluter sedan [det befintliga virtuella nätverket till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Du kan också [migrera det tillhörighetsbaserade virtuella nätverket till ett regionalt virtuellt nätverk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)och sedan lägga till önskade resurser igen.


