---
title: Exempel - PCI-DSS v3.2.1 skissen - distributionsstegen
description: Distribuera steg av Payment Card Industry Data Security Standard v3.2.1 skissen exemplet.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b4e9435e5f569a076fc5beb6441b9da935b87f3a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561371"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Distribuera exemplet PCI-DSS v3.2.1 skiss

Följande steg måste vidtas för att distribuera Azure skisser PCI-DSS v3.2.1 skissen exemplet:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Först måste implementera skissen exemplet genom att skapa en ny skiss i din miljö med exemplet som en starter.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Från den **komma igång** sidan till vänster, Välj den **skapa** knappen _skapa en skiss_.

1. Hitta den **PCI-DSS v3.2.1** skissen exemplet under _andra exempel_ och välj **använda det här exemplet**.

1. Ange den _grunderna_ av skiss exemplet:

   - **Namn på skiss**: Ange ett namn för din kopia av PCI-DSS v3.2.1 skissen exemplet.
   - **Definitionens plats**: Använd de tre punkterna och välj hanteringsgruppen för att spara din kopia av exemplet för att.

1. Välj den _artefakter_ fliken högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissen exemplet. Många av artefakter har parametrar som ska vi definiera senare. Välj **spara utkast** när du har granskat skissen exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel-kopia

Din kopia av exemplet skissen har skapats i din miljö. Den skapas i **Draft** läge och måste vara **publicerad** innan den kan tilldelas och distribueras. Kopia av exemplet skissen kan anpassas till din miljö och behov, utan att ändringar kan flytta dem från den PCI-DSS v3.2.1 som är standard.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta din kopia av exemplet skissen och markera den.

1. Välj **publicera skiss** överst på sidan. I den nya sidan till höger, tillhandahåller en **Version** för din kopia av exemplet skissen. Den här egenskapen är användbar för om du gör en ändring av senare. Ange **ändra anteckningar** som ”första versionen publicerade från PCI-DSS v3.2.1 skissen provet”. Välj sedan **publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempel-kopia

När kopian av exemplet skissen har installerats utan **publicerad**, så kan de tilldelas en prenumeration inom den sparades till hanteringsgruppen. Det här steget är där parametrar har angetts så att varje distribution av kopian av exemplet skissen unikt.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta din kopia av exemplet skissen och markera den.

1. Välj **tilldela skissen** överst på sidan för skissen definition.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som är i hanteringsgruppen som du sparade din kopia av exemplet skissen till. Om du väljer mer än en prenumeration skapas en tilldelning för att använda de parametrar som anges.
     - **Tilldelningsnamn**: Namnet är förifyllda baserat på namnet på skissen.
       Ändra vid behov eller lämna skick.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Definitionsversion för skissen**: Välj en **publicerad** version av din kopia av exemplet skissen.

   - Tilldelning av Lås

     Markera skiss Lås för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Låt standardvärdet _systemtilldelad_ hanterad identitet alternativet.

   - Artefakten parametrar

     De parametrar som definierats i det här avsnittet gäller för artefakten som den definieras. De här parametrarna kan [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definierades vid tilldelning av skissen. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [artefakt parametrar tabell](#artifact-parameters-table).

1. När alla parametrar har angetts, väljer **tilldela** längst ned på sidan. Skisstilldelningen har skapats och distribution av artefakt påbörjas. Distributionen tar ungefär en timme. Öppna skisstilldelningen för att kontrollera statusen för distributionen.

> [!WARNING]
> Tjänsten Azure skisser och inbyggda skissen exemplen är **kostar**. Azure-resurser är [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd den [priskalkylator](https://azure.microsoft.com/pricing/calculator/) att uppskatta kostnaden för att köra resurser som har distribuerats av den här skissen-exemplet.

## <a name="artifact-parameters-table"></a>Artefakten parametrar tabell

Följande tabell innehåller en lista över skissen artefakt parametrar:

|Artefaktnamn|Typ av artefakt|Parameternamn|Beskrivning|
|-|-|-|-|
|[Förhandsversion]\: Granska PCI v3.2.1:2018 kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över resurstyper | Granska diagnostikinställning för valda resurstyper. Standardvärdet är alla resurser har valts| 
|Tillåtna platser|Principtilldelning|Listan över tillåtna platser|Lista över data center platser som tillåts för en resurs som ska distribueras till. Den här listan kan anpassas till önskat Azure-platser globalt. Välj platser som du vill tillåta.| 
|Tillåtna platser för resursgrupper|Principtilldelning |Tillåtna plats |Den här principen kan du begränsa de platser som organisationen kan skapa resursgrupper i. Den används för att genomdriva kraven på geo-efterlevnad.| 
|Distribuera granskning på SQL-servrar|Principtilldelning|Kvarhållning i dagar|Data rentention i antal dagar. Standardvärdet är 180 men PCI kräver 365.| 
|Distribuera granskning på SQL-servrar|Principtilldelning|Resursgruppnamn för lagringskonto|Granskning skrivningar databashändelser till en granskningslogg logga in i Azure Storage-kontot (ett lagringskonto skapas i varje region där en SQL-Server skapas som ska delas av alla servrar i den regionen).| 

## <a name="next-steps"></a>Nästa steg

Nu när du har gått igenom stegen för att distribuera PCI-DSS v3.2.1 skissen exemplet finns i följande artiklar om du vill veta mer om översikt och kontroll mappning:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 skissen - översikt](./index.md)
> [PCI-DSS v3.2.1 skissen - kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
