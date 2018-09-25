---
title: Förstå resource låsning i Azure skisser
description: Läs mer om låsning alternativen för att skydda resurser när du tilldelar en skiss.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973260"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Förstå resource låsning i Azure skisser

Skapandet av enhetliga testmiljöer i stor skala är endast verkligen användbart om det finns en mekanism för att säkerställa att konsekvens sparas. Den här artikeln förklarar hur du låser resurs i Azure skisser.

## <a name="locking-modes-and-states"></a>Låsning lägen och tillstånd

Låsning läge gäller skisstilldelningen och har bara två alternativ: **ingen** eller **alla resurser**. Detta konfigureras när skisstilldelningen och kan inte ändras när tilldelningen har tillämpats för prenumerationen.

Resurser som skapas av definitioner av artefakter i skissen tilldelas prenumerationen har tre lägen: **inte låst**, **skrivskyddad**, eller **det går inte att redigera / ta bort**. Alla typer av artefakt kan ha den **inte låst** tillstånd. Icke-resursgruppen artefakter är dock som **skrivskyddad** och resursgrupper är som **det går inte att redigera / ta bort**. Det här är en viktig skillnad i hur de här resurserna hanteras.

Den **skrivskyddad** tillstånd är exakt som de definierats: resursen kan inte ändras på något sätt – inga ändringar och den kan inte tas bort. Den **det går inte att redigera / ta bort** mer nyanserade på grund av den ”behållare” natur resursgrupper. Gruppobjektet resurs är skrivskyddad, men det är möjligt att skapa, uppdatera och ta bort resurser i resursen gruppera – så länge som de inte är en del av alla skisstilldelningen med den **skrivskyddad** låsa tillstånd.

## <a name="overriding-locking-states"></a>Åsidosätta låsning tillstånd

Det är vanligtvis möjligt för en person med rätt [rollbaserad åtkomstkontroll](../../../role-based-access-control/overview.md) (RBAC) i prenumerationen, till exempel rollen ”ägare” för att kunna ändra eller ta bort alla resurser detta inte är fallet när skisser gäller låsning som en del av en tilldelning av distribuerade. Om tilldelningen har angetts med den **Lås** alternativet och ännu inte prenumerationen ägaren kan ändra de inkluderade resurserna.

Detta skyddar konsekvens definierade skissen och den miljö som den har utformats för att skapa från oavsiktliga eller programmässiga borttagning eller ändring.

## <a name="removing-locking-states"></a>Ta bort låsning tillstånd

Om du måste ta bort alla resurser som skapats av en tilldelning, är det enda sättet att ta bort dem först ta bort tilldelningen. När tilldelningen tas bort, tas lås som skapats av skisser bort. Resursen, men är kvar och då måste tas bort på normalt sätt av någon med rätt behörighet.

## <a name="how-blueprint-locks-work"></a>Hur skissen låser arbete

RBAC-roll `denyAssignments` tillämpas på artefakt resurser under tilldelningen av en skiss om tilldelningen har valt den **Lås** alternativet. Rollen läggs till av hanterade identiteten för skisstilldelningen och kan bara tas bort från artefakt resurser av samma hanterad identitet. Detta tillämpar mekanismen för låsning och förhindrar försök att ta bort skissen låset utanför skisser. Ta bort rollen och låset är endast möjligt genom att ta bort skisstilldelningen, vilket kan endast utföras av personer med rätt behörighet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [skiss livscykel](lifecycle.md)
- Förstå hur du använder [Statiska och dynamiska parametrar](parameters.md)
- Lär dig att anpassa den [skiss ordningsföljd](sequencing-order.md)
- Lär dig hur du [uppdatera befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)