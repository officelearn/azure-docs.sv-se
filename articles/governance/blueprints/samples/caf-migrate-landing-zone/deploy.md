---
title: Exempel-CAF migrera landnings zon skiss – distribuera steg
description: Distribuera steg i CAF migrera landnings zon skiss exemplet.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: 520eded941f625bf90204213188ac1afc94d3111
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802575"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Distribuera den Microsoft Cloud implementerings ramverket för Azure Migrates exempel på skiss zon skiss

Följande steg måste vidtas för att distribuera CAF för Azure-ritningar för att migrera landnings zon.

> [!div class="checklist1"]
> - Rekommenderat att distribuera [CAF Foundation](../caf-foundation/index.md) skiss-exemplet

> [!div class="checklist2"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Hitta skiss exemplet **CAF migration landnings zon** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:
   - **Skiss namn** Ange ett namn för din kopia av skiss exemplet för CAF migration landnings zon.
   - **Definitions plats** Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov, men ändringen kan flyttas från CAF migrera landnings zon vägledning.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från CAF migration landnings zon skiss exempel." Välj sedan **publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempel kopian

När kopieringen av skiss exemplet har **publicerats**kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. Det här steget är den plats där parametrar tillhandahålls för att göra varje distribution av kopian av skiss exemplet unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **tilldela skiss** överst på skiss definitions sidan.

1. Ange parameter värden för skiss tilldelningen:

   - Grundinställningar
     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer fler än en prenumeration skapas en tilldelning för varje användning av de angivna parametrarna.
     - **Tilldelnings namn**: Namnet är ifyllt för dig baserat på namnet på skissen.
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

       - **Organisation**: Ange organisationens namn, till exempel contoso eller Fabrikam, måste vara unikt.
       - **AzureRegion**: Välj en Azure-region för distribution.
       
   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan. Skiss tilldelningen skapas och artefakt distributionen påbörjas. Distributionen tar ungefär fem minuter. Du kan kontrol lera statusen för distributionen genom att öppna skiss tilldelningen.

> [!WARNING]
> Tjänsten Azure-ritningar och de inbyggda skiss exemplen är **kostnads fria**. Azure-resurser [priss ätts per produkt](https://azure.microsoft.com/pricing/). Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaden för att köra resurser som distribuerats i skiss exemplet.

## <a name="artifact-parameters-table"></a>Tabellen artefakt parametrar

I följande tabell visas en lista över Skissernas artefakt parametrar:

|Namn på artefakt|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Distribuera vNET-landnings zon|Resource Manager-mall|IPAddress_Space|**Locked** -Tillhandahåll två första oktetter, till exempel 10,0|
|Distribuera Key Vault|Resource Manager-mall|KV – Access policy|**Låst** -grupp eller användar objekt-ID för att ge behörighet till i Key Vault|
|Distribuera Log Analytics|Resource Manager-mall|LogAnalytics_DataRetention|**Låst** – antal dagar som data kommer att behållas i Log Analytics|
|Distribuera Log Analytics|Resource Manager-mall|LogAnalytics_Location|**Låst** – region som används vid etablering av arbets ytan|
|Distribuera Azure Migrate|Resource Manager-mall|Azure_Migrate_Location|**Låst** – Välj den region som ska distribueras Azure Migrate|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera CAF migrera landnings zon skiss exemplet kan du gå till följande artiklar om du vill lära dig mer om arkitekturen:

> [!div class="nextstepaction"]
> [CAF migrera landnings zon skiss – översikt](./index.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).