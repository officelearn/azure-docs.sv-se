---
title: "Azure betalning bearbetning modell - krav för fysisk åtkomst"
description: PCI DSS krav 9
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Fysisk åtkomst krav för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-9"></a>PCI DSS krav 9

**Begränsa fysisk åtkomst till kreditkortsinformation**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

En fysisk åtkomst till data eller system som house Kortinnehavarens data ger möjlighet för enskilda användare att komma åt enheter eller data och ta bort system eller hardcopies och begränsas på lämpligt sätt. Vid tillämpningen av krav 9 avser ”personal på plats” heltid och deltid anställda, tillfällig anställda och underleverantörer konsulter som är fysiskt närvarande entitetens lokalt. ”Besökare” refererar till en leverantör, Gäst alla personal på plats, service arbetare eller alla som behöver ange funktionen under en kort tid, vanligtvis inte mer än en dag. ”Media” syftar på alla papper och elektroniska mediet som innehåller kreditkortsinformation.

## <a name="pci-dss-requirement-91"></a>PCI DSS krav 9.1

**9.1** använder lämplig anläggning post kontroller för att begränsa och övervaka fysisk åtkomst till system i Kortinnehavarens datamiljö.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure är ansvarig för att implementera, tillämpa och övervaka fysisk åtkomstsäkerhet för datacenter. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-911"></a>PCI DSS krav 9.1.1

**9.1.1** använder antingen video kameror eller access control mekanismer (eller båda) för att övervaka enskilda fysisk åtkomst till känsliga områden. Granska insamlade data och samordna med andra poster. Lagra i tre månader om annars begränsas enligt lag.

> [!NOTE]
> ”Känsliga områden” syftar på alla datacenter, serverrum eller ett område som innehåller system som kan lagra, hantera och överför kreditkortsinformation. Detta omfattar inte offentliga områden där endast kassan terminaler finns, till exempel kassören områden i en återförsäljare.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure är ansvarig för att implementera, tillämpa och övervakning CCTV och biometrisk åtkomstkontroll för datacenter. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-912"></a>PCI DSS krav 9.1.2

**9.1.2** implementera fysiska och logiska kontroller för att begränsa åtkomsten till uttag allmänt tillgängligt nätverk. 

Till exempel nätverk uttag finns i offentliga områden och områden som är tillgängliga för besökare kan inaktiveras och aktiveras först när nätverket är explicit behörighet. Du kan också kan processer genomföras för att säkerställa att besökare är åtföljda vid alla tidpunkter i områden med aktiva nätverk uttag.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Det finns inga allmänt tillgängligt nätverk uttag i Microsoft Azure-plattform. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-913"></a>PCI DSS krav 9.1.3

**9.1.3** begränsa fysisk åtkomst till trådlösa åtkomstpunkter, gateways, handdatorer, nätverk och onlinekommunikation maskinvara och telekommunikation rader.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Fysisk åtkomst till Microsoft Azure nätverksmaskinvara hårt styrda av listor för olika former av autentisering, fysiska hinder för transaktionen och krav för företag måste godkännas för åtkomst till utrustning. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



## <a name="pci-dss-requirement-92"></a>PCI DSS krav 9.2

**9.2** utveckla procedurer för att lättare skilja mellan personal på plats och besökare, för att inkludera:
- Identifiera personal på plats och besökare (till exempel tilldela Aktivitetsikoner)
- Ändringar av åtkomstkrav
- Återkalla eller avslutar personal på plats och har upphört att gälla besökare identifiering (till exempel ID Aktivitetsikoner).

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure är ansvarig för att implementera, tillämpa och övervaka fysisk åtkomst säkerhet och medarbetare eller leverantör identifiering när du besöker datacenter. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



## <a name="pci-dss-requirement-93"></a>PCI DSS krav 9.3

