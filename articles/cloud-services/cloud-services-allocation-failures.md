---
title: Felsök Allokeringsfel för Cloud Service | Microsoft Docs
description: Felsök allokeringsfel när du distribuerar Cloud Services i Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 22888c76b27d287a8d7fb0f0f1f0a0d39d92375d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35968852"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Felsök allokeringsfel när du distribuerar Cloud Services i Azure
## <a name="summary"></a>Sammanfattning
När du distribuerar instanser i en molnbaserad tjänst eller lägga till nya webb- eller worker-rollinstanser, allokerar Microsoft Azure beräkningsresurser. Ibland kan du få felmeddelanden när du utför dessa åtgärder innan du når databasens begränsningar för Azure-prenumeration. Den här artikeln förklarar orsakerna till några av de vanliga Allokeringsfel och föreslår möjliga åtgärder. Informationen kan också vara användbart när du planerar distributionen av dina tjänster.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Bakgrund – hur allokering fungerar
Servrarna i Azure-datacenter partitioneras i kluster. En ny cloud service allokeringsbegäran görs i flera kluster. När den första instansen har distribuerats till en molntjänst (i antingen mellanlagring eller produktion), som molntjänst hämtar fästs till ett kluster. Eventuella ytterligare distributioner för Molntjänsten sker i samma kluster. I den här artikeln ska vi refererar till detta som ”Fäst till ett kluster”. Diagram 1 nedan illustrerar i fallet med en normal allokering som görs i flera kluster; Diagram 2 visas i fallet med en allokering som har fästs till klustret 2 eftersom det är där de befintliga Cloud Service CS_1 finns.

![Allokering Diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Varför Tilldelningsfel händer
När en begäran om minnesallokering är fäst till ett kluster, finns en högre risk för att hitta kostnadsfria resurser eftersom tillgängliga resurspoolen är begränsad till ett kluster inte. Dessutom, om din begäran om minnesallokering fästs till ett kluster, men typ av resurs som du har begärt stöds inte av klustret, din begäran misslyckas även om klustret har kostnadsfri resurs. Bild 3 nedan visar fall där en fäst allokering misslyckas eftersom endast kandidat klustret inte har kostnadsfria resurser. Bild 4 visar fall där en fäst allokering misslyckas eftersom endast kandidat klustret inte stöder den begärda storleken trots att klustret har kostnadsfria resurser.

![Fästa Allokeringsfel](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Felsök Allokeringsfel för cloud services
### <a name="error-message"></a>Felmeddelande
Du kan se följande felmeddelande visas:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Vanliga problem
Här följer vanliga scenarier för allokering som orsakar en begäran om minnesallokering till fästas på ett enda kluster.

* Distribuera till mellanlagringsplats – om en tjänst i molnet har en distribution i antingen fack, sedan fästs hela Molntjänsten till ett specifikt kluster.  Det innebär att om en distribution redan finns på produktionsplatsen kan en ny mellanlagringsdistribution endast allokeras i samma kluster som produktionsplatsen. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.
* Skalning – att lägga till nya instanser i en befintlig molntjänst måste du allokera i samma kluster.  Små skalningsbegäranden går vanligtvis att allokera, men inte alltid. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.
* Tillhörighetsgruppen – en ny distribution till en tom molntjänst kan allokeras av infrastrukturresurser i alla kluster i den regionen, såvida inte Molntjänsten som är fäst på en tillhörighetsgrupp. Distributioner till samma tillhörighetsgrupp görs i samma kluster. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.
* Tillhörighetsgruppen virtuellt nätverk – äldre virtuella nätverk har knutna till tillhörighetsgrupper i stället för regioner och molntjänster i dessa virtuella nätverk kan fästas på tillhörighetsgrupp-klustret. Distributioner till den här typen av virtuellt nätverk kommer att försökas på fästa klustret. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.

## <a name="solutions"></a>Lösningar
1. Distribuera om till en ny molntjänst – den här lösningen troligen är mest framgångsrika eftersom den låter plattformen att välja mellan alla kluster i den regionen.

   * Distribuera arbetsbelastningen till en ny molntjänst  
   * Uppdatera CNAME eller en post för att rikta trafik till den nya Molntjänsten
   * När noll trafik ska den gamla platsen, kan du ta bort gamla Molntjänsten. Den här lösningen bör någon avbrottstid.
2. Ta bort både produktions- och mellanlagringsplatser – den här lösningen kommer att behålla din befintliga DNS-namn, men leder till avbrott för dina program.

   * Ta bort produktions- och mellanlagringsplatser för en befintlig molntjänst så att Molntjänsten är tomt, och sedan
   * Skapa en ny distribution i den befintliga Molntjänsten. Det här försöker igen-allokeringen på alla kluster i regionen. Kontrollera att Molntjänsten inte är kopplat till en tillhörighetsgrupp.
3. Reserverade IP - den här lösningen kommer att behålla din befintliga IP-adress, men leder till avbrott för dina program.  

   * Skapa en ReservedIP för den befintliga distributionen med hjälp av Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Följ #2 ovanför, se till att ange den nya ReservedIP i tjänstens CSCFG.
4. Ta bort tillhörighetsgruppen för nya distributioner - Tillhörighetsgrupper inte längre rekommenderas. Följ stegen för #1 ovan för att distribuera en ny molntjänst. Kontrollera att Molntjänsten inte har en tillhörighetsgrupp.
5. Konvertera till ett regionalt virtuellt nätverk – Se [hur du migrerar från Tillhörighetsgrupper till ett regionalt virtuellt nätverk (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
