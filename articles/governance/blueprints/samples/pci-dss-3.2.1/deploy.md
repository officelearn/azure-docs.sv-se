---
title: Distribuera pci-Dss v3.2.1-ritningsprov
description: Distribuera steg för skissexemplet Payment Card Industry Data Security Standard v3.2.1, inklusive detalj på skissartefaktparameter.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 9df9392430fd0496ffb5e635a6fc3e31ba708208
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76905553"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Distribuera skissexemplet PCI-DSS v3.2.1

Om du vill distribuera skissprovet för Azure Blueprints PCI-DSS v3.2.1 måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Leta reda på **skissprovet PCI-DSS v3.2.1** under _Andra exempel_ och välj Använd det **här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn:** Ange ett namn för ditt exemplar av PCI-DSS v3.2.1 skissprov.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen för att spara din kopia av exemplet.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissprovet kan anpassas till din miljö och dina behov, men den ändringen kan flytta den bort från PCI-DSS v3.2.1-standarden.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Publicera skiss** högst upp på sidan. På den nya sidan till höger anger du en **version** för ditt exemplar av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar** som "Första versionen som publiceras från pci-DSs v3.2.1-skissexemplet". Välj sedan **Publicera** längst ned på sidan.

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

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|\[Förhandsgranska\] gransknings-PCI v3.2.1:2018-kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över resurstyper | Granska diagnostikinställningen för valda resurstyper. Standardvärdet är att alla resurser är markerade| 
|Tillåtna platser|Principtilldelning|Lista över tillåtna platser|Lista över datacenterplatser som tillåts för alla resurser som ska distribueras till. Den här listan är anpassningsbar till önskade Azure-platser globalt. Välj platser som du vill tillåta.| 
|Tillåtna platser för resursgrupper|Principtilldelning |Tillåten plats |Med den här principen kan du begränsa de platser som din organisation kan skapa resursgrupper i. Den används för att genomdriva kraven på geo-efterlevnad.| 
|Distribuera granskning på SQL-servrar|Principtilldelning|Kvarhållningsdagar|Lagring av data i antal dagar. Standardvärdet är 180 men PCI kräver 365.| 
|Distribuera granskning på SQL-servrar|Principtilldelning|Resursgruppsnamn för lagringskonto|Granskning skriver databashändelser till en granskningslogg i ditt Azure Storage-konto (ett lagringskonto skapas i varje region där en SQL Server skapas som delas av alla servrar i den regionen).| 

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera pci-DSS v3.2.1-skissexemplet kan du besöka följande artiklar om översikten och kontrollmappningen:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 plan - Översikt](./index.md)
> [PCI-DSS v3.2.1 plan - Kontroll kartläggning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
