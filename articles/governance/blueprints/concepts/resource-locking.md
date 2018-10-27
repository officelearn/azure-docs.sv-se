---
title: Förstå resource låsning i Azure skisser
description: Läs mer om låsning alternativen för att skydda resurser när du tilldelar en skiss.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139450"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Förstå resource låsning i Azure skisser

Skapandet av enhetliga testmiljöer i stor skala är endast verkligen användbart om det finns en mekanism för att underhålla den konsekvensen. Den här artikeln förklarar hur du låser resurs i Azure skisser.

## <a name="locking-modes-and-states"></a>Låsning lägen och tillstånd

Låsning läge gäller skisstilldelningen och har bara två alternativ: **ingen** eller **alla resurser**. Låsning läget konfigureras under skisstilldelningen och kan inte ändras när tilldelningen har tillämpats för prenumerationen.

Resurser som skapas av artefakter i en skisstilldelningen har tre lägen: **inte låst**, **skrivskyddad**, eller **det går inte att redigera / ta bort**. Varje artefakt kan finnas i den **inte låst** tillstånd. Icke-resursgruppen artefakter har dock **skrivskyddad** och resursgrupper har **det går inte att redigera / ta bort** tillstånd. Den här skillnaden är en viktig skillnad i hur de här resurserna hanteras.

Den **skrivskyddad** tillstånd är exakt som de definierats: resursen kan inte ändras på något sätt – inga ändringar och den kan inte tas bort. Den **det går inte att redigera / ta bort** mer nyanserade på grund av den ”behållare” natur resursgrupper. Gruppobjektet resurs är skrivskyddad, men det är möjligt att göra ändringar i icke-låst resurser i resursgruppen.

## <a name="overriding-locking-states"></a>Åsidosätta låsning tillstånd

Det är vanligtvis möjligt för någon med lämpliga [rollbaserad åtkomstkontroll](../../../role-based-access-control/overview.md) (RBAC) i prenumerationen, till exempel rollen ”ägare” ska kunna ändra eller ta bort alla resurser. Den här åtkomsten inte är fallet när skisser gäller låsning som en del av en tilldelning av distribuerade. Om tilldelningen har angetts med den **Lås** alternativet och ännu inte prenumerationen ägaren kan ändra de inkluderade resurserna.

Den här säkerhetsåtgärd skyddar konsekvens definierade skissen och den miljö som den har utformats för att skapa från oavsiktliga eller programmässiga borttagning eller ändring.

## <a name="removing-locking-states"></a>Ta bort låsning tillstånd

Om det blir nödvändigt att ta bort resurser som skapas av en tilldelning är sättet att ta bort dem först ta bort tilldelningen. När tilldelningen tas bort, tas lås som skapats av skisser bort. Resursen är kvar och måste tas bort på vanligt sätt.

## <a name="how-blueprint-locks-work"></a>Hur skissen låser arbete

RBAC-roll `denyAssignments` tillämpas på artefakt resurser under tilldelningen av en skiss om tilldelningen har valt den **Lås** alternativet. Rollen läggs till av hanterade identiteten för skisstilldelningen och kan bara tas bort från artefakt resurser av samma hanterad identitet. Den här säkerhetsåtgärd tillämpar mekanismen för låsning och förhindrar att ta bort skissen låset utanför skisser. Ta bort rollen och låset är endast möjligt genom att ta bort skisstilldelningen, vilket kan endast utföras av personer med rätt behörighet.

> [!IMPORTANT]
> Azure Resource Manager cachelagrar rollen tilldelningsinformation för upp till 30 minuter. Därför `denyAssignments` på skiss resurser kanske inte omedelbart att i fulla effekten. Under denna tidsperiod kan det vara möjligt att ta bort en resurs som ska skyddas av skiss Lås.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)