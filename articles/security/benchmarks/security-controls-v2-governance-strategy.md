---
title: Azures säkerhets benchmark v2 – styrning och strategi
description: Azure-säkerhet för benchmark v2 och styrning och strategi
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ae0930e0845e8e8bd6dc4571dc3e8e27491a7be6
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408800"
---
# <a name="security-control-v2-governance-and-strategy"></a>Säkerhets kontroll v2: styrning och strategi

Styrning och strategi ger vägledning för att säkerställa en konsekvent säkerhets strategi och dokumenterad styrnings metod för att hjälpa och upprätthålla säkerhets garantier, inklusive att upprätta roller och ansvar för de olika moln säkerhets funktionerna, en enhetlig teknisk strategi och stöd för principer och standarder.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och data skydd

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Data klassificerings standard i enlighet med affärs riskerna

- Insyn i säkerhets organisationen för risker och till gångs inventering 

- Godkännande av säkerhets organisation för Azure-tjänster för användning 

- Säkerhet för till gångar via deras livs cykel

- Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering

- Användning av data skydds funktioner i Azure Native och tredje part

- Krav på data kryptering för överförings-och rest-användnings fall

- Lämpliga kryptografiska standarder

Mer information finns i följande referenser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%252fsecurity%252fcompass%252fbreadcrumb%252ftoc.json&toc=%252fsecurity%252fcompass%252ftoc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../fundamentals/encryption-overview.md)

- [Ramverk för moln införande – metod tips för Azure Data Security och kryptering](../fundamentals/data-encryption-best-practices.md?amp;bc=%252fazure%252fcloud-adoption-framework%252f_bread%252ftoc.json&toc=%252fazure%252fcloud-adoption-framework%252ftoc.json)

- [Hantering av Azure Security-inventarier](security-controls-v2-asset-management.md)

- [Azure Security benchmark – data skydd](security-controls-v2-data-protection.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definiera strategi för företags segmentering

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Upprätta en företagsomfattande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhets separation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet/åtkomst modeller och kontroller för mänsklig process.

- [Vägledning om segment strategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segment strategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverks segmentering med strategi för företags segmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definiera säkerhets position hanterings strategi

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Kontinuerligt mäta och minimera risker för enskilda till gångar och den miljö som de finns i. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter osv.

- [Azure Security benchmark – position och sårbarhets hantering](security-controls-v2-posture-vulnerability-management.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: justera organisations roller, ansvars områden och Accountabilities

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-4 | Saknas | PL, PM |

Se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera att tillhandahålla tydliga ansvar för säkerhets beslut, utbilda alla på den delade ansvars modellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: definiera nätverks säkerhets strategi

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Centraliserad nätverks hantering och säkerhets ansvar

- Modell för virtuella nätverks segment justerade med strategin för företags segmentering

- Reparations strategi i olika hot och angrepps scenarier

- Strategi för Internet Edge och inkommande och utgående

- Hybrid moln och lokal strategi för samanslutning

- Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Mer information finns i följande referenser:

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhet för Azure-säkerhet](security-controls-v2-network-security.md)

- [Översikt över Azure Network Security](../fundamentals/network-overview.md)

- [Arkitektur strategi för företags nätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definiera identitets-och privilegie rad åtkomst strategi

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Upprätta en Azure Identity-och Privileged Access-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Ett centraliserat identitets-och autentiserings system och dess samanslutning med andra interna och externa identitets system

- Starka autentiseringsmetoder i olika användnings fall och villkor

- Skydd av privilegierade användare

- Avvikande användar aktiviteter, övervakning och hantering  

- Process för användar identitets-och åtkomst granskning och avstämning

Mer information finns i följande referenser:

- [Azure-säkerhet för prestandatest – identitets hantering](security-controls-v2-identity-management.md)

- [Azure Security benchmark – privilegie rad åtkomst](security-controls-v2-privileged-access.md)

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över Azure Identity Management-säkerhet](../fundamentals/identity-management-overview.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definiera strategi för loggning och hot svar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med hög kvalitets varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps) 

- En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk 

- Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad

- Centraliserad synlighet och korrelations information om hot, med SIEM, interna Azure-funktioner och andra källor 

- Kommunikation och meddelande plan med dina kunder, leverantörer och offentliga intressen

- Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning

- Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Mer information finns i följande referenser:
- [Azure-säkerhet för benchmark-loggning och hot identifiering](security-controls-v2-logging-threat-detection.md)

- [Azure-säkerhet benchmark – incident svar](security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management och Monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: definiera strategi för säkerhets kopiering och återställning

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| GS-8 | 10 | CP |

Upprätta en Azures säkerhets kopierings-och återställnings strategi för din organisation. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Återställnings tids mål (RTO) och återställnings punkt mål (för återställnings punkt) i enlighet med dina affärs återhämtnings mål

- Redundans design i dina program och infrastruktur konfiguration

- Skydd av säkerhets kopiering med åtkomst kontroll och data kryptering

Mer information finns i följande referenser:
- [Prestandatest för Azure-säkerhet – säkerhets kopiering och återställning](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework – säkerhets kopiering och haveri beredskap för Azure-program](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure adoption Framework – verksamhets kontinuitet och haveri beredskap](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alla intressenter](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)