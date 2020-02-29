---
title: Felsöka allokeringsfel Azure VM-allokeringsfel i den klassiska distributions modellen | Microsoft Docs
description: Felsök allokeringsfel när du skapar, startar om eller ändrar storlek på en klassisk virtuell dator i Azure
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913386"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Fel söknings steg som är speciella för scenarier med allokeringsfel i den klassiska distributions modellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Följande är vanliga tilldelnings scenarier som gör att en tilldelnings förfrågan fästs. Vi kommer att gå vidare till varje scenario längre fram i den här artikeln.

- Ändra storlek på en virtuell dator eller Lägg till virtuella datorer eller roll instanser i en befintlig moln tjänst
- Starta om delvis stoppade (frigjorda) virtuella datorer
- Starta om fullständigt stoppade (frigjorda) virtuella datorer
- Mellanlagrings-och produktions distributioner (endast plattform som en tjänst)
- Tillhörighets grupp (VM eller tjänst närhet)
- Tillhörighet – gruppbaserat virtuellt nätverk

När du får ett allokeringsfel kontrollerar du om någon av de listade scenarierna gäller för ditt fel. Använd det allokeringsfel som returneras av Azure-plattformen för att identifiera motsvarande scenario. Om din begäran har fästs tar du bort några av de fästa begränsningarna för att öppna din begäran till fler kluster, vilket ökar risken för att tilldelningen lyckades.
Om felet i allmänhet inte anger att "den begärda VM-storleken inte stöds" kan du alltid försöka igen vid ett senare tillfälle. Detta beror på att tillräckligt med resurser kan ha frigjorts i klustret för att rymma din begäran. Om problemet är att den begärda virtuella dator storleken inte stöds, försök med en annan storlek på virtuell dator. Annars är det enda alternativet att ta bort den fästa begränsningen.

Två vanliga haveri scenarier är relaterade till tillhörighets grupper. Tidigare användes en tillhörighets grupp för att ge nära närhet till virtuella datorer och tjänst instanser, eller så användes den för att aktivera skapandet av ett virtuellt nätverk. I introduktionen av regionala virtuella nätverk krävs inte längre tillhörighets grupper för att skapa ett virtuellt nätverk. Med minskad nätverks fördröjning i Azure-infrastrukturen har rekommendationen att använda tillhörighets grupper för virtuella datorer eller tjänst närhet ändrats.

Följande diagram visar taxonomin för tilldelnings scenarierna (fästa). 

