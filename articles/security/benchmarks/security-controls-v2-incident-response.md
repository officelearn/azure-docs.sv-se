---
title: Azure Security benchmark v2 – incident svar
description: Incident svar för Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ba7391b3b524f7532101083a444aab6059e8a10
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408763"
---
# <a name="security-control-v2-incident-response"></a>Säkerhets kontroll v2: incident svar

Incident svar omfattar kontroller i incident svars livs cykel-förberedelse, identifiering och analys, inne slutning och efter incident aktiviteter. Detta inkluderar användning av Azure-tjänster som Azure Security Center och Sentinel för att automatisera incident svars processen.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat dessa processer för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företags miljön](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Referens guide för incident svar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hotinformation](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: förberedelse – installations incident meddelande

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Konfigurera kontakt information för säkerhets incidenter i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../../security-center/security-center-provide-security-contact-details.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda ASC data Connector för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviserings regler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av export funktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

- [Så här konfigurerar du export](../../security-center/continuous-export.md)

- [Strömma aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hotinformation](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: identifiering och analys – Undersök en incident

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter, för att bygga en fullständig vy över vad som hände. Diverse loggar bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika att fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Data källorna för undersökningen innehåller de centraliserade loggnings källor som redan har samlats in från tjänster som omfattas och som kör system, men kan även innehålla:

- Nätverks data – Använd Nätverks säkerhets grupper flödes loggar, Azure Network Watcher och Azure Monitor för att avbilda nätverks flödes loggar och annan analys information. 

- Ögonblicks bilder av system som körs: 

    - Använd ögonblicks bild funktionen i Azure Virtual Machine för att skapa en ögonblicks bild av den aktiva system disken. 

    - Använd operativ systemets interna minnes dumpnings funktion för att skapa en ögonblicks bild av det aktiva systemets minne.

    - Använd ögonblicks bild funktionen i Azure-tjänsterna eller din program varu funktion för att skapa ögonblicks bilder av de system som körs.

Azure Sentinel tillhandahåller omfattande data analyser i praktiskt taget alla logg källor och en ärende hanterings Portal för att hantera den fullständiga livs cykeln för incidenter. Informations information under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicks bild av en Windows-dators disk](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicks bild av en Linux-dators disk](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure stöd för diagnostikinformation och insamling av minnes dum par](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersök incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hotinformation](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: identifiering och analys – prioritera incidenter

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Tillhandahålla en kontext till analytiker på vilka incidenter kan fokusera på den första baserat på aviseringens allvarlighets grad och till gångens känslighet. 

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera det som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera dessutom resurser med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hotinformation](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: inne slutning, utrotning och återställning – automatisera incident hanteringen

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatisera manuella återkommande uppgifter för att påskynda svars tiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar hur många incidenter en analytiker kan hantera. Manuella uppgifter ökar också analytikerns utmattning, vilket ökar risken för mänskligt fel som orsakar fördröjningar och minskar risken för att analytikerna ska kunna fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiserings funktioner för arbets flöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en Spelbok för att svara på inkommande säkerhets aviseringar. Spelbok tar åtgärder, till exempel att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera automatisering av arbets flöde i Security Center](../../security-center/workflow-automation.md)

- [Konfigurera automatiska hot svar i Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska hot svar i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hotinformation](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)