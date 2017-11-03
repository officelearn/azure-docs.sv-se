---
title: Regionhantering av i Azure-stacken | Microsoft Docs
description: "Översikt över regionhantering av i Azure-stacken."
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: efemmano
ms.openlocfilehash: d1310f0cb9a820366ab8712a782785e955a24134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="region-management-in-azure-stack"></a>Regionhantering av i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken har begreppet regioner som är logiska enheter som består av maskinvaruresurser som utgör Azure Stack-infrastruktur. I området management hittar du alla resurser som krävs för att använda Azure Stack infrastruktur livscykel.

Ett integrerat system-distribution (kallas en *Azure Stack molnet*) utgör en enskild region. Varje Azure-stacken Development Kit har en region som heter **lokala**. Om du distribuerar en andra Azure-stacken integrerat system eller om du ställer in en annan instans av development kit på separata maskinvara, är detta moln för Azure-stacken en annan region.

## <a name="information-available-through-the-region-management-tile"></a>Information som är tillgängliga via panelen Region management
Azure-stacken har en uppsättning region hanteringsfunktioner som är tillgängliga i den **Region management** panelen. Den här panelen är tillgänglig för en Azure-Stack-operator på standardinstrumentpanelen i administratörsportalen. Du kan övervaka och uppdatera din Azure-stacken region och dess komponenter som är regionspecifika via den här panelen.

 ![Panelen region management](media/azure-stack-manage-region/image1.png)

 Om du klickar på en region i panelen Region management kan du komma åt följande information:

  ![Beskrivning av rutor i bladet för hantering av Region](media/azure-stack-manage-region/image2.png)

1. **Resurs-menyn**. Här kan du har åtkomst till specifika infrastruktur management områden, och visa och hantera användarresurser, till exempel storage-konton och virtuella nätverk.

2. **Aviseringar**. Den här panelen visar varningar för hela systemet och innehåller information om var och en av dessa aviseringar.

3. **Uppdateringar**. Du kan visa den aktuella versionen av infrastrukturen för Azure-stacken i den här panelen.

4. **Resursproviders**. Resursproviders är för att hantera klient-funktionerna som erbjuds av de komponenter som krävs för att köra Azure stacken. Varje resursprovider finns en administrativa upplevelse. Det här upplevelsen kan innehålla varningar för leverantören, mätvärden och andra hanteringsfunktioner som är specifika för resursprovidern.
 
5. **Infrastrukturroller**. Infrastrukturroller är komponenterna som krävs för att köra Azure stacken. De infrastruktur roller som rapporterar aviseringar visas. Genom att välja en roll kan visa du de aviseringar som är associerade med den specifika-rollen och rollinstanser där den här rollen körs. Även om det inte finns möjlighet att starta, starta om, eller stänga av en instans för infrastruktur-rollen, göra **inte** göra detta i en utvecklingsmiljö kit. Dessa alternativ är utvecklade för en miljö med flera noder, där det finns fler än en instans av serverroll per infrastrukturrollen. Starta om rollinstansen (särskilt AzS-Xrp01) i development kit gör att systemet är instabilt.

## <a name="next-steps"></a>Nästa steg
[Övervaka hälsotillstånd och aviseringar i Azure-stacken](azure-stack-monitor-health.md)

[Hantera uppdateringar i Azure-stacken](azure-stack-updates.md)






