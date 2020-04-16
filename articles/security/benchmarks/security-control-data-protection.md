---
title: Azure Security Control - Dataskydd
description: Dataskydd för Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408550"
---
# <a name="security-control-data-protection"></a>Säkerhetskontroll: Dataskydd

Dataskyddsrekommendationer fokuserar på att ta itu med problem som rör kryptering, åtkomstkontrollistor, identitetsbaserad åtkomstkontroll och granskningsloggning för dataåtkomst.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.1 | 13.1 | Kund |

Använd taggar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Så här skapar och använder du taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.2 | 13.2, 2.10 | Kund |

Implementera isolering med hjälp av separata prenumerationer och hanteringsgrupper för enskilda säkerhetsdomäner som miljötyp och datakänslighetsnivå. Du kan begränsa åtkomstnivån till dina Azure-resurser som dina program och företagsmiljöer kräver. Du kan styra åtkomsten till Azure-resurser via Azure Active Directory-rollbaserad åtkomstkontroll. 

- [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Så här skapar du hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Så här skapar och använder du taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.3 | 13.3 | Delad |

Utnyttja en tredjepartslösning från Azure Marketplace på nätverksper perimeterer som övervakar obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetsexperter varnas.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och skyddar mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå kunddataskydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.4 | 14.4 | Delad |

Kryptera all känslig information under transport. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla om TLS 1.2 eller senare.

Följ Azure Security Center-rekommendationer för kryptering i vila och kryptering under överföring, där så är tillämpligt.

- [Förstå kryptering under överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.5 | 14.5 | Delad |

När ingen funktion är tillgänglig för din specifika tjänst i Azure använder du ett aktivt identifieringsverktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens tekniksystem, inklusive de som finns på plats eller hos en fjärrtjänstleverantör, och uppdaterar organisationens inventering av känslig information.

Använd Azure Information Protection för att identifiera känslig information i Office 365-dokument.

Använd Azure SQL Information Protection för att hjälpa till med klassificering och märkning av information som lagras i Azure SQL-databaser.

- [Implementerar Azure SQL Data Discovery](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Implementerar Azure-informationsskydd](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Förstå kunddataskydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använda rollbaserad åtkomstkontroll för att kontrollera åtkomsten till resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.6 | 14.6 | Kund |

Använd Azure AD RBAC för att kontrollera åtkomsten till data och resurser, annars använd tjänstspecifika åtkomstkontrollmetoder.

- [Konfigurera RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.7 | 14.7 | Delad |

Om det behövs för efterlevnad av beräkningsresurser implementerar du ett verktyg från tredje part, till exempel en automatiserad värdbaserad lösning för att förhindra data, för att framtvinga åtkomstkontroller till data även när data kopieras från ett system.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå kunddataskydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.8 | 14.8 | Kund |

Använd kryptering i vila på alla Azure-resurser. Microsoft rekommenderar att du låter Azure hantera dina krypteringsnycklar, men det finns möjlighet för dig att hantera dina egna nycklar i vissa fall. 

- [Förstå kryptering i vila i Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Konfigurera kundhanterade krypteringsnycklar](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 4.9 | 14.9 | Kund |

Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar sker till kritiska Azure-resurser.

- [Så här skapar du aviseringar för Azure Activity Log-händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Sårbarhetshantering](security-control-vulnerability-management.md)