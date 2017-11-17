---
title: Azure betalning bearbetning modell - identitetskrav?
description: PCI DSS krav 8
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Identitetskrav för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-8"></a>PCI DSS krav 8

**Identifiera och autentisera åtkomsten till systemkomponenter**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Tilldela en unik identifiering (ID) till varje person med tillgång garanterar att varje enskild person är unikt ansvarar för att hantera sina åtgärder. När sådana accountability är på plats, utförs av åtgärder som vidtas på kritiska data och system och kan spåras till kända och auktoriserade användare och processer.
Effektivitet med ett lösenord i stort sett bestäms av design och implementering av system för autentisering, särskilt, hur ofta lösenordsförsök kan göras genom att en angripare och säkerhetsmetoder för lösenorden i posten, under överföring och i lagringen.

> [!NOTE]
> Dessa krav gäller för alla konton, inklusive kassan konton med administrativa funktioner och alla konton för att visa och komma åt Kortinnehavarens data eller åtkomst system med kreditkortsinformation. Detta inkluderar konton som används av leverantörer och andra tredje part (till exempel för stöd eller underhåll). Dessa krav gäller inte för konton som används av konsumenterna (t.ex. cardholders). Dock är krav 8.1.1 8.2, 8.5, 8.2.3 via 8.2.5 och 8.1.6 via 8.1.8 inte avsedda att gäller för användarkonton i en kassan betalning programmet som endast har åtkomst till ett nummer i taget för att underlätta en enda transaktion ( till exempel kassören konton).
 
## <a name="pci-dss-requirement-81"></a>PCI DSS krav 8.1

**8.1** definiera och implementera principer och procedurer för att se till att rätt ID användarhantering för icke-consumer användare och administratörer på alla systemkomponenter på följande sätt.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore ger ett användningsfall och en beskrivning för rätt användning av administratörer för distributionen av exemplet.|



### <a name="pci-dss-requirement-811"></a>PCI DSS krav 8.1.1

**8.1.1** tilldela ett unikt ID för alla användare innan de tillåts att åtkomst systemkomponenter eller kreditkortsinformation.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore implementerar Azure Active Directory och Azure Active Directory Role-Based åtkomstkontroll (RBAC) för alla användare ha ett unikt ID. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS krav 8.1.2

**8.1.2** styra tillägg, borttagning och ändring av användar-ID, autentiseringsuppgifter och andra identifierare-objekt.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore implementerar Azure Active Directory och Azure Active Directory Role-Based åtkomstkontroll (RBAC) för alla användare ha ett unikt ID. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS krav 8.1.3

**8.1.3** omedelbart återkalla åtkomst för alla avslutas användare.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore använder Azure Active Directory för användarhantering. Upphävande av användare kan göras i Active Directory.|



### <a name="pci-dss-requirement-814"></a>PCI DSS krav 8.1.4

**8.1.4** ta bort eller inaktivera Inaktiva konton inom 90 dagar.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore använder Azure Active Directory för användarhantering. Den `-enableADDomainPasswordPolicy` alternativet kan ställas in så lösenord upphör att gälla inom 90 dagar.|



### <a name="pci-dss-requirement-815"></a>PCI DSS krav 8.1.5

**8.1.5** hantera-ID som används av tredje part för att komma åt, stöder eller underhålla systemkomponenter via fjärråtkomst på följande sätt:
- Aktivera endast under hur lång tid som behövs och inaktiveras som.
- Övervakade under användning.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure har antagit tillämpliga företagets och organisationens säkerhetsprinciper, inklusive en säkerhetsprincip för Information. Principerna har godkänts, publicerats och kommunicerade till Microsoft Azure. Säkerhetsprincipen Information kräver att åtkomst till Microsoft Azure-resurser för att beviljas åtkomst baserat på affärsjustering med tillgångens ägare auktorisering och begränsad baserat på ”till behovsnivå” och ”lägsta behörighet” principer. Dessutom principen åtgärdas även kraven för access management livscykel, inklusive åtkomst etablering, autentisering, åtkomstauktorisering, borttagande av åtkomstbehörighet och periodiska åtkomst till granskningar. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore demo har implementerat Azure Active Directory och Azure Active Directory Role-Based åtkomstkontroll för att hantera användarnas åtkomst till installationen. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS krav 8.1.6

**8.1.6** gränsen upprepas åtkomstförsök genom att låsa ut det användar-ID efter inte mer än sex försök.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore har implementerat tydlig uppdelning av uppgifter (MATJORD) för alla användare av demonstrationen. Mer information finns i ”” Azure Active Directory Identity Protection ”i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>PCI DSS krav 8.1.7

