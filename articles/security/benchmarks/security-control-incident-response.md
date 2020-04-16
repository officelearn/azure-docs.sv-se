---
title: Azure Security Control - Incident Response
description: Incidenthantering för Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408424"
---
# <a name="security-control-incident-response"></a>Säkerhetskontroll: Incident response

Skydda organisationens information, liksom dess rykte, genom att utveckla och implementera en infrastruktur för incidenthantering (t.ex. planer, definierade roller, utbildning, kommunikation, ledningstillsyn) för att snabbt upptäcka en attack och sedan effektivt begränsa skadan, eliminera angriparens närvaro och återställa integriteten i nätverket och systemen.

## <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Kund |

Skapa en incidenthanteringsguide för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faser av incidenthantering/hantering från identifiering till granskning efter incidenten.  

- [Vägledning om hur du bygger din egen process för hantering av säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center anatomi av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utnyttja NIST:s guide för hantering av datasäkerhetsincidenter för att underlätta skapandet av en egen incidenthanteringsplan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 10.2 | 19.8 | Kund |

Security Center tilldelar varje avisering en allvarlighetsgrad för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen. 

Dessutom tydligt markera abonnemang (för ex. produktion, icke-prod) med hjälp av taggar och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  Det är ditt ansvar att prioritera reparation av aviseringar baserat på kritiskt azure-resurser och miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Använda taggar för att ordna dina Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 10.3 | 19 | Kund |

Utför övningar för att testa systemens funktioner för incidentsvar på en regelbunden kadens för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och revidera planen efter behov.

- [NIST:s publikation - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 10.4 | 19.5 | Kund |

Kontaktinformation för säkerhetsincidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part. Granska incidenter i efterhand för att säkerställa att problemen löses.

- [Så här ställer du in säkerhetskontakten för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 10.5 | 19.6 | Kund |

Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt. Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna till Azure Sentinel.

- [Konfigurera kontinuerlig export](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Så här streamar du aviseringar till Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsvarningar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 10.6 | 19 | Kund |

Använd funktionen Arbetsflödesautomatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" på säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatiserings- och logikappar för arbetsflöde](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Penetrationstester och röda teamövningar](security-control-penetration-tests-red-team-exercises.md)