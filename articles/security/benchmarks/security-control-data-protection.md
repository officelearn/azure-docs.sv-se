---
title: Azure Security Control - Dataskydd
description: Dataskydd för säkerhetskontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934509"
---
# <a name="security-control-data-protection"></a>Säkerhetskontroll: Dataskydd

Dataskyddsrekommendationer fokuserar på att ta itu med problem som rör kryptering, åtkomstkontrollistor, identitetsbaserad åtkomstkontroll och granskningsloggning för dataåtkomst.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.1 | 13.1 | Kund |

Använd taggar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.2 | 13.2 | Kund |

Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Resurser ska avgränsas med VNet/Undernät, taggas på rätt sätt och skyddas av en NSG- eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade. För virtuella datorer som lagrar eller bearbetar känsliga data implementerar du princip och procedurer för att stänga av dem när de inte används.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hur man skapar en NSG med en Security Config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Distribuerar Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurera avisering eller avisering och neka med Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.3 | 13.3 | Kund |

Distribuera ett automatiserat verktyg på nätverksper perimeterer som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetsexperter varnas.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.4 | 14.4 | Delad |

Kryptera all känslig information under transport. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla om TLS 1.2 eller senare.

Följ Azure Security Center-rekommendationer för kryptering i vila och kryptering under överföring, där så är tillämpligt.

Förstå kryptering under överföring med Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.5 | 14.5 | Kund |

När ingen funktion är tillgänglig för din specifika tjänst i Azure använder du ett aktivt identifieringsverktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens tekniksystem, inklusive de som finns på plats, eller på en fjärrtjänstprovidern och uppdatera organisationens inventering av känslig information.

Använd Azure Information Protection för att identifiera känslig information i Office 365-dokument.

Använd Azure SQL Information Protection för att hjälpa till med klassificering och märkning av information som lagras i Azure SQL-databaser.

Implementerar Azure SQL Data Discovery:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Implementerar Azure-informationsskydd:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.6 | 14.6 | Kund |

Använd Azure AD RBAC för att kontrollera åtkomsten till data och resurser, annars använd tjänstspecifika åtkomstkontrollmetoder.

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Konfigurera RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.7 | 14.7 | Kund |

Implementera ett verktyg från tredje part, till exempel en automatiserad värdbaserad lösning för att förhindra dataförbränning, för att framtvinga åtkomstkontroller till data även när data kopieras från ett system.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.8 | 14.8 | Kund |

Använd kryptering i vila på alla Azure-resurser. Microsoft rekommenderar att du låter Azure hantera dina krypteringsnycklar, men det finns möjlighet för dig att hantera dina egna nycklar i vissa fall. 

Förstå kryptering i vila i Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Konfigurera kundhanterade krypteringsnycklar:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.9 | 14.9 | Kund |

Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar sker till kritiska Azure-resurser.

Så här skapar du aviseringar för Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhetskontroll: [Sårbarhetshantering](security-control-vulnerability-management.md)
