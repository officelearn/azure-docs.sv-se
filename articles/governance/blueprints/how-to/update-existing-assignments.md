---
title: Uppdatera en befintlig tilldelning från portalen
description: Lär dig mer om mekanismen för att uppdatera en befintlig skisstilldelning från portalen i Azure Blueprints.
ms.date: 11/21/2019
ms.topic: how-to
ms.openlocfilehash: 2c1a24399d8fa1529665bfa60d1b889a84a29a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264666"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Så här uppdaterar du en befintlig skisstilldelning

När en skiss har tilldelats kan tilldelningen uppdateras. Det finns flera orsaker till att uppdatera en befintlig tilldelning, bland annat:

- Lägga till eller ta bort [resurslåsning](../concepts/resource-locking.md)
- Ändra värdet på [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters)
- Uppgradera tilldelningen till en nyare **publicerad** version av skissen

## <a name="updating-assignments"></a>Uppdatera tilldelningar

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Tilldelade ritningar** från sidan till vänster.

1. Högerklicka på skisstilldelningen i listan över ritningar. Klicka sedan på knappen **Uppdatera tilldelning** ELLER högerklicka på skisstilldelningen och välj **Uppdatera tilldelning**.

   ![Uppdatera en befintlig skisstilldelning](../media/update-existing-assignments/update-assignment.png)

1. Sidan **Tilldela skiss** laddas förfyllda med alla värden från den ursprungliga tilldelningen.
   Du kan ändra **ritningsdefinitionsversionen,** tillståndet **Lås tilldelning** och alla dynamiska parametrar som finns i skissdefinitionen. Klicka på **Tilldela** när du är klar med att göra ändringar.

1. På sidan uppdaterad tilldelningsinformation läser du den nya statusen. I det här exemplet har vi lagt till **Låsning** i tilldelningen.

   ![Uppdaterade en befintlig skisstilldelning - låsläget ändrat](../media/update-existing-assignments/updated-assignment.png)

1. Utforska information om andra **tilldelningsåtgärder** med hjälp av listrutan. Tabellen **Hanterade resurser** uppdateras efter vald tilldelningsåtgärd.

   ![Tilldelning av en skisstilldelning](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regler för uppdatering av tilldelningar

Distributionen av de uppdaterade tilldelningarna följer några viktiga regler. Dessa regler avgör vad som händer med redan distribuerade resurser. Den begärda ändringen och typen av artefaktresurs som distribueras eller uppdateras avgör vilka åtgärder som vidtas.

- Rolltilldelningar
  - Om rollen eller rollordattagaren (användare, grupp eller app) ändras skapas en ny rolltilldelning. Rolltilldelningar som tidigare distribuerats finns kvar.
- Principtilldelningar
  - Om parametrarna för principtilldelningen ändras uppdateras den befintliga tilldelningen.
  - Om definitionen av principtilldelningen ändras skapas en ny principtilldelning.
    Principtilldelningar som tidigare distribuerats finns kvar.
  - Om principtilldelningsartefakten tas bort från skissen lämnas distribuerade principtilldelningar kvar på plats.
- Azure Resource Manager-mallar
  - Mallen bearbetas via Resource Manager som en **PUT**. När varje resurstyp hanterar den här åtgärden på olika sätt, granska dokumentationen för varje inkluderad resurs för att fastställa effekten av den här åtgärden när den körs av skisser.

## <a name="possible-errors-on-updating-assignments"></a>Möjliga fel vid uppdatering av tilldelningar

När du uppdaterar tilldelningar är det möjligt att göra ändringar som bryts när de körs. Ett exempel är att ändra platsen för en resursgrupp när den redan har distribuerats. Alla ändringar som stöds av [Azure Resource Manager](../../../azure-resource-manager/management/overview.md) kan göras, men alla ändringar som skulle resultera i ett fel via Azure Resource Manager kommer också att resultera i fel på tilldelningen.

Det finns ingen gräns för hur många gånger en tilldelning kan uppdateras. Om ett fel uppstår bestämmer du felet och gör en ny uppdatering av tilldelningen.  Exempel på felscenarier:

- En felaktig parameter
- Ett redan befintligt objekt
- En ändring som inte stöds av Azure Resource Manager

## <a name="next-steps"></a>Nästa steg

- Läs mer om [skisslivscykeln](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../concepts/resource-locking.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).