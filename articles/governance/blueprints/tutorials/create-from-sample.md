---
title: Skapa en miljö från ett exempel på skiss
description: Använd en skiss-exempel för att skapa skissdefinitionen som ställer in två resursgrupper och konfigurerar en rolltilldelning för var och en.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4f400e45d8defc304cf58c4bd05fa19f16d0501b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277780"
---
# <a name="create-an-environment-from-a-blueprint-sample"></a>Skapa en miljö från ett exempel på skiss

Exemplet skisser innehåller exempel på vad du kan göra med hjälp av Azure skisser. Var och en är ett exempel med ett specifikt syfte eller syfte, men skapa inte en fullständig miljö med själva. Var och en är avsedd som utgångspunkt för att börja använda Azure skisser med olika kombinationer av inkluderade artefakter, utformning och parametrar.

I följande självstudiekursen använder vi den **resursgrupper med RBAC** skissen exemplet för att demonstrera olika aspekter av tjänsten skisser. Följande steg beskrivs:

> [!div class="checklist"]
> - Skapa en ny skissdefinitionen från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration
> - Inspektera distribuerade resurser för tilldelningen
> - Ta bort tilldelning av skissen att ta bort låsen

## <a name="prerequisites"></a>Förutsättningar

Den här kursen krävs en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-blueprint-definition-from-sample"></a>Skapa skissdefinitionen från exempel

Först måste implementera skissen exemplet. Importera skapar en ny skiss i din miljö, baserat på exemplet.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från den **komma igång** sidan till vänster, Välj den **skapa** knappen _skapa en skiss_.

1. Hitta den **resursgrupper med RBAC** skissen exemplet under _andra exempel_ och välj **använda det här exemplet**.

1. Ange den _grunderna_ av skiss exemplet:

   - **Namn på skiss**: Ange ett namn för din kopia av exemplet skissen. Den här självstudien använder vi namnet _två-rgs-med--rolltilldelningar_.
   - **Definitionens plats**: Använd de tre punkterna och välj hanteringsgruppen eller prenumerationen för att spara din kopia av exemplet för att.

1. Välj den _artefakter_ fliken högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissen exemplet. Det här exemplet definierar två resursgrupper med visningsnamnen för _ProdRG_ och _PreProdRG_. Sista namnet och platsen för varje resursgrupp anges under skisstilldelningen. Den _ProdRG_ resursgrupp tilldelas den _deltagare_ roll och _PreProdRG_ resursgrupp tilldelas den _ägare_ och  _Läsare_ roller. De roller som tilldelats i definitionen är statiska, men användare, app eller grupp som tilldelas rollen som anges under skisstilldelningen.

1. Välj **spara utkast** när du har granskat skissen exemplet.

Det här steget skapar en kopia av skissdefinition exemplet i den valda hanteringsgruppen eller prenumerationen. Den sparade skissdefinitionen hanteras som alla skiss som skapats från grunden. Du kan spara exemplet till hanteringsgruppen eller prenumerationen så många gånger som behövs. Varje kopia måste dock tillhandahålla ett unikt namn.

När den **sparar skissdefinitionen har tagits** portal-meddelande visas, gå vidare till nästa steg.

## <a name="publish-the-sample-copy"></a>Publicera exempel-kopia

Din kopia av exemplet skissen har skapats i din miljö. Den skapas i **Draft** läge och måste vara **publicerad** innan den kan tilldelas och distribueras. Kopia av exemplet skissen kan anpassas till din miljö och behov. Vi kommer inte göra några ändringar i den här självstudien.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta den _två-rgs-med--rolltilldelningar_ skiss definition och markera den.

1. Välj **publicera skiss** överst på sidan. I den nya rutan till höger anger **Version** som _1.0_ för din kopia av exemplet skissen. Den här egenskapen är användbar för om du gör en ändring av senare. Ange **ändra anteckningar** som ”första versionen publicerade från resursgrupper med RBAC skissen exempel”. Välj sedan **publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När publicerats kan fortfarande ändras. Ytterligare ändringar måste publiceras med en ny **Version** värde att spåra skillnaderna mellan olika versioner av samma skissdefinitionen.

