---
title: Azure-säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - Media skydd
description: FedRAMP Web Applications Automation - Media-skydd
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 37812c2f7ee79685f9014a7999b4355e649ca6e1
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207103"
---
# <a name="media-protection-mp"></a>Media skydd (HP)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-mp-1"></a>NIST 800 53 kontrollen MP-1

#### <a name="media-protection-policy-and-procedures"></a>Skydd av medier och procedurer

**HP-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en skydd av medier som åtgärdar syfte, scope, roller, ansvar, management åtagande, samordning bland organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av skydd av medier och tillhörande media skydd kontroller. och granskar och uppdaterar den aktuella media skyddsprincipen [tilldelning: organisation definierats frekvens]; och media skydd procedurer [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå media protection-principen och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-mp-2"></a>NIST 800 53 kontrollen MP-2

#### <a name="media-access"></a>Media Access

**HP-2** organisationen begränsar åtkomsten till [tilldelning: organisation användardefinierade typer av digitala och/eller icke-digital media] till [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure har implementerat media åtkomst genom implementeringen av Microsoft-säkerhetsprincip. Logiska åtkomsten till digitala media styrs via Active Directory grupprincipobjekt (AD) och säkerhetsgrupper. Fysisk åtkomst till alla media är begränsat av datacenter åtkomst till processen. Åtkomsten är begränsad till personer som har ett syfte legitima företag för att komma åt data. Mer information om åtkomstkontroller datacenter på plats finns i PE-3, fysiska åtkomstkontroll. Tillgångsinformation skydd Standard definierar de åtgärder som krävs för att skydda sekretess, integritet och tillgänglighet av informationstillgångar i Microsoft Azure-datacenter. |


 ## <a name="nist-800-53-control-mp-3a"></a>NIST 800 53 kontrollen MP-3.a

#### <a name="media-marking"></a>Media-märkning

**HP-3.a** organisationen markerar information systemmedia som du anger begränsningarna distribution, hantering av varningar och tillämpliga säkerhet märkning (eventuella) information.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure markerar tillgångar i Microsoft datacenter med ett HBI, MBI eller LBI (hög, Måttlig eller låg påverkan på verksamheten) benämning som kräver olika nivåer av säkerhet och hantering av säkerhetsåtgärder. Tillgångsinformation ägare krävs för att klassificera sina tillgångar som lagras i ett Microsoft-datacenter. Mer information finns i till tillgången klassificering Standard och tillgångsinformation skydd Standard. |


 ## <a name="nist-800-53-control-mp-3b"></a>NIST 800 53 kontrollen MP-3.b

#### <a name="media-marking"></a>Media-märkning

**HP-3.b** organisation undantar [tilldelning: organisation användardefinierade typer av information systemmedia] från märkning så länge mediet finns kvar i [tilldelning: organisation definierats kontrollerade områden].

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure kräver tillgång ägare att tilldela deras tillgångar med en tillgång klassificering och inga tillgångar är undantagna från det här kravet. I Microsoft datacenter-miljö finns tillgångar servrar och nätverksenheter magnetband. Annan digitala media som USB-flash/USB enheter, externa flyttbara hårddiskar, eller CD eller DVD-skivor används inte. Icke-digital media används inte i datacentret. |


 ## <a name="nist-800-53-control-mp-4a"></a>NIST 800 53 kontrollen MP-4.a

#### <a name="media-storage"></a>Medialagring

**MP-4.a** fysiskt styr och lagras på ett säkert sätt [tilldelning: organisation användardefinierade typer av digitala och/eller icke-digital media] i [tilldelning: organisation definierats kontrollerade områden].

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure digitala media tillgångar lagras fysiskt och på ett säkert sätt i datacenter samordning lokaler. Microsoft-datacenter har flera lager av fysiska åtkomstkontroller (åtkomst Aktivitetsikon, biometrik, se PE 3 för ytterligare information om fysiska åtkomstkontroller) och video övervakning för att tillhandahålla säker lagring. Digital media för innehåller servrar och nätverksenheter magnetband som används för säkerhetskopiering. Icke-digital media används inte i datacentermiljö. |


 ## <a name="nist-800-53-control-mp-4b"></a>NIST 800 53 kontrollen MP-4.b

#### <a name="media-storage"></a>Medialagring

**HP-4.b** organisationen skyddar information systemmedia tills mediet förstörs eller språkoberoende med godkända utrustning, metoder och procedurer.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure digitala media tillgångar är skyddade i Microsoft datacenter colocations via fysiska åtkomstkontroller (PE 3) och logiska åtkomstkontroller (IA-2) för livslängden för tillgången. Microsoft Azure tillgångar är avmarkerad, rensas eller förstörs med metoder som är konsekventa med NIST SP 800 88 före avyttring tillgångar. För tillgångsinformation destruktion använder Microsoft Azure på plats tillgång destruktion tjänster. |


 ## <a name="nist-800-53-control-mp-5a"></a>NIST 800 53 kontrollen MP-5.a

#### <a name="media-transport"></a>Transport-Media

**HP-5.a** organisationen skyddar och styr [tilldelning: organisation användardefinierade typer av information systemmedia] under transport utanför kontrollerade områden med [tilldelning: organisation definierats säkerhetsåtgärder].

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure digitala media på Microsoft-datacenter består av servrar, nätverksenheter, och magnetiska band och diskar, där det behövs. Microsoft-datacenter använda inte icke-digital media. Microsoft använder tre metoder för att skydda media som transporteras utanför datacentret: 1) säker Transport, 2) kryptering 3) rengöra, rensa eller ta bort. |


 ## <a name="nist-800-53-control-mp-5b"></a>NIST 800 53 kontrollen MP-5.b

