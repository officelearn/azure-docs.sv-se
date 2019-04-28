---
title: Hur du uppdaterar en befintlig tilldelning från portalen
description: Läs mer om mekanism för att uppdatera en befintlig tilldelning från portalen i Azure skisser.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c75bd8c3831bad0c8217f16315843cbe3824fe4d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766564"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Hur du uppdaterar en befintlig tilldelning av skiss

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resource låsning](../concepts/resource-locking.md)
- Ändra värdet för [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters)
- Uppgradera tilldelning till en nyare **publicerad** version av skissen

## <a name="updating-assignments"></a>Uppdatering av tilldelningar

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Tilldelade skisser** på sidan till vänster.

1. I listan över skisser, klickar du på skisstilldelningen. Klicka sedan på den **uppdatera tilldelning** eller högerklicka på skisstilldelningen och välj **uppdatera tilldelning**.

   ![Uppdatera en befintlig tilldelning av skiss](../media/update-existing-assignments/update-assignment.png)

1. Den **tilldela skissen** sidan läses in fylls i automatiskt med alla värden från den ursprungliga tilldelningen. Du kan ändra den **skissen definitionsversion**, **Lås tilldelning** tillstånd och någon av de dynamiska parametrar som finns på skissdefinitionen. Klicka på **tilldela** när gjort önskade ändringar.

1. Se den nya statusen på informationssidan uppdaterade tilldelning. I det här exemplet vi har lagt till **Locking** att tilldelningen.

   ![Uppdatera en befintlig skisstilldelningen - låsläge har ändrats](../media/update-existing-assignments/updated-assignment.png)

1. Ta reda på information om andra **tilldelningsåtgärden** med hjälp av listrutan. Tabellen med **hanterade resurser** uppdateringar av valda tilldelning åtgärd.

   ![Tilldelning av driften av en tilldelning av skiss](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regler för uppdatering av tilldelningar

Distributionen av de uppdaterade tilldelningarna följer några viktiga regler. Reglerna avgör vad som händer med redan distribuerade resurser. Den begärda ändringen och typ av artefakt resurs att distribueras eller uppdateras bestämmer du vilka åtgärder vidtas.

- Rolltilldelningar
  - Om rollen eller rollen tilldelad person (användare, grupp eller app) ändras, skapas en ny rolltilldelning. Rolltilldelningar som tidigare har distribuerat lämnas kvar.
- Principtilldelningar
  - Om parametrarna för principtilldelningen ändras, uppdateras den befintliga tilldelningen.
  - Om definitionen av principtilldelningen har ändrats, skapas en ny principtilldelning. Principtilldelningar som tidigare har distribuerat lämnas kvar.
  - Om principen tilldelning artefakten tas bort från skissen, distribuerat principen tilldelningar lämnas kvar.
- Azure Resource Manager-mallar
  - Mallen har bearbetats via Resource Manager som en **PLACERA**. Eftersom varje resurstyp hanteras annorlunda i den här åtgärden, granska dokumentationen för varje resurs som ingår för att kontrollera effekten av den här åtgärden när kör av skisser.

## <a name="possible-errors-on-updating-assignments"></a>Möjliga fel vid uppdatering av tilldelningar

När du uppdaterar tilldelningar, är det möjligt att göra ändringar som dela när den körs. Ett exempel ändrar platsen för en resursgrupp när det redan har distribuerats. Ändringar som stöds av [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) kan göras, men någon ändra det skulle resultera i ett fel via Azure Resource Manager leder också fel på tilldelningen.

Det finns ingen gräns för hur många gånger en tilldelning kan uppdateras. Om ett fel inträffar felberäkningen och gör en annan uppdatering för tilldelning.  Exempelscenarier för fel:

- En ogiltig parameter
- Ett befintligt objekt
- En ändring som inte stöds av Azure Resource Manager

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).