**9.3** Kontrollera fysisk åtkomst för personal på plats till känsliga områden på följande sätt:
- Åtkomst måste godkännas och baserat på enskilda arbetsfunktion.
- Åtkomst har återkallats direkt vid avslutning och alla fysisk åtkomstmekanismer som nycklar, åtkomst kort, etc., returneras eller inaktiverad.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Tillstånd för åtkomst till Microsoft-datacenter kontrolleras med hjälp av en auktoriserad åtkomst lista över godkända av Datacenter-teamet som baseras på principen om minsta behörighet. Access control list granskat, verifiera och uppdatera kvartalsvis.<br /><br />Microsoft Azure-Datacenter använda fysisk åtkomst enheter såsom perimeter portar, elektroniska åtkomst Aktivitetsikon läsare, biometriska läsare, portaler-man-traps och anti pass tillbaka enheter. Åtkomst Aktivitetsikon enheter övervakas kontinuerligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



## <a name="pci-dss-requirement-94"></a>PCI DSS krav 9.4

**9.4** implementera procedurer för att identifiera och auktorisera besökare. Bör innehålla följande procedurer.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure är ansvarig för att framtvinga förhandsgodkända leveranser tas emot i en säker inläsning fack som är fysiskt isolerat från bearbeta lokaler och övervakas av auktoriserad personal. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-941"></a>PCI DSS krav 9.4.1

**9.4.1** besökare är auktoriserade innan du anger och åtföljda vid alla tidpunkter inom områden där kreditkortsinformation bearbetas eller bibehålls.


**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure är ansvarig för att framtvinga förhandsgodkända leveranser tas emot i en säker inläsning fack som är fysiskt isolerat från bearbeta lokaler och övervakas av auktoriserad personal. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-942"></a>PCI DSS krav 9.4.2

**9.4.2** besökare identifieras och få en Aktivitetsikon eller andra identifiering som upphör att gälla och som särskiljer synligt besökare från personal på plats.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Åtkomst till Microsoft data center måste vara förhandsgodkända och behöriga besökare krävs för att checka in med fysisk säkerhet vid ankomst och bekräftar ett giltigt ID innan transaktionen. Aktivitetsikoner tydligt ange anställda. Leverantörer och besökare får tillfällig Aktivitetsikoner som måste överlämnas efter funktionen. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-943"></a>PCI DSS krav 9.4.3

**9.4.3** besökarna uppmanas att avstå från skylt eller identifiering innan de lämnar anläggningen eller vid tidpunkten för upphör att gälla.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Besökare krävs att avstå från märken efter Microsoft anläggningar. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-944"></a>PCI DSS krav 9.4.4

**9.4.4** en besökare logg används för att underhålla en fysisk verifieringskedja för besökare aktivitet lokalen som datorn lokaler och datacenter där kreditkortsinformation lagras eller överförs.
Dokumentera de besökare namn, fast representeras och personal på plats som auktorisera fysisk åtkomst på loggen.
Behåll den här loggfilen under minst tre månader om annars begränsas enligt lag.

**Ansvarsområden:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ansvarar för att underhålla en logg för besökare som en fysisk verifieringskedja för besökare aktivitet lokalen som datorn lokaler och datacenter där kreditkortsinformation lagras eller överförs. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



## <a name="pci-dss-requirement-95"></a>PCI DSS krav 9.5

**9.5** fysiskt säker alla media.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>PCI DSS krav 9.5.1

**9.5.1** säkerhetskopieringar media i en säker plats, helst en extern anläggning, till exempel en alternativa eller sekundära plats, eller en extern lagringsutrymmet. Granska platsens säkerhet minst en gång om året.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>PCI DSS krav 9.6

**9.6** Underhåll strikt kontroll över distributionen av alla typer av media, inklusive följande interna eller externa.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>PCI DSS krav 9.6.1

**9.6.1** klassificera media så känsliga data kan fastställas.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>PCI DSS krav 9.6.2

**9.6.2** skicka skyddade courier eller andra leveransmetod kan spåras korrekt.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>PCI DSS krav 9.6.3

**9.6.3** Kontrollera management godkänner alla media som flyttas från en säker del (inklusive när mediet har distribuerats till enskilda användare).

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>PCI DSS krav 9.7

**9.7** Underhåll strikt kontroll över lagring och tillgängligheten för media.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>PCI DSS krav 9.7.1

**9.7.1** korrekt Underhåll lager-loggarna för alla media och utföra media inventeringar minst en gång om året.


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>PCI DSS krav 9.8

**9,8** förstöra media när den inte längre behövs för företag eller juridiska skäl på följande sätt.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>PCI DSS krav 9.8.1