#### <a name="media-transport"></a>Transport-Media

**HP-5.b** organisationen har ansvar för information systemmedia under transport utanför kontrollerade områden.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure har ansvar för att lämna datacenter genom att använda information från NIST SP 800 88 tillgångar: konsekvent Rensa/rensa, tillgångsinformation destruktion, kryptering, korrekt inventering, spårning och skydd av spårbarhet under transport. |


 ## <a name="nist-800-53-control-mp-5c"></a>NIST 800 53 kontrollen MP-5.c

#### <a name="media-transport"></a>Transport-Media

**HP-5.c** organisationen dokument aktiviteter som är associerade med transport av information systemmedia.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure underhåller poster i lagret före transport, spårning och skydd av spårbarhet under transport, rensning/rensa tillgången, tillgången destruktion, mottagande av tillgångar och lager validering efter transport. |


 ## <a name="nist-800-53-control-mp-5d"></a>NIST 800 53 kontrollen MP-5.d

#### <a name="media-transport"></a>Transport-Media

**HP-5.d** organisationen begränsar de aktiviteter som är associerade med transport av information systemmedia till auktoriserad personal.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure begränsar aktiviteter för tillgångsinformation transport till auktoriserad personal genom att skydda kedja för förvaring. Användning av lås, manipulera bevis försegling och som kräver validering av tillgångsinformation inventeringar säkerställer att endast auktoriserad personal är inblandad i tillgångsinformation transport. |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800 53 kontrollen MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Media Transport | Kryptografiska skydd

