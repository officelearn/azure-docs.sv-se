---
title: Azure betalning bearbetning modell - krav
description: PCI DSS krav 7
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: fb16a7973022421525e13313f53f615120caa38a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Krav för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-7"></a>PCI DSS krav 7

**Begränsa åtkomsten till kreditkortsinformation företag behöver känna till**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

För att säkerställa att viktiga data kan bara användas av auktoriserad personal, måste system och processer vara på plats för att begränsa åtkomsten baserat på behöver känna till och enligt ansvarsområden.

”Behöver känna till” är när åtkomst behörighet till ett minimum av data och behörigheter som krävs för att utföra en uppgift.

## <a name="pci-dss-requirement-71"></a>PCI DSS krav 7.1

**7.1** begränsa åtkomst till komponenter och kreditkortsinformation till endast de personer vars arbetsuppgifter kräver sådan åtkomst.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure tillämpar befintliga ISMS principer angående Azure personal åtkomst till Azure systemkomponenter, verifiering av access control effektivitet, som ger Just-In-Time administrativ åtkomst, återkalla åtkomst när du inte längre behövs och att se till att personal åtkomst till Azure-plattformen miljön har ett företag behöver. Azure åtkomst till kundmiljöer är mycket begränsad och tillåts endast med kundens godkännande.<br /><br />Procedurer har fastställts för att begränsa fysisk åtkomst till datacenter att behöriga anställda, leverantörer, konsulter och besökare. Säkerhetskontroll och checka in krävs för personal som kräver tillfällig åtkomst till funktionen inre data center. Tillgångsloggar granskas av Azure team varje kvartal. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Kunder ansvarar för att begränsa åtkomst till komponenter och kreditkortsinformation till endast de personer vars arbetsuppgifter kräver sådan åtkomst. Detta inkluderar att begränsa och begränsa åtkomsten till Azure-hanteringsportalen samt ange konton eller roller med behörighet att skapa, ändra eller ta bort PaaS-tjänster.|



### <a name="pci-dss-requirement-711"></a>PCI DSS krav 7.1.1

**7.1.1** definiera behövs för varje roll, inklusive:
- Komponenterna och data systemresurser som varje roll som behöver komma åt för arbetsuppgifter
- Privilegier krävs (till exempel användare, administratör, etc.) för åtkomst till resurser

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Kunder ansvarar för att definiera och dokumentera godkännandeprocessen för användar-ID, definiera åtminstone behörighet, att begränsa åtkomsten till kreditkortsinformation, med unika ID: N, tillhandahåller uppdelning av uppgifter och återkalla åtkomst när det inte längre behövs.|



### <a name="pci-dss-requirement-712"></a>PCI DSS krav 7.1.2

**7.1.2** begränsa åtkomsten till Privilegierade användar-ID till lägsta privilegier som krävs för att utföra ansvarsområden.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure har antagit tillämpliga företagets och organisationens säkerhetsprinciper, inklusive en säkerhetsprincip för Information. Principerna har godkänts, publiceras och meddelas till Windows Azure. Säkerhetsprincip för Microsoft Azure Information kräver att åtkomst till Microsoft Azure tillgångar beviljas vara baserat på affärsjustering med tillgångens ägare auktorisering och baserat på ”till behovsnivå” och ”lägsta behörighet” principer. Principen åtgärdas även kraven för access management livscykel, inklusive åtkomst etablering åtkomstauktorisering, autentisering borttagning av åtkomstbehörigheter, och granskar periodiska åtkomst. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Användarroller är begränsade till uppgifter baserat på scenariot dokumenterade demo.|



### <a name="pci-dss-requirement-713"></a>PCI DSS krav 7.1.3

**7.1.3** tilldela åtkomst baserat på enskilda personal Jobbklassificering och funktion.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Användarroller är begränsade till uppgifter baserat på scenariot dokumenterade demo.|



### <a name="pci-dss-requirement-714"></a>PCI DSS krav 7.1.4

**7.1.4** kräver dokumenterade godkännande av auktoriserade parterna att ange de behörigheter som krävs.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Kunder ansvarar för att begränsa åtkomst till komponenter och kreditkortsinformation till endast de personer vars arbetsuppgifter kräver sådan åtkomst. Detta inkluderar att begränsa och begränsa åtkomsten till Azure-hanteringsportalen samt ange konton eller roller med behörighet att skapa, ändra eller ta bort PaaS-tjänster.|



## <a name="pci-dss-requirement-72"></a>PCI DSS krav 7.2

**7.2** upprätta ett system för åtkomst för komponenter som begränsar åtkomst baserat på en användare behöver, och är inställd på ”neka alla” om du inte uttryckligen anges.
Access control systemet måste innefatta följande:
- 7.2.1 täckning för alla systemkomponenter.
- 7.2.2 tilldelningen av behörighet till enskilda användare baserat på Jobbklassificering och funktion.
- 7.2.3 standardinställningen ”neka alla”.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Active Directory för att begränsa åtkomsten till vissa användare. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>PCI DSS krav 7.3

**7.3** Kontrollera att IPSec-principer och operativa procedurer för att begränsa åtkomsten till kreditkortsinformation dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore dokumentationen innehåller ett användningsfall och en beskrivning som använder CHD och hur CHD används.|




