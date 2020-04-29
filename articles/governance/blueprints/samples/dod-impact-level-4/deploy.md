---
title: DoD effekt nivå 4 skiss exempel
description: Distribuera steg för DoD-effekt på nivå 4, inklusive information om skiss artefakts parametrar.
ms.date: 03/06/2020
ms.topic: sample
ms.openlocfilehash: c92e24327bc1879b8fea89cf85a50f845e473810
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79415317"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Distribuera DoD-effekt nivå 4-skiss exemplet

Om du vill distribuera ett skiss exempel på skydds avdelningen för Azure skisser på nivå 4 (DoD IL4), måste du vidta följande steg:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Hitta skiss exemplet på **DoD-effekt nivå 4** under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet för DoD-effekt nivå 4.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ överst på sidan eller **Nästa: artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov, men den ändringen kan flytta den bort från justeringen med DoD påverkan på nivå 4.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från DoD IL4 skiss-exemplet". Välj sedan **publicera** längst ned på sidan.

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
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Log Analytics arbetsyte-ID som virtuella datorer ska konfigureras för|Detta är ID (GUID) för Log Analytics arbets ytan som de virtuella datorerna ska konfigureras för.|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Lista över resurs typer som ska ha diagnostiska loggar aktiverade|Lista över resurs typer som ska granskas om diagnostisk logg inställning inte är aktive rad. Du hittar giltiga värden i [Azure Monitor-diagnostiska loggar scheman](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Lista över användare som ska undantas från gruppen Administratörer för virtuella Windows-datorer|En semikolonavgränsad lista med medlemmar som ska uteslutas i den lokala gruppen Administratörer. T. ex. administratör; Mina Användare1; myUser2|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Lista över användare som ska ingå i administratörs gruppen för en virtuell Windows-dator|En semikolonavgränsad lista med medlemmar som ska ingå i den lokala gruppen Administratörer. T. ex. administratör; Mina Användare1; myUser2|
|\[För\]hands version: Distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)|Principtilldelning|Log Analytics arbets yta för Linux VM Scale Sets (VMSS)|Om den här arbets ytan ligger utanför tilldelnings omfånget måste du manuellt bevilja behörigheten Log Analytics deltagare (eller liknande) till princip tilldelningens huvud-ID.|
|\[För\]hands version: Distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)|Principtilldelning|Valfritt: lista över virtuella dator avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfånget|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|\[För\]hands version: Distribuera Log Analytics agent för virtuella Linux-datorer|Principtilldelning|Log Analytics arbets yta för virtuella Linux-datorer|Om den här arbets ytan ligger utanför tilldelnings omfånget måste du manuellt bevilja behörigheten Log Analytics deltagare (eller liknande) till princip tilldelningens huvud-ID.|
|\[För\]hands version: Distribuera Log Analytics agent för virtuella Linux-datorer|Principtilldelning|Valfritt: lista över virtuella dator avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfånget|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|\[För\]hands version: Distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)|Principtilldelning|Log Analytics arbets yta för Windows VM Scale Sets (VMSS)|Om den här arbets ytan ligger utanför tilldelnings omfånget måste du manuellt bevilja behörigheten Log Analytics deltagare (eller liknande) till princip tilldelningens huvud-ID.|
|\[För\]hands version: Distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)|Principtilldelning|Valfritt: lista över virtuella dator avbildningar som har stöd för Windows OS som ska läggas till i omfånget|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|\[För\]hands version: Distribuera Log Analytics agent för virtuella Windows-datorer|Principtilldelning|Log Analytics arbets yta för virtuella Windows-datorer|Om den här arbets ytan ligger utanför tilldelnings omfånget måste du manuellt bevilja behörigheten Log Analytics deltagare (eller liknande) till princip tilldelningens huvud-ID.|
|\[För\]hands version: Distribuera Log Analytics agent för virtuella Windows-datorer|Principtilldelning|Valfritt: lista över virtuella dator avbildningar som har stöd för Windows OS som ska läggas till i omfånget|En tom matris kan användas för att ange inga valfria parametrar:\[\]|
|Distribuera Avancerat skydd på lagrings konton|Principtilldelning|Verkan|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|Distribuera granskning på SQL-servrar|Principtilldelning|Värdet i dagar för kvarhållningsperioden (0 anger obegränsad kvarhållning)|Retentions dagar (valfritt, 180 dagar om inget anges)|
|Distribuera granskning på SQL-servrar|Principtilldelning|Resurs grupp namn för lagrings konto för SQL Server-granskning|Granskning skriver databas händelser till en Gransknings logg i ditt Azure Storage konto (ett lagrings konto skapas i varje region där ett SQL Server skapas som kommer att delas av alla servrar i den regionen). Viktigt – för att kunna utföra granskningen tar du inte bort eller byter namn på resurs gruppen eller lagrings kontona.|
|Distribuera diagnostikinställningar för nätverks säkerhets grupper|Principtilldelning|Prefix för lagrings konto för diagnostik för nätverks säkerhets grupp|Det här prefixet kombineras med nätverks säkerhets gruppens plats för att bilda det skapade lagrings konto namnet.|
|Distribuera diagnostikinställningar för nätverks säkerhets grupper|Principtilldelning|Resurs grupp namn för lagrings konto för nätverksdiagnostik för nätverks säkerhets grupp (måste finnas)|Resurs gruppen som lagrings kontot kommer att skapas i. Resurs gruppen måste redan finnas.|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Tillåtna platser för resurser och resurs grupper|Lista över Azure-platser som din organisation kan ange när resurser distribueras. Det angivna värdet används också av principen för tillåtna platser i princip initiativet.|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Sårbarhets bedömning ska vara aktiverat på Virtual Machines|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Geo-redundant lagring ska vara aktiverat för lagrings konton|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Regler för nätverks säkerhets grupper för virtuella datorer som riktas mot Internet bör vara skärpta|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Webb program bör endast vara tillgängliga via HTTPS|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Funktionsapp bör endast vara tillgängligt via HTTPS|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Externa konton med Skriv behörighet bör tas bort från din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Externa konton med Läs behörighet bör tas bort från din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Externa konton med ägar behörigheter bör tas bort från din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Föråldrade konton bör tas bort från din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|CORS bör inte tillåta alla resurser åtkomst till ditt webb program|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|MFA ska vara aktiverat på konton med Skriv behörighet för din prenumeration|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|\[För\]hands version: DoD-effekt nivå 4|Principtilldelning|Långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser|Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md).|
|Tillåtna platser|Principtilldelning|Tillåtna platser|Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. Den används för att genomdriva kraven på geo-efterlevnad.|
|Tillåtna platser för resurs grupper|Principtilldelning |Tillåtna platser|Med den här principen kan du begränsa vilka platser som organisationen kan skapa resurs grupper i. Den används för att genomdriva kraven på geo-efterlevnad.|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera DoD-underläggnings nivå 4, kan du gå till följande artiklar om du vill veta mer om skiss-och kontroll mappning:

> [!div class="nextstepaction"]
> [DoD-effekt nivå 4 skiss – översikt](./index.md)
> [DoD effekt nivå 4 skiss-kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).