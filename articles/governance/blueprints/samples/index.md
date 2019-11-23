---
title: Index för skissexempel
description: Index över efterlevnads- och standardexempel för distribution av miljöer, principer och ramverket för molnimplementering med Azure Blueprints.
ms.date: 11/22/2019
ms.topic: sample
ms.openlocfilehash: ea8759db0fde7d6686acd3c58006be3f59adf6fd
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404133"
---
# <a name="azure-blueprints-samples"></a>Azure Blueprints-exempel

Följande tabell innehåller länkar till exempel för Azure Blueprints. Alla exempel håller produktionskvalitet och du kan börja distribuera dem redan idag för att uppfylla dina olika efterlevnadsbehov.

## <a name="standards-based-blueprint-samples"></a>Standardbaserade skissexempel

|  |  |
|---------|---------|
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Tillhandahåller riktlinjer för överensstämmelse med Kanadas PBMM (Protected B, Medium Integrity, Medium Availability). |
| [CIS Microsoft Azure Foundations Benchmark](./cis-azure-1.1.0/index.md)| Innehåller en uppsättning principer som hjälper dig att följa CIS Microsoft Azure Foundations Benchmark-rekommendationerna. |
| [FedRAMP Moderate](./fedramp-m/index.md) | Tillhandahåller en uppsättning principer som hjälper dig att följa FedRAMP Moderate. |
| [IRS 1075](./irs-1075/index.md) | Tillhandahåller efterlevnadsriktlinjer för ISO 1075.|
| [ISO 27001](./iso27001/index.md) | Tillhandahåller efterlevnadsriktlinjer för ISO 27001. |
| [ISO 27001 Delade tjänster](./iso27001-shared/index.md) | Tillhandahåller en uppsättning kompatibla infrastrukturmönster och principriktlinjer som hjälper dig med attesteringen för ISO 27001. |
| [ISO 27001 App Service-miljön/SQL Database-arbetsbelastning](./iso27001-ase-sql-workload/index.md) | Tillhandahåller ytterligare infrastruktur för skissexemplet [ISO 27001 Delade tjänster](./iso27001-shared/index.md). |
| [NIST SP 800-53 R4](./nist-sp-800-53-rev4/index.md) | Tillhandahåller efterlevnadsriktlinjer för NIST SP 800-53 R4. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Innehåller en uppsättning principer som hjälp för efterlevnad av PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Hjälper till med SWIFT CSP-CSCF v2020-efterlevnad. |
| [Styrning av UK OFFICIAL och UK NHS](./ukofficial/index.md) | Tillhandahåller en uppsättning kompatibla infrastruktursmönster och principriktlinjer som hjälper dig med attesteringen för UK OFFICIAL och UK NHS. |
| [CAF Foundation](./caf-foundation/index.md) | Innehåller en uppsättning kontroller som hjälper dig att hantera din molnegendom i enlighet med [Microsoft Cloud adoption Framework för Azure (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [CAF Migrate-landningszonen](./caf-migrate-landing-zone/index.md) | Innehåller en uppsättning kontroller som hjälper dig att förbereda migrering av din första arbetsbelastning samt hantera din molnegendom i enlighet med [Microsoft Cloud adoption Framework för Azure (CAF)](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Exempelstrategi

![Strategi för skissexempel](../media/blueprint-samples-strategy.png)

Skisserna för CAF Foundation och CAF Migrate-landningszonen förutsätter att kunden förbereder en befintlig, ren enstaka prenumeration för migrering av lokala tillgångar/arbetsbelastningar till Azure.
(Region A och B i bilden ovan).  

Det finns en möjlighet att iterera genom exempelskisserna och leta efter mönster för anpassningar som en kund använder. Det finns även möjligheten att proaktivt hantera ritningar som är branschspecifika, till exempel finansiella tjänster och näthandel (övre delen av region B). På ett liknande sätt tänker vi oss skapande av skisser för komplexa arkitektoniska överväganden såsom flera prenumerationer, hög tillgänglighet, resurser mellan flera regioner samt kunder som implementerar kontroller över flera prenumerationer och resurser (region C och D).

Det finns exempelskisser som rör det kundscenario där efterlevnadskraven är höga och den arkitektoniska komplexiteten är hög (region E i bilden ovan). Region F ovan kommer att åtgärdas av kunder och partner som utnyttjar exempelskisserna och anpassar dem till sina unika behov.

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).