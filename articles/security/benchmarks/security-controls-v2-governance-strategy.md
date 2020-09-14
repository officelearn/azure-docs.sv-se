---
title: Azures säkerhets benchmark v2 – styrning och strategi
description: Azure-säkerhet för benchmark v2 och styrning och strategi
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059393"
---
# <a name="security-control-governance-and-strategy"></a>Säkerhets kontroll: styrning och strategi

Säkerhets kopiering och återställning täcker kontroller för att säkerställa att säkerhets kopiering av data och konfigurationer på olika tjänst nivåer utförs, verifieras och skyddas.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och skydd

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Data klassificerings standard i enlighet med affärs riskerna

-   Insyn i säkerhets organisationen för risker och till gångs inventering 

-   Godkännande av säkerhets organisation för Azure-tjänster för användning 

-   Säkerhet för till gångar via deras livs cykel

-   Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krav på data kryptering för överförings-och rest-användnings fall

-   Lämpliga kryptografiska standarder

Obs! din till gångs hantering och din skydds metod för molnet och lokalt kan vara olika beroende på flera faktorer, till exempel program tjänst/värd modell, affärs risker och krav på efterlevnad. 

- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../fundamentals/encryption-overview.md)

- [Ramverk för moln införande – metod tips för Azure Data Security och kryptering](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Hantering av Azure Security-inventarier](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security benchmark – data skydd](/azure/security/benchmarks/security-controls-v2-data-protection)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: definiera säkerhets position hanterings strategi

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

Kontinuerligt mäta och minimera risker för enskilda till gångar och den miljö som de finns i. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter osv.

- [Azure Security benchmark – position och sårbarhets hantering](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: justera organisations roller, ansvars områden och Accountabilities

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-3 | E.t. | PL, PM |

Se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera att tillhandahålla tydliga ansvar för säkerhets beslut, utbildning på den delade ansvars modellen och teknisk utbildning för moln säkerhet. 

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](https://aka.ms/AzSec1)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](https://aka.ms/AzSec2)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](https://aka.ms/AzSec3)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: definiera nätverks säkerhets strategi

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-4 | 9 | CA, SC |

Upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverks hantering och säkerhets ansvar

-   Modell för virtuella nätverks segment justerade med strategin för företags segmentering

-   Reparations strategi i olika hot och angrepps scenarier

-   Strategi för Internet Edge och inkommande och utgående

-   Hybrid moln och lokal strategi för samanslutning

-   Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Obs! din nätverks säkerhets metod för molnet och lokalt kan vara olika beroende på flera faktorer, till exempel program tjänst modell, Hot exponering och hybrid nätverks installation.

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](https://aka.ms/AzSec11)

- [Säkerhet för Azure-säkerhet](/azure/security/benchmarks/security-controls-v2-network-security)

- [Översikt över Azure Network Security](../fundamentals/network-overview.md)

- [Arkitektur strategi för företags nätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: definiera identitets-och privilegie rad åtkomst strategi

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Upprätta en Azure Identity-och Privileged Access-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets-och autentiserings system och dess samanslutning med andra interna och externa identitets system

-   Starka autentiseringsmetoder i olika användnings fall och villkor

-   Skydd av privilegierade användare

-   Avvikande användar aktiviteter, övervakning och hantering  

-   Process för användar identitets-och åtkomst granskning och avstämning

Obs! din identitets-och privilegie rad åtkomst metod för molnet och lokalt kan vara olika beroende på flera faktorer, till exempel åtkomst Sök väg för data/program, tjänst modell och kund-/partner åtkomst strategi.

- [Azure-säkerhet för prestandatest – identitets hantering](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security benchmark – privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](https://aka.ms/AzSec11)

- [Översikt över Azure Identity Management-säkerhet](../fundamentals/identity-management-overview.md) 

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: definiera strategi för loggning och hot svar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med hög kvalitets varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps) 

-   En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk 

-   Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad

-   Centraliserad synlighet och korrelations information om hot, med SIEM, interna Azure-funktioner och andra källor 

-   Kommunikation och meddelande plan med dina kunder, leverantörer och offentliga intressen

-   Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Obs! din inloggnings-och hot identifierings metod för molnet och lokalt kan vara olika beroende på flera faktorer, till exempel krav på efterlevnad, Threat landskap och identifiering och reparation. 

- [Azure-säkerhet för benchmark-loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure-säkerhet benchmark – incident svar](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](https://aka.ms/AzSec11)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management och Monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: definiera strategi för säkerhets kopiering och återställning

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-7 | 10 | CP |

Upprätta en Azures säkerhets kopierings-och återställnings strategi för din organisation. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Återställnings tids mål (RTO) och återställnings punkt mål (för återställnings punkt) i enlighet med dina affärs återhämtnings mål

-   Redundans design i dina program och infrastruktur konfiguration

-   Skydd av säkerhets kopiering med åtkomst kontroll och data kryptering

Obs! din säkerhets kopierings-och återställnings metod för molnet och den lokala tjänsten kan vara olika beroende på flera faktorer, till exempel infrastrukturens redundans, program tjänst/värd modell och krav på efterlevnad.

- [Prestandatest för Azure-säkerhet – säkerhets kopiering och återställning](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Azures ramverk för well-Architecture – säkerhets kopiering och haveri beredskap för Azure-program](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure adoption Framework – verksamhets kontinuitet och haveri beredskap](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

