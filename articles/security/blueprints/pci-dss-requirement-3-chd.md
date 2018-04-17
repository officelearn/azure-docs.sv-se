---
title: Azure betalning bearbetning modell - CHD krav
description: PCI DSS krav 3
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3bbed692bfccaa2a3296ba4697c66e9069b6e914
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>CHD krav för PCI DSS-kompatibel miljöer
## <a name="pci-dss-requirement-3"></a>PCI DSS krav 3

**Skydda lagrade kreditkortsinformation**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Skyddsmetod som kryptering, trunkering, maskning, och hashing är viktiga komponenter av Kortinnehavarens dataskydd. Om en inkräktare kringgår andra säkerhetskontroller och får åtkomst till krypterade data, utan rätt kryptografiska nycklar är data inte kan läsas och kan inte användas som. Andra effektivt sätt att skydda lagrade data bör också övervägas som potentiella risker minskning affärsmöjligheter. Metoder för att minimera risk innehåller till exempel inte lagra kreditkortsinformation såvida absolut nödvändigt, tar bort kreditkortsinformation om fullständig PANORERA inte behövs och inte skicka oskyddade Panorera med slutanvändarens tekniker, till exempel e-post och snabbmeddelanden meddelanden.
Se PCI DSS och PA-DSS ordlista, förkortningar och förkortningar för definitioner av ”stark kryptografi” och andra PCI DSS-villkor.

## <a name="pci-dss-requirement-31"></a>PCI DSS krav 3.1

**3.1** Behåll Kortinnehavarens datalagring så mycket som möjligt genom att implementera data bevaras och avyttring principer, procedurer och processer som innehåller minst följande för lagring av alla Kortinnehavarens data (CHD):
- Begränsa data lagring belopp och förvaring tid som som krävs för rättsliga, och business-krav
- Krav på specifika datalagring för kreditkortsinformation
- Processer för säker borttagning av data när de inte längre behövs
- En kvartalsvis process för att identifiera och ta bort lagrade kreditkortsinformation som överskrider angiven kvarhållning på ett säkert sätt.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure ansvarar för att säkerställa den kundinformation som har utsetts för borttagning är inaktiverade på ett säkert sätt med hjälp av NIST 800 88 kompatibla protokoll som anges i dess säkra avyttring principerna. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tar inte bort eller förstöra alla lagrade CHD. Men alla data som är krypterade och inte primära lagras konto numret (PANORERA) data.<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS krav 3.2

**3.2** lagra inte känsliga autentiseringsdata efter tillstånd (även om krypterade). Återge alla data återställas vid slutförandet av auktoriseringen om för känsliga autentiseringsdata tas emot. 
- Det finns en motivering och 
- Data lagras på ett säkert sätt.
Känsliga autentiseringsdata innehåller data som anges i följande krav 3.2.1 via 3.2.3:


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore tar inte bort eller förstöra alla lagrade CHD; exempeldata lagras endast för demonstration. Men alla data som är krypterade och inte primära konto numret (PANORERA) data lagras.<br /><br />|



### <a name="pci-dss-requirement-321"></a>PCI DSS krav 3.2.1

**3.2.1** lagra inte hela innehållet i alla spår (från magnetiska stripe finns på baksidan av en kort, motsvarande data som finns på ett TPM-chip eller någon annanstans) efter tillstånd. Dessa data kallas också fullständig spåra, spåra, spåra 1, 2 och magnetiska stripe-data. 

> [!NOTE]
> I den normala verksamheten, kanske följande dataelement från magnetiska stripe måste behållas: 
> - Den Kortinnehavarens namn 
> - Primär kontonummer (PANORERA) 
> - Förfallodatum 
> - Service-kod 
>
> Lagra endast dessa dataelement efter behov för företag för att minimera risk.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore lagrar inte det fullständiga innehållet på alla CHD.|



### <a name="pci-dss-requirement-322"></a>PCI DSS krav 3.2.2

