---
title: 'Självstudiekurs: Hantera frågor i Azure Portal'
description: I den här självstudien skapar du en Resource Graph Query och delar den nya frågan med andra i Azure-portalen.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74303959"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Självstudiekurs: Skapa och dela en Azure Resource Graph-fråga i Azure-portalen

Med Azure Resource Graph Explorer kan du spara dina Resource Graph-frågor direkt i Azure-portalen. Det finns två typer av frågor: _Privat_ och _Delad_. En privat fråga sparas i inställningarna för Azure-portalen. Medan en delad fråga är en Resource Manager-resurs som kan hanteras med rollbaserade åtkomstkontroller (RBAC) och skyddas med resurslås. Båda typerna av frågor krypteras i vila.

Genom att spara frågor i Azure-portalen sparar du den tid du annars kan ägna åt att leta efter dina favorit- eller vanliga frågor. När du delar frågor hjälper du ditt team att förverkliga mål om konsekvens och effektivitet genom upprepning.

I den här självstudien ska du utföra följande uppgifter:

> [!div class="checklist"]
> - Skapa och ta bort en privat fråga
> - Skapa en delad fråga
> - Upptäck delade frågor
> - Ta bort en delad fråga

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-and-delete-a-private-query"></a>Skapa och ta bort en privat fråga

Privata frågor är endast tillgängliga och synliga för kontot som skapar dem. När de sparas i ett kontos Azure-portalinställningar kan de skapas, användas och tas bort endast inifrån Azure-portalen. En privat fråga är inte en Resource Manager-resurs. Så här skapar du en ny privat fråga:

1. På portalmenyn väljer du **Alla tjänster** eller använder sökrutan i Azure högst upp på alla sidor. Sök efter och välj sedan **Resource Graph Explorer**.

