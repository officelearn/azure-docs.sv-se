---
title: "Azure betalning bearbetning modell - krav för brandväggen"
description: PCI DSS krav 1
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Krav för brandväggen för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-1"></a>PCI DSS krav 1

**Installera och underhålla en brandväggskonfigurationen för att skydda kreditkortsinformation**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Brandväggar är enheter som styr datorn trafiken mellan en entitet nätverk (intern) och icke-betrodda nätverk (externt), samt trafik till och från känsliga områden inom en entitet interna betrodda nätverk. Kortinnehavarens data miljön är ett exempel på ett mer känsligt område inom en entitet betrott nätverk.
En brandvägg undersöker all nätverkstrafik och blockerar de överföringar som inte uppfyller säkerhetskriterierna som angivna.
Alla system måste skyddas mot obehörig åtkomst från ej betrodda nätverk, om du skriver systemet via Internet som e-handel, medarbetare Internetåtkomst via webbläsare, medarbetare e-poståtkomst, dedicerade anslutningar som Business-to-business-anslutningar, via trådlösa nätverk eller via andra källor. Ofta kan till synes obetydlig sökvägar till och från ej betrodda nätverk tillhandahålla oskyddade vägarna till viktiga system. Brandväggar är en mekanism för nyckelskydd för dator-nätverk.
Andra systemkomponenter kan ge brandväggsfunktioner, förutsatt att de uppfyller minimikraven för brandväggar som definieras i krav 1. Om andra systemkomponenter används i miljön Kortinnehavarens data för att tillhandahålla brandväggsfunktioner, måste dessa enheter inkluderas i omfång och utvärdering av krav 1.

## <a name="pci-dss-requirement-11"></a>PCI DSS krav 1.1

