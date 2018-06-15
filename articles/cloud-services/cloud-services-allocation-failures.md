---
title: Felsök problem med Cloud Service tilldelning | Microsoft Docs
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
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 33d017d0e09e9b288b0514e85c8865f83a8a2fa1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984124"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Felsök allokeringsfel när du distribuerar Cloud Services i Azure
## <a name="summary"></a>Sammanfattning
När du distribuera instanser till en molnbaserad tjänst eller lägga till nya webb eller arbetare rollinstanser allokerar Microsoft Azure beräkningsresurser. Ibland kan du få felmeddelanden när du utför dessa åtgärder innan du når Azure-prenumerationsbegränsningar. Den här artikeln förklarar orsakerna till några vanliga Allokeringsfel och föreslår möjliga reparation. Informationen kan också vara användbart när du planerar distributionen av dina tjänster.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Bakgrund – hur allokering fungerar
Servrarna i Azure-Datacenter partitioneras i kluster. En ny molnet allokering tjänstbegäran görs i flera kluster. När den första instansen har distribuerats till en tjänst i molnet (i Förproduktion eller produktion), som molntjänst hämtar fäst på ett kluster. Alla ytterligare distributioner för Molntjänsten sker i samma kluster. I den här artikeln ska vi refererar till den här som ”fäst på ett kluster”. Diagram 1 nedan illustrerar skiftläget för en normal allokering som görs i flera kluster; Figur 2 visar i fallet med en allokering som har fäst på klustret 2 eftersom det är där den befintliga Cloud Service CS_1 finns.

![Allokering av Diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Varför Allokeringsfel händer
När en begäran om minnesallokering är fäst på ett kluster, finns en högre risk för att hitta lediga resurser eftersom tillgängliga resurspoolen är begränsad till ett kluster inte. Dessutom misslyckas din begäran även om klustret har fri resurs om din begäran om minnesallokering är fäst på ett kluster men typ av resurs som du har begärt stöds inte av klustret. Bild 3 nedan visar de fall där en fast allokering misslyckas eftersom endast candidate klustret inte har lediga resurser. Bild 4 visar de fall där en fast allokering misslyckas eftersom endast kandidat kluster inte stöder den begärda VM-storleken, även om klustret har lediga resurser.

![Fäst Allokeringsfel](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Felsök tilldelningsproblem för molntjänster
### <a name="error-message"></a>Felmeddelande
Du kan se följande felmeddelande:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Vanliga problem
Här följer vanliga scenarier för allokering som gör en begäran om minnesallokering till fäst på ett enda kluster.

* Distribuerar till Förproduktion fack - en molnbaserad tjänst har en distribution på antingen platsen, är sedan hela Molntjänsten fäst på ett specifikt kluster.  Det innebär att om en distribution som redan finns på produktionsplatsen sedan en ny fristående distribution kan endast allokeras i samma kluster som produktionsplatsen. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.
* Skalning – att lägga till nya instanser i en befintlig molntjänst måste tilldelas i samma kluster.  Liten skala begäranden kan vanligen allokeras, men inte alltid. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.
* Tillhörighetsgruppen - en ny distribution till en tom molntjänst kan allokeras av infrastrukturresurser i ett kluster i den regionen om Molntjänsten är fäst på en tillhörighetsgrupp. Distributioner till samma tillhörighetsgrupp görs på samma kluster. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.
* Tillhörighetsgruppen vNet - äldre virtuella nätverk har knutna till tillhörighetsgrupper i stället för regioner och molntjänster i dessa virtuella nätverk skulle fäst på klustret tillhörighetsgrupp. Distributioner till den här typen av virtuellt nätverk görs på Fäst klustret. Om klustret närmar sig kapacitetsgränsen, misslyckas begäran.

## <a name="solutions"></a>Lösningar
1. Distribuera till en ny molntjänst – den här lösningen är troligt att de mest framgångsrika som tillåter plattform att välja mellan alla kluster i den regionen.

   * Distribuera arbetsbelastningen till en ny molntjänst  
   * Uppdatera CNAME eller en post pekar på ny molntjänst
   * När noll trafik ska den gamla platsen, kan du ta bort gamla Molntjänsten. Den här lösningen ska innebära ett driftstopp.
2. Ta bort produktion och mellanlagring platser – den här lösningen kommer att behålla din befintliga DNS-namn, men orsakar driftstopp för ditt program.

   * Ta bort produktions- och mellanlagring fack för en befintlig molntjänst så att Molntjänsten är tomt, och sedan
   * Skapa en ny distribution i en befintlig molntjänst. Detta försöker igen allokering på alla kluster i region. Kontrollera att Molntjänsten inte är kopplad till en tillhörighetsgrupp.
3. Reserverad IP - den här lösningen kommer att behålla din befintliga IP-adress, men orsakar driftstopp för ditt program.  

   * Skapa en ReservedIP för den befintliga distributionen med Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Följ #2 ovan och se till att ange den nya ReservedIP i tjänstens CSCFG.
4. Ta bort tillhörighetsgruppen för nya distributioner - Tillhörighetsgrupper rekommenderas inte längre. Följ stegen för #1 ovan för att distribuera en ny molntjänst. Kontrollera att Molntjänsten inte har en tillhörighetsgrupp.
5. Konvertera till ett regionalt virtuellt nätverk – Se [hur du migrerar från Tillhörighetsgrupper till ett regionalt virtuellt nätverk (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
