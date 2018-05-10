---
title: Azure betalning bearbetning modell - säker systemkrav
description: PCI DSS krav 6
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2f6f1bfc853f261eecf5357cef5d3e3d972781b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Säker systemkrav för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-6"></a>PCI DSS krav 6

**Utveckla och underhålla säkra system och program**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Skrupelfria personer använder säkerhetsproblem för att få privilegierad åtkomst till system. Många av problemen åtgärdas genom tillverkarens säkerhetskorrigeringar, som måste installeras av de enheter som hanterar system. Alla system måste ha alla lämpliga programuppdateringar för att skydda mot utnyttjande och röjande av kreditkortsinformation för angripare och skadlig programvara.

> [!NOTE]
> Lämpliga programuppdateringar är de korrigeringar som har utvärderats och testats tillräckligt för att fastställa att uppdateringsfilerna inte står i konflikt med befintliga säkerhetskonfigurationer. Internt utvecklade program kan många säkerhetsrisker undvika med hjälp av standard systemprocesser för utveckling och säkra kodning tekniker.

## <a name="pci-dss-requirement-61"></a>PCI DSS krav 6.1

**6.1** upprätta en process för att identifiera säkerhetsproblem med välkända utanför källor säkerhetsproblem säkerhetsinformation och tilldela en risk rangordning (till exempel som ”hög”, ”medel”, eller ”låg”) till nyligen identifierade säkerhet säkerhetsproblem.