**3.2.2** lagra inte kort Verifieringskod eller värde (tre siffror eller fyrsiffrigt tal ut på framsidan eller bakom en betalkort som används för att verifiera kort-inte-presentera transaktioner) efter tillstånd.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore krypterar alla data, inklusive CVV-exempel.|



### <a name="pci-dss-requirement-323"></a>PCI DSS krav 3.2.3

**3.2.3** lagra inte personlig kod (PIN) eller blocket krypterade PIN-kod efter tillstånd.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore sparas inte PIN-kodinformation.|



## <a name="pci-dss-requirement-33"></a>PCI DSS krav 3.3

**3.3** mask PANORERA när visas (de första sex och sista fyra siffrorna är det maximala antalet siffror som ska visas), så att endast personal med legitima företag behöver kan se det fullständiga personliga nätverket. 

> [!NOTE]
> Det här kravet inte ersätter striktare krav för visar kreditkortsinformation – till exempel juridiska eller en betalning kort varumärken krav för kassan (POS) inleveranser.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore döljer primära kontonummer (PANORERA) med transparent datakryptering, alltid krypterade kolumner och dynamisk datamaskning. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-34"></a>PCI DSS krav 3.4

**3.4** läsa PANORERA överallt lagras (inklusive bärbara digitala medier säkerhetskopieringsmedia, och loggar) genom att använda någon av följande metoder:
- Enkelriktade hashvärden baserat på stark kryptografi (hash måste vara av hela PANORERA)
- Trunkering (hash kan inte användas att ersätta det trunkerade segmentet med PANORERING)
- Index-token och Pad (PAD måste på ett säkert sätt lagras)
- Stark kryptografi med associerade nyckelhantering processer och procedurer. 

> [!NOTE]
> Det är en relativt trivial ansträngning för en obehörig person att rekonstruera ursprungliga PANORERA data om de har åtkomst till trunkerade och hashformaterats version av en personliga nätverket. Där hashas och trunkeras versioner av samma PANORERA finns i en entitets-miljö kan finnas ytterligare kontroller för att säkerställa att versionerna av hashformaterats och trunkerat kan korreleras om du vill återskapa det ursprungliga personliga nätverket.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore krypterar alla data som kreditkort och använder Azure Key Vault för hantering av nycklar, hindrar hämtning av CHD.<br /><br />Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-341"></a>PCI DSS krav 3.4.1

**3.4.1** om kryptering används (i stället för fil - eller kolumnnivå databaskryptering), logiska åtkomsten måste hanteras separat och oberoende av operativsystem autentisering och åtkomstkontroll (t.ex, genom att inte använda databaser med lokala användarkonton eller allmänt inloggningsuppgifter). Dekrypteringsnycklar får inte vara associerad med användarkonton. 