**9.8.1** hjälp, förbränna eller fruktkött papperskopior material så att kreditkortsinformation inte kan rekonstrueras. Säkra behållare för lagring som används för material som ska förstöras.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore lagrar alla data i Azure SQL Database. En PaaS SQL-databasinstans för demonstrerar säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>PCI DSS krav 9.8.2

**9.8.2** återge kreditkortsinformation på elektroniska medier oåterkalleligt så att kreditkortsinformation inte kan rekonstrueras.

**Ansvarsområden:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Data destruktion tekniker varierar beroende på vilken typ av dataobjekt förstörs, vare sig det är prenumerationer, lagring, virtuella datorer eller databaser. I den Microsoft Azure klientmiljön noggrann åtgärder vidtas för att säkerställa att en kunds data är inte tillåtet att antingen ”läcka” till en annan kundinformation eller när en kund tar bort data, ingen kund som (inklusive i de flesta fall kunden vem som äger en gång data) kan komma åt den borttagna datan.<br /><br />Microsoft Azure följer NIST 800 88 riktlinjer för Media rensning som adressera huvudnamn för att säkerställa att data inte har publicerats oavsiktligt. Dessa riktlinjer omfattar både elektroniska och fysiska rensning. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore kan tas bort helt och hållet genom att ta bort resursgruppen som används under distributionen.|



## <a name="pci-dss-requirement-99"></a>PCI DSS krav 9.9

**9.9** skydda enheter som samlar in kort Betalningsdata via direkt fysiska interaktion med kort mot manipulation och ersättning.

> [!NOTE]
> Dessa krav gäller för kort läsning enheter används i kort finns transaktioner (det vill säga kort eller dip) kassan. Det här kravet är inte avsedd att tillämpas på manuell nyckel post komponenter, till exempel dator tangentbord och POS och knappsatser. 

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore använder OMS för att logga alla systemändringar i.<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) innehåller utförlig loggning av ändringar. Ändringar kan granskas och verifiera noggrannhet. Mer specifika anvisningar finns [PCI - vägledning för Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>PCI DSS krav 9.9.1

**9.9.1** upprätthålla en aktuell lista över enheter. Listan bör innehålla följande:
- Märke, modell för enhet
- Platsen för enheten (till exempel adressen för webbplatsen eller anläggning där enheten finns)
- Enhetens serienummer eller annan metod för unik identifiering

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Contoso Webstore innehåller en Referensarkitektur och en lista över alla tjänster som används i dess Distributionsdokumentation.|



### <a name="pci-dss-requirement-992"></a>PCI DSS krav 9.9.2

**9.9.2** regelbundet kontrollera enhet hämtar för att upptäcka manipulering (till exempel lägga till kort tårtspadar till enheter) eller ersättning (till exempel genom att kontrollera serienumret eller annan enhet egenskaper för att verifiera att den har inte bytts med en bedrägliga enhet).

> [!NOTE]
> Exempel på tecken på att en enhet kan ha manipulerats eller ersätts är oväntat bifogade filer eller kablar ansluten till enheten, saknas eller har ändrats etiketter, avbrutna eller olika färger skiftläge eller ändringar av serienummer eller andra externa märkning.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



### <a name="pci-dss-requirement-993"></a>PCI DSS krav 9.9.3

**9.9.3** att tillhandahålla utbildning för personal vara medveten om försök till manipulering eller ersättning av enheter. Utbildningen bör innehålla följande:
- Verifieringen av identiteten för tredje parts personer utge sig för att vara reparation eller underhåll personal innan du ger dem behörighet att ändra eller felsöka enheter.
- Inte installera, ersätta eller returnera enheter utan att verifiera.
- Tänk på misstänkt beteende runt enheter (till exempel försök av okända personer att koppla från eller öppna enheter).
- Rapportera misstänkt beteende och uppgifter om enheten manipulering eller ersättning till vederbörlig personal (till exempel till som en chef eller säkerhet).

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|



## <a name="pci-dss-requirement-910"></a>PCI DSS krav 9.10

**9.10** Kontrollera att IPSec-principer och operativa procedurer för att begränsa fysisk åtkomst till kreditkortsinformation dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Providern<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI &#8209; DSS&nbsp;modell)** | Inte tillämpligt.|




