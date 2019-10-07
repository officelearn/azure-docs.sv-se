---
title: Skapa en miljö från ett skiss exempel
description: Använd ett skiss exempel för att skapa en skiss definition som konfigurerar två resurs grupper och konfigurerar en roll tilldelning för var och en.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
ms.openlocfilehash: f3250052a7e26b5d8ef7cb1d411f9d9252089875
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980728"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Självstudier: Skapa en miljö från ett skiss exempel

Exempel ritningar innehåller exempel på vad du kan göra med Azure-ritningar. Var och en är ett exempel med ett specifikt syfte eller syfte, men skapar inte en fullständig miljö själva. Var och en är avsedd som en start plats för att utforska användningen av Azure-ritningar med olika kombinationer av artefakter, konstruktioner och parametrar som ingår.

I följande självstudie används **resurs grupper med RBAC** -videoexempel för att visa olika aspekter av ritnings tjänsten. Följande steg beskrivs:

> [!div class="checklist"]
> - Skapa en ny skiss definition från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration
> - Granska distribuerade resurser för tilldelningen
> - Ta bort tilldelningen för att ta bort låsen

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien krävs en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-blueprint-definition-from-sample"></a>Skapa skiss definition från exempel

Börja med att implementera skiss exemplet. När du importerar skapas en ny skiss i din miljö baserat på exemplet.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Leta upp **resurs grupperna med RBAC** -skiss under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet. I den här självstudien använder vi namnet _två-RGS-with-Role-tilldelningar_.
   - **Definitions plats**: Använd ellipsen och välj hanterings gruppen eller prenumerationen för att spara din kopia av exemplet till.

1. Välj fliken _artefakter_ överst på sidan eller **Next: Artefakter @ no__t-0 längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Det här exemplet definierar två resurs grupper med visnings namn för _ProdRG_ och _PreProdRG_. Det slutliga namnet och platsen för varje resurs grupp anges under skiss tilldelningen. Resurs gruppen _ProdRG_ tilldelas rollen _deltagare_ och _PreProdRG_ resurs gruppen tilldelas rollen _ägare_ och _läsare_ . Rollerna som tilldelas i definitionen är statiska, men användaren, appen eller gruppen som tilldelas rollen anges under skiss tilldelningen.

1. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

Det här steget skapar en kopia av exempel ritnings definitionen i den valda hanterings gruppen eller prenumerationen. Den sparade skiss definitionen hanteras som alla skisser som skapats från grunden. Du kan spara exemplet i hanterings gruppen eller prenumerationen så många gånger som det behövs. Varje kopia måste dock anges med ett unikt namn.

När du har skickat ett meddelande om att **skiss definitionen lyckades** , går du vidare till nästa steg.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov. I den här självstudien kommer vi inte att göra några ändringar.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta skiss definitionen för _två-RGS-med-roll-tilldelningar_ och välj sedan den.

1. Välj **publicera skiss** överst på sidan. I det nya fönstret till höger anger du **version** som _1,0_ för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från resurs grupperna med RBAC-skiss." Välj sedan **publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När den har publicerats kan du fortfarande göra ändringar. Ytterligare ändringar kräver publicering med ett nytt **versions** värde för att spåra skillnader mellan olika versioner av samma skiss definition.

När du **har slutfört ett Portal meddelande om publicerings skissen** visas går du vidare till nästa steg.

## <a name="assign-the-sample-copy"></a>Tilldela exempel kopian

När kopieringen av skiss exemplet har **publicerats**kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. Det här steget är den plats där parametrar tillhandahålls för att göra varje distribution av kopian av skiss exemplet unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta skiss definitionen för _två-RGS-med-roll-tilldelningar_ och välj sedan den.

1. Välj **tilldela skiss** överst på skiss definitions sidan.

1. Ange parameter värden för skiss tilldelningen:

   - Grundinställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer fler än en prenumeration skapas en tilldelning för varje användning av de angivna parametrarna.
     - **Tilldelnings namn**: Namnet fylls i automatiskt för dig baserat på namnet på skiss definitionen.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       I den här självstudien väljer du _USA, östra 2_.
     - **Ritnings definitions version**: Välj den **publicerade** versionen _1,0_ av din kopia av exempel ritnings definitionen.

   - Lås tilldelning

     Välj Lås läget _skrivskyddad_ skiss. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna alternativet standard _tilldelat system_ . Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. Ange parametervärdet till vad som definieras i kolumnen **värde** för varje artefakt. För `{Your ID}` väljer du ditt Azure-användarkonto.

     |Namn på artefakt|Artefakttyp|Parameternamn|Value|Beskrivning|
     |-|-|-|-|-|
     |Resurs grupp för ProdRG|Resource group|Name|ProductionRG|Definierar namnet på den första resurs gruppen.|
     |Resurs grupp för ProdRG|Resource group|Location|Västra USA 2|Anger platsen för den första resurs gruppen.|
     |Deltagare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilken användare eller grupp som ska bevilja _deltagar_ roll tilldelningen i den första resurs gruppen.|
     |Resurs grupp för PreProdRG|Resource group|Name|PreProductionRG|Definierar namnet på den andra resurs gruppen.|
     |Resurs grupp för PreProdRG|Resource group|Location|Västra USA|Anger platsen för den andra resurs gruppen.|
     |Ägare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilken användare eller grupp som ska bevilja _ägar_ roll tilldelningen i den andra resurs gruppen.|
     |Läsare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilken användare eller grupp som ska ge roll tilldelningen _läsare_ i den andra resurs gruppen.|

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **Lås tilldelningen**. Skiss låsen kan ta upp till 30 minuter att tillämpa.

