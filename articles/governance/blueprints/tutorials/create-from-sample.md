---
title: 'Självstudiekurs: Skissexempel till ny miljö'
description: I den här självstudien använder du ett skissexempel för att skapa en skissdefinition som ställer in två resursgrupper och konfigurerar en rolltilldelning för varje.
ms.date: 03/25/2020
ms.topic: tutorial
ms.openlocfilehash: c4230282223b0a64f6254448fe069bf8f7ab9a15
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80282026"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Självstudiekurs: Skapa en miljö från ett skissexempel

Exempel på ritningar ger exempel på vad som kan göras med Azure Blueprints. Var och en är ett exempel med en specifik avsikt eller ett visst syfte, men skapar inte en fullständig miljö av sig själva. Var och en är avsedd som en startplats för att utforska med hjälp av Azure Blueprints med olika kombinationer av inkluderade artefakter, designer och parametrar.

Följande självstudiekurs använder **resursgrupper med RBAC-skissexempel** för att visa upp olika aspekter av blueprints-tjänsten. Följande steg beskrivs:

> [!div class="checklist"]
> - Skapa en ny skissdefinition från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration
> - Kontrollera distribuerade resurser för tilldelningen
> - Ta bort tilldelning av skissen för att ta bort låsen

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behövs en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-blueprint-definition-from-sample"></a>Skapa skissdefinition från exempel

Implementera först skissprovet. Om du importerar skapas en ny skiss i din miljö baserat på exemplet.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Leta reda på **resursgrupper med RBAC-skissexempel** under Andra _exempel_ och markera det.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn:** Ange ett namn för ditt exemplar av skissexemplet. För den här självstudien använder vi namnet _två-rgs-med-roll-tilldelningar_.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen eller prenumerationen för att spara kopian av exemplet till.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Det här exemplet definierar två resursgrupper med visningsnamn för _ProdRG_ och _PreProdRG_. Det slutliga namnet och platsen för varje resursgrupp anges under skisstilldelningen. _ProdRG-resursgruppen_ tilldelas rollen _Deltagare_ och _resursgruppen PreProdRG_ tilldelas rollerna _Ägare_ och _Läsare._ Rollerna som tilldelas i definitionen är statiska, men användare, app eller grupp som har tilldelats rollen anges under skisstilldelningen.

1. Välj **Spara utkast** när du har granskat skissexemplet.

I det här steget skapas en kopia av exempelritningsdefinitionen i den valda hanteringsgruppen eller prenumerationen. Den sparade skissdefinitionen hanteras som vilken skiss som helst som skapas från grunden. Du kan spara exemplet i din hanteringsgrupp eller prenumeration så många gånger som behövs. Varje kopia måste dock anges som ett unikt namn.

När spara **skissdefinitionen har lyckats** visas portalmeddelandet går du vidare till nästa steg.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissprovet kan anpassas till din miljö och dina behov. För den här självstudien kommer vi inte att göra några ändringar.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta skissdefinitionen _med två rgs-with-role-assignments_ och välj den sedan.

1. Välj **Publicera skiss** högst upp på sidan. I den nya rutan till höger anger du **Version** som _1.0_ för din kopia av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar** som "Första versionen som publiceras från resursgrupperna med rbac-skissexempel". Välj sedan **Publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När ändringarna har publicerats kan du fortfarande göra ändringar. Ytterligare ändringar kräver publicering med ett nytt **versionsvärde** för att spåra skillnader mellan olika versioner av samma skissdefinition.

När **portalmeddelandet för publiceringsritning har lyckats** gå du vidare till nästa steg.

## <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopian av skissexemplet har **publicerats**kan den tilldelas en prenumeration inom den hanteringsgrupp som den sparades till. Det här steget är där parametrar tillhandahålls för att göra varje distribution av kopian av skissexemplet unikt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta skissdefinitionen _med två rgs-with-role-assignments_ och välj den sedan.

1. Välj **Tilldela skiss** högst upp på skissdefinitionssidan.

