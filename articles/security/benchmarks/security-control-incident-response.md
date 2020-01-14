---
title: Azure säkerhets kontroll – incident svar
description: Incident svar för säkerhets kontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934407"
---
# <a name="security-control-incident-response"></a>Säkerhets kontroll: incident svar

Skydda organisationens information, samt dess rykte, genom att utveckla och implementera en infrastruktur för incident svar (t. ex. planer, definierade roller, utbildning, kommunikation, hanterings övervakning) för att snabbt upptäcka ett angrepp och sedan innehåller en effektiv skada, utrota angriparens närvaro och återställer nätverkets och systemens integritet.

## <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Kund |

Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

Konfigurera automatisering av arbets flöden i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Vägledning om hur du skapar en egen svars process för säkerhets incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center: s uppbyggnad av en incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 10.2 | 19,8 | Kund |

Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

## <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 10,3 | 19 | Kund |

Genomför övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 10,4 | 19,5 | Kund |

Kontakt information om säkerhets incidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olagligt eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

Så här ställer du in Azure Security Center säkerhets kontakt:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 10.5 | 19,6 | Kund |

Exportera dina Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

Så här konfigurerar du kontinuerlig export:

https://docs.microsoft.com/azure/security-center/continuous-export

Strömma aviseringar till Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 10,6 | 19 | Kund |

Använd funktionen för automatisering av arbets flöde i Azure Security Center att automatiskt utlösa svar via &quot;Logic Apps&quot; på säkerhets aviseringar och rekommendationer.

Konfigurera automatisering av arbets flöde och Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhets kontroll: [inträngande tester och röda team övningar](security-control-penetration-tests-red-team-exercises.md)