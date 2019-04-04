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
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: a9fba2dc37646476ff2d802509da7b30ace85894
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805541"
---
# <a name="region-management-in-azure-stack"></a>Regionshantering i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack används begreppet *regioner*, som är logiska entiteter som består av maskinvaruresurser som utgör Azure Stack-infrastruktur. Du kan hitta alla resurser som krävs för att använda Azure Stack-infrastruktur i regionshantering av.

Ett integrerat system-distribution (kallas en *Azure Stack-molnet*) utgör en enda region. Varje Azure Stack Development Kit (ASDK) har en region med namnet **lokala**. Om du distribuerar ett andra integrerade Azure Stack-system eller om du ställer in en annan instans av i development kit på separata maskinvara, är Azure Stack-molnet en annan region.

## <a name="information-available-through-the-region-management-tile"></a>Information som är tillgängliga via region management panelen

Azure Stack har en uppsättning region hanteringsfunktioner som är tillgängliga i den **regionshantering** panelen. Den här panelen är tillgänglig för Azure Stack-operatör på standardinstrumentpanelen i administratörsportalen. Du kan övervaka och uppdatera din Azure Stack-region och dess komponenter som finns regionspecifikt via den här panelen.

![Panelen för region-hantering](media/azure-stack-region-management/image1.png)

Om du klickar på en region i den **regionshantering** panelen, du kan komma åt följande information:

[![Beskrivning av fönster på bladet för hantering i regionen](media/azure-stack-region-management/regionssm.png "bladet för hantering av Region")](media/azure-stack-region-management/regions.png#lightbox)

1. **Resurs-menyn**. Få åtkomst till specifika infrastruktur hanteringsområden, och visa och hantera användarresurser, till exempel lagringskonton och virtuella nätverk.

2. **Aviseringar**. Lista över hela systemet aviseringar och innehåller detaljerad information om var och en av dessa aviseringar.

3. **Uppdateringar**. Visa den aktuella versionen av Azure Stack-infrastruktur, tillgängliga uppdateringar och update-historiken. Du kan också uppdatera din integrerat system.

4. **Resursproviders**. Hantera användare-funktionerna som erbjuds av de komponenter som krävs för att köra Azure Stack. Varje resursprovider levereras med ett administrativt perspektiv. Detta kan inkludera aviseringar för leverantören, mått och andra hanteringsfunktioner som är specifika för resursprovidern.

5. **Infrastrukturroller**. Komponenterna som krävs för att köra Azure Stack. Endast de infrastruktur-roller som rapporterar aviseringar visas. Du kan visa de aviseringar som är associerad med rollen och rollinstanserna där den här rollen körs genom att välja en roll.

6. **Egenskaper för**. Registreringsstatus och information om din miljö i bladet för hantering i regionen. Statusen kan vara **registrerad** eller **inte registrerad**. Om registrerat, visar den även Azure prenumerations-ID som användes för att registrera din Azure Stack, tillsammans med resursgruppen för registrering och namn.

## <a name="next-steps"></a>Nästa steg

- [Övervaka hälsa och aviseringar i Azure Stack](azure-stack-monitor-health.md)
- [Hantera uppdateringar i Azure Stack](azure-stack-updates.md)
