---
title: Skapa och dela en fråga i Azure Portal
description: I den här självstudien lär du dig att skapa en resurs diagram fråga och dela den med andra i Azure Portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: 10f93f34923fb2399a4b2053167576ba004ae467
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821685"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-azure-portal"></a>Självstudie: skapa och dela en Azure Resource Graph-fråga i Azure Portal

Med Azure Resource Graph Explorer kan du spara dina resurs diagram frågor direkt i Azure Portal. Det finns två typer av frågor, _privata_ och _delade_. En _privat_ fråga sparas i Azure Portal inställningar, men en _delad_ fråga är en Resource Manager-resurs som kan hanteras med ROLLBASERAD åtkomst kontroll (RBAC) och skyddas med resurs lås.

När du sparar frågor i Azure Portal sparas din tid för att söka efter dina favorit frågor eller vanliga frågor. När du delar frågor kan du låta ditt team vara konsekvent och upprepande. I den här självstudien utför du följande steg:

> [!div class="checklist"]
> - Skapa och ta bort en _privat_ fråga
> - Skapa en _delad_ fråga
> - Identifiera _delade_ frågor
> - Ta bort en _delad_ fråga

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-and-delete-a-private-query"></a>Skapa och ta bort en privat fråga

_Privata_ frågor är bara tillgängliga eller synliga för det konto som skapar dem. När de har sparats i ett kontos Azure Portal inställningar kan de bara skapas, användas och tas bort från Azure Portal. En _privat_ fråga är inte en Resource Manager-resurs. Skapa en ny _privat_ fråga genom att följa dessa steg:

1. Välj alla tjänster på menyn portal eller Använd Azure Search-rutan längst upp på alla sidor.
   Sök efter och välj "resurs diagram Utforskaren".