När du har skickat ett meddelande om att **skiss definitionen lyckades** , går du vidare till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspektera resurser som distribueras av tilldelningen

Skiss tilldelningen skapar och spårar de artefakter som definieras i skiss definitionen. Vi kan se statusen för resurserna på sidan skiss tilldelning och genom att titta på resurserna direkt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **tilldelade ritningar** till vänster. Använd filtren för att hitta _tilldelningen-två-RGS-med-roll tilldelningar_ och välj sedan den.

   På den här sidan kan vi se att tilldelningen lyckades och listan över skapade resurser tillsammans med deras skiss lås status. Om tilldelningen uppdateras visas information om distributionen av varje definitions version i list rutan **tilldelnings åtgärd** . Varje resurs i listan som du har skapat kan klicka på och öppna egenskaps sidan för resurser.

1. Välj resurs gruppen **ProductionRG** .

   Vi ser att namnet på resurs gruppen är **ProductionRG** och inte _ProdRG_för artefakt visnings namnet. Det här namnet matchar värdet som anges under skiss tilldelningen.

1. Välj sidan **åtkomst kontroll (IAM)** till vänster och sedan fliken **roll tilldelningar** .

   Här ser vi att ditt konto har beviljats _deltagar_ rollen för _resursens_omfattning. Skiss tilldelningen _tilldelning – två-RGS-med-roll tilldelningar_ har _ägar_ rollen som den användes för att skapa resurs gruppen. Dessa behörigheter används också för att hantera resurser med konfigurerade skiss lås.

1. Från Azure Portal dynamiska objekt väljer du **tilldelning-två-RGS – med-roll tilldelningar** för att gå tillbaka en sida och sedan välja resurs gruppen **PreProductionRG** .

1. Välj sidan **åtkomst kontroll (IAM)** till vänster och sedan fliken **roll tilldelningar** .

   Här ser vi att ditt konto har beviljats både _ägar_ -och _läsar_ rollerna, både i den _här resursens_omfattning. Skiss tilldelningen har också _ägar_ rollen som den första resurs gruppen.

1. Välj fliken **neka tilldelningar** .

   Skiss tilldelningen skapade en [neka-tilldelning](../../../role-based-access-control/deny-assignments.md) på den distribuerade resurs gruppen för att tvinga det _skrivskyddade_ utkast låset. Neka-tilldelningen hindrar någon med lämpliga rättigheter på fliken _roll tilldelningar_ från att vidta vissa åtgärder. Neka-tilldelningen påverkar _alla huvud konton_.

1. Välj neka tilldelning och välj sedan sidan **nekade behörigheter** till vänster.

   Neka-tilldelningen förhindrar alla åtgärder med **\*-och-** **Åtgärds** konfigurationen, men tillåter Läs åtkomst genom att utesluta **\*/läsning** via **NotActions**.

1. Från Azure Portal dynamiska länkar väljer du **PreProductionRG-Access Control (IAM)** . Välj sedan sidan **Översikt** till vänster och sedan knappen **ta bort resurs grupp** . Ange namnet _PreProductionRG_ för att bekräfta borttagningen och välj **ta bort** längst ned i fönstret.

   Portal meddelandet det **gick inte att ta bort resurs gruppen PreProductionRG** visas. Felet anger att när ditt konto har behörighet att ta bort resurs gruppen nekas åtkomst av skiss tilldelningen. Kom ihåg att vi valde lås läget _skrivskyddad_ skiss under skiss tilldelningen. Skiss låset förhindrar ett konto med behörighet, till och med _ägare_, från att ta bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

De här stegen visar att våra resurser har skapats som definierade och att skisserna låser sig förhindra oönskad borttagning, även från ett konto med behörighet.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelningen av skissen

Det sista steget är att ta bort tilldelningen av skissen och de resurser som den har distribuerat.
Om du tar bort tilldelningen tas inte de distribuerade artefakterna bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **tilldelade ritningar** till vänster. Använd filtren för att hitta _tilldelningen-två-RGS-med-roll tilldelningar_ och välj sedan den.

1. Välj knappen **ta bort tilldelning av skiss** överst på sidan. Läs varningen i bekräftelse dialog rutan och välj **OK**.

   När skiss tilldelningen har tagits bort tas även Skissernas lås bort. De skapade resurserna kan återigen tas bort av ett konto med behörighet.

1. Välj **resurs grupper** på Azure-menyn och välj sedan **ProductionRG**.

1. Välj sidan **åtkomst kontroll (IAM)** till vänster och sedan fliken **roll tilldelningar** .

Säkerheten för varje resurs grupp har fortfarande de distribuerade roll tilldelningarna, men skiss tilldelningen har inte längre _ägar_ åtkomst.

När aviseringen om att **ta bort skiss tilldelningen** visas går du vidare till nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här självstudien tar du bort följande resurser:

- _ProductionRG_ för resurs grupp
- _PreProductionRG_ för resurs grupp
- Skiss definition _två-RGS – med-roll-tilldelningar_

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [skiss livs cykeln](../concepts/lifecycle.md)
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md)
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md)
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md)
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md)
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md)