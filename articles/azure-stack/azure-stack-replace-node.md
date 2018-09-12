---
title: Ersätta en skala enhet nod på ett integrerat Azure Stack | Microsoft Docs
description: Lär dig hur du ersätter en fysisk skala enhet nod i ett integrerat Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 1b37b150dad4951a4ade81f226b515ce9cae9053
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377062"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Ersätt en skala enhet nod i ett integrerat Azure Stack-system

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskrivs den allmänna processen för att ersätta en fysisk dator (kallas även en *skala enhet noden*) på ett Azure Stack-integrerat system. Faktiska skala enhet noden ersättning stegen varierar baserat på din maskinvaruleverantör för OEM-tillverkare (original equipment manufacturer). Se dokumentationen från leverantören fältet replaceable enhet (FRU) för detaljerade anvisningar som är specifika för ditt system.

Följande flöde diagram visar den allmänna FRU-processen för att ersätta en hel skala enhet nod.

![Flödesschema för Ersätt noden process](media/azure-stack-replace-node/replacenodeflow.png)

* Den här åtgärden kan inte krävas baserat på fysiska villkoret för maskinvaran.

## <a name="review-alert-information"></a>Granska aviseringsinformation

Om en nod för skala enhet är nere kan får du följande kritiska aviseringar:

- Noden inte är ansluten till nätverksstyrenhet
- Nod blir otillgänglig för placering av virtuella datorer
- Skala enhet noden är offline

![Listan över aviseringar för skalningsenheten ned](media/azure-stack-replace-node/nodedownalerts.png)

Om du öppnar den **skala enhet noden är offline** aviseringen, varningsbeskrivningen innehåller noden skala enhet som inte är tillgänglig. Du får även fler aviseringar i OEM-specifika övervakningslösning som körs på värden för maskinvara livscykel.

![Information om noden offline avisering](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Skala enhet noden ersättning process

Följande steg anges som en översikt över skala enhet noden ersättningsprocessen. Se OEM maskinvara FRU dokumentationen från leverantören detaljerade anvisningar som är specifika för ditt system. Följ inte stegen utan att referera till din OEM-tillverkarens dokumentation.

1. Använd den [tömma](azure-stack-node-actions.md#scale-unit-node-actions) åtgärden att placera noden skala enhet i underhållsläge. Den här åtgärden krävas inte baserat på fysiska villkoret för maskinvaran.

   > [!NOTE]
   > Dock endast en nod kan tömda och stängts av på samma gång utan att spräcka S2D (Storage Spaces Direct).

2. Om noden fortfarande är påslagen, använder du den [stängs av](azure-stack-node-actions.md#scale-unit-node-actions) åtgärd. Den här åtgärden krävas inte baserat på fysiska villkoret för maskinvaran.
 
   > [!NOTE]
   > Använd Webbgränssnitt för baseboard management controller (BMC) i stället förmodan att Stäng av åtgärden inte fungerar.

1. Ersätt den fysiska datorn. Detta görs vanligtvis av maskinvaruleverantören OEM.
2. Använd den [reparera](azure-stack-node-actions.md#scale-unit-node-actions) åtgärder för att lägga till den nya fysiska datorn i skalningsenheten.
3. Använda privileged slutpunkten till [Kontrollera status för virtuell disk reparera](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Med nya enheter, en fullständig storage reparera jobbet kan ta flera timmar beroende på systembelastning och Förbrukat utrymme.
4. När reparationsåtgärden har slutförts verifierar du att alla aktiva aviseringar har stängts automatiskt.

## <a name="next-steps"></a>Nästa steg

- Information om du vill ersätta en växlas fysisk disk, finns i [ersätta en disk](azure-stack-replace-disk.md). 
- Information om du vill ersätta en icke växlas maskinvarukomponent finns i [ersätter en maskinvarukomponent](azure-stack-replace-component.md).
