---
title: Flytta Azure-resurser till en annan region
description: Ger en översikt över hur du flyttar Azure-resurser över Azure-regioner.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485213"
---
# <a name="moving-azure-resources-across-regions"></a>Flytta Azure-resurser mellan regioner

Den här artikeln innehåller information om hur du flyttar Azure-resurser över Azure-regioner.

Azure-geografiska områden, regioner och tillgänglighetszoner utgör grunden för den globala Azure-infrastrukturen. [Azure-geografiska områden](https://azure.microsoft.com/global-infrastructure/geographies/) innehåller vanligtvis två eller flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/). En region är ett område inom en geografi som innehåller tillgänglighetszoner och flera datacenter. 

När du har distribuerat resurser i en viss Azure-region finns det flera orsaker till att du kanske vill flytta resurser till en annan region.

- **Anpassa till en regionstart:** Flytta dina resurser till en nyligen introducerad Azure-region som inte tidigare var tillgänglig.
- **Justera för tjänster/funktioner:** Flytta resurser för att dra nytta av tjänster eller funktioner som är tillgängliga i en viss region.
- **Svara på affärsutvecklingen:** Flytta resurser till en region som svar på affärsförändringar, till exempel sammanslagningar eller förvärv.
- **Justera för närhet:** Flytta resurser till en region som är lokal till ditt företag.
- **Uppfylla datakrav:** Flytta resurser för att anpassa dig till datahemvistkrav eller dataklassificeringsbehov. [Läs mer](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Svara på distributionskrav:** Flytta resurser som har distribuerats av misstag eller flytta som svar på kapacitetsbehov. 
- **Svara på avveckling:** Flytta resurser på grund av avveckling av regioner.

## <a name="move-process"></a>Flytta process

Själva flyttprocessen beror på vilka resurser du flyttar. Det finns dock några vanliga viktiga steg:

- **Verifiera förutsättningar**: Förkunskaper inkluderar att se till att de resurser du behöver är tillgängliga i målregionen, kontrollera att du har tillräckligt med kvot och verifiera att din prenumeration kan komma åt målregionen.
- **Analysera beroenden**: Dina resurser kan ha beroenden av andra resurser. Innan du flyttar, räkna ut beroenden så att flyttade resurser fortsätter att fungera som förväntat efter flytten.
- **Förbered för flytten**: Det här är de steg du tar i din primära region före flytten. Du kan till exempel behöva exportera en Azure Resource Manager-mall eller börja replikera resurser från källa till mål.
- **Flytta resurserna**: Hur du flyttar resurser beror på vad de är. Du kan behöva distribuera en mall i målregionen eller växla resurser över till målet.
- **Ignorera målresurser:** När du har flyttat resurser kanske du vill ta en titt på de resurser som nu finns i målregionen och bestämma om det finns något du inte behöver.
- **Slutför flytten**: När du har verifierat resurser i målregionen kan vissa resurser kräva en slutlig genomförandeåtgärd. I en målregion som nu är den primära regionen kan du till exempel behöva ställa in haveriberedskap till en ny sekundär region. 
- **Rensa källan:** Slutligen, när allt är igång i den nya regionen, kan du rensa upp och inaktivera resurser som du skapade för flytten och resurser i din primära region.



## <a name="next-steps"></a>Nästa steg

En lista över vilka resurser som stöder flyttning mellan regioner finns i [Flytta åtgärdsstöd för resurser](region-move-support.md).