> [!NOTE]
> Risk rangordning ska baseras på branschstandardens rekommendationer samt hänsyn till potentiella påverkan. Kriterierna för rangordning säkerhetsproblem kan exempelvis innehålla beaktandet av CVSS-resultat och/eller klassificering av leverantörs- och typ av datorer som påverkas. 
> 
> Metoder för utvärdering av säkerhetsrisker och tilldela risk klassificeringar varierar beroende på miljö och riskbedömning strategin för en organisation. Risk rangordning, åtminstone, identifiera alla sårbarheter betraktas som en ”hög risk” i miljön. Förutom rangordning risk kan säkerhetsproblem betraktas som ”kritiska” om de utgör ett nära förestående hot mot miljön påverkan kritiska system, och/eller skulle leda till potentiella hot om de inte åtgärdas. Exempel på kritiska system kan inkludera säkerhetssystem, offentliga enheter och datorer, databaser och andra system som lagrar process, eller överför kreditkortsinformation.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Procedurer har upprättats och genomförs för att söka efter säkerhetsproblem i hypervisor-värdar i scope-gräns. Säkerhetsproblem genomsökning utförs på server-operativsystem, databaser och nätverksenheter med lämpliga säkerhetsrisken genomsökning verktyg. Sårbarhetsgenomsökningar utförs på kvartalsbasis som minimum. Microsoft Azure-kontrakt med oberoende experter att utföra intrång tester för Microsoft Azure-gräns. Röd Team övningarna utförs även regelbundet och resultat som används för att göra förbättringar av säkerhet. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg och OWASP RuleSet-metod aktiverad. Mer information finns i [PCI - vägledning för minskning av Risk säkerhetsproblem](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>PCI DSS krav 6.2

**6.2** säkerställer att alla systemkomponenter och programvaran skyddas från kända säkerhetsrisker genom att installera tillämpliga från leverantören säkerhetskorrigeringar. Installera kritiska säkerhetsuppdateringar inom en månad senare.

> [!NOTE]
> Kritiska säkerhetsuppdateringar bör identifieras enligt risken rangordning process som definierats i kravet 6.1.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure är ansvarig för alla nätverksenheter och hypervisor OS programvaran skyddas mot kända säkerhetsrisker genom att installera tillämpliga från leverantören säkerhetskorrigeringar. Om en kund begär att inte använda tjänsten, finns en process för hantering av korrigering för att säkerställa att operativsystemet nivån säkerhetsrisker förhindrade och reparerade inom rimlig tid. Produktionsservrar genomsöks för att validera korrigering efterlevnad månadsvis. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en lösning för PaaS-tjänsten. Azure tillhandahåller underhåll av alla service Pack.|



## <a name="pci-dss-requirement-63"></a>PCI DSS krav 6.3

**6.3** utveckla interna och externa program (inklusive webbaserade administrativ åtkomst till program) på ett säkert sätt, enligt följande:
- I enlighet med PCI DSS (till exempel säker autentisering och loggning)
- Baserat på branschstandarder och bästa praxis
- Inkludera informationssäkerhet genom hela livscykeln för utveckling av programvara 

> [!NOTE]
> Detta gäller för alla program som utvecklats internt samt beställer eller anpassade programvara som har utvecklats av en tredje part.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure-program och slutpunkter utvecklas i enlighet med den metod som Microsoft Security Development Lifecycle (SDL) som inte motsvarar DSS krav. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har utformats för att följa branschens bästa praxis för att skydda CHD. Vägledning för distribution innehåller information om säkerhet meassures och loggning är aktiverat.|



### <a name="pci-dss-requirement-631"></a>PCI DSS krav 6.3.1

**6.3.1** ta bort utveckling, testa och/eller anpassat program konton, användar-ID och lösenord innan program blir aktiva eller publiceras till kunder.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | En slutlig säkerhet granska (FSR) utförs för större versioner före Produktionsdistribution av en avsedda Security Advisor utanför Azure Utvecklingsteamet så bara program som är redo för produktion släpps. Som en del av den här slutgiltiga granska säkerställer att alla testkonton och testdata har tagits bort. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en fristående tjänst som är inloggade och isolerat. Varje nätverk nivåer har en dedikerad nätverkssäkerhetsgrupp [NSG]. Mer information finns i [PCI - vägledning för Nätverkssäkerhetsgrupper](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>PCI DSS krav 6.3.2

**6.3.2** granska anpassad kod innan du släpper produktion eller kunder för att kunna identifiera eventuella säkerhetsproblem (med manuell eller automatisk processer)-kodning för att innehålla minst följande:
- Kodändringar granskas av personer än den ursprungliga koden författaren och av personer om koden granska tekniker och säkra kodning metoder.
- Koden granskningar Kontrollera koden har utvecklats enligt riktlinjer för säker kodning
- Korrigeringar är implementerad innan du släpper
- Koden granska resultaten granskas och godkänns av management före lansering 

> [!NOTE]
> Det här kravet för koden granskningar gäller för alla anpassade kod (både interna och offentliga), som en del av system-livscykeln för utveckling. 
>
> Koden granskningar kan utföras genom kunniga interna personal eller tredje part. Offentliga webbprogram regleras också ytterligare kontroller till adressen pågående hot och säkerhetsrisker efter implementering som definierats på PCI DSS krav 6.6.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure-program och slutpunkter utvecklas i enlighet med metoden som Microsoft Security Development Lifecycle (SDL). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en fristående tjänst som är inloggade och isolerat. Varje nätverk nivåer har en dedikerad nätverkssäkerhetsgrupp [NSG]. Mer information finns i [PCI - vägledning för Nätverkssäkerhetsgrupper](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>PCI DSS krav 6.4

**6.4** Följ ändra Kontrollera processer och procedurer för att alla ändringar till systemkomponenter. Processer måste innehålla följande (se krav punkterna 6.4.1 6.4.6).

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft följer NIST vägledning om säkerhetsaspekter i programvaruutveckling i den informationssäkerheten integreras i SDLC från system starten. Kontinuerlig integration av säkerhetspraxis i Microsoft-SDL kan:<ul><li>Tidig identifiering och minskning av säkerhetsproblem och felaktig konfiguration</li><li>Medvetenhet om potentiella programvara kodning utmaningar som orsakas av säkerhetsåtgärder som krävs</li><li>Identifiering av delade tjänster och återanvändning av bästa praxis säkerhetsverktyg för vilket förbättrar säkerhetstillståndet via beprövade metoder och tekniker</li><li>Tillämpning av Microsofts redan omfattande risk management-program</li></ul>Microsoft Azure har upprättat ändringen och släpp hanteringsprocesser att styra implementering av större ändringar, inklusive:<ul><li>I dokumentationen för planerad och identifiering</li><li>Identifiering av affärsmål, prioritet och scenarier vid produktplanering</li><li>Specificering av funktionen/komponent design</li><li>Operativ beredskap granska baserat på en fördefinierad kriterier/checklista att utvärdera övergripande risk effekter</li><li>Testar, auktorisering och ändringshantering baserat på kriterier för in-/ utgång för utveckling (utveckling), INT (Integreringstestning), steg (Förproduktion) och PRODUKTPRENUMERATION (produktion) miljöer efter behov. Kunder ansvarar för sina egna program som finns i Microsoft Azure.</li></ul> |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore demo är en fristående tjänst som är inloggade och isolerat. <br /><br />Varje nätverk nivåer har en dedikerad nätverkssäkerhetsgrupp [NSG]. Mer information finns i [PCI - vägledning för Nätverkssäkerhetsgrupper](payment-processing-blueprint.md#network-security-groups).<br /><br />Ändringar loggas med Operations Management Suite och Runbooks som används för att samla in loggar. Logganalys innehåller utförlig loggning av ändringar. Ändringar kan granskas och verifiera noggrannhet. Mer specifika anvisningar finns [PCI - vägledning för Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>PCI DSS krav punkterna 6.4.1

**Punkterna 6.4.1** separata miljöer för utveckling och testning från produktionsmiljöer och tillse åtskillnad mellan med åtkomstkontroller.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en fristående tjänst som är inloggade och isolerat. Varje nätverk nivåer har en dedikerad nätverkssäkerhetsgrupp [NSG]. Mer information finns i [PCI - vägledning för Nätverkssäkerhetsgrupper](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>PCI DSS krav 6.4.2

**6.4.2** uppdelning av uppgifter mellan miljöer för utveckling och testning och produktion

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en fristående tjänst som är inloggade och isolerat. Varje nätverk nivåer har en dedikerad nätverkssäkerhetsgrupp [NSG]. Mer information finns i [PCI - vägledning för Nätverkssäkerhetsgrupper](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>PCI DSS krav 6.4.3

**6.4.3** produktionsdata (live Panorera) används inte för testning och utveckling.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore inte har någon realtidsdata primära konto-nummer (PANORERA).|



### <a name="pci-dss-requirement-644"></a>PCI DSS krav 6.4.4

**6.4.4** borttagning av testdata och konton från systemkomponenter innan systemet blir aktiv eller implementeras i produktionen.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har inte några testkonton.|



### <a name="pci-dss-requirement-645"></a>PCI DSS krav punkt 6.4.5

**Punkt 6.4.5** ändringskontroll för implementering av säkerhetskorrigeringar och programvaruändringar måste innehålla följande:
- **6.5.4.1** dokumentation av effekt.
- **6.5.4.2** dokumenterade ändra godkännande av auktoriserade parterna.
- **6.5.4.3** funktioner tester för att verifiera att ändringen inte negativt påverkar säkerheten för systemet.
- **6.5.4.4** tillbaka ut procedurer.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en lösning för PaaS-tjänsten. Och Azure tillhandahåller underhåll av alla service Pack.|



### <a name="pci-dss-requirement-646"></a>PCI DSS krav 6.4.6

**6.4.6** vid slutförandet av en betydande förändring alla relevanta PCI DSS krav måste implementeras på alla nya eller ändrade system och nätverk och dokumentation som uppdateras i tillämpliga fall.

> [!NOTE]
> Det här kravet är bästa praxis till 31 januari 2018, efter vilken den blir ett krav.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en lösning för PaaS-tjänsten. Och Azure tillhandahåller underhåll av alla service Pack.|



## <a name="pci-dss-requirement-65"></a>PCI DSS krav 6.5

**6.5** adress vanliga kodning säkerhetsproblem i processer för utveckling av programvara på följande sätt:
- Träna utvecklare minst årligen i aktuell säker kodning tekniker, inklusive hur du undviker vanliga kodning säkerhetsproblem.
- Utveckla program baserat på säker riktlinjer.

> [!NOTE]
> De säkerhetsrisker som nämns i 6.5.1 via 6.5.10 var aktuella med branschens bästa praxis när den här versionen av PCI DSS publicerades. Dock som branschstandardens rekommendationer för hantering av säkerhetsproblem uppdateras (till exempel guiden OWASP SAN CWE upp 25, CERT Secure kodning osv), måste de aktuella metodtips användas för dessa krav. 
> 
> [!NOTE]
> Krav för 6.5.1 via 6.5.6, nedan, gäller för alla program (interna eller externa). Krav för 6.5.7 via 6.5.10, nedan, gäller för webbprogram och programgränssnitt (interna eller externa). 

- **6.5.1** injektion fel, särskilt SQL injection. Överväg också att OS-kommandot injektion, LDAP och XPath injection fel samt andra injection fel.
- **6.5.2** bufferten över
- **6.5.3** osäker kryptografiska lagring
- **6.5.4** osäker kommunikation
- **6.5.5** felaktig felhantering
- **6.5.6** alla ”hög risk” sårbarheter som identifieras i identifieringsprocessen säkerhetsproblem (enligt definitionen i PCI DSS krav 6.1)
- **6.5.7** globala webbplatsskript (XSS)
- **6.5.8** felaktig åtkomstkontroll (till exempel osäker direct objektreferenser, det gick inte att begränsa åtkomst-URL, directory traversal och underlåtenhet att begränsa användaråtkomst till funktioner)
- **6.5.9** förfalskning av begäran mellan webbplatser (CSRF)
- **6.5.10** bruten autentiserings- och hantering

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Se ”Microsoft Azure” avsnittet [krav 6.4](#pci-dss-requirement-6-4). |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore demo vägledning till säker utveckling, en DFD och hot modellen för att illustrera säkerhet i utvecklingsarbetet metoder.|



## <a name="pci-dss-requirement-66"></a>PCI DSS krav 6.6

**6.6** för offentliga webbprogram adressera nya hot och säkerhetsrisker med jämna mellanrum och se till att dessa program är skyddade mot kända attacker med någon av följande metoder:

- Granska offentliga webbprogram via manuell eller automatisk vulnerability assessment säkerhetsverktyg eller -metoder, minst en gång om året och efter ändringar 

   > [!NOTE]
   > Denna bedömning är inte samma som sårbarhetsgenomsökningar utförs för krav 11.2. 

- Installera en automatisk tekniska lösning som identifierar och förhindrar webbaserade attacker (till exempel en brandvägg webbprogram) framför offentliga webbprogram, att kontinuerligt Kontrollera all trafik.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure testar offentliga webbprogram som en del av dess SDL-processen innan program distribueras till produktionsmiljön. Dessutom Microsoft söker igenom alla offentliga webbprogram i produktion regelbundet att identifiera alla möjliga säkerhetsproblem. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Referens för lösningen minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg och OWASP RuleSet-metod aktiverad. Mer information finns i [PCI - vägledning för minskning av Risk säkerhetsproblem](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>PCI DSS krav 6.7

**6.7** se till att säkerhetsprinciper och operativa procedurer för att utveckla och underhålla säkra system och program dokumenteras används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Referens för lösningen minskar risken för säkerhetsproblem med hjälp av en Programgateway med Brandvägg och OWASP RuleSet-metod aktiverad. Mer information finns i [PCI - vägledning för minskning av Risk säkerhetsproblem](payment-processing-blueprint.md#application-gateway).|




