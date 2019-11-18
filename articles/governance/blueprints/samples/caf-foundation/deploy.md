---
title: CAF Foundation skiss-exempel – distribuera steg
description: Distribuera steg för CAF Foundation-skissen som innehåller information om skiss artefakt parameter.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: afb7b19a2e5ad1f1b115526ac85d63a724101f9b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128716"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Distribuera skiss exemplet Microsoft Cloud adoption Framework för Azure Foundation

Om du vill Microsoft Cloud distribuera CAF (implementering Framework for Azure) Foundation skiss-exemplet måste du vidta följande steg:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Hitta skiss exemplet **CAF Foundation** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av CAF Foundation skiss-exemplet.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ överst på sidan eller **Nästa: artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från CAF Foundation-skissen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från CAF Foundation-skissen". Välj sedan **publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempel kopian

När kopieringen av skiss exemplet har **publicerats**kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. Det här steget är den plats där parametrar tillhandahålls för att göra varje distribution av kopian av skiss exemplet unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **tilldela skiss** överst på skiss definitions sidan.

1. Ange parameter värden för skiss tilldelningen:

   - Grundläggande inställningar
       - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer fler än en prenumeration skapas en tilldelning för varje användning av de angivna parametrarna.
     - **Tilldelnings namn**: namnet fylls i automatiskt för dig baserat på namnet på skissen.
       Ändra vid behov eller lämna det som är.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i.
     - Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen.
       Mer information finns i [Hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skiss lås inställningen för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Välj antingen alternativet standard _system som tilldelats_ hanterad identitet eller _användaren tilldelad_ identitet.

   - Skissparametrar

     De parametrar som definieras i det här avsnittet används av många av artefakterna i skiss definitionen för att ge konsekvens.

     - **Organisation**: Ange organisationens namn, till exempel contoso, måste vara unikt.
     - **Azure-region**: Välj Azure-regionen för distribution.
     - **Tillåtna platser**: vilka Azure-regioner kommer du att tillåta att resurser skapas?
     
   - Artefakt parametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan. Skiss tilldelningen skapas och artefakt distributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrol lera statusen för distributionen genom att öppna skiss tilldelningen.

> [!WARNING]
> Tjänsten Azure-ritningar och de inbyggda skiss exemplen är **kostnads fria**. Azure-resurser [priss ätts per produkt](https://azure.microsoft.com/pricing/). Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaden för att köra resurser som distribuerats i skiss exemplet.

## <a name="artifact-parameters-table"></a>Tabellen artefakt parametrar

I följande tabell visas en lista över Skissernas artefakt parametrar:

|Artefakt namn|Artefakt typ|Parameternamn|Beskrivning|
|-|-|-|-|
|Tillåtna SKU:er för lagringskonto|Principtilldelning|Policy_Allowed-StorageAccount-SKU: er|SKU som används i konton för diagnostisk logg lagring|
|Tillåtna SKU: er för virtuella datorer|Principtilldelning|Policy_Allowed-VM-SKU: er|Tillåtna SKU: er för virtuella datorer|
|Lägg till CostCenter-TAGGEN i resurs grupper|Principtilldelning|Policy_CostCenter_Tag|Lägg till CostCenter-TAGGEN och dess värde från resurs gruppen|
|Resurs typer som du inte vill tillåta i din miljö|Principtilldelning|Princip _Allowed-resurs-typer|Vilka Azure-resurser du vill tillåta i din miljö|
|Distribuera Key Vault|Resource Manager-mall|KV – Access policy|**Låst** – Azure AD-grupp eller användar <Object ID> att bevilja behörighet till i Key Vault|
|Distribuera Log Analytics|Resource Manager-mall|LogAnalytics_DataRetention|**Låst** – antal dagar som data kommer att behållas i Log Analytics|
|Distribuera Log Analytics|Resource Manager-mall|LogAnalytics_Location|**Låst** – region som används vid etablering av arbets ytan|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera CAF Foundation skiss-exemplet kan du gå till följande artikel om du vill lära dig mer om arkitekturen:

> [!div class="nextstepaction"]
> [CAF Foundation-skiss – översikt](./index.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).