1. Ange parametervärdena för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av prenumerationerna i hanteringsgruppen som du sparade din kopia av skissexemplet till. Om du väljer mer än en prenumeration skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelningsnamn**: Namnet är förifyllt för dig baserat på namnet på skissdefinitionen.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       För den här självstudien väljer du _Östra USA 2_.
     - **Blueprint definition version**: Välj **den publicerade** versionen _1.0_ av ditt exemplar av exempel skiss definition.

   - Lås tilldelning

     Välj _skrivskyddat_ skisslåsläge. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna standardalternativet _Systemtilldelad._ Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som det definieras under. Dessa parametrar är [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de har definierats under tilldelningen av skissen. För varje artefakt anger du parametervärdet till det som definieras i kolumnen **Värde.** För `{Your ID}`väljer du ditt Azure-användarkonto.

     |Artefaktnamn|Artefakttyp|Parameternamn|Värde|Beskrivning|
     |-|-|-|-|-|
     |Resursgrupp för ProdRG|Resursgrupp|Namn|ProduktionRG|Definierar namnet på den första resursgruppen.|
     |Resursgrupp för ProdRG|Resursgrupp|Location|USA, västra 2|Anger platsen för den första resursgruppen.|
     |Deltagare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilken användare eller grupp som ska beviljas _deltagarrolltilldelningen_ inom den första resursgruppen.|
     |Resursgrupp för PreProdRG|Resursgrupp|Namn|FörproduktionRG|Definierar namnet på den andra resursgruppen.|
     |Resursgrupp för PreProdRG|Resursgrupp|Location|USA, västra|Anger platsen för den andra resursgruppen.|
     |Ägare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilken användare eller grupp som ska beviljas _ägarrolltilldelningen_ inom den andra resursgruppen.|
     |Läsare|Rolltilldelning|Användare eller grupp|{Ditt ID}|Definierar vilken användare eller grupp som ska beviljas _rolltilldelningen Läsare_ inom den andra resursgruppen.|

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **låstilldelningen**. Skisslås kan ta upp till 30 minuter att tillämpa.

När **portalmeddelandet Tilldela skissdefinitionen har slutförts** går du vidare till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspektera resurser som distribueras av tilldelningen

Skisstilldelningen skapar och spårar de artefakter som definieras i skissdefinitionen. Vi kan se status för resurserna från skisstilldelningssidan och genom att titta direkt på resurserna.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Tilldelade ritningar** till vänster. Använd filtren för att hitta skisstilldelningen tilldelning av _tilldelningar av tilldelningar av tilldelningar av tilldelningar av tilldelningar_ av tilldelningar av tilldelningar och välj den sedan.

   Från den här sidan kan vi se tilldelningen lyckades och listan över skapade resurser tillsammans med deras skisslåstillstånd. Om tilldelningen uppdateras visar listrutan **Tilldelningsåtgärd** information om distributionen av varje definitionsversion. Varje listad resurs som har skapats kan klickas och öppnar egenskapssidan för resurser.

1. Välj resursgruppen **ProductionRG.**

   Vi ser att namnet på resursgruppen är **ProductionRG** och inte artefaktens _visningsnamn ProdRG_. Det här namnet matchar värdet som angetts under skisstilldelningen.

1. Välj sidan **Access control (IAM)** till vänster och sedan fliken **Rolltilldelningar.**

   Här ser vi att ditt konto har tilldelats rollen _Deltagare_ i omfattningen av den _här resursen_. Skisstilldelningen _För tilldelning-två-rgs-med-roll-tilldelningar_ har rollen _Ägare_ som den användes för att skapa resursgruppen. Dessa behörigheter används också för att hantera resurser med konfigurerade skisslås.

1. Välj **Tilldelning-två-rgs-med-roll-tilldelningar** i Azure-portalens brödsmulor för att gå tillbaka till en sida och välj sedan resursgruppen **PreProductionRG.**

1. Välj sidan **Access control (IAM)** till vänster och sedan fliken **Rolltilldelningar.**

   Här ser vi att ditt konto har beviljats både _ägar-_ och _läsarrollerna,_ både på _resursens_omfattning . Skisstilldelningen har också rollen _Ägare_ som den första resursgruppen.

1. Välj fliken **Neka tilldelningar.**

   Skisstilldelningen skapade en [neka tilldelning](../../../role-based-access-control/deny-assignments.md) för den distribuerade resursgruppen för att framtvinga skisslåsläget _skrivskydd._ Neka tilldelning förhindrar att någon med lämpliga rättigheter på fliken _Rolltilldelningar_ vidtar specifika åtgärder. Neka-tilldelningen påverkar _Alla huvudnamn_.

1. Markera neka tilldelningen och välj sedan sidan **Nekad behörighet till** vänster.

   Neka tilldelningen förhindrar alla åtgärder **\*** med konfigurationen och **Åtgärd,** men tillåter läsåtkomst genom att utesluta ** \*/read** via **NotActions**.

1. Välj **IAM (PreProductionRG - Access control) i**Azure-portalens brödsmulor . Välj sedan sidan **Översikt** till vänster och sedan knappen **Ta bort resursgrupp.** Ange namnet _PreProductionRG_ för att bekräfta borttagningen och välj **Ta bort** längst ned i fönstret.

   Det gick inte **att ta bort resursgruppen Förproduktion av resursgruppen FörproduktionS-Ogeringen.** Felet anger att medan ditt konto har behörighet att ta bort resursgruppen, nekas åtkomst av skisstilldelningen. Kom ihåg att vi valde _skrivskyddat_ skisslåsläge under skisstilldelning. Skisslåset hindrar ett konto med behörighet, även _Ägare,_ från att ta bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

Dessa steg visar att våra resurser skapades enligt definitionen och skisslås förhindrade oönskad radering, även från ett konto med behörighet.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelning av skissen

Det sista steget är att ta bort tilldelningen av skissen och de resurser som den distribuerade.
Om du tar bort tilldelningen tas inte de distribuerade artefakterna bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Tilldelade ritningar** till vänster. Använd filtren för att hitta skisstilldelningen tilldelning av _tilldelningar av tilldelningar av tilldelningar av tilldelningar av tilldelningar_ av tilldelningar av tilldelningar och välj den sedan.

1. Välj knappen **Ta bort tilldelning** av skiss högst upp på sidan. Läs varningen i bekräftelsedialogrutan och välj sedan **OK**.

   När skisstilldelningen har tagits bort tas även skisslåsen bort. De skapade resurserna kan återigen tas bort av ett konto med behörigheter.

1. Välj **Resursgrupper** på Azure-menyn och välj sedan **ProductionRG**.

1. Välj sidan **Access control (IAM)** till vänster och sedan fliken **Rolltilldelningar.**

Säkerheten för varje resursgrupper har fortfarande de distribuerade rolltilldelningarna, men skisstilldelningen har inte längre _ägaråtkomst._

När **portalaviseringen Ta bort skisstilldelningen** visas går du vidare till nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort följande resurser:

- _Resursgrupp ProductionRG_
- Resursgrupp _FörproduktionRG_
- Skissdefinition _två-rgs-med-roll-tilldelningar_

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en ny skiss från en exempeldefinition. Om du vill veta mer om Azure Blueprints fortsätter du till artikeln om skisslivscykeln.

> [!div class="nextstepaction"]
> [Läs mer om skisslivscykeln](../concepts/lifecycle.md)