**HP-5.4** informationssystem implementerar kryptografiska metoder för att skydda sekretess och integriteten för information som lagras på digitala media under transport utanför kontrollerade områden.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure sådana Data Protection Service (DP) att hantera kryptografiska nycklar med hjälp av en FIPS 140-2-3-validerade kryptering modulen (cert #1694) och HSM (cert #1178) för att skydda AES 256-bitars krypterade data på magnetband. |


 ## <a name="nist-800-53-control-mp-6a"></a>NIST 800 53 kontrollen MP-6.a

#### <a name="media-sanitization"></a>Media rensning

**MP-6.a** organisationen sanitizes [tilldelning: organisation definierats information systemmedia] före avyttring, övergång utanför organisations kontroll eller släppa för återanvändning med [tilldelning: tekniker som definierats av organisationen rensning och procedurer] i enlighet med tillämpliga federala och organisationens normer och principer.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure kräver digitala media i Microsoft Azure datacenter-miljö att rengöras/rensas med hjälp av Microsoft Azure godkända verktyg och i överensstämmelse med NIST SP 800 88 riktlinjer för Media rensning innan som återanvänds eller avyttras . Icke-digital media används inte av Microsoft Azure i datacentermiljö. |


 ## <a name="nist-800-53-control-mp-6b"></a>NIST 800 53 kontroll-MP-6 b

#### <a name="media-sanitization"></a>Media rensning

**HP-6 b** organisationen använder rensning mekanismer med styrkan och integritet proportion till säkerhetskategorin eller klassificering av informationen.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure använder utplåning enheter och processer för att rensa/rensa data i enlighet med NIST SP 800 88 och som är i proportion till Microsoft Azure tillgången klassificering av tillgången. För att kräva destruktion tillgångar, använder Microsoft Azure på plats tillgång destruktion tjänster. |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800 53 kontrollen MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Media rensning | Granska / godkänna / spåra / dokumentera / Kontrollera

**HP-6.1** organisationen granskar, godkänner, spårar, dokument och verifierar media rensning och avyttring åtgärder.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure har implementerats media rensning procedurer i enlighet med riktlinjerna i NIST SP 800 88 för tillgångsinformation klassificering Standard och tillgångsinformation skydd Standard. Alla magnetiska eller elektroniska media är rengöras/rensas genom följande NIST SP 800 88 specifikationer i enlighet med sin klassificering för Azure-tillgång. Azure använder utplåning enheter från extrema protokollet lösningar (EPS). EPS programvara stöder NIST SP 800 88 krav för rengöring och rensa/secure utplåning. |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800 53 kontrollen MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Media rensning | Utrustning testning

**HP-6.2** organisationen testar rensning utrustning och procedurer [tilldelning: organisation definierats frekvens] att verifiera att rätt rensning som uppnås.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure använder utplåning enheter och processer för att rensa/rensa data i enlighet med NIST SP 800 88. Varje 180 dagar domänkontrollanter operations testar Microsoft Azure data utplåning enheter och processen för utplåning. I det här testet kontrollerar domänkontrollanter operations är avsedda rensning som uppnås genom en kriminalteknisk analys av testade hårddiskar för att bekräfta att data har tagits språkoberoende av data utplåning enheter |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800 53 kontrollen MP-6.3

#### <a name="media-sanitization--nondestructive-techniques"></a>Media rensning | Icke-förstörande tekniker

**HP-6.3** organisationen gäller icke-förstörande rensning tekniker för flyttbara lagringsenheter innan du ansluter dessa enheter till informationssystem under följande omständigheter: [tilldelning: organisation definierats omständigheter som kräver rensning av flyttbara lagringsenheter IT-avdelning].

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure säkerställer att Azure-Datacenter följer de verktyg och flyttbara Media säkerhet proceduren i Data Center tjänster kör bok för att förhindra angrepp Government miljön från skadlig programvara på flyttbara lagringsenheter. Förfarandet som anger att följande åtgärder vidtas med USB-enheter innan de används i Government-miljö: <br /> (1) formatera USB-enheter när enheterna som köps först från tillverkaren eller leverantören innan den första gången eller när återanvänds för ett annat verktyg. <br /> (2) söka igenom alla USB-enhet som ska användas i ett utsedda område för skadlig kod, innan du tar enheten till området. <br /> (3) när du använder en enhet inom ett område utsedda, formateras innan de lämnar området. <br /> Verktyg och flyttbara Media säkerhet proceduren kräver också att alla förlorade, borttagna, stulna eller borttappade USB enheter aldrig vara nytt introducerades i Azure-datacenter, men att de kan i stället katalogiserats och förstörs. |


 ## <a name="nist-800-53-control-mp-7"></a>NIST 800 53 kontrollen MP-7

#### <a name="media-use"></a>Användning av medier

**MP-7** organisationen [markeringen: begränsar; förhindrar] användningen av [tilldelning: organisation användardefinierade typer av information systemmedia] på [tilldelning: organisation definierats informationssystem eller systemkomponenter] med [tilldelning: organisation definierats säkerhetsåtgärder].

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure kräver tillgång ägare att tilldela deras tillgångar med en tillgång klassificering och inga tillgångar är undantagna från det här kravet. I Microsoft Azure datacenter-miljö finns tillgångar servrar och nätverksenheter. Andra digitala medier som USB-flash/USB-enheter som hanteras av specifika principer och procedurer som styr hur dessa enheter hanteras. CD eller DVD-skivor används inte. Icke-digital media används inte i datacentret. Användning av digital media i Microsoft Azure datacenter-miljöer är övervakade 24 x 7 via CCTV täckning. Mer information finns i PE-06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800 53 kontrollen MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Använd Media | Förbjuda utan ägare

**HP-7.1** organisationen förhindrar användning av flyttbara lagringsenheter i organisationsinformation system när sådana enheter har ingen identifierbar ägare.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns ingen kund-kontrollerade media inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft begränsar användningen av skrivbar, flyttbara media till media som uttryckligen har godkänts av DATACENTERHANTERING via domänkontrollanter verktyg och flyttbara Media-procedur. Media som är personligt ägda eller har ingen identifierbar ägare är inte tillåtet i ett produktionsområde enligt beskrivningen i dokumentet Microsoft Datacenter arbete regler och förordningar. |
