---
title: Flytta Azure-resurser till en annan region
description: Ger en översikt över hur du flyttar Azure-resurser i Azure-regioner.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485213"
---
# <a name="moving-azure-resources-across-regions"></a>Flytta Azure-resurser mellan regioner

Den här artikeln innehåller information om hur du flyttar Azure-resurser i Azure-regioner.

Azures geografiska områden, regioner och Tillgänglighetszoner utgör grunden i den globala Azure-infrastrukturen. Azures [geografiska](https://azure.microsoft.com/global-infrastructure/geographies/) områden innehåller vanligt vis två eller flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). En region är ett område inom ett geografiskt område som innehåller Tillgänglighetszoner och flera data Center. 

När du har distribuerat resurser i en särskild Azure-region finns det ett antal orsaker till att du kanske vill flytta resurser till en annan region.

- **Justera till en regions lansering**: flytta dina resurser till en nyligen introducerad Azure-region som inte var tillgänglig tidigare.
- **Justera för tjänster/funktioner**: flytta resurser för att dra nytta av tjänster eller funktioner som är tillgängliga i en speciell region.
- **Svara på affärs utveckling**: flytta resurser till en region som svar på affärs ändringar, till exempel fusioner eller förvärv.
- **Justera mot närhet**: flytta resurser till en region som är lokal för din verksamhet.
- **Uppfylla data kraven**: flytta resurser så att de överensstämmer med data placering-krav eller data klassificerings behov. [Läs mer](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Svara på distributions krav**: flytta resurser som har distribuerats i fel eller flytta som svar på kapacitets behoven. 
- **Svara på**inaktive ring: flytta resurser på grund av inaktive ring av regioner.

## <a name="move-process"></a>Flytta process

Själva flyttnings processen beror på vilka resurser du flyttar. Det finns dock några vanliga viktiga steg:

- **Verifiera förutsättningar**: förutsättningarna är att se till att de resurser du behöver är tillgängliga i mål regionen, kontrol lera att du har tillräckligt med kvot och verifiera att din prenumeration har åtkomst till mål regionen.
- **Analysera beroenden**: dina resurser kan ha beroenden på andra resurser. Innan du flyttar kan du räkna ut beroenden så att flyttade resurser fortsätter att fungera som förväntat efter flytten.
- **Förbered för flytt**: det här är de steg du utför i din primära region innan du går vidare. Du kan till exempel behöva exportera en Azure Resource Manager mall eller börja replikera resurser från källa till mål.
- **Flytta resurserna**: hur du flyttar resurser beror på vad de är. Du kan behöva distribuera en mall i mål regionen eller redundansväxla resurser till målet.
- **Ta bort mål resurser**: när du har flyttat resurser kanske du vill ta en titt på resurserna nu i mål regionen och bestämma om det finns något som du inte behöver.
- **Genomför flyttningen**: när du har verifierat resurser i mål regionen kan vissa resurser kräva en slutgiltig inchecknings åtgärd. I en mål region som nu är den primära regionen kan du behöva konfigurera haveri beredskap till en ny sekundär region. 
- **Rensa källan**: När allt är igång i den nya regionen kan du rensa upp och ta bort resurser som du har skapat för flytten och resurserna i din primära region.



## <a name="next-steps"></a>Nästa steg

En lista över vilka resurser som stöder förflyttning mellan regioner finns i avsnittet [Flytta åtgärds stöd för resurser](region-move-support.md).
