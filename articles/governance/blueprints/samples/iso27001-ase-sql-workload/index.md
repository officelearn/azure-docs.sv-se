---
title: Exempel – Översikt över skissexemplet ISO 27001 ASE/SQL-arbetsbelastning
description: Översikt och arkitektur för skissexemplet ISO 27001 App Service-miljön/SQL Database-arbetsbelastning.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b17b07124cf4491a6c58e9be2b10f958e29217ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876098"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Översikt över skissexemplet ISO 27001 App Service-miljön/SQL Database-arbetsbelastning

Skissexemplet ISO 27001 App Service-miljön/SQL Database-arbetsbelastning tillhandahåller ytterligare infrastruktur för skissexemplet [ISO 27001 Delade tjänster](../iso27001-shared/index.md).
Den här skissen hjälper kunder att distribuera molnbaserade arkitekturer som erbjuder lösningar på scenarion med auktoriserings- och efterlevnadskrav.

Det finns två ISO 27001-skissexempel: det här exemplet och [ISO 27001 Delade tjänster](../iso27001-shared/index.md).

> [!IMPORTANT]
> Det här exemplet är beroende av den infrastruktur som distribueras med skissexemplet [ISO 27001 Delade tjänster](../iso27001-shared/index.md). Det exemplet måste distribueras först.

## <a name="architecture"></a>Arkitektur

Skissexemplet ISO 27001 App Service-miljön/SQL Database-arbetsbelastning distribuerar en PaaS-baserad webbmiljö (plattform som en tjänst). Miljön kan användas som värd för webbappar, webb-API:er och SQL Database-instanser som följer ISO 27001-standarderna. Det här skissexemplet är beroende av skissexemplet [ISO 27001 Delade tjänster](../iso27001-shared/index.md).

![Utformning för skissexemplet ISO 27001 ASE/SQL-arbetsbelastning](../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png)

Den här miljön består av flera Azure-tjänster ger en säker, fullständigt övervakad arbetsbelastningsinfrastruktur för företag baserat på ISO 27001-standarder. Den här miljön består av:

- En [RBAC-roll](../../../../role-based-access-control/overview.md) (rollbaserad åtkomstkontroll) med namnet DevOps som har behörighet att distribuera och hantera resurser i [Azure App Service-miljöer](../../../../app-service/environment/intro.md) som distribuerats med skissexemplet
- [Azure-principer](../../../policy/overview.md) för att låsa vilka tjänster som ska kunna distribueras till miljön och avvisa skapande av offentliga IP-adressresurser (PIP)
- Ett virtuellt nätverk som innehåller ett enskilt undernät som är peerkopplat tillbaka till en redan befintlig miljö för [delade tjänster](../iso27001-shared/index.md) och som tvingar all trafik att gå via brandväggen för [delade tjänster](../iso27001-shared/index.md). Det virtuella nätverket är värd för följande resurser:
  - En [Azure App Service-miljön](../../../../app-service/environment/intro.md) som kan användas som värd för en eller flera webbappar, webb-API:er eller funktioner
  - En [Azure Key Vault](../../../../key-vault/key-vault-whatis.md)-instans med en tjänstslutpunkt för virtuellt nätverk för att lagra hemligheter som används av appar som körs i arbetsbelastningsmiljön
  - En [Azure SQL Database](../../../../sql-database/sql-database-technical-overview.md)-instans med en tjänstslutpunkt för virtuellt nätverk för att lagra databaser som används för appar i arbetsbelastningsmiljön

## <a name="next-steps"></a>Nästa steg

Du har läst översikten och arkitekturen för skissexemplet ISO 27001 App Service-miljö/SQL Database-arbetsbelastning. Nu kan du gå vidare till följande artiklar och lära dig om kontrollmappning och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Skissen ISO 27001 App Service-miljön/SQL Database-arbetsbelastning – Kontrollmappning](./control-mapping.md)
> [Skissen ISO 27001 App Service-miljö/SQL Database-arbetsbelastning – Distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).