---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "31531063"
---
Nedan följer vanliga scenarier för allokering som gör en begäran om minnesallokering för att fästa. Vi kommer fördjupa dig i varje scenario senare i den här artikeln.

- Ändra storlek på en virtuell dator eller lägga till VM: ar eller rollinstanser i en befintlig molntjänst
- Starta om delvis stoppats (frigjorts) virtuella datorer
- Starta om fullständigt stoppats (frigjorts) virtuella datorer
- Mellanlagring och produktion distributioner (plattform som en tjänst endast)
- Tillhörighetsgruppen (virtuell dator eller tjänst närhet)
- Tillhörighet – grupp-baserat virtuellt nätverk

När du får ett Allokeringsfel, kontrollera om någon av de listade scenarierna gäller för din fel. Använd Allokeringsfel som returneras av Azure-plattformen att identifiera motsvarande scenario. Om din begäran fästs avlägsna fästa begränsningarna för att öppna din förfrågan om att flera kluster, vilket ökar risken för allokering lyckades.
Om felet inte tillstånd att ”den begärda VM-storleken inte stöds”, kan du i allmänhet alltid försök vid ett senare tillfälle. Det beror på att tillräckligt med resurser kan ha frigjorts i klustret för att hantera din förfrågan. Om problemet är att den begärda VM-storleken inte stöds, kan du prova en annan VM-storlek. Annars är det enda alternativet att ta bort fästa begränsningen.

Två vanliga scenarier är relaterade till tillhörighetsgrupper. En tillhörighetsgrupp som användes för att ange närhet av virtuella datorer och instanser av tjänsten tidigare eller användes för att skapa ett virtuellt nätverk. Tillhörighetsgrupper är inte längre krävs för att skapa ett virtuellt nätverk med introduktionen av regionala virtuella nätverk. Rekommendationen att använda tillhörighetsgrupper för virtuella datorer eller tjänsten närhet har ändrats med för att minska Nätverksfördröjningen i Azure-infrastrukturen.

Följande Diagram visar taxonomi (fast) fördelning scenarier. 

