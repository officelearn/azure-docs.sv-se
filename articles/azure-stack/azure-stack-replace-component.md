---
title: Ersätt en maskinvarukomponent på Azure Stack skala enhet nod | Microsoft Docs
description: Lär dig hur du ersätter en maskinvarukomponent på ett integrerat Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.openlocfilehash: 9657fd448f6fb98eec87a5999af100d4d08594e5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717729"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Ersätt en maskinvarukomponent på Azure Stack skala enhet nod

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskrivs den allmänna processen för att ersätta de komponenter som är icke växlas. Faktiska ersättning åtgärder som behövs varierar baserat på din maskinvaruleverantör för OEM-tillverkare (original equipment manufacturer). Detaljerade steg som är specifika för ditt integrerade Azure Stack-system finns i maskinvaruleverantörens dokumentation om utbytbara enheter och komponenter.

Följande är icke växlas komponenter:

- CPU *
- Minne *
- Moderkortet/hanteringsstyrenhet för baskort (BMC) / video kort
- Disk controller/värdbusskort (HBA) / bakplan
- Nätverkskort (NIC)
- Operativsystemet disk *
- Enheter (enheter som inte stöder kan bytas till exempel PCI-e-tillägg-kort) *

* Dessa komponenter stöder bytas, men kan variera beroende på leverantörens implementering. Se dokumentationen från leverantören OEM FRU detaljerade anvisningar.

Följande flöde diagram visar den allmänna FRU-processen för att ersätta en icke växlas maskinvarukomponent.

![Flow diagram som visar komponenten ersättning flöde](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Den här åtgärden krävas inte baserat på fysiska villkoret för maskinvaran.

** Om maskinvaruleverantören OEM utför utbyte av komponenter och den inbyggda programvaran kan variera uppdateringar baserat på ditt supportkontrakt för.

## <a name="review-alert-information"></a>Granska aviseringsinformation

Azure Stack-hälsa och övervakningssystem spåra hälsotillståndet för nätverkskort och dataenheter som styrs av Lagringsdirigering. Den spårar inte andra maskinvarukomponenter. Aviseringar skapas för alla andra maskinvarukomponenter i leverantörsspecifika maskinvara övervakningslösning som körs på värden för maskinvara livscykel.  

## <a name="component-replacement-process"></a>Process för ersättning av komponenter

I följande steg finns en översikt över processen för komponenten ersättning. Följ inte stegen utan att referera till OEM-tillverkarens FRU-dokumentationen.

1. Använd avstängningsåtgärden till stängs noden skala enhet. Den här åtgärden krävas inte baserat på fysiska villkoret för maskinvaran.

2. I en förmodan avstängningsåtgärden misslyckas, använder den [tömma](azure-stack-node-actions.md#drain) åtgärden att placera noden skala enhet i underhållsläge. Den här åtgärden krävas inte baserat på fysiska villkoret för maskinvaran.

   > [!NOTE]  
   > Dock endast en nod kan inaktiveras och avstängd samtidigt utan att spräcka S2D (Storage Spaces Direct).

3. När noden skala enhet är i underhållsläge, använda den [stängs av](azure-stack-node-actions.md#scale-unit-node-actions) åtgärd. Den här åtgärden krävas inte baserat på fysiska villkoret för maskinvaran.

   > [!NOTE]  
   > Använd Webbgränssnitt för baseboard management controller (BMC) i stället förmodan att Stäng av åtgärden inte fungerar.

4. Ersätt den skadade maskinvarukomponenten. Om din maskinvaruleverantör för OEM utför ersättningen komponent kan variera beroende på ditt supportkontrakt för.  
5. Uppdatera den inbyggda programvaran. Följ dina leverantörsspecifika uppdateringsprocessen använda maskinvara livscykel värden och kontrollera att den ersatta maskinvarukomponenten har den godkända av inbyggd programvara som har använts. Om din maskinvaruleverantör för OEM utför det här steget kan variera beroende på ditt supportkontrakt för.  
6. Använd den [reparera](azure-stack-node-actions.md#scale-unit-node-actions) åtgärder för att se noden skala enhet till skalningsenheten.
7. Använda privileged slutpunkten till [Kontrollera status för virtuell disk reparera](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Med nya enheter, en fullständig storage reparera jobbet kan ta flera timmar beroende på systembelastning och Förbrukat utrymme.
8. När reparationsåtgärden har slutförts verifierar du att alla aktiva aviseringar har stängts automatiskt.

## <a name="next-steps"></a>Nästa steg

- Information om du vill ersätta en växlas fysisk disk, finns i [ersätta en disk](azure-stack-replace-disk.md).
- Information om du vill ersätta en fysisk nod finns i [ersätta en skala enhet nod](azure-stack-replace-node.md).
