---
title: Azure betalning bearbetning modell - krav på lösenord
description: PCI DSS krav 2
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 8fafc277d7410ab3f8f54b0defee127fd6ec2f4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Lösenordskraven för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-2"></a>PCI DSS krav 2

**Använd inte från leverantören standardinställningar för system lösenord och andra parametrar för säkerhet**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Obehörig (externa och interna till en enhet) använder ofta leverantör standard lösenord och andra standardinställningarna för leverantören för att äventyra system. Dessa inställningar och lösenord är kända av hackare communities och identifiera enkelt via offentlig information.

## <a name="pci-dss-requirement-21"></a>PCI DSS krav 2.1
 
**2.1** alltid ändra från leverantören standardinställningarna och ta bort eller inaktivera onödiga standardkonton **innan** installerar ett system i nätverket.
Detta gäller för alla standardlösenordet, inklusive men inte begränsat till de som används av operativsystem, programvara som tillhandahåller tjänster, program- och konton, kassan (Kassaterminaler), Simple Network Management Protocol (SNMP) community strängar, osv.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure Active Directory lösenordskrav principen tillämpas för de nya lösenorden som tillhandahålls av kunder i AADUX-portalen. Kund-initierad självbetjäning lösenordsändringar kräver verifiering av tidigare lösenord. Administratören återställa lösenord måste ändras vid efterföljande inloggningen. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore kräver att användare använder ange starka lösenord för alla användare. Inga exempel eller gäst-konton är aktiverade i demonstrationen.<br /><br />Trådlösa och SNMP har inte implementerats i lösningen.|



### <a name="pci-dss-requirement-211"></a>PCI DSS krav 2.1.1

**2.1.1** för trådlösa miljöer ansluten till Kortinnehavarens datamiljö eller sändande kreditkortsinformation, ändra alla standardvärden för trådlösa leverantör vid installationen, inklusive men inte begränsat till standard trådlösa krypteringsnycklar lösenord, och Gruppsträngar för SNMP.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Trådlösa och SNMP har inte implementerats i lösningen.|



## <a name="pci-dss-requirement-22"></a>PCI DSS krav 2.2