![Fästa allokering av taxonomi](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Ändra storlek på en virtuell dator eller Lägg till virtuella datorer eller roll instanser i en befintlig moln tjänst
**Fels**

Upgrade_VMSizeNotSupported eller GeneralError

**Orsak till kluster låsning**

En begäran om att ändra storlek på en virtuell dator eller lägga till en virtuell dator eller en roll instans till en befintlig moln tjänst måste utföras på det ursprungliga klustret som är värd för den befintliga moln tjänsten. Genom att skapa en ny moln tjänst kan Azure-plattformen hitta ett annat kluster som har kostnads fria resurser eller som stöder den VM-storlek som du har begärt.

**Lösning**

Om felet är Upgrade_VMSizeNotSupported * kan du prova med en annan storlek på den virtuella datorn. Om du inte använder en annan virtuell dator storlek, men om det är acceptabelt att använda en annan virtuell IP-adress (VIP), skapar du en ny moln tjänst som är värd för den nya virtuella datorn och lägger till den nya moln tjänsten i det regionala virtuella nätverket där de befintliga virtuella datorerna körs. Om den befintliga moln tjänsten inte använder ett regionalt virtuellt nätverk kan du fortfarande skapa ett nytt virtuellt nätverk för den nya moln tjänsten och sedan ansluta ditt [befintliga virtuella nätverk till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Se mer information om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Om felet är GeneralError * är det troligt att typen av resurs (till exempel en viss VM-storlek) stöds av klustret, men klustret har inte några lediga resurser för tillfället. I likhet med scenariot ovan lägger du till önskad beräknings resurs genom att skapa en ny moln tjänst (Observera att den nya moln tjänsten måste använda en annan VIP) och använda ett regionalt virtuellt nätverk för att ansluta dina moln tjänster.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppade (frigjorda) virtuella datorer
**Fels**

GeneralError*

**Orsak till kluster låsning**

Partiell avallokering innebär att du har stoppat (Frigjord) en eller flera, men inte alla, virtuella datorer i en moln tjänst. När du stoppar (frigör) en virtuell dator frigörs de tillhör ande resurserna. Att starta om som Stoppad (Frigjord) VM är därför en ny tilldelnings förfrågan. Att starta om virtuella datorer i en delvis fribelagd moln tjänst motsvarar att lägga till virtuella datorer i en befintlig moln tjänst. Tilldelnings förfrågan måste göras på det ursprungliga klustret som är värd för den befintliga moln tjänsten. Genom att skapa en annan moln tjänst kan Azure-plattformen hitta ett annat kluster som har kostnads fria resurser eller som stöder den VM-storlek som du har begärt.

**Lösning**

Om det är acceptabelt att använda en annan VIP tar du bort de stoppade (friallokerade) virtuella datorerna (men behåller de associerade diskarna) och lägger till de virtuella datorerna via en annan moln tjänst. Använd ett regionalt virtuellt nätverk för att ansluta dina moln tjänster:

* Om den befintliga moln tjänsten använder ett regionalt virtuellt nätverk lägger du helt enkelt till den nya moln tjänsten i samma virtuella nätverk.
* Om den befintliga moln tjänsten inte använder ett regionalt virtuellt nätverk skapar du ett nytt virtuellt nätverk för den nya moln tjänsten och [ansluter sedan ditt befintliga virtuella nätverk till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Se mer information om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppade (frigjorda) virtuella datorer
**Fels**

GeneralError*

**Orsak till kluster låsning**

Fullständig avallokering innebär att du har stoppat (Frigjord) alla virtuella datorer från en moln tjänst. Tilldelnings begär Anden för att starta om de virtuella datorerna måste göras vid det ursprungliga klustret som är värd för moln tjänsten. Genom att skapa en ny moln tjänst kan Azure-plattformen hitta ett annat kluster som har kostnads fria resurser eller som stöder den VM-storlek som du har begärt.

**Lösning**

Om det är acceptabelt att använda en annan VIP tar du bort de ursprungligen stoppade (friallokerade) virtuella datorerna (men behåller de associerade diskarna) och tar bort motsvarande moln tjänst (de associerade beräknings resurserna släpptes redan när du stoppade (frigjorde) de virtuella datorerna. Skapa en ny moln tjänst för att lägga till de virtuella datorerna igen.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Mellanlagring/produktions distributioner (endast plattform som en tjänst)
**Fels**

New_General * eller New_VMSizeNotSupported *

**Orsak till kluster låsning**

Mellanlagrings distributionen och produktions distributionen av en moln tjänst finns i samma kluster. När du lägger till den andra distributionen görs ett försök att utföra motsvarande allokering i samma kluster som är värd för den första distributionen.

**Lösning**

Ta bort den första distributionen och den ursprungliga moln tjänsten och distribuera om moln tjänsten. Den här åtgärden kan landa den första distributionen i ett kluster som har tillräckligt med lediga resurser för att passa både distributioner eller i ett kluster som stöder de VM-storlekar som du har begärt.

## <a name="affinity-group-vmservice-proximity"></a>Tillhörighets grupp (VM/service närhet)
**Fels**

New_General * eller New_VMSizeNotSupported *

**Orsak till kluster låsning**

Alla beräknings resurser som är kopplade till en tillhörighets grupp är knutna till ett kluster. Nya beräknings resurs begär anden i den tillhörighets gruppen görs i samma kluster där de befintliga resurserna finns. Detta är sant om de nya resurserna skapas via en ny moln tjänst eller via en befintlig moln tjänst.

**Lösning**

Om ingen tillhörighets grupp är nödvändig ska du inte använda en tillhörighets grupp eller gruppera dina beräknings resurser i flera tillhörighets grupper.

## <a name="affinity-group-based-virtual-network"></a>Tillhörighet – gruppbaserat virtuellt nätverk
**Fels**

New_General * eller New_VMSizeNotSupported *

**Orsak till kluster låsning**

Innan du introducerade regionala virtuella nätverk, var du tvungen att koppla ett virtuellt nätverk till en tillhörighets grupp. Därför är beräknings resurser som placeras i en tillhörighets grupp bundna av samma begränsningar som beskrivs i avsnittet "tilldelnings scenario: tillhörighets grupp (VM/service närhet)" ovan. Beräknings resurserna är kopplade till ett kluster.

**Lösning**

Om du inte behöver en tillhörighets grupp skapar du ett nytt regionalt virtuellt nätverk för de nya resurser som du lägger till och ansluter sedan [ditt befintliga virtuella nätverk till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Se mer information om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternativt kan du [migrera ditt tillhörighets gruppbaserade virtuella nätverk till ett regionalt virtuellt nätverk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)och sedan lägga till önskade resurser igen.