**8.1.7** ange varaktighet för kontoutelåsning till minst 30 minuter eller tills en administratör aktiverar användar-ID.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa, tillämpa och övervaka en lösenordsprincip som är kompatibla med PCI DSS krav.|



### <a name="pci-dss-requirement-818"></a>PCI DSS krav 8.1.8

**8.1.8** om en session har varit inaktiv under mer än 15 minuter, kräver att användaren autentiseras igen om du vill aktivera terminal eller sessionen igen.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att skapa, tillämpa och övervaka en lösenordsprincip som är kompatibla med PCI DSS krav.|



## <a name="pci-dss-requirement-82"></a>PCI DSS krav 8.2

**8.2** förutom att tilldela ett unikt ID säkerställa rätt användarautentisering för icke-consumer användare och administratörer på alla systemkomponenter genom att använda minst en av följande metoder för att autentisera alla användare:
- Något användaren vet, till exempel lösenord eller lösenfraser
- Något du har, till exempel en token enhet eller smartkort
- Något du är, till exempel en biometriska

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore-implementering för multi-Factor authentication har inaktiverats för att tillhandahålla enkel användning för demonstrationen. Multifaktorautentisering kan implementeras med hjälp av [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>PCI DSS krav 8.2.1

**8.2.1** med stark kryptografi läsa alla autentiseringsuppgifter (till exempel lösenord/fraser) under överföringen och lagringen på alla systemkomponenter.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure har upprättat nyckelhantering procedurer för att hantera krypteringsnycklar under deras livscykel (t.ex. generation, distribution, återkallade certifikat). Microsoft Azure använder Microsofts företagets PKI-infrastruktur. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore tillämpar starka lösenord, beskrivs i distributionsguiden. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS krav 8.2.2

**8.2.2** verifiera användarens identitet innan du ändrar autentiseringsuppgift autentisering, till exempel utföra lösenordsåterställning, etablera nya token eller nya nycklar genereras.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure har upprättat nyckelhantering procedurer för att hantera krypteringsnycklar under deras livscykel (t.ex. generation, distribution, återkallade certifikat). Microsoft Azure använder Microsofts företagets PKI-infrastruktur. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore tillämpar starka lösenord, beskrivs i distributionsguiden. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>PCI DSS krav 8.2.3

**8.2.3** lösenord/lösenfraser måste uppfylla följande:
- Kräv en minsta längd på minst sju tecken.
- Innehåller tecken med både siffror och bokstäver.
Lösenord/lösenfraser måste också ha komplexitet och styrkan minst motsvarar de parametrar som anges ovan.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore tillämpar starka lösenord, beskrivs i distributionsguiden.|



### <a name="pci-dss-requirement-824"></a>PCI DSS krav 8.2.4

**8.2.4** och ändra användarens lösenord/lösenfraser minst en gång var 90: e dag.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore använder Azure Active Directory för användarhantering. Den `-enableADDomainPasswordPolicy` alternativet kan ställas in så lösenord upphör att gälla minst en gång var 90: e dag.|



### <a name="pci-dss-requirement-825"></a>PCI DSS krav 8.2.5

**8.2.5** tillåter inte en enskild att skicka ett nytt lösenord/lösenfras som är identiskt med något av de fyra sista lösenord/lösenfraser han eller hon har använt.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore tillämpar starka lösenord, beskrivs i distributionsguiden. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS krav 8.2.6

**8.2.6** anger lösenord/lösenfraser för första gången och vid återställning till ett unikt värde för varje användare och ändrar omedelbart efter den första användningen.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore tillämpar starka lösenord, beskrivs i distributionsguiden. Mer information finns i [PCI - vägledning för Identitetshantering](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>PCI DSS krav 8.3

**8.3** skydda alla enskilda icke-administrativa konsolåtkomst och alla fjärråtkomst till Kortinnehavarens data miljön (CDE) med hjälp av multifaktorautentisering.

> [!NOTE]
> Multifaktorautentisering kräver att minst två av de tre autentiseringsmetoder (se krav 8.2 beskrivningar av autentiseringsmetoder) används för autentisering. Använder en faktor två gånger (till exempel med två olika lösenord) anses inte multifaktorautentisering.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Azure administratörer krävs för att använda multifaktorautentisering för att komma åt när du utför underhåll och administration till Azure-datorer och servrar. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Multifaktorautentisering har inte implementerats för demonstrationen.|



### <a name="pci-dss-requirement-831"></a>PCI DSS krav 8.3.1

**8.3.1** lägga Multi-Factor authentication för alla icke-konsol åtkomst till CDE för personal med administratörsbehörighet.

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Azure administratörer krävs för att använda multifaktorautentisering för att komma åt när du utför underhåll och administration till Azure-datorer och servrar. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Multifaktorautentisering har inte implementerats för demonstrationen.|



### <a name="pci-dss-requirement-832"></a>PCI DSS krav 8.3.2

**8.3.2** lägga multifaktorautentisering för alla fjärranslutna nätverksåtkomst (både användar- och administratör och inklusive tredjeparts-åtkomst för support och underhåll) från utanför entitetens nätverk.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Azure administratörer krävs för att använda multifaktorautentisering för att komma åt när du utför underhåll och administration till Azure-datorer och servrar. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Multifaktorautentisering har inte implementerats för demonstrationen.|



## <a name="pci-dss-requirement-84"></a>PCI DSS krav 8.4

**8.4** dokumentet och kommunicera autentiseringsprinciper och procedurer för att alla användare, inklusive:
- Hjälp med att välja starka autentiseringsuppgifter
- Riktlinjer för hur användarna ska skydda sina autentiseringsuppgifter
- Instruktioner för inte att återanvända tidigare använda lösenord
- Instruktioner för att ändra lösenord om det finns misstanke lösenordet äventyras

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för följande riktlinjer och dokumentera och kommunikation förfaranden för autentisering och principer för alla användare.|



## <a name="pci-dss-requirement-85"></a>PCI DSS krav 8.5

**8.5** inte använder gruppen delade eller allmänt ID, lösenord eller andra metoder för autentisering på följande sätt:
- Allmän användar-ID har inaktiverats eller tagits bort.
- Det finns inga delade användar-ID för systemadministration och andra viktiga funktioner.
- Delad och allmänna användar-ID används inte för att administrera alla systemkomponenter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Multifaktorautentisering har inte implementerats för demonstrationen.|



### <a name="pci-dss-requirement-851"></a>PCI DSS krav 8.5.1

**8.5.1** **ytterligare krav för endast tjänstleverantörer:** leverantörer med fjärråtkomst till kunden lokala (till exempel för stöd av POS-system eller servrar) måste använda en unik autentisering autentiseringsuppgifter ( till exempel en/lösenfras) för varje kund. 

> [!NOTE]
> Det här kravet är inte avsedd att tillämpa på delade webbhotell åtkomst till sina egna värdmiljön där flera kundmiljöer finns.

**Ansvarsområden:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt för Microsoft Azure-kunder. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt för Microsoft Azure-kunder.|



## <a name="pci-dss-requirement-86"></a>PCI DSS krav 8.6

**8.6** om andra autentiseringsmekanismer används (till exempel fysiska eller logiska säkerhetstoken, smartkort, certifikat, etc.) användning av dessa mekanismer måste tilldelas på följande sätt:
- Autentiseringsmekanismer måste tilldelas ett enskilt konto och inte delas mellan flera konton.
- Fysiska och logiska kontroller måste vara för att säkerställa att endast avsedda kontot kan använda den mekanismen för att få åtkomst.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore skapas tre konton under distributionen: admin, sqladmin, och edna (standardanvändaren loggat in webbprogrammet under körning av demo). Multifaktorautentisering har inte implementerats för demonstrationen. All åtkomst hanteras via [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), vilket hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och tjänster. |



## <a name="pci-dss-requirement-87"></a>PCI DSS krav 8.7

**8.7** all åtkomst till en databas som innehåller kreditkortsinformation (inklusive åtkomst av program, administratörer och andra användare) är begränsad på följande sätt:
- Alla användaråtkomst till användarfrågor av och användaråtgärder i databaser är via programmering.
- Endast databasadministratörer har möjlighet att komma åt direkt eller fråga databaser.
- Program-ID: N för databasprogram kan endast användas av program (och inte av enskilda användare eller andra programprocesser).

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore skyddar alla kreditkortsinformation med Azure Key Vault och kryptering av poster beskrivs i dokumentationen för distribution. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS krav 8.8

**8.8** Kontrollera att IPSec-principer och operativa procedurer för identifiering och autentisering dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Kunder ansvarar för att säkerställa att säkerhetsprinciper och operativa procedurer för identifiering och autentisering dokumenterat, används, och kända för alla berörda parter.|