> [!NOTE]
> Det här kravet gäller utöver andra PCI DSS kryptering och nyckelhantering krav.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore krypterar alla lagrade data och segregerar trafiken för att förhindra Privilegierade höjning för DevOps-funktioner.<br /><br />Apptjänst-miljön är skyddad och låst, måste det finnas en mekanism för att tillåta för DevOps-versioner eller ändringar som kan krävas, till exempel möjligheten att övervaka ett webbprogram med hjälp av Kudu.<br /><br />En virtuell dator upprättas som en jumpbox (skyddsmiljö host) med följande konfigurationer:<br /><br /><ul><li>[Tillägg för program mot skadlig kod](/azure/security/azure-security-antimalware)</li><li>[Log Analytics övervakning tillägg](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Diagnostik för Virtuella datorer tillägg](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[BitLocker-krypterade Disk](/azure/security/azure-security-disk-encryption)</li></ul>Använda Azure Key Vault justerar med Azure Government och PCI DSS HIPAA krav.|



## <a name="pci-dss-requirement-35"></a>PCI DSS krav 3.5

**3,5-tums** dokument och implementera procedurer för att skydda nycklar som används för att säkra lagrade kreditkortsinformation mot avslöjande och missbruk. 

> [!NOTE]
> Det här kravet gäller för nycklar som används för att kryptera lagrade kreditkortsinformation och gäller även för kryptering av nyckeln nycklar som används för att skydda nycklar för kryptering av data, sådana nycklar för kryptering av nyckeln måste vara minst så stark som nyckel för kryptering av data.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Microsoft Azure säkerställer att kunden nyckelvalv är logiskt isolerade från varandra och logiskt isolerade från management-plan för Key Vault-tjänsten. Key Vault är utformat så att Microsoft inte har någon stående-åtkomst till kundens nyckelvalvet. <br /><br />Nycklar skyddas av Microsoft Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler (HSM).<br /><br />En nyckel som lagras i Microsoft Azure Key Vault kan användas för att skydda en annan nyckel. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore innehåller dokumentation för att illustrera och för att distribuera en lösning för skyddade nyckel för att skydda demo CHD.|



### <a name="pci-dss-requirement-351"></a>PCI DSS krav 3.5.1

**3.5.1** *ytterligare krav för endast tjänstleverantörer:* upprätthålla en dokumenterad beskrivning av den kryptografiska arkitekturen som innehåller:
- Information om alla algoritmer, protokoll och nycklar som används för att skydda Kortinnehavarens data, inklusive viktiga styrka och upphör att gälla datum
- Beskrivning av nyckelanvändning för varje nyckel
- Inventering av alla HSM: er och andra SCDs som används för hantering av nycklar 

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Microsoft Azure säkerställer att kunden nyckelvalv är logiskt isolerade från varandra och logiskt isolerade från management-plan för Key Vault-tjänsten. Key Vault är utformat så att Microsoft inte har någon stående-åtkomst till kundens nyckelvalvet. <br /><br />Nycklar skyddas av Microsoft Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler (HSM).<br /><br />En nyckel som lagras i Microsoft Azure Key Vault kan användas för att skydda en annan nyckel. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore innehåller dokumentation för att illustrera och för att distribuera en lösning för skyddade nyckel för att skydda demo CHD.|



### <a name="pci-dss-requirement-352"></a>PCI DSS krav 3.5.2

**3.5.2** begränsa åtkomsten till kryptografiska nycklar till det minsta antalet nödvändiga custodians.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Key Vault stöder detaljerade åtkomstprinciper, som tillåter en Key Vault ägare att bevilja åtkomst till specifika funktioner för att utföra specifika åtgärder till specifika enheter. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore nyckelhantering är isolerad till ett användarkonto (admin ##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>PCI DSS krav 3.5.3

**3.5.3** lagra hemliga och privata nycklar som används för att kryptera/dekryptera kreditkortsinformation i (minst) av följande sätt vid alla tidpunkter:
- Krypterade med en nyckel kryptera nyckel som är minst så stark som nyckel för kryptering av data och som lagras separat från kryptering av data-nyckel
- I en säker kryptografiska enhet (till exempel en (värd) maskinvarusäkerhetsmodul (HSM) eller punkter godkända punkt för interaktion enhet)
- Som minst två viktiga komponenter för full längd eller viktiga resurser godkänt i enlighet med en bransch - metod 

> [!NOTE]
> Det är inte krävs för att lagra offentliga nycklar i något av dessa formulär.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Nycklar lagras i de specifika nyckelvalv som identifieras av kunden.<br /><br />Key Vault kan användas samtidigt och globalt av flera program, vilket minskar behovet av att kopiera en nyckel och lagra på flera platser. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-354"></a>PCI DSS krav 3.5.4

**3.5.4** lagra kryptografiska nycklar på lägsta möjliga platser.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Nycklar lagras i de specifika nyckelvalv som identifieras av kunden. <br /><br />Key Vault kan användas samtidigt och globalt av flera program, vilket minskar behovet av att kopiera en nyckel och lagra på flera platser. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



## <a name="pci-dss-requirement-36"></a>PCI DSS krav 3,6

**3,6** fullständigt dokumentera och genomföra alla nyckelhantering processer och procedurer för kryptografiska nycklar som används för kryptering av kreditkortsinformation, inklusive följande. 

> [!NOTE]
> Ett stort antal branschstandarder för hantering av nycklar är tillgängliga från olika resurser inklusive NIST som finns på http://csrc.nist.gov.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-361"></a>PCI DSS krav 3.6.1

**3.6.1** generering av starka kryptografiska nycklar

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:** <br /><br />Vid generering av nycklar i Nyckelvalvet, ansvarar Azure för att generera nycklar per kundens specifikationer. Nycklar genereras med hjälp av en HSM. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-362"></a>PCI DSS krav 3.6.2

**3.6.2** säker distribution av kryptografiska nycklar

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Ta din egen nyckel (BYOK)-verktyget kapslar in nyckeln för kund och riktar sig till en specifik säkerhet valvet som är knutna till en viss Azure-prenumeration. Nyckeln kan bara importeras till definierade prenumerationen nyckelvalv för den angivna regionen. Den här processen använder kryptering-procedurer som erhållits från tillverkaren. Kunderna får ett meddelande om att överföringen lyckades. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-363"></a>PCI DSS krav 3.6.3

**3.6.3** säker lagring av kryptografiska nycklar

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Nycklar lagras i HSM: er och skyddas med hjälp av maskinvarutillverkarens kryptografiska säkerhet. Metadata i nycklar lagras i Azure Storage i ett krypterat tillstånd som är unik för varje nyckelvalvet. <br /><br /> |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-364"></a>PCI DSS krav 3.6.4

**3.6.4** kryptografiska viktiga ändringar för nycklar som har nått slutet av deras cryptoperiod (till exempel efter en viss tidsperiod har överskridits och/eller efter en viss mängd chiffertext har producerats av en viss nyckel), som definieras av den associerade leverantören av tillämpningsprogrammet eller viktiga ägare och beroende på bransch bästa praxis och riktlinjer (till exempel NIST Specialpublicering 800-57).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Key Vault stöder funktioner för att uppdatera eller återställa nycklar, som definieras av kunden. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-365"></a>PCI DSS krav 3.6.5

**3.6.5** ur bruk eller ersättning (exempelvis arkivering, förstörelse och/eller återkallade) av nycklar som anses vara nödvändigt när integriteten hos nyckeln har minskats (till exempel utgångspunkten för en medarbetare med kunskap om en nyckel i klartext komponent) eller nycklar misstänks komprometteras. 

> [!NOTE]
> Om avyttras eller ersätts krypteringsnycklar behovet av att behållas, måste dessa nycklar arkiveras på ett säkert sätt (till exempel med hjälp av en nyckel krypteringsnyckel). Arkiverade kryptografiska nycklar bör endast användas för dekryptering/säkerhetsskäl.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Key Vault stöder funktioner för att ta bort eller ersätta, som definieras av kunden. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-366"></a>PCI DSS krav 3.6.6

**3.6.6** om manuell klartext-kryptografiska nyckel-hanteringsåtgärder används dessa åtgärder måste hanteras med hjälp av dela kunskap och dubbel kontroll. 

> [!NOTE]
> Exempel på manuella åtgärder för hantering av nyckel inkludera, men är inte begränsade till: nyckeln produktion, överföring, inläsning, lagring och förstörs.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-367"></a>PCI DSS krav 3.6.7

**3.6.7** förhindra obehörig ersättning av kryptografiska nycklar.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | **För kunder som använder Key Vault:**<br /><br />Nyckeln valv avgränsas logiskt och har inte stöd för cross-directory-auktorisering. Därför kan förhindras obehörig ersättning. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-368"></a>PCI DSS krav 3.6.8

**3.6.8** krav för kryptografiska nyckel custodians formellt bekräftar att de förstår och godkänner deras ansvarsområden skyddar nyckeln.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore nyckelhantering är isolerad till ett användarkonto (admin ##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>PCI DSS krav 3.7

**3.7** Kontrollera att IPSec-principer och operativa procedurer för att skydda lagrade kreditkortsinformation dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore använder Azure Key Vault för alla nyckelhantering. Mer information finns i [PCI vägledning - kryptering](payment-processing-blueprint.md#encryption-and-secrets-management).|




