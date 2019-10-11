---
title: Exempel på federala PBMM-skiss – distribuera steg
description: Distribuera steg för det federala PBMM skiss-exemplet, inklusive information om skiss artefakt parameter.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 0e799de92f25c688b41d3ac5718fe3a2b7a8aba0
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249017"
---
# <a name="deploy-the-canada-federal-pbmm-blueprint-samples"></a>Distribuera de federala PBMM-skiss exemplen i Kanada

Följande steg måste vidtas för att distribuera de federala PBMM-skisserna i Kanada:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **alla tjänster** och Sök efter och välj **princip** i det vänstra fönstret. Välj **ritningar**på sidan **princip** .

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Hitta det **federala PBMM** skiss-exemplet under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ överst på sidan eller **Nästa: artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och behov, men den ändringen kan flytta den bort från standard.

1. Välj **alla tjänster** och Sök efter och välj **princip** i det vänstra fönstret. Välj **ritningar**på sidan **princip** .

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från det federala PBMM skiss exemplet". Välj sedan **publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempel kopian

När kopieringen av skiss exemplet har **publicerats**kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. Det här steget är den plats där parametrar tillhandahålls för att göra varje distribution av kopian av skiss exemplet unik.

1. Välj **alla tjänster** och Sök efter och välj **princip** i det vänstra fönstret. Välj **ritningar**på sidan **princip** .

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **tilldela skiss** överst på skiss definitions sidan.

1. Ange parameter värden för skiss tilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer fler än en prenumeration skapas en tilldelning för varje användning av de angivna parametrarna.
     - **Tilldelnings namn**: namnet fylls i automatiskt för dig baserat på namnet på skissen.
       Ändra vid behov eller lämna det som är.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
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

Artefakt namn|Artefakt typ|Parameternamn|Beskrivning|
|-|-|-|-|
|\[Preview @ no__t-1: Distribuera Log Analytics agent för virtuella Linux-datorer |Principtilldelning |Log Analytics arbets yta för virtuella Linux-datorer |Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Preview @ no__t-1: Distribuera Log Analytics agent för virtuella Linux-datorer |Principtilldelning |Valfritt: lista över virtuella dator avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfånget |En tom matris kan användas för att indikera inga valfria parametrar: `[]` |
|\[Preview @ no__t-1: Distribuera Log Analytics agent för virtuella Windows-datorer |Principtilldelning |Valfritt: lista över virtuella dator avbildningar som har stöd för Windows OS som ska läggas till i omfånget |En tom matris kan användas för att indikera inga valfria parametrar: `[]` |
|\[Preview @ no__t-1: Distribuera Log Analytics agent för virtuella Windows-datorer |Principtilldelning |Log Analytics arbets yta för virtuella Windows-datorer |Mer information finns i [skapa en Log Analytics arbets yta i Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Preview @ no__t-1: granska Kanadas federala PBMM kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Principtilldelning |Log Analytics arbetsyte-ID som virtuella datorer ska konfigureras för |Detta är ID (GUID) för Log Analytics arbets ytan som de virtuella datorerna ska konfigureras för. |
|\[Preview @ no__t-1: granska Kanadas federala PBMM kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Principtilldelning |Lista över resurs typer som ska ha diagnostiska loggar aktiverade |Lista över resurs typer som ska granskas om diagnostisk logg inställning inte är aktive rad. Du hittar giltiga värden i [Azure Monitor-diagnostiska loggar scheman](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type). |
|\[Preview @ no__t-1: granska Kanadas federala PBMM kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Principtilldelning |Administratörs grupp |Grupp. Exempel: `Administrator; myUser1; myUser2` |
|\[Preview @ no__t-1: granska Kanadas federala PBMM kontroller och distribuera särskilda VM-tillägg för att stödja gransknings krav |Principtilldelning |Lista över användare som ska ingå i administratörs gruppen för en virtuell Windows-dator |En semikolonavgränsad lista med medlemmar som ska ingå i den lokala gruppen Administratörer. Exempel: `Administrator; myUser1; myUser2` |
|Distribuera Avancerat skydd på lagrings konton |Principtilldelning |Verkan |Information om princip effekter finns i [förstå Azure policys effekter](../../../policy/concepts/effects.md). |
|Distribuera granskning på SQL-servrar |Principtilldelning |Värdet i dagar för kvarhållningsperioden (0 anger obegränsad kvarhållning) |Retentions dagar (valfritt, _180_ dagar om inget anges) |
|Distribuera granskning på SQL-servrar |Principtilldelning |Resurs grupp namn för lagrings konto för SQL Server-granskning |Granskning skriver databas händelser till en Gransknings logg i ditt Azure Storage konto (ett lagrings konto skapas i varje region där ett SQL Server skapas som delas av alla servrar i regionen). Viktigt – för att granskningen ska fungera korrekt tar du inte bort eller byter namn på resurs gruppen eller lagrings kontona. |
|Distribuera diagnostikinställningar för nätverks säkerhets grupper |Principtilldelning |Prefix för lagrings konto för diagnostik för nätverks säkerhets grupp |Det här prefixet kombineras med nätverks säkerhets gruppens plats för att bilda det skapade lagrings konto namnet. |
|Distribuera diagnostikinställningar för nätverks säkerhets grupper |Principtilldelning |Resurs grupp namn för lagrings konto för nätverksdiagnostik för nätverks säkerhets grupp (måste finnas) |Resurs gruppen som lagrings kontot skapas i. Resurs gruppen måste redan finnas. |

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera Kanadas federala PBMM-exempel kan du gå till följande artiklar om du vill veta mer om översikt och kontroll mappning:

> [!div class="nextstepaction"]
> [Kanadas federala PBMM-ritningar – översikt](./index.md)
> [Kanadas federala PBMM-ritningar-kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).