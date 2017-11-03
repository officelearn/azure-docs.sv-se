---
title: "Ersätta en scale unit nod på ett Azure-stacken integrerat system | Microsoft Docs"
description: "Lär dig mer om att ersätta en fysisk skala enhet nod i ett Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: troettinger
manager: byronr
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: 63ba6cedd32bcf6ea3039bc80cc4a3f3407adfa7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Ersätta en scale unit nod på ett Azure-stacken integrerat system

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver den allmänna processen för att ersätta en fysisk dator (kallas även en *skala enhet nod*) på en Azure-stacken integrerat system. Faktiska skala nod ersättnings stegen varierar utifrån maskinvaruleverantören OEM-tillverkaren (OEM). Dokumentationen leverantörens fältet utbytbara enhet (FRU) för detaljerade anvisningar som är specifika för ditt system.

Följande flöde diagram visar den allmänna FRU-processen att ersätta en hela skalan enhet nod.

![Flödesschema för Ersätt nod process](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* Den här åtgärden kan inte krävas baserat på villkoret fysisk maskinvara.

## <a name="review-alert-information"></a>Granska aviseringsinformation

Om en enhet skala-nod är nere kan får du följande kritiska aviseringar:

- Noden som inte är ansluten till nätverksstyrenheten
- Nod blir otillgänglig för placering av virtuella datorer
- Skala enhet noden är offline

![Listan över aviseringar för skalenheten ned](media/azure-stack-replace-node/NodeDownAlerts.PNG)

Om du öppnar aviseringen ”skala enhet noden är offline” innehåller varningsbeskrivningen noden skala enhet som inte är tillgänglig. Du kan också få ytterligare aviseringar i OEM-specifika övervakningslösning som körs på värden för maskinvara livscykel.

![Information om noden offline avisering](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>Skala enhet nod ersättning process

Följande steg tillhandahålls som en översikt över skala enhet nod ersättningsprocessen. Dokumentationen din OEM maskinvaruleverantören FRU detaljerade anvisningar som är specifika för ditt system. Följ inte stegen utan att referera till din OEM-tillverkarens dokumentation.

1. Använd den [tömmer](azure-stack-node-actions.md#scale-unit-node-actions) åtgärden att placera noden skala enhet i underhållsläge. Den här åtgärden kan inte krävas baserat på villkoret fysisk maskinvara.
2. Om noden fortfarande är påslagen, använder du den [stängs](azure-stack-node-actions.md#scale-unit-node-actions) åtgärd. Den här åtgärden kan inte krävas baserat på villkoret fysisk maskinvara.
 
   > [!NOTE]
   > Använd webbgränssnittet baseboard management controller (BMC) i stället förmodan som inte fungerar stänga av åtgärden.

1. Ersätt den fysiska datorn. Detta görs normalt genom maskinvaruleverantören OEM.
2. Använd den [reparera](azure-stack-node-actions.md#scale-unit-node-actions) åtgärder för att lägga till den nya fysiska datorn i skalningsenheten.
3. Använda privileged slutpunkt till [Kontrollera status för virtuell disk reparera](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Med nya dataenheter ett jobb för reparation av fullständig lagring kan ta flera timmar beroende på systembelastning och förbrukas utrymme.
4. När reparationsåtgärden har slutförts måste du verifiera att alla aktiva aviseringar automatiskt har stängts.

## <a name="next-steps"></a>Nästa steg

- Information om du vill ersätta en växlas fysisk disk finns [ersätta en disk](azure-stack-replace-disk.md). 
- Information om du vill ersätta en icke växlas maskinvarukomponent finns [ersätta en maskinvarukomponent](azure-stack-replace-component.md). 