![Fäst allokering taxonomi](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Ändra storlek på en virtuell dator eller lägga till VM: ar eller rollinstanser i en befintlig molntjänst
**Fel**

Upgrade_VMSizeNotSupported eller GeneralError

**Orsaken till klustret fästning**

En begäran att ändra storlek på en virtuell dator eller lägga till en virtuell dator eller en rollinstans i en befintlig molntjänst måste göras i det ursprungliga klustret som är värd för befintlig molntjänst. Skapa en ny molntjänst kan Azure-plattformen att hitta ett annat kluster som har lediga resurser eller stöder VM-storlek som du har begärt.

**Lösning**

Om felet är Upgrade_VMSizeNotSupported *, kan du prova en annan VM-storlek. Om du använder en annan VM-storlek inte är ett alternativ, men om det är tillåtet att använda en annan virtuell IP-adress (VIP), skapa en ny molntjänst för att vara värd för den nya virtuella datorn och Lägg till ny molntjänst regionalt virtuellt nätverk med de befintliga virtuella datorerna som kör. Om en befintlig molntjänst inte använder ett regionalt virtuellt nätverk du kan fortfarande skapa ett nytt virtuellt nätverk för den nya Molntjänsten och sedan ansluta din [befintligt virtuellt nätverk till ett nytt virtuellt nätverk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Om felet GeneralError *, är det troligt att typ av resurs (till exempel en viss VM-storlek) stöds av klustret, men klustret saknar lediga resurser för tillfället. Liknar scenariot ovan lägger du till den önskade beräkningsresursen genom att skapa en ny molntjänst (Observera att nya Molntjänsten måste använda en annan VIP) och ett regionalt virtuellt nätverk för att ansluta dina molntjänster.

## <a name="restart-partially-stopped-deallocated-vms"></a>Starta om delvis stoppats (frigjorts) virtuella datorer
**Fel**

GeneralError *

**Orsaken till klustret fästning**

Flyttningen är delvis innebär att du har stoppats (frigjorts) en eller mer, men inte alla virtuella datorer i en molntjänst. När du stoppar (frigöra) en VM, associerade resurser släpps. Starta om den stoppats (frigjorts) VM är därför en ny begäran om minnesallokering. Starta om virtuella datorer i en delvis frigjord molntjänst är detsamma som att lägga till virtuella datorer i en befintlig molntjänst. Begäran om minnesallokering måste göras i det ursprungliga klustret som är värd för befintlig molntjänst. Skapa en annan molntjänst kan Azure-plattformen att hitta ett annat kluster som har fri resurs eller stöder VM-storlek som du har begärt.

**Lösning**

Om det går att använda en annan VIP, ta bort de stoppats (frigjorts) virtuella datorerna (men behålla de associera diskarna) och lägga till de virtuella datorerna tillbaka via en annan molntjänst. Använd ett regionalt virtuellt nätverk för att ansluta dina molntjänster:

* Om en befintlig molntjänst använder ett regionalt virtuellt nätverk kan bara lägga till ny molntjänst till samma virtuella nätverk.
* Om en befintlig molntjänst inte använder ett regionalt virtuellt nätverk, skapa ett nytt virtuellt nätverk för den nya Molntjänsten och sedan [ansluta befintliga virtuella nätverket till ett nytt virtuellt nätverk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Starta om fullständigt stoppats (frigjorts) virtuella datorer
**Fel**

GeneralError *

**Orsaken till klustret fästning**

Fullständig flyttningen innebär att du har stoppats frigjorts () alla virtuella datorer från en tjänst i molnet. Av allokeringsbegäranden att starta om dessa virtuella datorer måste göras i det ursprungliga klustret som är värd för Molntjänsten. Skapa en ny molntjänst kan Azure-plattformen att hitta ett annat kluster som har lediga resurser eller stöder VM-storlek som du har begärt.

**Lösning**

Om det går att använda en annan VIP, ta bort de ursprungliga stoppats (frigjorts) virtuella datorerna (men behåll de associera diskarna) och ta bort motsvarande Molntjänsten (associerade beräkningsresurser har redan getts ut när du stoppats (frigjorts) de virtuella datorerna). Skapa en ny molntjänst för att lägga till de virtuella datorerna tillbaka.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Förproduktion/Produktionsdistribution (plattform som en tjänst endast)
**Fel**

New_General * eller * New_VMSizeNotSupported

**Orsaken till klustret fästning**

Fristående distributionen och Produktionsdistribution av en tjänst i molnet finns i samma kluster. När du lägger till den andra distributionen görs motsvarande allokeringsbegäran i samma kluster som är värd för den första distributionen.

**Lösning**

Ta bort den första distributionen och ursprungligt Molntjänsten och distribuera Molntjänsten. Den här åtgärden kan hamna den första distributionen i ett kluster som har tillräckligt med lediga resurser för att få plats båda distributionerna eller i ett kluster som stöder VM-storlekar som du har begärt.

## <a name="affinity-group-vmservice-proximity"></a>Tillhörighetsgruppen (VM-tjänsten närhet)
**Fel**

New_General * eller * New_VMSizeNotSupported

**Orsaken till klustret fästning**

Att beräkna någon resurs som tilldelats en tillhörighetsgrupp som är kopplat till ett kluster. Nya beräkningsresurser begäranden i den tillhörighetsgrupp görs i samma kluster där de befintliga resurserna finns. Detta är SANT om nya resurser som har skapats via en ny molntjänst eller en befintlig molntjänst.

**Lösning**

Om en tillhörighetsgrupp inte är nödvändigt, inte använda en tillhörighetsgrupp, eller gruppera dina beräkningsresurser i flera tillhörighetsgrupper.

## <a name="affinity-group-based-virtual-network"></a>Mappning mellan en gruppbaserad virtuellt nätverk
**Fel**

New_General * eller * New_VMSizeNotSupported

**Orsaken till klustret fästning**

Innan regionala virtuella nätverk har introducerats var du tvungen att koppla ett virtuellt nätverk med en tillhörighetsgrupp. Därför kan beräkna resurser placeras i en tillhörighetsgrupp som är bundna av samma begränsningar som beskrivs i den ”allokering scenario: tillhörighetsgrupp (VM-tjänsten närhet)” ovan. Beräkningsresurserna är knutna till ett kluster.

**Lösning**

Om du inte behöver en tillhörighetsgrupp, skapa ett nytt regionalt virtuellt nätverk för de nya resurser som du vill lägga till, och sedan [ansluta befintliga virtuella nätverket till ett nytt virtuellt nätverk](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Läs mer [regionala virtuella nätverk](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Du kan också [migrera tillhörighet-grupp-baserat virtuellt nätverk till ett regionalt virtuellt nätverk](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), och Lägg sedan till önskade resurser.
