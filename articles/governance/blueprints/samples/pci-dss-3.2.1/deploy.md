---
title: Distribuera PCI-DSS v 3.2.1-skiss exempel
description: Distribuera steg för betalnings kortet bransch Data Security Standard v 3.2.1-exempel, inklusive information om skiss artefakt parameter.
ms.date: 08/19/2020
ms.topic: sample
ms.openlocfilehash: a7917f09e5e5ff898bd155f0961e5c9765b8d791
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649218"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Distribuera skiss exemplet PCI-DSS v 3.2.1

För att distribuera Azure-skisser PCI-DSS v 3.2.1-skiss exemplet måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Hitta skiss exemplet **PCI-DSS v 3.2.1** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av provet för PCI-DSS v 3.2.1-skissen.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ överst på sidan eller **Nästa: artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov, men ändringen kan flyttas bort från PCI-DSS v 3.2.1-standarden.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från PCI-DSS v 3.2.1-exemplet". Välj sedan **publicera** längst ned på sidan.

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
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skiss lås inställningen för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna alternativet standard _tilldelade_ hanterade identiteter.

   - Artefakt parametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan. Skiss tilldelningen skapas och artefakt distributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrol lera statusen för distributionen genom att öppna skiss tilldelningen.

> [!WARNING]
> Tjänsten Azure-ritningar och de inbyggda skiss exemplen är **kostnads fria**. Azure-resurser [priss ätts per produkt](https://azure.microsoft.com/pricing/). Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaden för att köra resurser som distribuerats i skiss exemplet.

## <a name="artifact-parameters-table"></a>Tabellen artefakt parametrar

I följande tabell visas en lista över Skissernas artefakt parametrar:

|Artefakt namn|Artefakt typ|Parameternamn|Beskrivning|
|-|-|-|-|
|\[För hands versions \] granskning PCI v 3.2.1:2018 kontrollerar och distribuerar särskilda VM-tillägg för att stödja gransknings krav|Principtilldelning|Lista över resurs typer | Granska diagnostikinställningar för valda resurs typer. Standardvärdet är alla resurser markerade| 
|Tillåtna platser|Principtilldelning|Lista över tillåtna platser|Lista över data Center platser som tillåts för alla resurser som ska distribueras till. Den här listan kan anpassas till önskade Azure-platser globalt. Välj de platser som du vill tillåta.| 
|Tillåtna platser för resurs grupper|Principtilldelning |Tillåten plats |Med den här principen kan du begränsa vilka platser som organisationen kan skapa resurs grupper i. Den används för att genomdriva kraven på geo-efterlevnad.| 
|Distribuera granskning på SQL-servrar|Principtilldelning|Kvarhållning dagar|Data kvarhållning i antal dagar. Standardvärdet är 180 men PCI kräver 365.| 
|Distribuera granskning på SQL-servrar|Principtilldelning|Resurs grupp namn för lagrings konto|Granskning skriver databas händelser till en Gransknings logg i ditt Azure Storage konto (ett lagrings konto skapas i varje region där ett SQL Server skapas som kommer att delas av alla servrar i den regionen).| 

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera PCI-DSS v 3.2.1-skissen kan du gå till följande artiklar om du vill veta mer om översikt och kontroll mappning:

> [!div class="nextstepaction"]
> [PCI – DSS v 3.2.1 skiss – översikt](./index.md) 
>  [PCI-DSS v 3.2.1 skiss-kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
