---
title: Distribuera brittiska officiella & brittiska NHS skiss prover
description: Distribuera steg för brittiska officiella och brittiska NHS skiss prover inklusive skiss artefakt parameter detaljer.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 3d69f4477163618aa225150ac6352a16e901f826
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74546404"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Distribuera storbritanniens officiella och brittiska NHS-stickprover

För att distribuera de brittiska officiella och brittiska NHS-skissproverna måste följande åtgärder vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Hitta storbritannien **OFFICIELLA** eller **brittiska NHS** skiss prov under _Andra prover_ och välj Använd det **här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn:** Ange ett namn för ditt exemplar av skissexemplet.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen för att spara din kopia av exemplet.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissprovet kan anpassas till din miljö och dina behov, men den ändringen kan flytta den bort från standarden.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Publicera skiss** högst upp på sidan. På den nya sidan till höger anger du en **version** för ditt exemplar av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ge **ändra anteckningar** som "Första versionen publiceras från Storbritannien OFFICIELLA eller brittiska NHS skiss prov." Välj sedan **Publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopian av skissexemplet har **publicerats**kan den tilldelas en prenumeration inom den hanteringsgrupp som den sparades till. Det här steget är där parametrar tillhandahålls för att göra varje distribution av kopian av skissexemplet unikt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Tilldela skiss** högst upp på skissdefinitionssidan.

1. Ange parametervärdena för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av prenumerationerna i hanteringsgruppen som du sparade din kopia av skissexemplet till. Om du väljer mer än en prenumeration skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelningsnamn**: Namnet är förifyllt för dig baserat på namnet på skissen.
       Ändra efter behov eller lämna som det är.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint definition version**: Välj en **publicerad** version av ditt exemplar av skissen provet.

   - Lås tilldelning

     Välj inställningen för skisslås för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna _standardalternativet för tilldelade_ hanterade identiteter.

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som det definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de har definierats under tilldelningen av skissen. En fullständig lista eller artefaktparametrar och deras beskrivningar finns i [tabellen Artefaktparametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen börjar. Distributionen tar ungefär en timme. Om du vill kontrollera status för distributionen öppnar du skisstilldelningen.

> [!WARNING]
> Azure Blueprints-tjänsten och de inbyggda skissexemplen är **kostnadsfria**. Azure-resurser [prissätts efter produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaden för att köra resurser som distribueras av det här skissexemplet.

## <a name="artifact-parameters-table"></a>Tabellen Artefaktparametrar

Följande tabell innehåller en lista över parametrarna för skissartefakt:

Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Planinitiativ för brittisk tjänsteman eller brittisk NHS|Principtilldelning |Resurstyper för att granska diagnostiska loggar (Policy: Blueprint initiativ för brittiska OFFICIELLA eller brittiska NHS) |Lista över resurstyper som ska granskas om diagnostiklogginställningen är anteckningsaktiverad.  Godkända värden finns i [Tjänster, scheman och kategorier som stöds för Azure Diagnostic Logs](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|\[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer |Principtilldelning |Valfritt: Lista över VM-avbildningar som har \[stöd\]för Linux OS att lägga till i omfattning (Policy: Preview: Deploy Log Analytics Agent för virtuella Linux-datorer) |(Valfritt) Standardvärdet är _inget_. Mer information finns [i Skapa en log analytics-arbetsyta i Azure-portalen](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer |Principtilldelning |Valfritt: Lista över vm-avbildningar som har \[stöd\]för Windows OS att lägga till i scopet (Princip: Förhandsversion: Deploy Log Analytics Agent för virtuella Windows-datorer) |(Valfritt) Standardvärdet är _inget_. Mer information finns [i Skapa en log analytics-arbetsyta i Azure-portalen](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera de brittiska officiella och brittiska NHS-skissexemplen kan du besöka följande artiklar om hur du kan läsa om översikten och kontrollmappningen:

> [!div class="nextstepaction"]
> [Brittiska officiella och brittiska NHS ritningar - Översikt](./index.md)
> [Storbritannien officiella och brittiska NHS ritningar - Kontroll kartläggning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).