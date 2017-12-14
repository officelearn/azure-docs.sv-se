---
title: "Ersätta en maskinvarukomponent på ett Azure skala enhet stacknod | Microsoft Docs"
description: "Lär dig mer om att ersätta en maskinvarukomponent på ett Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 5f760ae0cc33e138fc3d484711b8747b984977d4
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Ersätta en maskinvarukomponent på Azure-stacken skala enhet nod

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver den allmänna processen för att ersätta maskinvarukomponenter som är inte bytas. Faktiska ersättning stegen varierar utifrån maskinvaruleverantören OEM-tillverkaren (OEM). Dokumentationen leverantörens fältet utbytbara enhet (FRU) för detaljerade anvisningar som är specifika för din Azure-stacken integrerat system.

Följande är icke växlas komponenter:

- CPU *
- Minne *
- Moderkortet/hanteringsstyrenhet för baskort (BMC) / video kort
- Disk-styrenhet/värdbusskort (HBA) / bakplan
- Nätverkskort (NIC)
- Operativsystemet disk *
- Dataenheter (enheter som inte stöder bytas, till exempel PCI-e-tillägg-kort) *

* De här komponenterna stöder bytas, men kan variera beroende på leverantör implementering. Finns i dokumentationen från leverantören av OEM-FRU detaljerade anvisningar.

Följande flöde diagram visar den allmänna FRU-processen att ersätta en icke växlas maskinvarukomponent.

![Flödesdiagram för visar komponenten ersättning flöde](media/azure-stack-replace-component/ReplaceComponentFlow.PNG)

* Den här åtgärden kan inte krävas baserat på villkoret fysisk maskinvara.

** Om maskinvaruleverantören OEM utför ersättningen komponent och uppdaterar den inbyggda programvaran kan variera beroende på du stöder kontrakt.

## <a name="review-alert-information"></a>Granska aviseringsinformation

Azure-stacken hälsotillstånd och övervakningssystemet övervakar hälsan för nätverkskort och dataenheter som styrs av Storage Spaces Direct. Den övervakar inte andra maskinvarukomponenter. Aviseringar som aktiveras för alla andra maskinvarukomponenter i leverantörsspecifika maskinvara övervakningslösning som körs på värden för maskinvara livscykel.

## <a name="component-replacement-process"></a>Process för ersättning av komponenter

Följande steg tillhandahålls som en översikt över processen för att byta komponent. Följ inte stegen utan att referera till OEM-tillverkarens FRU-dokumentationen.

1. Använd den [tömmer](azure-stack-node-actions.md#scale-unit-node-actions) åtgärden att placera noden skala enhet i underhållsläge. Den här åtgärden kan inte krävas baserat på villkoret fysisk maskinvara.
2. När noden skala enhet är i underhållsläge, använda den [stängs](azure-stack-node-actions.md#scale-unit-node-actions) åtgärd. Den här åtgärden kan inte krävas baserat på villkoret fysisk maskinvara.
 
   > [!NOTE]
   > Använd webbgränssnittet baseboard management controller (BMC) i stället förmodan som inte fungerar stänga av åtgärden.

3. Ersätt den skadade maskinvarukomponenten. Om maskinvaruleverantören OEM utför ersättningen komponenten kan variera beroende på din support-kontrakt.  
4. Uppdatera den inbyggda programvaran. Följ uppdateringsprocessen för din leverantörsspecifika inbyggd programvara med maskinvara livscykel värd ersatta maskinvarukomponent har godkänt firmware använts. Om maskinvaruleverantören OEM utför det här steget kan variera beroende på din support-kontrakt.  
5. Använd den [reparera](azure-stack-node-actions.md#scale-unit-node-actions) åtgärder för att göra noden skala enhet skalningsenheten.
6. Använda privileged slutpunkt till [Kontrollera status för virtuell disk reparera](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Med nya dataenheter ett jobb för reparation av fullständig lagring kan ta flera timmar beroende på systembelastning och förbrukas utrymme.
7. När reparationsåtgärden har slutförts måste du verifiera att alla aktiva aviseringar automatiskt har stängts.

## <a name="next-steps"></a>Nästa steg

- Information om du vill ersätta en växlas fysisk disk finns [ersätta en disk](azure-stack-replace-disk.md).
- Information om du vill ersätta en fysisk nod finns [ersätta en scale unit nod](azure-stack-replace-node.md). 