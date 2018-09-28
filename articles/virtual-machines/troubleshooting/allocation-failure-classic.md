---
title: Felsöka Allokeringsfel för virtuella Azure-datorer i klassiska distributionsmodellen | Microsoft Docs
description: Felsök Allokeringsfel när du skapar, startar om eller ändra storlek på en klassisk virtuell dator i Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: genli
ms.openlocfilehash: 786957f8b6aa8721a0613260f6c4a6ec6e06ef45
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414704"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Felsökningssteg för specifika att allokering scenarier i den klassiska distributionsmodellen

Här följer några vanliga scenarier för allokering som orsakar en begäran om minnesallokering till fästas. Vi kommer att fördjupa dig i varje scenario senare i den här artikeln.

- Ändra storlek på en virtuell dator eller lägga till virtuella datorer eller rollinstanser i en befintlig molntjänst
- Starta om delvis stoppad (frigjord) virtuella datorer
- Starta om fullständigt Stoppad (frigjord) virtuella datorer
- Mellanlagring och produktion distributioner (plattform som en tjänst endast)
- Tillhörighetsgruppen (virtuell dator eller tjänst närhet)
- Tillhörighet – grupp-baserat virtuellt nätverk

När du får ett Allokeringsfel kan du kontrollera om någon av de listade scenarierna gäller för din fel. Använd Allokeringsfel som returneras av Azure-plattformen för att identifiera motsvarande scenario. Om din begäran är fäst, kan du ta bort några av de fästa restriktioner för att öppna din begäran om att flera kluster, vilket ökar risken för allokering.
I allmänhet om felet inte tillstånd att ”den begärda storleken inte stöds”, du kan alltid försöka igen vid ett senare tillfälle. Det beror på att tillräckligt med resurser kan ha frigjorts i klustret för att hantera din förfrågan. Om problemet är att den begärda storleken inte stöds kan du prova en annan VM-storlek. Annars är det enda alternativet att ta bort fästa begränsningen.

Två vanliga felscenarier är relaterade till tillhörighetsgrupper. Tidigare var en tillhörighetsgrupp som användes för att tillhandahålla nära till virtuella datorer och instanser av tjänsten eller den har använts för att aktivera skapandet av ett virtuellt nätverk. Med introduktionen av regionala virtuella nätverk är tillhörighetsgrupper inte längre krävs för att skapa ett virtuellt nätverk. Med minskning av svarstid för nätverk i Azure-infrastrukturen, har rekommenderar vi att använda tillhörighetsgrupper för virtuella datorer eller tjänsten närhet ändrats.

I följande Diagram visas taxonomi (Fäst) allokering-scenarier. 

