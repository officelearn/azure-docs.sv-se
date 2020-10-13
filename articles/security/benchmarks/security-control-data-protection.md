---
title: Azure säkerhets kontroll – data skydd
description: Skydd av Azure Security Control-data
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f2162ee6df551e1bc64741229aec99d5e697fd29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91395999"
---
# <a name="security-control-data-protection"></a>Säkerhets kontroll: data skydd

Data skydds rekommendationer fokuserar på att hantera problem som rör kryptering, åtkomst kontrol listor, identitetsbaserade åtkomst kontroller och gransknings loggning för data åtkomst.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4.1 | 13,1 | Kund |

Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4.2 | 13,2, 2,10 | Kund |

Implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. Du kan begränsa åtkomst nivån till dina Azure-resurser som dina program och företags miljöer kräver. Du kan styra åtkomsten till Azure-resurser via rollbaserad åtkomst kontroll i Azure (Azure RBAC). 

- [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4.3 | 13,3 | Delad |

Utnyttja en lösning från en tredje part från Azure Marketplace på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar, samtidigt som de meddelar information om informations säkerhet.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4.4 | 14,4 | Delad |

Kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS 1,2 eller senare.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

- [Förstå kryptering i överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4,5 | 14,5 | Delad |

Om det inte finns någon funktion för din tjänst i Azure använder du ett aktivt identifierings verktyg från tredje part för att identifiera all känslig information som lagras, bearbetas eller överförs av organisationens teknik system, inklusive de som finns på plats eller på en fjärrtjänstprovider, och uppdaterar organisationens känsliga informations inventering.

Använd Azure Information Protection för att identifiera känslig information i Microsoft 365 dokument.

Använd Azure SQL-Information Protection för att hjälpa till med klassificering och märkning av information som lagras i Azure SQL Database.

- [Implementera identifiering av Azure SQL-data](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Implementera Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4,6 | 14,6 | Kund |

Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till data och resurser, Använd annars tjänstens speciella metoder för åtkomst kontroll.

- [Så här konfigurerar du Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4.7 | 14,7 | Delad |

Om det krävs för att göra beräknings resurser implementerar du ett verktyg från tredje part, till exempel en automatiserad värdbaserade lösning för data förlust skydd, för att genomdriva åtkomst kontroller till data även när data kopieras av ett system.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4,8 | 14,8 | Kund |

Använd kryptering i vila på alla Azure-resurser. Microsoft rekommenderar att Azure hanterar dina krypterings nycklar, men det finns möjlighet att hantera dina egna nycklar i vissa instanser. 

- [Förstå kryptering i vila i Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Konfigurera kundens hanterade krypterings nycklar](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 4,9 | 14,9 | Kund |

Använd Azure Monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll:  [sårbarhets hantering](security-control-vulnerability-management.md)