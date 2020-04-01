---
title: Distribuera FedRAMP-omfattande skiss-exempel
description: Distribuera steg för FedRAMP High-skissexemplet, inklusive information om skissartefaktparameter.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 91f208105a07770c557b1f273f3420c03b3d6dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77087309"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>Distribuera FedRAMP High-skissprovet

Om du vill distribuera Azure Blueprints FedRAMP High-skissexemplar måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Hitta **FedRAMP High** skiss exempel under _Andra prover_ och välj Använd det **här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn:** Ange ett namn för ditt exemplar av FedRAMP High skissprov.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen för att spara din kopia av exemplet.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissen provet kan anpassas till din miljö och behov, men att ändringen kan flytta den bort från anpassning med FedRAMP Höga kontroller.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Publicera skiss** högst upp på sidan. På den nya sidan till höger anger du en **version** för ditt exemplar av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar** som "Första versionen som publiceras från FedRAMP High-skissexemplet". Välj sedan **Publicera** längst ned på sidan.

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
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Logganalys arbetsyte-ID som virtuella datorer ska konfigureras för|Det här är ID (GUID) på arbetsytan Log Analytics som de virtuella datorerna ska konfigureras för.|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över resurstyper som ska ha diagnostikloggar aktiverade|Lista över resurstyper som ska granskas om diagnostiklogginställningen inte är aktiverad. Acceptabla värden finns i [Azure Monitor diagnostikloggar scheman](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över användare som bör uteslutas från gruppen Administratörer för virtuella datorer|En semikolonavgränsad lista över medlemmar som bör uteslutas i den lokala gruppen Administratörer. Ex: Administratör; myUser1; myUser2|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Lista över användare som ska ingå i gruppen Administratörer för Windows VM|En semikolonavgränsad lista över medlemmar som ska ingå i den lokala gruppen Administratörer. Ex: Administratör; myUser1; myUser2|
|\[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)|Principtilldelning|Log Analytics-arbetsyta för VMSS (Linux VMSS)|Om den här arbetsytan ligger utanför tilldelningens omfattning måste du manuellt bevilja behörigheter för Logganalysdeltagare (eller liknande) till principtilldelningens huvud-ID.|
|\[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux OS för att lägga till i omfång|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|\[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer|Principtilldelning|Log Analytics arbetsyta för virtuella Linux-datorer|Om den här arbetsytan ligger utanför tilldelningens omfattning måste du manuellt bevilja behörigheter för Logganalysdeltagare (eller liknande) till principtilldelningens huvud-ID.|
|\[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux OS för att lägga till i omfång|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|\[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)|Principtilldelning|Log Analytics-arbetsyta för Windows VM-skalningsuppsättningar (VMSS)|Om den här arbetsytan ligger utanför tilldelningens omfattning måste du manuellt bevilja behörigheter för Logganalysdeltagare (eller liknande) till principtilldelningens huvud-ID.|
|\[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)|Principtilldelning|Valfritt: Lista över vm-avbildningar som har stöd för Windows OS att lägga till i omfång|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|\[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer|Principtilldelning|Log Analytics-arbetsyta för virtuella Windows-datorer|Om den här arbetsytan ligger utanför tilldelningens omfattning måste du manuellt bevilja behörigheter för Logganalysdeltagare (eller liknande) till principtilldelningens huvud-ID.|
|\[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer|Principtilldelning|Valfritt: Lista över vm-avbildningar som har stöd för Windows OS att lägga till i omfång|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|Distribuera avancerat skydd mot hot på lagringskonton|Principtilldelning|Verkan|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|Distribuera granskning på SQL-servrar|Principtilldelning|Värdet i dagar av kvarhållningsperioden (0 anger obegränsad kvarhållning)|Kvarhållningsdagar (valfritt, 180 dagar om det inte anges)|
|Distribuera granskning på SQL-servrar|Principtilldelning|Resursgruppsnamn för lagringskonto för SQL-servergranskning|Granskning skriver databashändelser till en granskningslogg i ditt Azure Storage-konto (ett lagringskonto skapas i varje region där en SQL Server skapas som delas av alla servrar i den regionen). Viktigt - för korrekt drift av granskning inte ta bort eller byta namn på resursgruppen eller lagringskonton.|
|Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper|Principtilldelning|Prefix för lagringskonto för diagnostik av nätverkssäkerhetsgrupper|Det här prefixet kombineras med nätverkssäkerhetsgruppsplatsen för att skapa det skapade lagringskontonamnet.|
|Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper|Principtilldelning|Resursgruppsnamn för lagringskonto för nätverkssäkerhetsgruppdiagnostik (måste finnas)|Resursgruppen som lagringskontot ska skapas i. Den här resursgruppen måste redan finnas.|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Tillåtna platser för resurser och resursgrupper|Lista över Azure-platser som din organisation kan ange när du distribuerar resurser. Detta angivna värde används också av policyn "Tillåtna platser" inom det politiska initiativet.|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Sårbarhetsbedömning bör aktiveras på dina SQL-servrar|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Sårbarhetsbedömning bör aktiveras på virtuella datorer|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Geo redundant lagring bör aktiveras för lagringskonton|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Geo redundant säkerhetskopiering bör aktiveras för Azure Database för MariaDB|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Geo redundant säkerhetskopiering bör aktiveras för Azure Database for MySQL|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Geo redundant säkerhetskopiering bör aktiveras för Azure Database för PostgreSQL|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Regler för nätverkssäkerhetsgruppen för internetvända virtuella datorer bör härdas|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Webbprogrammet ska endast vara tillgängligt via HTTPS|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Funktionsappen ska endast vara tillgänglig via HTTPS|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Externa konton med skrivbehörighet ska tas bort från din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Externa konton med läsbehörighet bör tas bort från din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Externa konton med ägarbehörigheter ska tas bort från din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Inaktuella konton ska tas bort från din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|CORS bör inte tillåta alla resurser att komma åt ditt webbprogram|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|MFA ska aktiveras på konton med läsbehörighet för din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|MFA ska aktiveras på konton med ägarbehörighet för din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|MFA ska aktiveras på konton med skrivbehörighet för din prenumeration|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|
|\[Förhandsversion:\]Granska FedRAMP-höga kontroller och distribuera specifika VM-tillägg för att stödja granskningskrav|Principtilldelning|Långsiktig geo-redundant säkerhetskopiering bör aktiveras för Azure SQL-databaser|Information om principeffekter finns på [Förstå Azure-principeffekter](../../../policy/concepts/effects.md).|


## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera FedRAMP High skiss exempel, besök följande artiklar för att lära dig om skissen och kontroll mappning:

> [!div class="nextstepaction"]
> [FedRAMP Hög plan - Översikt](./index.md)
> [FedRAMP Hög plan - Kontroll kartläggning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).