![Fäst allokering taxonomi](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Ändra storlek på en virtuell dator eller lägga till virtuella datorer eller rollinstanser i en befintlig molntjänst
**Fel**

Upgrade_VMSizeNotSupported eller GeneralError

**Orsaken till att fästa klustret**

En begäran om att ändra storlek på en virtuell dator eller lägga till en virtuell dator eller en rollinstans i en befintlig molntjänst måste göras i det ursprungliga klustret som är värd för den befintliga Molntjänsten. Skapa en ny molntjänst kan Azure-plattformen för att hitta ett annat kluster som har kostnadsfria resurser eller stöder VM-storleken som du har begärt.

**Lösning**

Om felet är Upgrade_VMSizeNotSupported *, prova en annan VM-storlek. Om du använder en annan VM-storlek inte är ett alternativ, men det går att använda en annan virtuell IP-adress (VIP), kan du skapa en ny molntjänst för att vara värd för den nya virtuella datorn och Lägg till ny molntjänst till regionala virtuella nätverk där de befintliga virtuella datorerna körs. Om din befintliga Molntjänsten inte använder ett regionalt virtuellt nätverk kan du fortfarande skapa ett nytt virtuellt nätverk för den nya Molntjänsten och sedan ansluta din [befintligt virtuellt nätverk till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Om felet är GeneralError *, är det troligt att typ av resurs (till exempel en viss VM-storlek) stöds av klustret, men klustret inte har kostnadsfria resurser för tillfället. Lägg till önskade beräkningsresursen genom att skapa en ny molntjänst (Observera att den nya Molntjänsten måste använda en annan VIP) för liknande sätt för scenariot ovan och använda ett regionalt virtuellt nätverk för att ansluta dina molntjänster.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppad (frigjord) virtuella datorer
**Fel**

GeneralError *

**Orsaken till att fästa klustret**

Flyttningen är delvis innebär att du stoppat (frigjort) en eller mer, men inte alla virtuella datorer i en molntjänst. När du stoppar (frigöra) en virtuell dator, de associerade resurserna blir tillgängliga. Starta om den Stoppad (frigjord) virtuell datorn är därför en ny begäran om minnesallokering. Starta om virtuella datorer i en delvis frigjort molntjänst är detsamma som att lägga till virtuella datorer i en befintlig molntjänst. Begäran om minnesallokering måste göras i det ursprungliga klustret som är värd för den befintliga Molntjänsten. Skapa en annan molntjänst kan Azure-plattformen för att hitta ett annat kluster som har en kostnadsfri resurs eller stöder VM-storleken som du har begärt.

**Lösning**

Om det går att använda en annan VIP, ta bort de Stoppad (frigjord) virtuella datorerna (men behåll de associera diskarna) och Lägg till de virtuella datorerna tillbaka via en annan molntjänst. Använd ett regionalt virtuellt nätverk för att ansluta cloud services:

* Om din befintliga Molntjänsten använder ett regionalt virtuellt nätverk kan bara lägga till ny molntjänst till samma virtuella nätverk.
* Om din befintliga Molntjänsten inte använder ett regionalt virtuellt nätverk skapar du ett nytt virtuellt nätverk för den nya Molntjänsten och sedan [Anslut det befintliga virtuella nätverket till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt Stoppad (frigjord) virtuella datorer
**Fel**

GeneralError *

**Orsaken till att fästa klustret**

Flyttningen är fullständig innebär att du stoppat frigjord () alla virtuella datorer från en tjänst i molnet. Allokeringsbegäranden att starta om de virtuella datorerna måste göras i det ursprungliga klustret som är värd för Molntjänsten. Skapa en ny molntjänst kan Azure-plattformen för att hitta ett annat kluster som har kostnadsfria resurser eller stöder VM-storleken som du har begärt.

**Lösning**

Om det går att använda en annan VIP, ta bort de ursprungliga Stoppad (frigjord) virtuella datorerna (men behåll de associera diskarna) och ta bort motsvarande Molntjänsten (de associera beräkningsresurserna har redan getts ut när du stoppat (frigjort) de virtuella datorerna). Skapa en ny molntjänst för att lägga till de virtuella datorerna tillbaka.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Mellanlagrings-/ Produktionsdistribution (plattform som en tjänst endast)
**Fel**

New_General * eller New_VMSizeNotSupported *

**Orsaken till att fästa klustret**

Mellanlagringsplatser distributionen och Produktionsdistribution av en molnbaserad tjänst finns i samma kluster. När du lägger till den andra distributionen görs motsvarande begäran om minnesallokering i samma kluster som är värd för den första distributionen.

**Lösning**

Ta bort den första distributionen och den ursprungliga Molntjänsten och distribuera om Molntjänsten. Den här åtgärden kan hamnar den första distributionen i ett kluster som har tillräckligt med lediga resurser så att de passar båda distributionerna eller i ett kluster som har stöd för VM-storlekar som du har begärt.

## <a name="affinity-group-vmservice-proximity"></a>Tillhörighetsgruppen (VM/tjänst närhet)
**Fel**

New_General * eller New_VMSizeNotSupported *

**Orsaken till att fästa klustret**

Någon compute-resurs som tilldelats en tillhörighetsgrupp som är kopplat till ett kluster. Ny beräkningsresurs begäranden i den tillhörighetsgrupp görs i samma kluster där de befintliga resurserna finns. Det här gäller oavsett om de nya resurserna har skapats via en ny molntjänst eller en befintlig molntjänst.

**Lösning**

Om en tillhörighetsgrupp inte behövs, inte använda en tillhörighetsgrupp eller gruppera dina beräkningsresurser i flera tillhörighetsgrupper.

## <a name="affinity-group-based-virtual-network"></a>Mappning mellan och-grupp-baserat virtuellt nätverk
**Fel**

New_General * eller New_VMSizeNotSupported *

**Orsaken till att fästa klustret**

Innan regionala virtuella nätverk har introducerats tvungen du att koppla ett virtuellt nätverk med en tillhörighetsgrupp. Därför compute resurser placeras i en tillhörighetsgrupp som är bundna av samma begränsningar som beskrivs i den ”allokering scenario: tillhörighetsgrupp (VM/tjänst närhet)” ovan. Beräkningsresurserna är knutna till ett kluster.

**Lösning**

Om du inte behöver en tillhörighetsgrupp, skapa en ny regionalt virtuellt nätverk för de nya resurserna som du vill lägga till, och sedan [Anslut det befintliga virtuella nätverket till det nya virtuella nätverket](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer om [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Du kan också [migrera ditt tillhörighet-grupp-baserade virtuella nätverk till ett regionalt virtuellt nätverk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), och Lägg sedan till önskade resurser.