1. Ange följande fråga på sidan "fråga 1" på Azure-resursens Graph-Utforskare. Information om den här frågan finns i [samples-Count Virtual Machines by OS Type](../samples/starter.md#count-virtual-machines-by-os-type).
   Välj **Kör fråga** för att se frågeresultaten i det nedre fönstret.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Välj **Spara** eller **Spara som**, ange _namnet_ som antal virtuella datorer per operativ system, lämna _typen_ som privat fråga och välj sedan **Spara** längst ned i fönstret _Spara fråga_ . Rubriken på fliken ändras från "fråga 1" till "antal virtuella datorer per operativ system".

1. Bläddra bort från Azure Resource Graph Explorer i Azure Portal och gå tillbaka till den. Den sparade frågan visas inte längre och fliken "fråga 1" har returnerats.

1. Välj **öppna en fråga**. Kontrol lera att _typen_ är "privat fråga". Det sparade antalet virtuella datorer efter operativ system visas nu i listan _frågenamn_ . Välj rubrik länken för den sparade frågan och den läses in på en ny flik med samma frågor.

   > [!NOTE]
   > När en sparad fråga är öppen och fliken visar dess _namn_, uppdaterar knappen **Spara** med alla ändringar som görs. Om du vill skapa en ny sparad fråga använder du **Spara som** och följer stegen som om det var en helt ny sparad fråga.

1. Om du vill ta bort den sparade frågan väljer du **öppna en fråga** igen och kontrollerar att _typen_ är "privat fråga". På raden i frågan antal virtuella datorer per operativ system, väljer du pappers korgs ikonen. I bekräftelse dialog rutan väljer du **Ja** för att slutföra borttagningen av frågan. Stäng sedan fönstret _öppna en fråga_ .

## <a name="create-a-shared-query"></a>Skapa en delad fråga

Till skillnad från en _privat_ fråga är en _delad_ fråga en Resource Manager-resurs. Detta faktum innebär att frågan sparas i en resurs grupp, kan hanteras och kontrol leras med RBAC och till och med vara skyddad med resurs lås. Som en resurs kan alla med lämpliga behörigheter Se och använda den. Skapa en ny _delad_ fråga genom att följa dessa steg:

1. Välj alla tjänster på menyn portal eller Använd Azure Search-rutan längst upp på alla sidor.
   Sök efter och välj "resurs diagram Utforskaren".

1. Ange följande fråga på sidan "fråga 1" på Azure-resursens Graph-Utforskare. Information om den här frågan finns i [samples-Count Virtual Machines by OS Type](../samples/starter.md#count-virtual-machines-by-os-type).
   Välj **Kör fråga** för att se frågeresultaten i det nedre fönstret.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Välj **Spara** eller **Spara som**.

   ![Spara den nya frågan med knappen Spara](../media/create-share-query/save-shared-query-buttons.png)

1. I fönstret _Spara fråga_ anger du _namnet_ som antal virtuella datorer per operativ system, ändrar _typ_ till delad fråga, anger _Beskrivning_ till antal virtuella datorer efter OS-typ och väljer den _prenumeration_ där frågans resurs skapas. Låt kryss rutan publicera till resurs-Graph-fråga resurs grupp vara markerad och _resurs gruppens plats_ har värdet (US) västra centrala USA. Välj sedan **Spara** längst ned i fönstret _Spara fråga_ . Rubriken på fliken ändras från "fråga 1" till "antal virtuella datorer per operativ system". Den första gången resurs gruppen resurs-Graph-frågars används, och spara tar längre tid när resurs gruppen skapas.

   ![Spara den nya frågan som en delad fråga](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE]
   > Om du vill kan du ta bort kontrollen för att ange namnet på en befintlig resurs grupp för att spara den delade frågan i. Om du använder standard resurs gruppen för frågor blir det enklare att upptäcka _delade_ frågor. Det ger också mer uppenbart syftet med den här resurs gruppen. Att välja en befintlig resurs grupp kan dock göras av säkerhets skäl baserat på befintliga behörigheter.

1. Bläddra bort från Azure Resource Graph Explorer i Azure Portal och gå tillbaka till den. Den sparade frågan visas inte längre och fliken "fråga 1" har returnerats.

1. Välj **öppna en fråga**. Kontrol lera att _typen_ är "delad fråga" och kombinationen av _prenumeration_ och _resurs grupp_ matchning där du sparade frågan. Det sparade antalet virtuella datorer efter operativ system visas nu i listan _frågenamn_ . Välj rubrik länken för den sparade frågan och den läses in på en ny flik med samma frågor. Som en _delad_ fråga visas en ikon på fliken bredvid rubriken som anger den som delad.

   ![Visa den delade frågan med ikonen](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE]
   > När en sparad fråga är öppen och fliken visar dess _namn_, uppdaterar knappen **Spara** med alla ändringar som görs. Om du vill skapa en ny sparad fråga använder du **Spara som** och följer stegen som om det var en helt ny sparad fråga.

## <a name="discover-shared-queries"></a>Identifiera delade frågor

Som en _delad_ fråga är en Resource Manager-resurs finns det flera sätt att hitta dem på:

- I resurs diagram Utforskaren väljer du **öppna en fråga** och anger _typ_ till ' delad fråga '
- Portal sidan för resurs diagram frågor
- Resurs gruppen som den sparades i
- Med en fråga till resurs diagram

### <a name="view-resource-graph-queries"></a>Visa resurs diagram frågor

I Azure Portal visar sidan resurs diagram frågor _delade_ frågor som det inloggade kontot har åtkomst till. På den här sidan kan du filtrera efter namn, prenumeration, resurs grupp och andra egenskaper för resurs diagram frågan. Resurs diagram frågor kan också märkas, exporteras och tas bort med det här gränssnittet.

Om du väljer en av frågorna öppnas sidan resurs diagram fråga. Precis som andra Resource Manager-resurser erbjuder den här sidan en interaktiv översikt tillsammans med aktivitets loggen, åtkomst kontrollen och taggarna. Du kan också använda ett resurs lås direkt från den här sidan.

Gå till sidan för resurs diagram frågor från Portal-menyn genom att välja alla tjänster eller använda Azure Search-rutan längst upp på alla sidor. Sök efter och välj "resurs diagram Utforskaren".

### <a name="list-resource-groups-resources"></a>Visa resurs grupp resurser

Resurs diagram frågan visas bredvid andra resurser som ingår i en resurs grupp.
Om du väljer resurs diagram frågan öppnas sidan för frågan. Ellipsen eller högerklickar på alternativen fungerar på samma sätt som sidan för resurs diagrammets fråga.

### <a name="query-resource-graph"></a>Fråga resurs diagram

Som Resource Manager-resurs kan du hitta resurs diagram frågor med en fråga till resurs diagram.
Följande resurs diagram fråga begränsar gränser efter typ `Microsoft.ResourceGraph/queries`och använder sedan `project` för att bara visa en lista över namnet, tiden som har ändrats och själva frågan:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Ta bort en delad fråga

Om en _delad_ fråga inte längre behövs tar du bort den. Att ta bort en _delad_ fråga tar bort den faktiska Resource Manager-resursen. Alla instrument paneler resultat diagrammet har fästs för att visa ett fel meddelande. När fel meddelandet visas använder du knappen **ta bort från instrument panelen** för att rensa instrument panelen.

En _delad_ fråga kan tas bort från följande gränssnitt:
- Sidan frågor för resurs diagram
- Sidan resurs diagram fråga
- Resurs diagram Utforskare öppnar en fråga-sida
- Sidan resurs grupper

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort _privata_ och _delade_ frågor som du har skapat om du inte längre vill ha dem.

## <a name="next-steps"></a>Nästa steg

- Kör din första fråga med [Azure Portal](../first-query-portal.md)
- Få mer information om [frågespråket](../concepts/query-language.md)
- Lär dig att [utforska resurser](../concepts/explore-resources.md)
- Se exempel på [startfrågor](../samples/starter.md)
- Se exempel på [avancerade frågor](../samples/advanced.md)
- Ge feedback på [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)