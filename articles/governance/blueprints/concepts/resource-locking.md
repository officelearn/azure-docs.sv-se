---
title: Förstå resource låsning
description: Läs mer om låsning alternativen för att skydda resurser när du tilldelar en skiss.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2e281896d45ada8010f24a1f18265a8cdd523d31
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697001"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Förstå resource låsning i Azure skisser

Skapandet av enhetliga testmiljöer i stor skala är endast verkligen användbart om det finns en mekanism för att underhålla den konsekvensen. Den här artikeln förklarar hur du låser resurs i Azure skisser.

## <a name="locking-modes-and-states"></a>Låsning lägen och tillstånd

Låsning läge gäller skisstilldelningen och det finns tre alternativ: **Lås**, **skrivskyddad**, eller **ta inte bort**. Låsning läget konfigureras under distributionen av artefakt under en skisstilldelningen. Ett annat låsning läge kan ställas in genom att uppdatera skisstilldelningen.
Låsning lägen, men kan inte ändras utanför skisser.

Resurser som skapas av artefakter i en skisstilldelningen har fyra lägen: **Inte låst**, **skrivskyddad**, **det går inte att redigera / ta bort**, eller **kan inte ta bort**. Varje typ av artefakt som kan finnas i den **inte låst** tillstånd. I följande tabell kan användas för att bestämma tillståndet för en resurs:

|Läge|Artefakten resurstyp|Status|Beskrivning|
|-|-|-|-|
|Lås inte|*|Inte låst|Resurser som skyddas inte av skisser. Det här tillståndet används också för resurser som har lagts till i en **skrivskyddad** eller **ta inte bort** resource group artefakt från utanför en skisstilldelningen.|
|Skrivskyddad|Resursgrupp|Det går inte att redigera / ta bort|Resursgruppen är skrivskyddad och går inte att ändra taggar på resursgruppen. **Inte låst** resurser kan vara har lagts till, flyttas, ändras eller tas bort från den här resursgruppen.|
|Skrivskyddad|Icke-resursgrupp|Skrivskyddad|Resursen kan inte ändras på något sätt – inga ändringar och den kan inte tas bort.|
|Ta inte bort|*|Det går inte att ta bort|Resurserna kan ändras, men kan inte tas bort. **Inte låst** resurser kan vara har lagts till, flyttas, ändras eller tas bort från den här resursgruppen.|

## <a name="overriding-locking-states"></a>Åsidosätta låsning tillstånd

Det är vanligtvis möjligt för någon med lämpliga [rollbaserad åtkomstkontroll](../../../role-based-access-control/overview.md) (RBAC) i prenumerationen, till exempel rollen ”ägare” ska kunna ändra eller ta bort alla resurser. Den här åtkomsten inte är fallet när skisser gäller låsning som en del av en tilldelning av distribuerade. Om tilldelningen har angetts med den **skrivskyddad** eller **ta inte bort** alternativet och ännu inte prenumerationen ägare kan utföra den blockerade åtgärden på den skyddade resursen.

Den här säkerhetsåtgärd skyddar konsekvens definierade skissen och den miljö som den har utformats för att skapa från oavsiktliga eller programmässiga borttagning eller ändring.

## <a name="removing-locking-states"></a>Ta bort låsning tillstånd

Om det blir nödvändigt att ändra eller ta bort en resurs som skyddas av en tilldelning, finns det två sätt att göra detta.

- Uppdaterar skisstilldelningen till ett låsning **inte Lås**
- Ta bort tilldelning av skiss

När tilldelningen tas bort, tas lås som skapats av skisser bort. Resursen är kvar och måste tas bort på vanligt sätt.

## <a name="how-blueprint-locks-work"></a>Hur skissen låser arbete

En RBAC [neka tilldelningar](../../../role-based-access-control/deny-assignments.md) neka åtgärd tillämpas på artefakt resurser under tilldelningen av en skiss om tilldelningen har valt den **skrivskyddad** eller **ta inte bort** alternativet. Neka-åtgärden läggs till av hanterade identiteten för skisstilldelningen och kan bara tas bort från artefakt resurser av samma hanterad identitet. Den här säkerhetsåtgärd tillämpar mekanismen för låsning och förhindrar att ta bort skissen låset utanför skisser.

> [!IMPORTANT]
> Azure Resource Manager cachelagrar rollen tilldelningsinformation för upp till 30 minuter. Därför kan neka tilldelningar neka åtgärdens på skiss resurser inte kanske omedelbart i fulla effekten. Under denna tidsperiod kan det vara möjligt att ta bort en resurs som ska skyddas av skiss Lås.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)