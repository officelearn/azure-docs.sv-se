---
title: Visa uppdateringsutvärderingar för Azure Update Management
description: I den här artikeln beskrivs hur du visar uppdateringsutvärderingar för uppdateringsdistributioner.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278420"
---
# <a name="view-azure-update-management-update-assessments"></a>Visa uppdateringsutvärderingar för Azure Update Management

I ditt Azure Automation-konto väljer du **Uppdateringshantering** för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer, uppdateringar som saknas, uppdaterar distributioner och schemalagda uppdateringsdistributioner. I kolumnen **EFTERLEVNAD** kan du se när maskinen senast utvärderades. I kolumnen **UPPDATERINGSAGENTBEREDSKAP** kan du se hälsotillståndet för uppdateringsagenten. Om det finns ett problem väljer du länken för att gå till felsökningsdokumentation som kan hjälpa dig att åtgärda problemet.

Om du vill köra en loggsökning som returnerar information om datorn, uppdateringen eller distributionen väljer du motsvarande objekt i listan. Fönstret **Loggsökning** öppnas med en fråga för det markerade objektet:

![Standardvy för uppdateringshantering](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visa uppdateringar som saknas

Välj **Saknade uppdateringar** om du vill visa listan över uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdatering, operativsysteminformation och en länk för mer information visas alla. **Loggsökfönstret** visar också mer information om uppdateringarna.

![Uppdateringar som saknas](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klassificering av uppdateringar

I följande tabeller visas de uppdateringsklassificeringar som stöds i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett specifikt problem som åtgärdar ett kritiskt, icke-säkerhetsrelaterat fel.        |
|Säkerhetsuppdateringar     | En uppdatering för ett produktspecifikt, säkerhetsrelaterat problem.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabbkorrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produktfunktioner som distribueras utanför en produktversion.        |
|Service pack     | En kumulativ uppsättning snabbkorrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virusfiler eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att utföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som för närvarande är installerad.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett specifikt problem eller ett produktspecifikt, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är av avgörande betydelse eller som inte är säkerhetsuppdateringar.        |

För Linux kan uppdateringshantering skilja mellan viktiga uppdateringar och säkerhetsuppdateringar i molnet samtidigt som bedömningsdata visas. (Den här granulariteten är möjlig på grund av dataanrikning i molnet.) För korrigering förlitar sig Uppdateringshantering på klassificeringsdata som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i RTM-versionerna av produkten. Om du har CentOS-datorer konfigurerade för att returnera säkerhetsdata för följande kommando kan Uppdateringshantering korrigera baserat på klassificeringar:

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera inbyggt klassificeringsdatatillgänglighet på CentOS. För närvarande ges endast support med bäst insats till kunder som har aktiverat den här funktionen på egen hand.

Om du vill klassificera uppdateringar på Red Hat Enterprise version 6 måste du installera yum-security-insticksprogrammet. På Red Hat Enterprise Linux 7 är plugin redan en del av yum själv, det finns ingen anledning att installera något. Mer information finns i följande [red hat-kunskapsartikel](https://access.redhat.com/solutions/10021).

## <a name="next-steps"></a>Nästa steg

När du har visat eventuella uppdateringsutvärderingar kan du schemalägga en uppdateringsdistribution genom att följa stegen vid [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