1. På fliken **Fråga 1** på sidan Utforskaren i Azure Resource Graph anger du följande fråga:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Välj **Kör fråga** om du vill visa frågeresultaten i det nedre fönstret.

   Mer information om den här frågan finns i [Exempel – Räkna virtuella datorer efter OS-typ](../samples/starter.md#count-virtual-machines-by-os-type).


1. Välj **Spara** eller **Spara som**, ange Antal virtuella datorer efter **operativsystemet** som namn, lämna typen som **privat fråga**och välj sedan **Spara** längst ned i **frågefönstret Spara.** Tabbrubriken ändras från **fråga 1** till **Antal virtuella datorer efter operativsystem**.

1. Flytta dig bort från Azure Resource Graph Explorer i Azure-portalen och sedan återgå till den. Observera att den sparade frågan inte längre visas och fliken **Fråga 1** har returnerats.

1. Välj **Öppna en fråga**. Kontrollera att typen är **Privat fråga**. Det sparade namnet **Antal virtuella datorer efter operativsystem** visas nu i listan **Frågenamn.** När du väljer rubriklänken för den sparade frågan läses den in på en ny flik med frågans namn.

   > [!NOTE] 
   > När en sparad fråga är öppen och fliken visar dess namn uppdateras den med alla ändringar som har gjorts om du väljer knappen **Spara.** Om du vill skapa en ny sparad fråga från den här öppna frågan väljer du **Spara som** och fortsätter som om du sparade en helt ny fråga.

1. Om du vill ta bort den sparade frågan väljer du **Öppna en fråga** igen och kontrollerar att fältet **Typ** är inställt på **Privat fråga**. På raden med `Count VMs by OS` den sparade frågan väljer du **Ta bort** (ikonen Papperskorgen). Välj **Ja** för att slutföra borttagningen av frågan i bekräftelsedialogrutan.
   Stäng sedan **ett frågefönster.**

## <a name="create-a-shared-query"></a>Skapa en delad fråga

Till skillnad från en privat fråga är en delad fråga en Resource Manager-resurs. Detta innebär att frågan sparas i en resursgrupp, kan hanteras och kontrolleras med RBAC och kan även skyddas med resurslås. Som en resurs kan alla som har rätt behörighet se och använda den.
Så här skapar du en ny delad fråga:

1. På portalmenyn väljer du **Alla tjänster**eller använder sökrutan i Azure högst upp på alla sidor för att söka efter och välj Resource **Graph Explorer**.

1. På fliken **Fråga 1** på sidan Utforskaren i Azure Resource Graph anger du följande fråga:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Välj **Kör fråga** om du vill visa frågeresultaten i det nedre fönstret.

   Mer information om den här frågan finns i [Exempel – Räkna virtuella datorer efter OS-typ](../samples/starter.md#count-virtual-machines-by-os-type).

1. Välj **Spara** eller **Spara som**.

   
   ![Spara den nya frågan med hjälp av spara-knappen](../media/create-share-query/save-shared-query-buttons.png)

1. I **fönstret Spara fråga** anger du **Antal virtuella datorer efter operativsystem** för namnet.

1. Ändra typen till **Delad fråga,** ange beskrivningen till **Antal virtuella datorer efter OS-typ**och ange **Prenumeration** för att ange var frågeresursen skapas.

1. Lämna **resursgruppen Publicera till resursdiagramfrågor** markerad och **plats för resursgrupp** inställd på **(USA) Västra centrala USA**.

1. Välj **Spara** längst ned i **fönstret Spara fråga.** Tabbrubriken ändras från **fråga 1** till **Antal virtuella datorer efter operativsystem**. Första gången resursgruppen **resursgrupper för resursdiagramfrågor** används tar det längre tid än förväntat att spara när resursgruppen skapas.
   
   ![Spara den nya frågan som en delad fråga](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Du kan avmarkera **resursgruppen Publicera till resursdiagramfrågor** om du vill ange namnet på en befintlig resursgrupp som du vill spara den delade frågan i. Om du använder standardnamnet resursgrupp för frågor blir delade frågor lättare att identifiera. Det gör också syftet med denna resursgrupp tydligare. Du kan dock välja att välja en befintlig resursgrupp av säkerhetsskäl baserat på befintliga behörigheter.

1. Flytta dig bort från Azure Resource Graph Explorer i Azure-portalen och sedan återgå till den. Observera att den sparade frågan inte längre visas och fliken **Fråga 1** har returnerats.

1. Välj **Öppna en fråga**. Kontrollera att typen är inställd på **Delad fråga** och kombinationen av **prenumerations-** och **resursgruppmatchning** där du sparade frågan. Det sparade **objektet Antal virtuella datorer per operativsystem** visas nu i listan **Frågenamn.** Välj rubriklänken för den sparade frågan om du vill läsa in den på en ny flik med frågans namn. Som en delad fråga visas en ikon på fliken bredvid titeln och den betecknas som delad.

   ![Visa ikonen Delad fråga med](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > När en sparad fråga är öppen och fliken visar dess namn **uppdateras** den med alla ändringar som har gjorts. Om du vill skapa en ny sparad fråga väljer du **Spara som** och fortsätter som om du sparade en helt ny fråga.

## <a name="discover-shared-queries"></a>Upptäck delade frågor

Eftersom en delad fråga är en Resource Manager-resurs finns det flera sätt att hitta en:

- I Resource Graph Explorer väljer du **Öppna en fråga** och anger typen till Delad **fråga**.
- Från portalsidan för Resource Graph-frågor.
- Från resursgruppen som den delade frågan sparades i.
- Genom en fråga till Resource Graph.

### <a name="view-resource-graph-queries"></a>Visa resursdiagramfrågor

På Azure-portalen visar sidan Resource Graph-frågor Delade frågor som det inloggade kontot har åtkomst till. På den här sidan kan du filtrera efter namn, prenumeration, resursgrupp och andra egenskaper i resursdiagramfrågan. Du kan också tagga, exportera och ta bort Resource Graph-frågor med hjälp av det här gränssnittet.

Om du väljer en av frågorna öppnas frågesidan för Resursdiagram. Precis som andra Resource Manager-resurser erbjuder den här sidan en interaktiv översikt tillsammans med aktivitetsloggen, åtkomstkontrollen och taggarna. Du kan också använda ett resurslås direkt från den här sidan.

Gå till sidan Resource Graph-frågor på portalmenyn genom att välja **Alla tjänster** eller genom att använda sökrutan i Azure högst upp på alla sidor. Sök efter och välj **Resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Ange resursgrupper för lista

Resursdiagramfrågan visas tillsammans med andra resurser som ingår i en resursgrupp.
Om du väljer frågan Resursdiagram öppnas sidan för den frågan. Ellips- och snabbmenyalternativen (utlöses av högerklickning) fungerar på samma sätt som på frågesidan för Resursdiagram.

### <a name="query-resource-graph"></a>Frågeresursdiagram

Du kan hitta Resource Graph-frågor via en fråga till Resursdiagram. Följande resursdiagram fråga gränser `Microsoft.ResourceGraph/queries`efter typ `project` och sedan används för att lista endast namn, tid ändras och frågan själv:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Ta bort en delad fråga

Om en delad fråga inte längre behövs tar du bort den. Genom att ta bort en delad fråga tar du bort motsvarande Resource Manager-resurs. Alla instrumentpaneler som resultatdiagrammet har fästs på visar nu ett felmeddelande. När felmeddelandet visas använder du knappen **Ta bort från instrumentpanelen** för att rensa instrumentpanelen.

Du kan ta bort en delad fråga via följande gränssnitt:
- Sidan Resursdiagramfrågor
- Frågesida för resursdiagram
- Sidan **Öppna en fråga** i Utforskaren För resursdiagram
- Sidan Resursgrupper

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort de privata och delade frågor som du har skapat om du inte längre vill ha dem.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat privata och delade frågor. Om du vill veta mer om resursdiagramspråket fortsätter du till sidan med frågespråkinformation.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](../concepts/query-language.md)