**2.2** utveckla configuration standarder för alla systemkomponenter. Garantera att dessa standarder åtgärda alla kända säkerhetsproblem och är konsekventa med branschen accepteras systemhärdning standarder.
I branschen accepteras systemhärdning standarder kan innefatta, men är inte begränsade till:
- Center för Internet Security (CIS)
- International Organization for Standardization (ISO)
- SysAdmin Audit Network Security (SAN) Institute
- National Institute of Standards Technology (NIST)

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | För Microsoft Azure utvecklar OSSC tekniska säkerhetstjänster teamet configuration säkerhetskrav för datorer i Microsoft Azure-miljö som är konsekventa med branschen godkänd härdning standarder. De här konfigurationerna dokumenteras i systemet baslinjer och relevanta konfigurationsändringar förmedlas till påverkas team (t.ex. IPAK team). Procedurer implementeras för att övervaka kompatibilitet mot configuration säkerhetskrav. Säkerhetskrav för konfigurationen för datorer i Microsoft Azure-miljön är konsekventa med standarder i branschen accepteras härdning och granskas minst en gång om året. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore ger Härdning av alla tjänster i omfånget för Kortinnehavarens datamiljö (CDE). <br /><br />Contoso Webstore distribuerar också de [Azure Security Center](https://azure.microsoft.com/services/security-center/), som tillhandahåller en centraliserad översikt över säkerhetsläget för alla dina Azure-resurser. En överblick över kan du kontrollera att lämpliga säkerhetsåtgärder som är installerade och korrekt konfigurerad och att du snabbt kan identifiera alla resurser som kräver uppmärksamhet.<br /><br />Contoso Webstore använder Operations Management Suite för att logga alla systemändringar i. Logganalys innehåller utförlig loggning av ändringar. Ändringar kan granskas och verifiera noggrannhet. Mer specifika anvisningar finns [PCI - vägledning för Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>PCI DSS krav 2.2.1

**2.2.1** implementera endast en huvudfunktion per server för att förhindra funktionerna som kräver olika säkerhetsnivåer från befintliga samtidigt på samma server. (Till exempel webbservrar, databasservrar och DNS ska implementeras på olika servrar.) 

> [!NOTE]
> Om virtualiseringsteknik används, implementera endast en huvudfunktion per virtual komponent.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tjänster distribueras som PaaS-tjänster. Alla tjänster isoleras och segmenterade nätverkssegmentering.<br /><br />Contoso Webstore använder också ett [App Service miljö (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) tillämpa viktiga metoder. Mer information finns i [PCI vägledning - Apptjänstmiljö](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>PCI DSS krav 2.2.2

**2.2.2** Aktivera endast nödvändiga tjänster, protokoll, Daemon, etc., vilket krävs för systemets funktion.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure-program- och maskinvarukonfigurationer granskas åtminstone kvartalsvis för att identifiera och ta bort alla onödiga funktioner, portar, protokoll och tjänster. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tjänster distribueras som PaaS-tjänster. Alla tjänster isoleras och segmenterade nätverkssegmentering.<br /><br />Contoso Webstore använder också ett [App Service miljö (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) tillämpa viktiga metoder. Mer information finns i [PCI vägledning - Apptjänstmiljö](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>PCI DSS krav 2.2.3

**2.2.3** implementera ytterligare säkerhetsfunktioner för alla nödvändiga tjänster, protokoll eller daemon som anses vara osäker. 

> [!NOTE]
> Om SSL för tidig TLS används måste kraven i bilaga A2 utföras.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tjänster distribueras som PaaS-tjänster. Alla tjänster isoleras och segmenterade nätverkssegmentering. Distributionen innehåller också Härdning av alla tjänster i omfånget för CDE. <br /><br />Contoso Webstore distribuerar också de [Azure Security Center](https://azure.microsoft.com/services/security-center/), som tillhandahåller en centraliserad översikt över säkerhetsläget för alla dina Azure-resurser. En överblick över kan du kontrollera att lämpliga säkerhetsåtgärder som är installerade och korrekt konfigurerad och att du snabbt kan identifiera alla resurser som kräver uppmärksamhet.<br /><br />Contoso Webstore använder också ett [App Service miljö (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) tillämpa viktiga metoder. Mer information finns i [PCI vägledning - Apptjänstmiljö](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>PCI DSS krav 2.2.4

**2.2.4** konfigurera parametrar för att förhindra missbruk system.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure garanterar bara auktoriserad personal kan konfigurera Azure-plattformen säkerhetskontroller med flerfunktionsåtkomstkontroll kontroller och dokumenterade affärsbehov. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder AAD och AD RBAC kan du hantera säkerhetsparametrar distribueras på rätt sätt. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>PCI DSS krav 2.2.5

**2.2.5** ta bort alla onödiga funktioner, till exempel skript, drivrutiner, funktioner, delsystem, filsystem och onödiga webbservrar.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore innehåller dokumentation om hur upprättas gränser. Contosos hotmodell och data flow diagram visar alla tjänster som används och kontroller som är aktiverad.|



## <a name="pci-dss-requirement-23"></a>PCI DSS krav 2.3

**2.3** kryptera alla icke-administrativa konsolåtkomst med stark kryptografi. 

> [!NOTE]
> Om SSL för tidig TLS används måste kraven i bilaga A2 utföras.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure innebär användning av stark kryptering krävs vid åtkomst till hypervisor-infrastruktur. Microsoft Azure garanterar även att kunder som använder Microsoft Azure Management Portal har tillgång till sina service/IaaS-konsoler med stark kryptografi. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore visar hur starkt lösenord kan implementeras i en lösning. Dessutom kan du utföra alla tester för att verifiera att kryptering implementeras i hela lösningen.<br /><br />Contoso Webstore använder också ett [App Service miljö (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) tillämpa viktiga metoder. Mer information finns i [PCI vägledning - Apptjänstmiljö](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>PCI DSS krav 2.4

**2.4** Upprätthåll en förteckning över komponenter som ingår i omfattningen för PCI DSS.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore demo PaaS lösning inventeringen kan ses i angivna dokumentationen. Mer information finns i [PCI vägledning - förinstallerat hanteringslösningar](payment-processing-blueprint.md#management-solutions).|



## <a name="pci-dss-requirement-25"></a>PCI DSS krav 2,5

**2.5** Kontrollera att säkerhetsprinciper och operativa procedurer för att hantera leverantör standardinställningar och andra parametrar är dokumenterade i användning, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore innehåller dokumentation som ger inblick i parametrar och dokument service element. |



## <a name="pci-dss-requirement-26"></a>PCI DSS krav 2.6

**2.6** delade webbhotell måste skydda data för varje entitet värdbaserade miljön och Kortinnehavarens. Dessa providers måste uppfylla vissa krav enligt anvisningarna i *bilaga A: PCI DSS tilläggskrav för providrar för delade värdar.*

**Ansvarsområden:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. Microsoft Azure är inte en delad värdtjänst. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Inte tillämpligt. Microsoft Azure är inte en delad värdtjänst.|




