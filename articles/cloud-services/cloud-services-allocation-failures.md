---
title: Felsöka molntjänstallokeringsfel | Microsoft-dokument
description: Felsök allokeringsfel när du distribuerar Cloud Services i Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247545"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Felsök allokeringsfel när du distribuerar Cloud Services i Azure
## <a name="summary"></a>Sammanfattning
När du distribuerar instanser till en molntjänst eller lägger till nya webb- eller arbetsrollinstanser allokerar Microsoft Azure beräkningsresurser. Ibland kan du få fel när du utför dessa åtgärder redan innan du når Azure-prenumerationsgränserna. I den här artikeln beskrivs orsakerna till några av de vanliga allokeringsfelen och det föreslås möjliga åtgärder. Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Bakgrund – Så fungerar allokeringen
Servrarna i Azure-datacenter partitioneras i kluster. En ny begäran om molntjänstallokering görs i flera kluster. När den första instansen distribueras till en molntjänst (i antingen mellanlagring eller produktion) fästs molntjänsten i ett kluster. Eventuella ytterligare distributioner för molntjänsten kommer att ske i samma kluster. I den här artikeln refererar vi till detta som "fäst i ett kluster". Diagram 1 nedan illustrerar fallet med en normal allokering som görs i flera kluster. Diagram 2 illustrerar fallet med en allokering som är fäst vid Kluster 2 eftersom det är där den befintliga molntjänsten CS_1 finns.

![Fördelningsdiagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Varför allokeringsfel inträffar
När en allokeringsbegäran är fäst i ett kluster finns det större risk att det inte går att hitta lediga resurser eftersom den tillgängliga resurspoolen är begränsad till ett kluster. Om allokeringsbegäran är fäst i ett kluster men den typ av resurs som du har begärt inte stöds av klustret, misslyckas begäran även om klustret har en ledig resurs. Diagram 3 nedan illustrerar det fall där en fäst allokering misslyckas eftersom det enda kandidatklustret inte har lediga resurser. Diagram 4 illustrerar det fall där en fäst allokering misslyckas eftersom det enda kandidatklustret inte stöder den begärda vm-storleken, även om klustret har lediga resurser.

![Fel vid fäst allokering](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Felsöka allokeringsfel för molntjänster
### <a name="error-message"></a>Felmeddelande
Följande felmeddelande kan visas:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Vanliga problem
Här är de vanliga allokeringsscenarierna som gör att en allokeringsbegäran fästs i ett enda kluster.

* Distribuera till mellanlagringsplats - Om en molntjänst har en distribution i någon av kortplats, är hela molntjänsten fäst på ett visst kluster.  Det innebär att om en distribution redan finns på produktionsplatsen kan en ny mellanlagringsdistribution endast allokeras i samma kluster som produktionsplatsen. Om klustret närmar sig kapacitet kan begäran misslyckas.
* Skalning – När nya instanser läggs till i en befintlig molntjänst måste de allokeras i samma kluster.  Små skalningsbegäranden går vanligtvis att allokera, men inte alltid. Om klustret närmar sig kapacitet kan begäran misslyckas.
* Tillhörighetsgrupp - En ny distribution till en tom molntjänst kan allokeras av infrastrukturen i alla kluster i den regionen, såvida inte molntjänsten är fäst vid en tillhörighetsgrupp. Distributioner till samma tillhörighetsgrupp görs på samma kluster. Om klustret närmar sig kapacitet kan begäran misslyckas.
* Tillhörighetsgrupp vNet - Äldre virtuella nätverk var knutna till tillhörighetsgrupper i stället för regioner, och molntjänster i dessa virtuella nätverk skulle fästas i tillhörighetsgruppsklustret. Distributioner till den här typen av virtuellt nätverk kommer att försökas på det fästa klustret. Om klustret närmar sig kapacitet kan begäran misslyckas.

## <a name="solutions"></a>Lösningar
1. Distribuera om till en ny molntjänst - Den här lösningen är sannolikt mest framgångsrik eftersom den tillåter plattformen att välja mellan alla kluster i den regionen.

   * Distribuera arbetsbelastningen till en ny molntjänst  
   * Uppdatera CNAME- eller A-posten för att peka trafik till den nya molntjänsten
   * När noll trafik går till den gamla webbplatsen kan du ta bort den gamla molntjänsten. Den här lösningen bör medföra noll driftstopp.
2. Ta bort både produktions- och mellanlagringsplatser - Den här lösningen bevarar ditt befintliga DNS-namn, men orsakar driftstopp för ditt program.

   * Ta bort produktions- och mellanlagringsplatser för en befintlig molntjänst så att molntjänsten är tom och sedan
   * Skapa en ny distribution i den befintliga molntjänsten. Detta kommer att försöka allokera på alla kluster i regionen. Kontrollera att molntjänsten inte är knuten till en tillhörighetsgrupp.
3. Reserverad IP - Den här lösningen kommer att bevara din befintliga IP-adress, men kommer att orsaka driftstopp för ditt program.  

   * Skapa ett reserverat IP för din befintliga distribution med Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Följ #2 ovanifrån och se till att ange den nya ReservedIP i tjänstens CSCFG.
4. Ta bort tillhörighetsgrupp för nya distributioner - Tillhörighetsgrupper rekommenderas inte längre. Följ stegen i punkt 1 ovan för att distribuera en ny molntjänst. Kontrollera att molntjänsten inte finns i en tillhörighetsgrupp.
5. Konvertera till ett regionalt virtuellt nätverk – Se [Hur du migrerar från tillhörighetsgrupper till ett virtuellt nätverk (Regionalt nätverk)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
