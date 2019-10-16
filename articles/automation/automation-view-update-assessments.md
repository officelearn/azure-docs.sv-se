---
title: Visa Azure Uppdateringshantering Update-utvärderingar
description: Den här artikeln beskriver hur du visar uppdaterings utvärderingar för uppdaterings distributioner
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377725"
---
# <a name="view-azure-update-management-update-assessments"></a>Visa Azure Uppdateringshantering Update-utvärderingar

I ditt Automation-konto väljer du **uppdateringshantering** för att visa status för dina datorer.

Den här vyn innehåller information om dina datorer, uppdateringar som saknas, uppdaterings distributioner och schemalagda uppdaterings distributioner. I **kolumnen efterlevnad**kan du se den senaste gången datorn utvärderades. I kolumnen **Uppdatera agent beredskap** kan du se om hälso tillståndet för uppdaterings agenten. Om det uppstår ett problem väljer du länken för att gå till fel söknings dokumentation som kan hjälpa dig att lära dig vilka steg du bör vidta för att åtgärda problemet.

Om du vill köra en loggs ökning som returnerar information om datorn, uppdateringen eller distributionen väljer du objektet i listan. Fönstret **loggs ökning** öppnas med en fråga för det valda objektet:

![Uppdateringshantering standardvy](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visa saknade uppdateringar

Välj **saknade uppdateringar** om du vill visa en lista med uppdateringar som saknas på dina datorer. Varje uppdatering visas och kan väljas. Information om antalet datorer som kräver uppdateringen, operativ systemet och en länk för mer information visas. I fönstret **loggs ökning** visas mer information om uppdateringarna.

![Uppdateringar som saknas](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Uppdaterings klassificeringar

I följande tabeller visas uppdaterings klassificeringarna i Uppdateringshantering, med en definition för varje klassificering.

### <a name="windows"></a>Windows

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett särskilt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service pack     | En kumulativ uppsättning snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus-eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som är installerad för närvarande.        |

### <a name="linux-2"></a>Linux

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska i natur eller inte är säkerhets uppdateringar.        |

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet och Visa utvärderings data på grund av data berikning i molnet. Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i rutan. Om du har CentOS-datorer som har kon figurer ATS på ett sätt för att returnera säkerhets data för följande kommando, kommer Uppdateringshantering kunna korrigeras baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För närvarande tillhandahålls kunder som kan ha aktiverat detta på egen hand.

## <a name="next-steps"></a>Nästa steg

När du har granskat uppdaterings utvärderingen kan du schemalägga en uppdaterings distribution genom att följa stegen under [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).