---
title: FedRAMP Azure utkast Automation - identifiering och verifiering
description: "Webbprogram för FedRAMP - identifiering och verifiering"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5ceac04f3f5c58ac5810dda6c99b40ebc8ad736f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="identification-and-authentication-ia"></a>Identifiering och verifiering (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800 53 kontrollen IA-1

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Identifiering och principer för autentisering och procedurer

**IA-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en princip för identifiering och autentisering som åtgärdar syfte, scope, roller, ansvar, management åtagande samordning av organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av identifiering och principer för autentisering och associerade identifiering och autentiseringskontroller; och granskar och uppdaterar den aktuella principen för identifiering och verifiering [tilldelning: organisation definierats frekvens]; och procedurer för identifiering och verifiering [tilldelning: organisation definierats frekvens].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå identifiering och verifiering principer och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800 53 kontrollen IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identifiering och verifiering (organisationens användare)

**IA-2** systemet som unikt identifierar och autentiserar organisationens användare (eller processer som agerar på uppdrag av organisationens användare).

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Konton som skapats av den här Azure-modell har unika identifierare. Inbyggda konton med icke-unika identifierare inaktiverats eller tagits bort. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800 53 kontrollen IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identifiering och verifiering (organisationens användare) | Nätverksåtkomst till Privilegierade konton

**IA-2 (1)** informationssystem implementerar multifaktor-autentisering för nätverksåtkomst till behöriga konton.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att implementera multifaktorautentisering för nätverksåtkomst till behöriga konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800 53 kontrollen IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identifiering och verifiering (organisationens användare) | Nätverksåtkomst till icke-privilegierade konton

**IA-2 (2)** informationssystem implementerar multifaktor-autentisering för nätverksåtkomst till icke-privilegierade konton.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att implementera multifaktorautentisering för nätverksåtkomst till icke-privilegierade konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800 53 kontrollen IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identifiering och verifiering (organisationens användare) | Lokal åtkomst till Privilegierade konton

**IA-2 (3)** informationssystem implementerar flerfunktionsautentisering för lokal åtkomst till behöriga konton.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunden har inte åtkomst till alla systemresurser i Azure-datacenter. |
| **Providern (Microsoft Azure)** | Microsoft Azure tillåter inte lokal åtkomst om fysisk åtkomst krävs. Lokal administratörsåtkomst endast användas för att felsöka problem i fall där medlemsservern upplever nätverksproblem och domänautentisering fungerar inte. <br /> Azure implementerar flerfunktionsautentisering för lokal åtkomst via åtkomst kontrollmekanismer som krävs för fysisk åtkomst till miljön. Lokaler i Azure-datacenter som innehåller alla Azure infrastruktursystem systemgränsen begränsas via olika fysiska säkerhetsmekanismer, inklusive krav för företagets smartkort badging åtkomst och biometriska enheter. Båda typer av autentisering krävs för fysisk åtkomst vid ingång pekar på Azure-datacenter colocations. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800 53 kontrollen IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identifiering och verifiering (organisationens användare) | Lokal åtkomst till icke-privilegierade konton

**IA-2 (4)** informationssystem implementerar flerfunktionsautentisering för lokal åtkomst till icke-privilegierade konton.

**Ansvarsområden:**`Azure Only`

|||
|---|---|
| **Kunden** | Kunden har inte åtkomst till alla systemresurser i Azure-datacenter. |
| **Providern (Microsoft Azure)** | Microsoft Azure tar hänsyn till alla Microsoft Azure Government-konton som används av Microsoft Azure Government-personal som Privilegierade. Multifaktorautentisering har implementerats för alla Microsoft Azure Government personal konton med smartkort och PIN-koder, vilket innefattar lokal åtkomst. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800 53 kontrollen IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identifiering och verifiering (organisationens användare) | Autentisering för grupper

**IA-2 (5)** organisationen kräver enskilda användare autentiseras med en enskild autentiserare när en grupp authenticator anställda.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Inga delade/grupp-konton är aktiverade på resurser som distribueras med den här Azure-modell. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800 53 kontrollen IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identifiering och verifiering (organisationens användare) | Nätverksåtkomst till Privilegierade konton - repetitionsattacker motståndskraftiga

**IA-2 (8)** informationssystem implementerar replay-motståndskraftiga autentiseringsmekanismer för nätverksåtkomst till Privilegierade konton.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Åtkomst till resurser som distribueras med den här Azure-modell är skyddat från repetitionsattacker av inbyggda Kerberos-funktionerna i Azure Active Directory, Active Directory och Windows-operativsystemet. I Kerberos-autentisering innehåller autentiseraren skickades av klienten ytterligare data, till exempel en krypterad IP-lista och klienten tidsstämplar biljett livslängd. Om ett paket spelas kontrolleras tidsstämpel. Om tidsstämpeln är tidigare än, eller samma som en tidigare authenticator paketet avvisas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800 53 kontrollen IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identifiering och verifiering (organisationens användare) | Nätverksåtkomst till icke-privilegierade konton - repetitionsattacker motståndskraftiga

**IA-2 (9)** informationssystem implementerar replay-motståndskraftiga autentiseringsmekanismer för nätverksåtkomst till icke-privilegierade konton.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Åtkomst till resurser som distribueras med den här Azure-modell är skyddat från repetitionsattacker av inbyggda Kerberos-funktionerna i Azure Active Directory, Active Directory och Windows-operativsystemet. I Kerberos-autentisering innehåller autentiseraren skickades av klienten ytterligare data, till exempel en krypterad IP-lista och klienten tidsstämplar biljett livslängd. Om ett paket spelas kontrolleras tidsstämpel. Om tidsstämpeln är tidigare än, eller samma som en tidigare authenticator paketet avvisas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800 53 kontrollen IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identifiering och verifiering (organisationens användare) | Fjärråtkomst - separat enhet

**IA-2 (11)** informationssystem implementerar flerfunktionsautentisering för fjärråtkomst till med privilegier och icke-privilegierade konton så att en av faktorerna som tillhandahålls av en enhet som är separat från systemet tillgång och att enheten uppfyller [ Tilldelning: organisation definierats styrkan hos mekanism krav].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att implementera multifaktorautentisering för att fjärransluta till kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800 53 kontrollen IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identifiering och verifiering (organisationens användare) | Godkännande av Piv-autentiseringsuppgifter

**IA-2 (12)** informationssystem accepterar och elektroniskt verifierar personliga Identity Verification PIV ()-autentiseringsuppgifter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att acceptera och verifiera personliga Identity Verification PIV ()-autentiseringsuppgifter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800 53 kontrollen IA-3

#### <a name="device-identification-and-authentication"></a>Enheten identifiering och verifiering

**IA-3** systemet som unikt identifierar och autentiserar [tilldelning: organisation definierats specifika och/eller typer av enheter] innan du upprättar en [markeringen (en eller flera): lokal; fjärrkontroll; nätverket] anslutning.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att implementera enheten identifiering och verifiering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800 53 kontrollen IA-4.a

#### <a name="identifier-management"></a>ID-hantering

**IA-4.a** organisationen hanterar information system-ID: n genom att ta emot tillstånd från [tilldelning: personal som definierats av organisationen eller roller] att tilldela en individ, grupp, roll eller enhet identifierare.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera identifierare (d.v.s. enskilda användare, grupper, roller och -enheter) för kund-resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800 53 kontrollen IA-4.b

#### <a name="identifier-management"></a>ID-hantering

**IA-4.b** organisationen hanterar information system-ID: n genom att välja en identifierare som identifierar en individ, grupp, roll eller enhet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell uppmanar under distributionen för anges av kunden identifierare för enskilda konton.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800 53 kontrollen IA-4.c

#### <a name="identifier-management"></a>ID-hantering

**IA-4.c** organisationen hanterar information system-ID: n genom att tilldela identifieraren för den avsedda person, grupp, roll eller enhet.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera identifierare (d.v.s. enskilda användare, grupper, roller och -enheter) för kund-resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800 53 kontrollen IA-4.d

#### <a name="identifier-management"></a>ID-hantering

**IA-4.d** organisationen hanterar information system-ID: n genom att förhindra återanvändning av identifierare för [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Active Directory och lokala konton i Windows-operativsystem har tilldelats en unika säkerhetsidentifierare (SID). Azure Active Directory-konton tilldelas ett globalt unikt objekt-ID. De här unika ID: N är inte kan återanvändas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800 53 kontrollen IA-4.e

#### <a name="identifier-management"></a>ID-hantering

**IA-4.e** Organisationen hanterar information system-ID: n genom att inaktivera identifierare efter [tilldelning: definierad tidsperiod inaktivitet organisation].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell implementerar en schemalagd aktivitet för Active Directory för att automatiskt inaktivera konton efter 35 dagar av inaktivitet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800 53 kontrollen IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>ID-hantering | Identifiera användarstatus

**IA-4.4** organisationen hanterar enskilda identifierare som unikt identifierar varje enskild person som [tilldelning: organisation definierad egenskap identifiera enskilda status].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Azure Active Directory och Active Directory-stöd som anger leverantörer, leverantörer och andra användare med hjälp av namnkonventioner som tillämpas på deras identifierare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800 53 kontrollen IA-5.a

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.a** organisationen hanterar information system autentiserare genom att verifiera som en del av inledande authenticator distribution, identiteten för enskilda, grupp, roll eller enhet som tar emot autentiseraren.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera autentiserare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800 53 kontrollen IA-5.b

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.b** organisationen hanterar information system autentiserare genom att etablera inledande authenticator innehåll för autentiserare som definierats av organisationen.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Alla inledande authenticator innehåll för konton som skapats av den här Azure-modell uppfyller de krav som anges i IA-5 (1) kontrolleras när anges av kunden under distributionen.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800 53 kontrollen IA-5.c

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.c** organisationen hanterar information system autentiserare genom att säkerställa att autentiserare har tillräcklig styrkan hos mekanism för den avsedda användningen.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Autentiseringar som används av den här Azure utkast uppfyller kraven för styrkan som krävs av FedRAMP. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800 53 kontrollen IA-5.d

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.d** organisationen hanterar information system autentiserare genom att upprätta och implementera administrativ procedurer för inledande authenticator distribution, förlorat/komprometteras eller skadad autentiseringar och återkalla autentiserare.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera autentiserare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800 53 kontrollen IA-5.e

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.e** Organisationen hanterar information system autentiserare genom att ändra standardinnehållet i autentiserare innan installationen information.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Alla autentiserare för komponenter i den här Azure-modell har ändrats från standardvärdena. Autentiserare kan anges av kunden under distributionen av den här lösningen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800 53 kontrollen IA-5.f

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.f** organisationen hanterar information system autentiserare genom att skapa begränsningar för lägsta och högsta livstid och återanvändning villkor för autentiserare.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att hantera autentiserare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800 53 kontrollen IA-5.g

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.g** organisationen hanterar information system autentiserare genom att ändra/uppdatera autentiserare [tilldelning: organisation definierad tidsperiod av autentiseringstyp].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En Grupprincip är etablerad och konfigurerad för att implementera begränsningar för lösenord livslängden (60 dagar). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800 53 kontrollen IA-5.h

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.h** organisationen hanterar information system autentiserare genom att skydda authenticator innehåll från obehöriga avslöjande och ändringar.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell implementerar Key Vault för att skydda authenticator innehåll från obehöriga avslöjande och ändringar. Följande autentiserare lagras i Nyckelvalvet: Azure-lösenord för distribuera kontot, virtuella administratörslösenord och lösenordet för SQL Server-tjänsten. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800 53 kontrollen IA-5.i

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.i** organisationen hanterar information system autentiserare genom att kräva att personer ska börja och med enheter som implementerar specifika säkerhetsåtgärder för att skydda autentiserare.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell implementerar Key Vault för att skydda authenticator innehåll från obehöriga avslöjande och ändringar. Följande autentiserare lagras i Nyckelvalvet: Azure-lösenord för distribuera kontot, virtuella administratörslösenord och lösenordet för SQL Server-tjänsten. Key Vault krypterar nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800 53 kontrollen IA-5.j

#### <a name="authenticator-management"></a>Autentiseraren Management

**IA-5.j** organisationen hanterar information system autentiserare genom att ändra autentiserare för grupp/roll konton när medlemskap i dessa konton ändringar.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Inga delade/grupp-konton är aktiverade på resurser som distribueras med den här Azure-modell. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800 53 kontrollen IA-5 (1) en

#### <a name="authenticator-management--password-based-authentication"></a>Hantering av Authenticator | Lösenordsbaserad autentisering

**IA-5 (1) en** informationssystem för lösenordsbaserad autentisering tillämpar minsta lösenordskomplexitet av [tilldelning: organisation definierats krav för skiftlägeskänslighet, antal tecken, blanda av versaler, gemener bokstäver, siffror och specialtecken, inklusive minimikraven för varje typ].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En Grupprincip är etablerad och konfigurerats för att upprätthålla kraven på lösenordskomplexitet för lokala konton för virtuell dator och AD-konton.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800 53 kontrollen IA-5 (1) .b

#### <a name="authenticator-management--password-based-authentication"></a>Hantering av Authenticator | Lösenordsbaserad autentisering

**IA-5 (1) .b** informationssystem för lösenordsbaserad autentisering kräver minst följande antal ändrade tecken när nya lösenord skapas: [tilldelning: organisation definierats nummer].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda lösenordsbaserad autentisering med kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800 53 kontrollen IA-5 (1) .c

#### <a name="authenticator-management--password-based-authentication"></a>Hantering av Authenticator | Lösenordsbaserad autentisering

**IA-5 (1) .c** informationssystem för lösenordsbaserad autentisering lagrar och överför kryptografiskt skyddat lösenord.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Azure Directory används för att se till att alla lösenord skyddas kryptografiskt medan lagras och överförs. Lösenord som lagras av Active Directory och lokalt på distribuerade virtuella Windows-datorer kodas automatiskt som en del av inbyggda säkerhetsfunktionerna. Remote desktop autentisering sessioner är skyddade använder TLS för att garantera autentiserare skyddas när de skickas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800 53 kontrollen IA-5 (1) .d

#### <a name="authenticator-management--password-based-authentication"></a>Hantering av Authenticator | Lösenordsbaserad autentisering

**IA-5 (1) .d** informationssystem för lösenordsbaserad autentisering tillämpar lösenord lägsta och högsta livstid begränsningar för [tilldelning: organisation definierats siffror för livstid minimum, livstid maximala].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En grupprincip upprättas och konfigurerats till att framtvinga begränsningar för lösenord som tvinga minsta (1 dag) och högsta (60 dagar) livstid begränsningar för AD och lokala konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800 53 kontrollen IA-5 (1) .e

#### <a name="authenticator-management--password-based-authentication"></a>Hantering av Authenticator | Lösenordsbaserad autentisering

**.E IA-5 (1)** Systemets information för lösenordsbaserad autentisering förhindrar återanvändning av lösenord för [tilldelning: organisation definierats nummer] generationer.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Den här Azure-modell distribuerar en domänkontrollant som alla distribuerade virtuella datorer är anslutna. En Grupprincip är etablerad och konfigurerats till att framtvinga begränsningar på återanvändning villkor (24 lösenord) för AD och lokala konton. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800 53 kontrollen IA-5 (1) .f

#### <a name="authenticator-management--password-based-authentication"></a>Hantering av Authenticator | Lösenordsbaserad autentisering

**IA-5 (1) .f** informationssystem för lösenordsbaserad autentisering tillåter användning av ett tillfälligt lösenord för system-inloggningar med en omedelbar ändring permanent lösenord.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Azure Active Directory används för att hantera styr åtkomsten till systemet. När ett konto skapas, eller ett tillfälligt lösenord skapas, används Azure Active Directory och kräver att användaren ändra lösenord vid nästa inloggning. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800 53 kontrollen IA-5 (2) en

#### <a name="authenticator-management--pki-based-authentication"></a>Hantering av Authenticator | PKI-baserad autentisering

**IA-5 (2) en** informationssystem för PKI-baserad autentisering verifierar certifikat genom att konstruera och verifierar en certifieringssökvägen till en godkänd förtroendeankare inklusive kontrollerar certifikatstatus-information.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda PKI-baserad autentisering inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800 53 kontrollen IA-5.2 .b

#### <a name="authenticator-management--pki-based-authentication"></a>Hantering av Authenticator | PKI-baserad autentisering

**IA-5.2 .b** systemets information för PKI-baserad autentisering tillämpar auktoriserad åtkomst till motsvarande privata nyckel.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda PKI-baserad autentisering inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800 53 kontrollen IA-5 (2) .c

#### <a name="authenticator-management--pki-based-authentication"></a>Hantering av Authenticator | PKI-baserad autentisering

**IA-5.2 .c** informationssystem för PKI-baserad autentisering mappar autentiserade identitet till kontot på personen eller grupp.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda PKI-baserad autentisering inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800 53 kontrollen IA-5 (2) .d

#### <a name="authenticator-management--pki-based-authentication"></a>Hantering av Authenticator | PKI-baserad autentisering

**IA-5.2 .d** informationssystem för PKI-baserad autentisering implementerar ett lokalt cacheminne för återkallningsinformation stöd för identifiering av sökväg och verifiering vid inte komma åt återkallningsinformation via nätverket.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda PKI-baserad autentisering inom kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800 53 kontrollen IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Hantering av Authenticator | i Person eller betrodda tredjeparts-registrering

**IA-5.3** organisationen kräver att registreringen bearbeta för att ta emot [tilldelning: organisation användardefinierade typer av och/eller specifika autentiserare] utföras [markeringen: personligen; av en betrodd tredje part] innan [tilldelning: organisation definierats registreringsutfärdare] med auktorisering av [tilldelning: personal som definierats av organisationen eller roller].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att registrera autentiserare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800 53 kontrollen IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Hantering av Authenticator | Automatisk stöd för lösenord styrkan bestämning

**IA-5.4** organisationen använder automatiserade verktyg för att fastställa om lösenordet autentiserare är tillräckligt starkt att uppfylla [tilldelning: krav som definierats av organisationen].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Användarkonton som distribueras med den här Azure-modell innehåller AD och lokala användarkonton. Båda dessa ger mekanismer som tvingar kompatibilitet med etablerade lösenordskrav för att skapa ett initialt lösenord och under lösenordsändringar. Azure Active Directory är automatiseringsverktyg utnyttjas för att fastställa om lösenordet autentiserare är tillräckligt starkt att uppfylla längd på lösenord, komplexitet, rotation och livslängd begränsningar som fastställs i IA-5 (1). Azure Active Directory säkerställer att authenticator lösenordssäkerhet när skapas uppfyller dessa normer. Kunden har angett lösenord som används för att distribuera den här lösningen kontrolleras för att uppfylla lösenordskrav. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800 53 kontrollen IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Hantering av Authenticator | Skydd av autentiseringar

**IA-5 (6)** organisationen skyddar autentiserare proportion till kategorin skydd av information som tillåter användning av autentiseraren åtkomst.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att skydda autentiserare. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800 53 kontrollen IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Hantering av Authenticator | Inga inbäddade okrypterad statiska autentiseringar

**IA-5.7** organisationen säkerställer att okrypterad statiska autentiserare inte inbäddade i program eller skript åtkomst eller lagras på funktionstangenterna.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen användning av okrypterade statiska autentiserare inbäddade i program, skript för att komma åt eller funktionstangenter som distribueras med den här Azure-modell. Skript eller program som använder en autentiserare gör ett anrop till en Azure Key Vault-behållare före varje användning. Åtkomst till Azure Key Vault behållare granskas, vilket gör att identifiering av brott mot detta förbud om ett tjänstkonto som används för att komma åt ett system utan ett motsvarande anrop till Azure Key Vault-behållaren. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800 53 kontrollen IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Hantering av Authenticator | Flera Information System-konton

**IA-5 (8)** organisation implementerar [tilldelning: organisation definierats säkerhetsåtgärder] att hantera risken för angrepp på grund av individer med konton på flera informationssystem.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på företagsnivå säkerhetsåtgärder för att hantera risker som är kopplade till enskilda användare med konton på flera system. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800 53 kontrollen IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Hantering av Authenticator | Maskinvara tokenbaserad autentisering

**IA-5 (11)** informationssystem för maskinvara tokenbaserad autentisering, använder mekanismer som uppfyller [tilldelning: organisation definierats token kvalitetskrav].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda mekanismer för att uppfylla maskinvarukraven tokenbaserad autentisering kvalitet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800 53 kontrollen IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Hantering av Authenticator | Förfallotiden för cachelagrade autentiseringar

**IA-5 (13)** informationssystem förhindrar att den cachelagrade autentiserare efter [tilldelning: definierad tidsperiod organisation].

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Inga resurser har distribuerats av den här Azure-modell är konfigurerade för att tillåta användning av cachelagrade autentiserare. Autentisering för att distribuerade virtuella datorer kräver att en autentiserare anges vid tidpunkten för autentisering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800 53 kontrollen IA-6

#### <a name="authenticator-feedback"></a>Autentiseraren Feedback

**IA-6** systemet dölja feedback för autentiseringsinformation under autentiseringsprocessen skydda informationen från möjliga utnyttjande/användning av obehöriga.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Åtkomst till resurser som distribueras med den här Azure-modell är via fjärrskrivbord och bygger på Windows-autentisering. Standardbeteendet för Windows-autentisering sessioner döljer lösenord när indata under en session för autentisering.  |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800 53 kontrollen IA-7

#### <a name="cryptographic-module-authentication"></a>Kryptografiska modulen för autentisering

**IA-7** informationssystem implementerar mekanismer för autentisering till en kryptografiska modul som uppfyller kraven för tillämpliga federal lagar Executive order, direktiven, principer, standarder, och riktlinjer för sådana autentisering.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Windows-autentisering, fjärrskrivbord och BitLocker är anställda av den här Azure-modell. Dessa komponenter kan konfigureras för att förlita sig på FIPS 140 validerade kryptografiska modulerna. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800 53 kontrollen IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identifiering och verifiering (icke-organisationens användare)

**IA-8** systemet som unikt identifierar och autentiserar inte organisationens användare (eller processer som agerar på uppdrag av icke-organisationens användare).

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att identifiera och autentisera icke organisationens användare åtkomst till kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800 53 kontrollen IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identifiering och verifiering (icke-organisationens användare) | Godkännande av Piv autentiseringsuppgifter från andra myndigheter

**IA-8.1** informationssystem accepterar och elektroniskt verifierar personliga identitet verifiering (PIV) autentiseringsuppgifter från andra federala myndigheter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att acceptera och verifiera personliga Identity Verification PIV ()-autentiseringsuppgifter som utfärdats av andra federala myndigheter. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800 53 kontrollen IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identifiering och verifiering (icke-organisationens användare) | Godkännande av tredjeparts-autentiseringsuppgifter

**IA-8 (2)** informationssystem accepterar endast FICAM godkända tredjeparts-autentiseringsuppgifter.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att endast acceptera tredjeparts-autentiseringsuppgifter som har godkänts av initiativet Federal identitet, autentiseringsuppgifter och Access Management (FICAM) förtroende Framework lösningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800 53 kontrollen IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identifiering och verifiering (icke-organisationens användare) | Användning av Ficam godkända produkter

**IA-8.3** organisationen använder endast godkända FICAM information komponenterna i [tilldelning: organisation definierats informationssystem] acceptera autentiseringsuppgifter från tredje part.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att använda endast Federal identitet, autentiseringsuppgifter och Access Management (FICAM) förtroende Framework lösningar initiativ godkända resurser för att acceptera autentiseringsuppgifter från tredje part. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800 53 kontrollen IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identifiering och verifiering (icke-organisationens användare) | Användning av Ficam-utfärdade profiler

**IA-8 (4)** informationssystem överensstämmer med FICAM utfärdade profiler.

**Ansvarsområden:**`Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för som överensstämmer med de profiler som utfärdats av initiativet Federal identitet, autentiseringsuppgifter och Access Management (FICAM) förtroende Framework lösningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |
