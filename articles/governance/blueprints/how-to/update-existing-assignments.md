---
title: Hur du uppdaterar en befintlig Azure-skisstilldelningen
description: Läs mer om mekanism för att uppdatera en befintlig tilldelning i Azure skisser.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2c9f660e54da50e32ce1d0dc43b0efeacd643c57
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093793"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Hur du uppdaterar en befintlig tilldelning av skiss

När en skiss tilldelas kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, inklusive:

- Lägg till eller ta bort [resource låsning](../concepts/resource-locking.md)
- Ändra värdet för [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters)
- Uppgradera tilldelning till en nyare **publicerad** version av skissen

## <a name="updating-assignments"></a>Uppdatering av tilldelningar

1. Klicka på **alla tjänster** och söka efter och välja **princip** i den vänstra rutan. Klicka på **Skisser** på sidan **Princip**.

1. Välj **Tilldelade skisser** på sidan till vänster.

1. I listan över skisser, klickar du på skisstilldelningen. Klicka sedan på den **Uppdateringstilldelning** eller högerklicka på skisstilldelningen och välj **Uppdateringstilldelning**.

   ![Uppdateringstilldelning](../media/update-existing-assignments/update-assignment.png)

1. Den **tilldela skissen** sidan läses in fylls i automatiskt med alla värden från den ursprungliga tilldelningen. Du kan ändra den **skissen definitionsversion**, **Lås tilldelning** tillstånd och någon av de dynamiska parametrar som finns på skissdefinitionen. Klicka på **tilldela** när gjort önskade ändringar.

1. Se den nya statusen på informationssidan uppdaterade tilldelning. I det här exemplet vi har lagt till **Locking** att tilldelningen.

   ![Uppdaterade tilldelning - låst](../media/update-existing-assignments/updated-assignment.png)

1. Ta reda på information om andra **tilldelningsåtgärden** med hjälp av listrutan. Tabellen med **hanterade resurser** uppdateringar av valda tilldelning åtgärd.

   ![Tilldelningsåtgärden](../media/update-existing-assignments/assignment-operations.png)

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

- Lär dig mer om [livscykeln för en skiss](../concepts/lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md)
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)
