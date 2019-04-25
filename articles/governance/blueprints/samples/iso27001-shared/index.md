---
title: Översikt över skissexemplet ISO 27001 Delade tjänster
description: Översikt och arkitektur för skissexemplet ISO 27001 Delade tjänster.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c54d8aedb9464364f93a087de4bdb00c693a96ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875169"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Översikt över skissexemplet ISO 27001 Delade tjänster

Skissexemplet ISO 27001 Delade tjänster tillhandahåller en uppsättning kompatibla infrastrukturmönster och principriktlinjer som hjälper dig med attesteringen för ISO 27001. Den här skissen hjälper kunder att distribuera molnbaserade arkitekturer som erbjuder lösningar på scenarion med auktoriserings- och efterlevnadskrav.

Skissexemplet [ISO 27001 App Service-miljön/SQL Database-arbetsbelastning](../iso27001-ase-sql-workload/index.md) utökar det här exemplet.

## <a name="architecture"></a>Arkitektur

Skissexemplet ISO 27001 Delade tjänster distribuerar en grundinfrastruktur i Azure som organisationer kan använda som värd för flera arbetsbelastningar baserat på VDC-metoden (virtuellt datacenter).
VDC är en uppsättning beprövade referensarkitekturer, automatiseringsverktyg och en interaktionsmodell som Microsoft använder för sina stora företagskunder. Skissexemplet för Delade tjänster är baserat på en helt intern Azure VDC-miljö (se nedan).

![Utformning för skissexemplet ISO 27001 Delade tjänster](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

Den här miljön består av flera Azure-tjänster ger en säker, fullständigt övervakad infrastruktur för delade tjänster för företag baserat på ISO 27001-standarder. Den här miljön består av:

- [RBAC-roller](../../../../role-based-access-control/overview.md) (rollbaserad åtkomstkontroll) som används för ansvarsfördelning ur ett kontrollplansperspektiv. Tre roller definieras före distributionen av all infrastruktur:
  - NetOps-rollen har behörigheter för att hantera nätverksmiljön, inklusive brandväggsinställningar, NSG-inställningar, routning och andra nätverksfunktioner
  - SecOps-rollen har nödvändiga behörigheter för att distribuera och hantera [Azure Security Center](../../../../security-center/security-center-intro.md), definiera [Azure-principer](../../../policy/overview.md) och andra säkerhetsrelaterade behörigheter
  - SysOps-rollen har nödvändiga behörigheter för att definiera [Azure-principer](../../../policy/overview.md) i prenumerationen, hantera [Log Analytics](../../../../azure-monitor/overview.md) för hela miljön och andra behörigheter
- [Log Analytics](../../../../azure-monitor/overview.md) distribueras som första Azure-tjänsten för att se till att alla åtgärder och tjänster loggas till en central plats direkt när du påbörjar din säkra distribution
- Ett virtuellt nätverk med stöd för undernät för anslutning tillbaka till ett lokalt datacenter, en inkommande och utgående stack för Internetanslutning och ett undernät för delad tjänst med NSG:er och ASG:er för fullständig mikrosegmentering som innehåller:
  - En jumpbox eller bastionvärd som används för hantering, som endast kan nås via en [Azure-brandvägg](../../../../firewall/overview.md) som är distribuerad i undernätet för den inkommande stacken
  - Två virtuella datorer som kör Active Directory Domain Services (ADDS) och DNS som endast kan nås via jumpboxen, och som kan konfigureras för att endast replikera AD över en VPN- eller [ExpressRoute](../../../../expressroute/expressroute-introduction.md)-anslutning (distribueras inte av skissen)
  - Användning av [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) och DDoS-skydd (standard)
- En [Azure Key Vault](../../../../key-vault/key-vault-whatis.md)-instans som används som värd för hemligheter används för de virtuella datorerna som är distribuerade i miljön för delade tjänster

Alla dessa element följer beprövade metoder som finns publicerade i [Referensarkitekturer i Azure Architecture Center](/azure/architecture/reference-architectures/).

> [!NOTE]
> Infrastrukturen för ISO 27001 Delade tjänster bildar en grundläggande arkitektur för arbetsbelastningar.
> Du måste fortfarande distribuera arbetsbelastningar bakom den här grundläggande arkitekturen.

Mer information finns i [dokumentationen för virtuellt datacenter](/azure/architecture/vdc/).

## <a name="next-steps"></a>Nästa steg

Du har läst översikten och arkitektur för skissexemplet ISO 27001 Delade tjänster.
Nu kan du gå vidare till följande artiklar och lära dig om kontrollmappning och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Skissen ISO 27001 Delade tjänster – Kontrollmappning](./control-mapping.md)
> [Skissen ISO 27001 Delade tjänster – Distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).