**1.1** upprätta och implementera brandvägg och router konfigureringsstandarder som innehåller följande (se 1.1.1 via 1.1.7).


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore ger firewalling av CDE med PaaS isolering och en Apptjänstmiljö implementering garanterar att CDE ingående och utgående data skyddas.<br /><br />En [App Service miljö (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) är en Premium service-plan som används av kompatibilitetsskäl. Mer information om ASE kontroller och konfiguration finns [PCI vägledning - Apptjänstmiljö](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>PCI DSS krav 1.1.1

**1.1.1** en formell process för godkännande och testa alla nätverksanslutningar och ändringar i brandväggen och router-konfigurationer


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | En instans av Contoso Webstore upprättar en CI/CD DevOps-modell för att säkerställa att alla ändringar hanteras korrekt. [Operations Management Suite (OMS)](/azure/operations-management-suite/) innehåller utförlig loggning av ändringar. Ändringar kan granskas och verifiera noggrannhet. Mer specifika anvisningar finns [PCI - vägledning för Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Azure Security Center](https://azure.microsoft.com/services/security-center/) innehåller en centraliserad för säkerhetsläget för alla dina Azure-resurser. En överblick över kan du kontrollera att lämpliga säkerhetsåtgärder som är installerade och korrekt konfigurerad och att du snabbt kan identifiera alla resurser som kräver uppmärksamhet.|



### <a name="pci-dss-requirement-112"></a>PCI DSS krav 1.1.2

**1.1.2** aktuella nätverksdiagram som identifierar alla anslutningar mellan Kortinnehavarens datamiljö och andra nätverk, inklusive alla trådlösa nätverk

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Finns i Contoso Webstore referens arkitektur och utformning dokumentationen som en del av installationen mönstret för lösningen.|



### <a name="pci-dss-requirement-113"></a>PCI DSS krav 1.1.3

**1.1.3** aktuella diagram som visar alla kreditkortsinformation flödar mellan olika system och nätverk

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Referera till Contoso vi besvarar DFD och Hotmodell.|



### <a name="pci-dss-requirement-114"></a>PCI DSS krav 1.1.4

**1.1.4** krav för en brandvägg på varje Internet-anslutning och mellan alla demilitariserad zon (DMZ) och det interna nätverkszon

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder gräns enheter som gateways, ACL: er för nätverket och programmet brandväggar kontrollen kommunikation med externa och interna gränser på nivån plattform. Kunden konfigurerar sedan dessa till sina specifikationer och krav. Microsoft Azure filtrerar kommunikation när i plattformen. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore ger en DMZ med PaaS isolering och en Apptjänstmiljö implementering garanterar att CDE ingående och utgående data skyddas.<br /><br />En [App Service miljö (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) är en Premium service-plan som används av kompatibilitetsskäl. Mer information om ASE kontroller och konfiguration finns [PCI vägledning - Apptjänstmiljö](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>PCI DSS krav 1.1.5

**1.1.5** beskrivning av grupper, roller och ansvarsområden för hantering av nätverkskomponenter

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore använder [rollbaserad åtkomstkontroll (RBAC)](/azure/active-directory/role-based-access-control-configure) att isolera användarroller. RBAC kan exakt fokuserad åtkomsthantering för Azure. Det finns specifika konfigurationer för prenumerationen åtkomst och Azure Key Vault-åtkomst.|



### <a name="pci-dss-requirement-116"></a>PCI DSS krav 1.1.6

**1.1.6** dokumentationen för affärsjustering och godkännande för användning av alla tjänster, protokoll och portar som tillåts, inklusive dokumentation av säkerhetsfunktioner för dessa protokoll som anses vara osäker.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore öppnar endast nödvändiga portarna och protokollen RA-utformningen. Information om dataflöde kan ses i modellen DFD och hot.|



### <a name="pci-dss-requirement-117"></a>PCI DSS krav 1.1.7

**1.1.7** kravet på att granska brandvägg och router regeln anger minst var sjätte månad

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | I den Contoso Webstore granskas regeluppsättningar brandväggen för att säkerställa att inga onödiga eller oanvända regler ingår. Avsiktligt distribueras demonstrationen med en minsta privilegium minsta storleken för sökvägen.|



## <a name="pci-dss-requirement-12"></a>PCI DSS krav 1.2

**1.2** skapa konfigurationer för brandväggen och router som begränsar anslutningar mellan ej betrodda nätverk och alla komponenter i Kortinnehavarens datamiljö. 

> [!NOTE]
> ”Ej betrodda nätverk” är något nätverk som är externa nätverk som hör till entiteten granskas och/eller som ligger utanför entitetens möjlighet att styra och hantera.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Den Contoso Webstore CDE definieras i dokumentationen för RA och distribution. Ej betrodda nätverk nekas avsiktligt.|



### <a name="pci-dss-requirement-121"></a>PCI DSS krav 1.2.1

**1.2.1** begränsa inkommande och utgående trafik till den som krävs för miljön Kortinnehavarens data och neka specifikt all annan trafik.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Den Contoso Webstore CDE definieras i dokumentationen för RA och distribution. Ej betrodda nätverk nekas avsiktligt. Contoso Webstore demo konfigurerar Microsoft Azure-program brandväggen att tillåta endast angivna intervall med IP-adresser för att få åtkomst till Microsoft Azure-tjänster. Contoso Webstore innehåller en neka alla brandvägg på alla CDE gränser. Alla konfigurationer utförs under installationen av distributionen.

> [!NOTE]
> App Service miljö (ASE) används i den här lösningen att isolera CDE men det är viktigt att din kvalificerade säkerhet utvärderare (QSA) utvärderar den här lösningen som ASE implementerar en DMZ isolering som tillåter utgående anslutningar som ASE. PCI-DSS kräver att alla inkommande och utgående anslutningar som inte krävs blockeras. För ASE ska fungera korrekt, ASE ska upprätta utgående anslutningar som behövs som definierats i [”nätverk att tänka på när en Apptjänst-miljö”](/azure/app-service/app-service-environment/network-info). Kunder bör utvärderas utgående anslutningar med din QSA innan du distribuerar lösningen till en produktionsmiljö för att säkerställa att den uppfyller kraven. |



### <a name="pci-dss-requirement-122"></a>PCI DSS krav 1.2.2

**1.2.2** säker och synkronisera router konfigurationsfiler.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore innehåller konfigurationer som synkroniserats för Microsoft Azure interna nätverkskontroller.|



### <a name="pci-dss-requirement-123"></a>PCI DSS krav 1.2.3

**1.2.3** installera perimeter brandväggar mellan alla trådlösa nätverk och Kortinnehavarens datamiljö och konfigurera dessa brandväggar för att neka eller, om trafiken är nödvändigt för affärsändamål, Tillåt endast behörighet trafik mellan trådlöst miljö och Kortinnehavarens datamiljö.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore har inte några trådlösa lösningar eller funktioner.|



## <a name="pci-dss-requirement-13"></a>PCI DSS krav 1.3

**1.3** förbjuda direktåtkomst offentliga mellan Internet och varje komponent i Kortinnehavarens datamiljö.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder gräns för Nätverks- och värd-baserade enheter, till exempel brandväggar och belastningsutjämnare och ACL: er. Enheterna använder mekanismer som VLAN-isolering, NAT och paket filtrering separat kund trafik från Internet och hantering av trafik. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore ger vid tidpunkten för distribution, konfigurationer för Azure-program du vill tillåta endast angivna intervall med IP-adresser till åtkomst till webbplatsen kan du inkludera deras CDE skyddsmiljö virtuella Azure-datorer.|



### <a name="pci-dss-requirement-131"></a>PCI DSS krav 1.3.1

**1.3.1** implementera en DMZ om du vill begränsa inkommande trafik till endast systemkomponenter som ger auktoriserade offentligt tillgänglig tjänster, protokoll och portar.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore implementeringen av dess DMZ säkerställer att endast auktoriserade tjänster kan ansluta med CDE.|



### <a name="pci-dss-requirement-132"></a>PCI DSS krav 1.3.2

**1.3.2** gränsen inkommande Internet-trafik till IP-adresser i Perimeternätverket.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore implementeringen av dess DMZ säkerställer att endast auktoriserade tjänster kan ansluta med CDE.|



### <a name="pci-dss-requirement-133"></a>PCI DSS krav 1.3.3

**1.3.3** implementera skydd mot förfalskning åtgärder för att identifiera och blockera förfalskad källans IP-adresser försätts i nätverket. (Exempelvis blockera trafik som kommer från Internet med en intern källadress.)

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementerar nätverket filtrering för att förhindra falska trafik och begränsa inkommande och utgående trafik till betrodda platform-komponenter. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-134"></a>PCI DSS krav 1.3.4

**1.3.4** inte tillåta obehöriga utgående trafik från Kortinnehavarens data miljön till Internet.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Arkitektur för Contoso Webstore förhindrar obehörig utgående trafik från i scope-miljön till Internet. Detta åstadkoms genom att konfigurera utgående trafik ACL: er för godkända portar och protokoll i Microsoft Azure. De här kontrollerna inkludera åtkomst till CDE i SQL Server-databasen. <br /><br />En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>PCI DSS krav 1.3.5

**1.3.5** bevilja endast ”upprättat” anslutningar till nätverket.


**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementerar nätverket filtrering för att förhindra falska trafik och begränsa inkommande och utgående trafik till betrodda platform-komponenter. Microsoft Azure-nätverk är åtskild skiljer kunden trafik från hantering av trafik. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-136"></a>PCI DSS krav 1.3.6

**1.3.6** plats systemkomponenter som lagrar kreditkortsinformation (t.ex en databas) i ett internt nätverk zonen åtskild från Perimeternätverket och andra icke-betrodda nätverk.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder nätverket särskiljning och NAT för att separera kunden trafiken från hantering av trafik. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Arkitektur för Contoso Webstore förhindrar obehörig utgående trafik från i scope-miljön till Internet. Detta åstadkoms genom att konfigurera utgående trafik ACL: er för godkända portar och protokoll i Microsoft Azure. De här kontrollerna inkludera åtkomst till CDE i SQL Server-databasen. <br /><br />En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>PCI DSS krav 1.3.7

**1.3.7** inte avslöjar privata IP-adresser och routning information till obehöriga personer. 

> [!NOTE]
> Metoder för att dölja IP-adresser kan inkludera, men är inte begränsade till:
> - Network Address Translation (NAT).
> - Placera servrar som innehåller kreditkortsinformation bakom proxy-servrar/brandväggar.
> - Ta bort eller filtrering av flödet annonser för privata nätverk som använder du registrerade adressering.
> - Intern användning av RFC1918-adressutrymmet i stället för registrerade adresser.


**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure använder NAT (Network Address Translation) och nätverk särskiljning för att separera kunden trafiken från hantering av trafik. Azure enheter identifieras unikt med deras UUID och autentiseras med hjälp av Kerberos. Azure hanterade nätverk enheter identifieras av RFC 1918 IP åtgärdas. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore placeras alla kreditkortsinformation bakom proxy-servrar/brandväggar och används internt av RFC1918 adressutrymme.|



## <a name="pci-dss-requirement-14"></a>PCI DSS krav 1.4

**1.4** installera brandvägg programvara eller motsvarande funktioner på alla bärbara datorer (inklusive företag och/eller medarbetarägda) som ansluter till Internet när utanför nätverket (till exempel bärbara datorer används av anställda), och som används också för att få åtkomst till CDE. Brandvägg (eller motsvarande) konfigurationerna omfattar:-specifika konfigurationsinställningar har definierats.
- Brandvägg (eller motsvarande funktioner) körs aktivt.
- Brandvägg (eller motsvarande funktioner) är inte alterable av användare av bärbara datorer.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore ger inte skydd av slutanvändar-enheter. [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) kan användas för att hantera mobila enheter som personalen använder för att komma åt företagets data.|



## <a name="pci-dss-requirement-15"></a>PCI DSS krav 1.5

**1.5** Kontrollera att IPSec-principer och operativa procedurer för att hantera brandväggar dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore ger vid tidpunkten för distribution, konfigurationer för Azure-program du vill tillåta endast angivna intervall med IP-adresser till åtkomst till webbplatsen kan du inkludera deras CDE skyddsmiljö virtuella Azure-datorer.|




