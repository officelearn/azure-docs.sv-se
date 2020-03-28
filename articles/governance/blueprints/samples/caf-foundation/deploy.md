---
title: Distribuera skissexempel för CAF Foundation
description: Distribuera steg för skissexemplet CAF Foundation, inklusive detaljinformation för skissartefaktparameter.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: df17f8c3f539e25635ea4718be9d51d5e5e3f708
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545525"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Distribuera skissexemplet microsoft cloud adoption för Azure Foundation

Om du vill distribuera skissexemplet Microsoft Cloud Adoption Framework for Azure (CAF) Foundation måste följande åtgärder vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Leta reda på exemplet **med CAF** Foundation-skissen under _Andra exempel_ och välj Använd det **här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn**: Ange ett namn för ditt exemplar av CAF Foundation-skissexemplet.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen för att spara din kopia av exemplet.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissexemplet kan anpassas till din miljö och dina behov, men den ändringen kan flytta den bort från CAF Foundation-skissen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Publicera skiss** högst upp på sidan. På den nya sidan till höger anger du en **version** för ditt exemplar av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar** som "Första versionen som publiceras från caf Foundation-skissexemplet". Välj sedan **Publicera** längst ned på sidan.

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
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i.
     - Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen.
       Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint definition version**: Välj en **publicerad** version av ditt exemplar av skissen provet.

   - Lås tilldelning

     Välj inställningen för skisslås för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Välj antingen alternativet _standardsystemtilldelade_ hanterade identiteter eller det _användartilldelade identitetsalternativet._

   - Skissparametrar

     De parametrar som definieras i det här avsnittet används av många av artefakterna i skissdefinitionen för att ge konsekvens.

     - **Organisation**: Ange ditt organisationsnamn, till exempel Contoso, måste vara unikt.
     - **Azure-region**: Välj Azure-regionen för distribution.
     - **Tillåtna platser**: Vilka Azure-regioner tillåter du att resurser byggs in?
     
   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som det definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de har definierats under tilldelningen av skissen. En fullständig lista eller artefaktparametrar och deras beskrivningar finns i [tabellen Artefaktparametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen börjar. Distributionen tar ungefär en timme. Om du vill kontrollera status för distributionen öppnar du skisstilldelningen.

> [!WARNING]
> Azure Blueprints-tjänsten och de inbyggda skissexemplen är **kostnadsfria**. Azure-resurser [prissätts efter produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaden för att köra resurser som distribueras av det här skissexemplet.

## <a name="artifact-parameters-table"></a>Tabellen Artefaktparametrar

Följande tabell innehåller en lista över parametrarna för skissartefakt:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Tillåtna SKU:er för lagringskonto|Principtilldelning|Policy_Allowed-StorageAccount-SKU:er|SKU används i lagringskonton för diagnostiklogg|
|Tillåtna SKU:er för virtuella datorer|Principtilldelning|Policy_Allowed-VM-SKU:er|Tillåtna SKU:er för virtuella datorer|
|Lägg till CostCenter TAG i resursgrupper|Principtilldelning|Policy_CostCenter_Tag|Lägg till CostCenter TAG och dess värde från resursgruppen|
|Resurstyper som du inte vill tillåta i din miljö|Principtilldelning|Princip _Allowed-Resurs-Typer|Vilka Azure-resurser du vill tillåta i din miljö|
|Distribuera nyckelvalv|Resource Manager-mall|KV-AccessPolicy|**Låst** - Azure AD-grupp eller användare <Object ID> att bevilja behörigheter till i Key Vault|
|Distribuera logganalys|Resource Manager-mall|LogAnalytics_DataRetention|**Låst** - Antal dagar data kommer att behållas i Log Analytics|
|Distribuera logganalys|Resource Manager-mall|LogAnalytics_Location|**Låst** - Region som används vid etablering av arbetsytan|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera skissexemplet för CAF Foundation kan du läsa följande artikel om arkitekturen:

> [!div class="nextstepaction"]
> [CAF Foundation-ritning - Översikt](./index.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).