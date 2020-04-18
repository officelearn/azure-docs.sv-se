---
title: Visa uppdateringsutvärderingar för Azure Automation Update Management
description: I den här artikeln beskrivs hur du visar uppdateringsutvärderingar för uppdateringsdistributioner.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617383"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Visa uppdateringsutvärderingar för Azure Automation Update Management

I ditt Azure Automation-konto väljer du **Uppdateringshantering** för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer, uppdateringar som saknas, uppdaterar distributioner och schemalagda uppdateringsdistributioner. I kolumnen **EFTERLEVNAD** kan du se när maskinen senast utvärderades. I kolumnen **UPPDATERINGSAGENTBEREDSKAP** kan du se hälsotillståndet för uppdateringsagenten. Om det finns ett problem väljer du länken för att gå till felsökningsdokumentation som kan hjälpa dig att åtgärda problemet.

Om du vill köra en loggsökning som returnerar information om datorn, uppdateringen eller distributionen väljer du motsvarande objekt i listan. Fönstret Loggsökning öppnas med en fråga för det markerade objektet.

![Standardvy för uppdateringshantering](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visa uppdateringar som saknas

Välj **Saknade uppdateringar** om du vill visa listan över uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdatering, operativsysteminformation och en länk för mer information visas alla. I fönstret Loggsökning visas också mer information om uppdateringarna.

![Uppdateringar som saknas](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klassificering av uppdateringar

I följande tabeller visas de uppdateringsklassificeringar som stöds i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | Uppdateringar för specifika problem som åtgärdar kritiska, icke-säkerhetsrelaterade fel.        |
|Säkerhetsuppdateringar     | Uppdateringar för produktspecifika, säkerhetsrelaterade problem.        |
|Samlade uppdateringar     | Uppsättningar med snabbkorrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produktfunktioner som distribueras utanför en produktversion.        |
|Service pack     | Uppsättningar med snabbkorrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | Uppdateringar av virusfiler eller andra definitionsfiler.        |
|Verktyg     | Verktyg eller funktioner som hjälper till att utföra en eller flera uppgifter.        |
|Uppdateringar     | Uppdateringar av program eller filer som är installerade för närvarande.        |

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