När den **publicering skiss definition lyckades** portal-meddelande visas, gå vidare till nästa steg.

## <a name="assign-the-sample-copy"></a>Tilldela exempel-kopia

När kopian av exemplet skissen har installerats utan **publicerad**, så kan de tilldelas en prenumeration inom den sparades till hanteringsgruppen. Det här steget är där parametrar har angetts så att varje distribution av kopian av exemplet skissen unikt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta den _två-rgs-med--rolltilldelningar_ skiss definition och markera den.

1. Välj **tilldela skissen** överst på sidan för skissen definition.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som är i hanteringsgruppen som du sparade din kopia av exemplet skissen till. Om du väljer mer än en prenumeration skapas en tilldelning för att använda de parametrar som anges.
     - **Tilldelningsnamn**: Namnet är förifyllda baserat på namnet på skissdefinitionen.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       Den här självstudien väljer _östra USA 2_.
     - **Definitionsversion för skissen**: Välj den **publicerad** version _1.0_ för ditt exemplar av skissdefinition exemplet.

   - Lås tilldelning

     Välj den _skrivskyddad_ skissen lock-läge. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - Hanterad identitet

     Låt standardvärdet _systemtilldelad_ alternativet. Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Artefaktparametrar

     De parametrar som definierats i det här avsnittet gäller för artefakten som den definieras. De här parametrarna kan [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definierades vid tilldelning av skissen. Ange parametervärdet för varje artefakt enligt definitionen i den **värdet** kolumn. För `{Your ID}`, Välj ditt Azure-konto.

     |Namn på artefakt|Artefakttyp|Parameternamn|Värde|Beskrivning|
     |-|-|-|-|-|
     |ProdRG resursgrupp|Resursgrupp|Namn|ProductionRG|Definierar namnet på den första resursgruppen.|
     |ProdRG resursgrupp|Resursgrupp|Plats|Västra USA 2|Anger platsen för den första resursgruppen.|
     |Deltagare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilka användare eller grupp för att bevilja den _deltagare_ rolltilldelning i den första resursgruppen.|
     |PreProdRG resursgrupp|Resursgrupp|Namn|PreProductionRG|Definierar namnet på resursgruppen som andra.|
     |PreProdRG resursgrupp|Resursgrupp|Plats|Västra USA|Anger platsen för andra resursgruppen.|
     |Ägare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilka användare eller grupp för att bevilja den _ägare_ rolltilldelning i andra resursgruppen.|
     |Läsare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilka användare eller grupp för att bevilja den _läsare_ rolltilldelning i andra resursgruppen.|

1. När alla parametrar har angetts, väljer **tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **Lås tilldelning**. Skissen Lås kan ta upp till 30 minuter att tillämpa.

När den **tilldela skissdefinitionen har tagits** portal-meddelande visas, gå vidare till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Kontrollera resurser som distribueras av tilldelningen

Skisstilldelningen skapar och spårar artefakter som definierats i skissdefinitionen för. Vi kan se statusen för resurserna från sidan för tilldelning av skissen och genom att titta på resurserna direkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **tilldelade skisser** sidan till vänster. Använd filtren för att hitta den _Assignment-two-rgs-with-role-assignments_ skiss tilldelning och markera den.

   Vi kan se tilldelningen lyckades och en lista över skapade resurserna tillsammans med deras skissen låser upp från den här sidan. Om tilldelningen har uppdaterats kan den **tilldelningsåtgärd** listrutan visar information om distributionen av varje definitionsversion. Varje listad resurs som har skapats kan du klicka på och öppnar den egenskapssidan för resurser.

1. Välj den **ProductionRG** resursgrupp.

   Vi ser att namnet på resursgruppen är **ProductionRG** och inte visningsnamnet artefakt _ProdRG_. Det här namnet matchar det värde som anges under skisstilldelningen.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster och sedan den **rolltilldelningar** fliken.

   Här ser vi att ditt konto har beviljats den _deltagare_ roll på omfattning _den här resursen_. Den _Assignment-two-rgs-with-role-assignments_ skisstilldelningen har den _ägare_ rollen eftersom den används för att skapa resursgruppen. De här behörigheterna används också för att hantera resurser med konfigurerade skissen Lås.

1. Azure portal länken Välj **Assignment-two-rgs-with-role-assignments** om du vill gå tillbaka en sida, Välj den **PreProductionRG** resursgrupp.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster och sedan den **rolltilldelningar** fliken.

   Här ser vi att ditt konto har beviljats både den _ägare_ och _läsare_ roller, både på omfånget för _den här resursen_. Skisstilldelningen har också den _ägare_ rollen som den första resursgruppen.

1. Välj den **neka tilldelningar** fliken.

   Skisstilldelningen skapas en [neka tilldelning](../../../role-based-access-control/deny-assignments.md) på den distribuerade resursgruppen för att tvinga den _skrivskyddad_ skissen lock-läge. Neka tilldelningen förhindrar att någon med rätt behörighet på den _rolltilldelningar_ fliken från att utföra specifika åtgärder. Neka tilldelningen påverkar _alla huvudkonton_.

1. Välj neka tilldelningen och välj sedan den **nekad behörighet** sidan till vänster.

   Neka tilldelningen förhindrar alla åtgärder med den **\*** och **åtgärd** konfiguration, men tillåter läsbehörighet genom att exkludera  **\* /läsa**via **NotActions**.

1. Azure portal länken Välj **PreProductionRG - åtkomstkontroll (IAM)**. Välj sedan den **översikt** sidan till vänster och sedan den **ta bort resursgrupp** knappen. Ange namnet _PreProductionRG_ att bekräfta ta bort och välj **ta bort** längst ned i fönstret.

   Portal-meddelande **ta bort resursgrupp PreProductionRG misslyckades** visas. Felet anger att även om ditt konto har behörighet att ta bort resursgruppen, nekas åtkomst av skisstilldelningen. Kom ihåg att vi har valt den _skrivskyddad_ skissen låsläge under skisstilldelningen. Skissen låset förhindrar att ett konto med behörighet, även _ägare_, tar bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

De här stegen visar att våra resurser har skapats enligt definitionen och skissen låsen hindrade oönskad borttagning, även från ett konto med behörighet.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelning av skissen

Det sista steget är att ta bort tilldelning av skissen och resurser som den har distribuerats.
Ta bort tilldelningen tas inte bort distribuerade artefakter.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj den **tilldelade skisser** sidan till vänster. Använd filtren för att hitta den _Assignment-two-rgs-with-role-assignments_ skiss tilldelning och markera den.

1. Välj den **otilldelad skissen** längst upp på sidan. Läs varningen i bekräftelsedialogrutan och välj sedan **OK**.

   Med skisstilldelningen har tagits bort, tas skissen låsen också bort. De skapade resurserna kan återigen tas bort av ett konto med behörighet.

1. Välj **resursgrupper** från Azure-menyn, Välj **ProductionRG**.

1. Välj den **åtkomstkontroll (IAM)** sidan till vänster och sedan den **rolltilldelningar** fliken.

Säkerhet för alla resursgrupper har fortfarande distribuerade rolltilldelningarna, men skisstilldelningen har inte längre _ägare_ åtkomst.

När den **ta bort skisstilldelningen har tagits** portal-meddelande visas, gå vidare till nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort följande resurser:

- Resursgrupp _ProductionRG_
- Resursgrupp _PreProductionRG_
- Skiss definition _två-rgs-med--rolltilldelningar_

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](../concepts/lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md)
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)