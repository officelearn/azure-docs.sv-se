---
title: Regionshantering i Azure Stack | Microsoft Docs
description: Översikt över regionshantering i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 9a10d4fc90b916b3cb1eda7b9bac99c5d5f9deba
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160791"
---
# <a name="region-management-in-azure-stack"></a>Regionshantering i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack används begreppet *regioner*, som är logiska entiteter som består av maskinvaruresurser som utgör Azure Stack-infrastruktur. Du kan hitta alla resurser som krävs för att använda Azure Stack-infrastruktur i regionshantering av.

Ett integrerat system-distribution (kallas en *Azure Stack-molnet*) utgör en enda region. Varje Azure Stack Development Kit har en region med namnet **lokala**. Om du distribuerar ett andra integrerade Azure Stack-system eller om du ställer in en annan instans av i development kit på separata maskinvara, är Azure Stack-molnet en annan region.

## <a name="information-available-through-the-region-management-tile"></a>Information som är tillgängliga via Region management panelen

Azure Stack har en uppsättning region hanteringsfunktioner som är tillgängliga i den **regionshantering** panelen. Den här panelen är tillgänglig för Azure Stack-operatör på standardinstrumentpanelen i administratörsportalen. Du kan övervaka och uppdatera din Azure Stack-region och dess komponenter som finns regionspecifikt via den här panelen.

![Panelen för region-hantering](media/azure-stack-manage-region/image1.png)

Om du klickar på en region i den **regionshantering** panelen, du kan komma åt följande information:

![Beskrivning av fönster på bladet för hantering i Region](media/azure-stack-manage-region/image2.png)

1. **Resurs-menyn**. Få åtkomst till specifika infrastruktur hanteringsområden, och visa och hantera användarresurser, till exempel lagringskonton och virtuella nätverk.

2. **Aviseringar**. Visar en lista över hela systemet aviseringar och innehåller information om var och en av dessa aviseringar.

3. **Uppdateringar**. Visa den aktuella versionen av Azure Stack-infrastruktur, tillgängliga uppdateringar och update-historiken. Du kan också uppdatera din integrerat system.

4. **Resursproviders**. Hantera användare-funktionerna som erbjuds av de komponenter som krävs för att köra Azure Stack. Varje resursprovider levereras med ett administrativt perspektiv. Detta kan inkludera aviseringar för leverantören, mått och andra hanteringsfunktioner som är specifika för resursprovidern.

5. **Infrastrukturroller**. Komponenterna som krävs för att köra Azure Stack. Endast de infrastruktur-roller som rapporterar aviseringar visas. Du kan visa de aviseringar som är associerad med rollen och rollinstanserna där den här rollen körs genom att välja en roll.

## <a name="next-steps"></a>Nästa steg

- [Övervaka hälsotillstånd och aviseringar i Azure Stack](azure-stack-monitor-health.md)
- [Hantera uppdateringar i Azure Stack](azure-stack-updates.md)
