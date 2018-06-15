---
title: Azure betalning bearbetning modell - Övervakningskrav
description: PCI DSS krav 10
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3d26d31c592c34d1e51c3e2c2d6501edefa8dce
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894942"
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Övervakningskrav för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-10"></a>PCI DSS krav 10

**Spåra och övervaka all åtkomst till nätverksresurser och kreditkortsinformation**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Loggning metoder och möjlighet att spåra användaraktiviteter är viktiga i hindrar, identifiering och minimera effekten av en kompromettering av data. Förekomsten av loggar i alla miljöer kan grundlig spårning, varningar och analys om något går fel. Att fastställa orsaken till ett angrepp är det mycket svårt, omöjligt, utan systemloggar för aktiviteten.

## <a name="pci-dss-requirement-101"></a>PCI DSS krav 10.1

**10.1** implementera granskningshistorik länka all åtkomst till systemkomponenter enskilda användare.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure begränsar åtkomsten till Administration och diagnostiska verktyg till auktoriserad personal med relevanta jobbet ansvar. Microsoft Azure begränsar privilegierad åtkomst till de verktyg som används i produktionsmiljön baserat på minsta privilegium principer. Microsoft Azure registrerar och upprätthåller en logg över alla användaråtkomst till Microsoft Azure komponenterna i miljön plattform.<br /><br />Microsoft Azure platform-komponenter (inklusive OS, CloudNet, Infrastrukturresurser och så vidare) har konfigurerats för att logga in och samla in säkerhetshändelser. Administratören aktiviteten i Microsoft Azure-plattformen loggas. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har utförlig loggning för alla system och användaraktivitet (inklusive CHD loggning). Mer information finns i [PCI vägledning - loggning](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>PCI DSS krav 10.2

**10.2** implementera automatiserad granskningshistoriken för alla systemkomponenter att rekonstruera följande händelser:
- **10.2.1** alla enskilda användare har åtkomst till kreditkortsinformation
- **10.2.2** alla åtgärder som utförs av en person med administratörsbehörighet eller rot
- **10.2.3** åtkomst till alla granskningshistorik
- **10.2.4** ogiltig logiska åtkomstförsök
- **10.2.5** användning av och ändringar av identifiering och autentiseringsmekanismer, inklusive men inte begränsat till, skapa nya konton och rättighetsökning, och alla ändringar, tillägg och borttagningar till konton med rot eller administrativa privilegier
- **10.2.6** initiering, stoppas eller pausas i granskningsloggarna
- **10.2.7** skapas eller tas bort på systemnivå objekt

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure begränsar åtkomsten till Administration och diagnostiska verktyg till auktoriserad personal med relevanta jobbet ansvar. Microsoft Azure begränsar privilegierad åtkomst till de verktyg som används i produktionsmiljön baserat på minsta privilegium principer. Microsoft Azure registrerar och upprätthåller en logg över alla användaråtkomst till Microsoft Azure komponenterna i miljön plattform.<br /><br />Microsoft Azure platform-komponenter (inklusive OS, CloudNet, Infrastrukturresurser och så vidare) har konfigurerats för att logga in och samla in säkerhetshändelser. Administratören aktiviteten i Microsoft Azure-plattformen loggas. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har utförlig loggning för alla system- och användaraktivitet, inklusive CHD loggning. Mer information finns i [PCI vägledning - loggning](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>PCI DSS krav 10.3

**10.3** registrera minst följande granskningsposter av verifieringskedja för alla komponenter för varje händelse:
- **10.3.1** användar-ID
- **10.3.2** typen av händelse
- **10.3.3** datum och tid
- **10.3.4** lyckad eller misslyckad uppgift
- **10.3.5** ursprung för händelse
- **10.3.6** identitet eller namnet på berörda, komponent eller resurs

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure har upprättat procedurer för att synkronisera servrar och nätverksenheter i Microsoft Azure-miljö med NTP Stratum 1 tidsservrar synkroniseras till globala placering System (GPS) satelliter. Synkronisering utförs automatiskt var femte minut. Microsoft Azure är ansvarig för värdar korrekt synkroniseringstid. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore registrerar användar-ID, händelsetyp, datum tidsstämpel, lyckade misslyckades händelser, källan för händelsen och namnet på resursen som krävs av 10.3 kontrollen.|



## <a name="pci-dss-requirement-104"></a>PCI DSS krav 10.4

**10.4** med tidssynkronisering teknik, synkronisera alla kritiska systemklockorna och tider och se till att följande implementeras för att införskaffa, distribuerar och spara tid. 
> [!NOTE]
> Ett exempel på teknik för synkronisering av tid är Network Time Protocol (NTP).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure har upprättat procedurer för att synkronisera servrar och nätverksenheter i Microsoft Azure-miljö med NTP Stratum 1 tidsservrar synkroniseras till globala placering System (GPS) satelliter. Synkronisering utförs automatiskt var femte minut. Microsoft Azure är ansvarig för värdar korrekt synkroniseringstid. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Tidssynkronisering för PaaS-tjänsten utförs av Azure.|



### <a name="pci-dss-requirement-1041"></a>PCI DSS krav 10.4.1

**10.4.1** kritiska system har korrekt och konsekvent tid.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.4](#pci-dss-requirement-10-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Tidssynkronisering för PaaS-tjänsten utförs av Azure.|



### <a name="pci-dss-requirement-1042"></a>PCI DSS krav 10.4.2

**10.4.2** tidsdata skyddas.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.4](#pci-dss-requirement-10-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Tidssynkronisering för PaaS-tjänsten utförs av Azure.|



### <a name="pci-dss-requirement-1043"></a>PCI DSS krav 10.4.3

**10.4.3** tidsinställningar tas emot från branschen accepteras tidskällor.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.4](#pci-dss-requirement-10-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Tidssynkronisering för PaaS-tjänsten utförs av Azure.|



## <a name="pci-dss-requirement-105"></a>PCI DSS krav 10.5

**10.5** säker audit musspårning så att de inte kan ändras.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM och verktyg för ID: N implementeras i Microsoft Azure-miljön. Microsoft Azure använder EWS för att stödja realtid analys av händelser inom driftsmiljön. Generera nära realtid aviseringar om händelser som potentiellt äventyrar systemet mAs och mål. <br /><br />Loggning av händelser som tjänsten, användare och säkerhetsgrupper (webbserverloggar, FTP-server-loggar och så vidare) är aktiverat och lagras centralt. Azure begränsar åtkomsten för att granska loggarna till auktoriserad personal baserat på ansvarsområden. Händelseloggar arkiveras på Azure säker arkivering infrastruktur och bevaras i 180 dagar. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tillhandahåller för granskning av alla element till Azure. Säkerhetskopiera till en extern källa kan utföras av [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>PCI DSS krav 10.5.1

**10.5.1** Begränsa visning av audit musspårning dem med jobbrelaterade behov.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.5](#pci-dss-requirement-10-5). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tillhandahåller för granskning av alla element till Azure. Säkerhetskopiera till en extern källa kan utföras av [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>PCI DSS krav 10.5.2

**10.5.2** skydda audit spår filer från obehöriga ändringar.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.5](#pci-dss-requirement-10-5). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tillhandahåller för granskning av alla element till Azure. Säkerhetskopiera till en extern källa kan utföras av [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>PCI DSS krav 10.5.3

**10.5.3** omedelbart säkerhetskopiera audit spår filer till en centraliserad loggen server eller ett medium som är svår att ändra.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.5](#pci-dss-requirement-10-5). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tillhandahåller för granskning av alla element till Azure. Säkerhetskopiera till en extern källa kan utföras av [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>PCI DSS krav 10.5.4

**10.5.4** skriva loggar för externa tekniker på en säker, central, internt eller enhet.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.5](#pci-dss-requirement-10-5). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tillhandahåller för granskning av alla element till Azure. Säkerhetskopiera till en extern källa kan utföras av [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>PCI DSS krav 10.5.5

**10.5.5** använda-integritet övervakning eller identifiering av ändring av programvara på loggar så att befintliga loggdata inte kan ändras utan att generera aviseringar (även om nya data som läggs till inte bör orsakar en avisering).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.5](#pci-dss-requirement-10-5). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tillhandahåller för granskning av alla element till Azure. Säkerhetskopiera till en extern källa kan utföras av [Azure Backup](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>PCI DSS krav 10.6

**10.6** granska loggarna och säkerhetshändelser för alla komponenter att identifiera avvikelser eller misstänkta aktiviteter.
 
> [!NOTE]
> Logg hämta parsning och aviseringar verktyg kan användas för att uppfylla detta krav.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM och verktyg för ID: N implementeras i Microsoft Azure-miljön. Microsoft Azure använder EWS för att stödja realtid analys av händelser inom driftsmiljön. Generera nära realtid aviseringar om händelser som potentiellt äventyrar systemet mAs och mål. <br /><br />Loggning av händelser som tjänsten, användare och säkerhetsgrupper (webbserverloggar, FTP-server-loggar och så vidare) är aktiverat och lagras centralt. Azure begränsar åtkomsten för att granska loggarna till auktoriserad personal baserat på ansvarsområden. Händelseloggar arkiveras på Azure säker arkivering infrastruktur och bevaras i 180 dagar. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder [Azure Security Center](https://azure.microsoft.com/services/security-center/) att övervaka, rapportera och förhindra avvikelser. [Azure Advisor](/azure/advisor/advisor-security-recommendations) ger en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser.|



### <a name="pci-dss-requirement-1061"></a>PCI DSS krav 10.6.1

**10.6.1** kontrollerar du följande minst dagligen:
- Alla säkerhetshändelser
- Loggar alla systemkomponenter som lagra, hantera och överföra CHD och/eller SAD
- Loggar alla viktiga komponenter
- Loggar på alla servrar och systemkomponenter som utför säkerhetsfunktioner (till exempel brandväggar, identifiering av adressintrång system-intrångsskydd system (ID: N/IP-adresser), authentication-servrar, e-handel omdirigering av servrar, och så vidare).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.6](#pci-dss-requirement-10-6). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder [Azure Security Center](https://azure.microsoft.com/services/security-center/) att övervaka, rapportera och förhindra avvikelser. [Azure Advisor](/azure/advisor/advisor-security-recommendations) ger en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser.|



### <a name="pci-dss-requirement-1062"></a>PCI DSS krav 10.6.2

**10.6.2** granska loggarna för alla andra komponenter med jämna mellanrum baserat på organisationens principer och risker hanteringsstrategi, utifrån organisationens årliga riskbedömning.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 10.6](#pci-dss-requirement-10-6). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder [Azure Security Center](https://azure.microsoft.com/services/security-center/) att övervaka, rapportera och förhindra avvikelser. [Azure Advisor](/azure/advisor/advisor-security-recommendations) ger en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser.|



### <a name="pci-dss-requirement-1063"></a>PCI DSS krav 10.6.3

**10.6.3** följa upp undantag och inkonsekvenser identifieras vid i granskningsprocess.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se avsnittet ”Microsoft Azure” [krav 10.6](#pci-dss-requirement-10-6). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder [Azure Security Center](https://azure.microsoft.com/services/security-center/) att övervaka, rapportera och förhindra avvikelser. [Azure Advisor](/azure/advisor/advisor-security-recommendations) ger en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser.|



## <a name="pci-dss-requirement-107"></a>PCI DSS krav 10.7

**10.7** Behåll spår granskningshistorik för minst ett år med minst tre månader omedelbart tillgängliga för analys (t.ex, online, arkiverade eller återställningsbara från säkerhetskopian).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure behåller granskningsloggarna för ett år, med de senaste 3 månaderna omedelbart tillgängligt via deras interna portal. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har utförlig loggning för alla system och användaraktivitet (inklusive CHD loggning). Mer information finns i [PCI vägledning - loggning](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>PCI DSS krav 10.8

**10.8** **ytterligare krav för endast tjänstleverantörer:** implementerar du en process för att snabbt identifiera och rapportering av fel i kritiska säkerhetssystem, inklusive men inte begränsat till fel på:
- Brandväggar
- ID: N/IP-ADRESSER
- FIM
- Antivirusprogram
- Fysiska åtkomstkontroller
- Logiska åtkomstkontroller
- Granska mekanismer för loggning
- Segmentering kontroller (om sådan används) 

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.



**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder EWS för att stödja realtid analys av händelser inom driftsmiljön. Generera nära realtid aviseringar om händelser som potentiellt äventyrar systemet mAs och mål. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har utförlig loggning för alla system och användaraktivitet (inklusive CHD loggning). Mer information finns i [PCI vägledning - loggning](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>PCI DSS krav 10.8.1

**10.8.1** **ytterligare krav för endast tjänstleverantörer:** svara på fel i alla kritiska säkerhetskontroller inom rimlig tid. Processer för att svara på fel i säkerhetskontroller måste inkludera:
- Återställa säkerhetsfunktioner
- Identifiera och dokumentera varaktighet (datum och tid som från början till slut) för säkerhetsfel
- Identifiera och dokumentera cause(s) skulle misslyckas, inklusive grundorsaken och dokumentera åtgärder som krävs för att adressen grundorsaken
- Identifiera och adressering eventuella säkerhetsproblem som uppstod under felet
- Utföra en riskbedömning för att avgöra om ytterligare åtgärder krävs på grund av ett säkerhetsfel inträffade
- Implementera kontroller för att förhindra att orsaken till felet i igen - återupptar övervakning av säkerhetsåtgärder 

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder EWS för att stödja realtid analys av händelser inom driftsmiljön. Generera nära realtid aviseringar om händelser som potentiellt äventyrar systemet mAs och mål. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har utförlig loggning för alla system och användaraktivitet (inklusive CHD loggning). Mer information finns i [PCI vägledning - loggning](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>PCI DSS krav 10.9

**10.9** Kontrollera att IPSec-principer och operativa procedurer för att övervaka all åtkomst till nätverksresurser och kreditkortsinformation dokumenterade används, och kända för alla berörda parter.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore ger ett användningsfall och en beskrivning av hur CHD hanteras och skyddas.|




