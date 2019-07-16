---
title: Exempel - SP NIST 800-53 R4 skissen - distributionsstegen
description: Distribuera steg SP för NIST 800-53 R4 skissen exempel.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 206763e2d17f4ad711ff5fd897f1429814e61837
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228907"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Distribuera SP NIST 800-53 R4 skissen exempel

Om du vill distribuera SP för Azure skisser NIST 800-53 R4 skissen exempel, vidtas följande steg:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Först måste implementera skissen exemplet genom att skapa en ny skiss i din miljö med exemplet som en starter.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Från den **komma igång** sidan till vänster, Välj den **skapa** knappen _skapa en skiss_.

1. Hitta den **SP NIST 800-53 R4** skissen exemplet under _andra exempel_ och välj **använda det här exemplet**.

1. Ange den _grunderna_ av skiss exemplet:

   - **Namn på skiss**: Ange ett namn för din kopia av SP NIST 800-53 R4 skissen exemplet.
   - **Definitionens plats**: Använd de tre punkterna och välj hanteringsgruppen för att spara din kopia av exemplet för att.

1. Välj den _artefakter_ fliken högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissen exemplet. Många av artefakter har parametrar som ska vi definiera senare. Välj **spara utkast** när du har granskat skissen exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel-kopia

Din kopia av exemplet skissen har skapats i din miljö. Den skapas i **Draft** läge och måste vara **publicerad** innan den kan tilldelas och distribueras. Kopia av exemplet skissen kan anpassas till din miljö och behov, utan att ändringar kan överger den justering med SP NIST 800-53 kontroller.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta din kopia av exemplet skissen och markera den.

1. Välj **publicera skiss** överst på sidan. I den nya sidan till höger, tillhandahåller en **Version** för din kopia av exemplet skissen. Den här egenskapen är användbar för om du gör en ändring av senare. Ange **ändra anteckningar** som ”första versionen publicerade från SP NIST 800-53 R4 skissen exemplet”. Välj sedan **publicera** längst ned på sidan.

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
|\[Förhandsversion av\]: Granska SP NIST 800-53 R4 kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Log Analytics arbetsyte-ID som virtuella datorer ska konfigureras för|Detta är ID (GUID) för Log Analytics-arbetsytan som de virtuella datorerna ska konfigureras för.|
|\[Förhandsversion av\]: Granska SP NIST 800-53 R4 kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över resurstyper som ska ha aktiverat diagnostikloggar|Lista över resurstyper granska om diagnostiklogg inställningen inte är aktiverad. Godkända värden finns på [scheman för diagnostikloggar för Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Förhandsversion av\]: Granska SP NIST 800-53 R4 kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över användare som ska undantas från gruppen Administratörer för Windows-VM|En semikolonavgränsad lista över medlemmar som ska undantas i den lokala gruppen Administratörer. Exempel: Administratören; myUser1; myUser2|
|\[Förhandsversion av\]: Granska SP NIST 800-53 R4 kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över användare som ska ingå i gruppen Administratörer för Windows-VM|En semikolonavgränsad lista över medlemmar som ska ingå i den lokala gruppen Administratörer. Exempel: Administratören; myUser1; myUser2|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)|Principtilldelning|Log Analytics-arbetsyta för Linux VM Scale Sets (VMSS)|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt ge Log Analytics-bidragsgivare behörigheter (eller liknande) till den principtilldelning principal-ID.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Linux-operativsystem att lägga till i omfånget|En tom matris kan användas för att ange några valfria parametrar: \[\]|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer|Principtilldelning|Log Analytics-arbetsyta för virtuella Linux-datorer|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt ge Log Analytics-bidragsgivare behörigheter (eller liknande) till den principtilldelning principal-ID.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Linux-operativsystem att lägga till i omfånget|En tom matris kan användas för att ange några valfria parametrar: \[\]|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)|Principtilldelning|Log Analytics-arbetsyta för Windows VM Scale Sets (VMSS)|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt ge Log Analytics-bidragsgivare behörigheter (eller liknande) till den principtilldelning principal-ID.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Windows-operativsystem för att lägga till omfånget|En tom matris kan användas för att ange några valfria parametrar: \[\]|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer|Principtilldelning|Log Analytics-arbetsyta för virtuella Windows-datorer|Om den här arbetsytan ligger utanför omfånget för tilldelningen måste du manuellt ge Log Analytics-bidragsgivare behörigheter (eller liknande) till den principtilldelning principal-ID.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Windows-operativsystem för att lägga till omfånget|En tom matris kan användas för att ange några valfria parametrar: \[\]|
|Distribuera Avancerat skydd på Storage-konton|Principtilldelning|Verkan|Information om effekterna av principen finns på [förstå effekterna för Azure Policy](../../../policy/concepts/effects.md)|
|Distribuera granskning på SQL-servrar|Principtilldelning|Värdet i dagar för kvarhållningsperioden (0 betyder obegränsad kvarhållning)|Kvarhållning i dagar (valfritt, 180 dagar om inget anges)|
|Distribuera granskning på SQL-servrar|Principtilldelning|Resursgruppens namn för storage-konto för SQL server-granskning|Granskning skrivningar databashändelser till en granskningslogg logga in i Azure Storage-kontot (ett lagringskonto skapas i varje region där en SQL-Server skapas som ska delas av alla servrar i den regionen). Viktigt för att granskning ska fungera korrekt inte bort eller byta namn på resursgruppen eller lagringskontona.|
|Distribuera diagnostikinställningar för Nätverkssäkerhetsgrupper|Principtilldelning|Lagringskontoprefix för Nätverksdiagnostik security grupp|Det här prefixet kommer att kombineras med en nätverksplats säkerhet gruppen nedan för att bilda skapade lagringskontonamn.|
|Distribuera diagnostikinställningar för Nätverkssäkerhetsgrupper|Principtilldelning|Resursgruppnamn för lagringskonto för Nätverksdiagnostik security group (måste finnas)|Den resursgrupp som lagringskontot kommer att skapas i. Den här resursgruppen måste redan finnas.|

## <a name="next-steps"></a>Nästa steg

Nu när du har gått igenom stegen för att distribuera SP NIST 800-53 R4 skissen exempel finns i följande artiklar och se hur skissen och kontroll mappning:

> [!div class="nextstepaction"]
> [SP NIST 800-53 R4 skissen - översikt](./index.md)
> [SP NIST 800-53 R4 skissen - kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
