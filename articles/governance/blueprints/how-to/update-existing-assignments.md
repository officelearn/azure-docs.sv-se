---
title: Hur du uppdaterar en befintlig Azure-skisstilldelningen
description: Läs mer om mekanism för att uppdatera en befintlig tilldelning i Azure skisser.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956208"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Hur du uppdaterar en befintlig tilldelning av skiss

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resource låsning](../concepts/resource-locking.md)
- Ändra värdet för [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters)
- Uppgradera tilldelning till en nyare **publicerad** version av skissen

## <a name="updating-assignments"></a>Uppdatering av tilldelningar

1. Starta tjänsten Azure skisser i Azure portal genom att klicka på **alla tjänster** och söka efter och välja **princip** i den vänstra rutan. På den **princip** klickar du på **skisser**.

1. Välj **tilldelade skisser** från sidan till vänster.

1. I listan över skisser, klickar du på skisstilldelningen och klicka sedan på den **Uppdateringstilldelning** eller högerklicka på skisstilldelningen och välj **Uppdateringstilldelning**.

   ![Uppdateringstilldelning](../media/update-existing-assignments/update-assignment.png)

1. Den **tilldela skissen** sidan läses in fylls i automatiskt med alla värden från den ursprungliga tilldelningen. Du kan ändra den **skissen definitionsversion**, **Lås tilldelning** tillstånd och någon av de dynamiska parametrar som finns på skissdefinitionen. Klicka på **tilldela** när gjort önskade ändringar.

1. Se den nya statusen på informationssidan uppdaterade tilldelning. I det här exemplet vi har lagt till **Locking** att tilldelningen.

   ![Uppdaterade tilldelning - låst](../media/update-existing-assignments/updated-assignment.png)

1. Ta reda på information om andra **tilldelningsåtgärden** med hjälp av listrutan. Tabellen med **hanterade resurser** uppdateringar av valda tilldelning åtgärd.

   ![Tilldelningsåtgärden](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regler för uppdatering av tilldelningar

Distributionen av de uppdaterade tilldelningarna följer några viktiga regler. Reglerna avgör vad som händer med en befintlig resurs beroende på den begärda ändringen och vilken typ av artefakt resurs som distribuerats eller uppdateras.

- Rolltilldelningar
  - Om rollen eller rollen tilldelad person (användare, grupp eller app) ändras, skapas en ny rolltilldelning. Tidigare distribuerade rolltilldelningen lämnas på plats.
- Principtilldelningar
  - Om parametrarna för principtilldelningen ändras, uppdateras den befintliga tilldelningen.
  - Om definitionen av principtilldelningen ändras, skapas en ny principtilldelning. Tidigare distribuerade principtilldelningen lämnas på plats.
  - Om principen tilldelning artefakten tas bort från skissen, vänster tidigare distribuerad principtilldelningen på plats.
- Azure Resource Manager-mallar
  - Mallen har bearbetats via Resource Manager som en **PLACERA**. Läs dokumentationen för varje resurs som ingår för att kontrollera effekten av den här åtgärden när kör av skisser som varje resurstyp hanterar detta på olika sätt.

## <a name="possible-errors-on-updating-assignments"></a>Möjliga fel vid uppdatering av tilldelningar

När du uppdaterar tilldelningar, är det möjligt att göra ändringar som dela när den körs. Ett exempel på detta ändrar platsen för en resursgrupp när det redan har distribuerats. Ändringar som stöds av [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) kan göras, men någon ändra det skulle resultera i ett fel via Azure Resource Manager leder också fel på tilldelningen.

Det finns ingen gräns för hur många gånger en tilldelning kan uppdateras. Därför om ett fel inträffar, antingen på grund av en ogiltig parameter, ett redan befintligt objekt eller en ändring tillåts inte i Azure Resource Manager, felberäkningen och gör en annan uppdatering för tilldelning.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [skiss livscykel](../concepts/lifecycle.md)
- Förstå hur du använder [Statiska och dynamiska parametrar](../concepts/parameters.md)
- Lär dig att anpassa den [skiss ordningsföljd](../concepts/sequencing-order.md)
- Ta reda på hur du får använda [skiss resource låsning](../concepts/resource-locking.md)
- Lös problem vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)
