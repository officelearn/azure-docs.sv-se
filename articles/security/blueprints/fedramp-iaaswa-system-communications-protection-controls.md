---
title: Säkerhet och efterlevnad modell - FedRAMP Web Applications Automation - systemet och Azure kommunikation skydd
description: FedRAMP Web Applications Automation - System och kommunikation skydd
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6749ad50cd1ea1cd4ec6ca2f86fef43a9f1515d9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207133"
---
# <a name="system-and-communications-protection-sc"></a>System och kommunikation skydd (SC)

> [!NOTE]
> De här kontrollerna definieras av NIST och USA Handelsdepartementet som en del av NIST Special Publication 800-53 version 4. Läs NIST 800 53 Rev. 4 för information om hur du testar procedurer och riktlinjer för varje kontroll.

## <a name="nist-800-53-control-sc-1"></a>NIST 800 53 kontrollen SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>System och kommunikation Protection-principen och procedurer

**SC-1** organisation utvecklar, dokument och sprider till [tilldelning: personal som definierats av organisationen eller roller] en system- och skyddsprincip som åtgärdar syfte, scope, roller, ansvar, management åtagande samordning av organisationens entiteter och efterlevnad; och procedurer för att underlätta genomförandet av systemet och kommunikation protection-principen och associerade system och kommunikation skydd kontroller. och granskar och uppdaterar den aktuella system och kommunikation skyddsprincipen [tilldelning: organisation definierats frekvens]; och procedurer för system och kommunikation skydd [tilldelning: organisation definierats frekvens].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå system och kommunikation protection-principen och procedurer kan vara tillräcklig för att åtgärda den här kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800 53 kontrollen SC-2

#### <a name="application-partitioning"></a>Programmet partitionering

**SC-2** informationssystem skiljer funktioner för användarnamn (inklusive användare gränssnittet services) från information system-hanteringsfunktioner.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet skiljer användaren funktioner från system management-funktion via verkställandet av logiska åtkomstkontroller och systemarkitektur. Användaren funktionerna är begränsade till kunden distribuerade program webbgränssnitt. Gränssnitt för system management-funktioner är separat från användargränssnitt. Alla anslutning är via en säker skyddsmiljö värd (jumpbox) finns i ett undernät för hantering med regler för nätverkssäkerhetsgrupper att begränsa åtkomsten till produktionsresurser efter behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800 53 kontrollen SC-3

#### <a name="security-function-isolation"></a>Funktionen Säkerhetsisolering

**SC-3** systemet isolerar säkerhetsfunktioner från icke-funktioner.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Windows underhåller separat körning domäner för varje process som körs genom att tilldela varje process ett privat virtuellt adressutrymme. Dessutom kan de lösning implementerar en arkitektur och åtkomst-kontroller som utformats för att isolera säkerhetsfunktionerna vid behov. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800 53 kontrollen SC-4

#### <a name="information-in-shared-resources"></a>Informationen i delade resurser

**SC-4** systemet som förhindrar obehörig och oavsiktliga överföringen via delade systemresurser.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Operativsystemet hanterar resurser (t.ex. minne, lagring) så att informationen är bara tillgängliga för användare och roller med rätt behörighet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800 53 kontrollen SC-5

#### <a name="denial-of-service-protection"></a>Denial of Service-skydd

**SC-5** systemet som skyddar mot eller begränsar effekterna av följande typer av DOS-attacker: [tilldelning: organisation användardefinierade typer av denial of service-attacker eller referenser till källor för sådan information] av använda [tilldelning: organisation definierats säkerhetsåtgärder].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en Programgateway som innehåller en brandvägg för webbaserade program och läsa in belastningsutjämning funktioner. Den distribuerade virtuella datorer stöder webbnivå, databasnivå och Active Directory har distribuerats i en skalbar tillgänglighetsuppsättning. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800 53 kontrollen SC-6

#### <a name="resource-availability"></a>Resurstillgänglighet

**SC-6** systemet skyddar tillgängligheten för resurser genom att allokera [tilldelning: organisation definierats resurser] av [markeringen (en eller flera) prioritet; kvot. [Tilldelning: organisation definierats säkerhetsåtgärder]].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Varje Windows-process innehåller resurser som behövs för att köra ett program. Prioritet för resursen hanteras av operativsystemet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800 53 kontrollen SC-7.a

#### <a name="boundary-protection"></a>Gräns-skydd

**SC-7.a** informationssystem Övervakare och kontrollerar kommunikationen på den externa gränsen för systemet och viktiga interna gränser i systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en Programgateway, belastningsutjämnare och konfigurerar regler för nätverkssäkerhetsgrupper för att styra commutations på externa gränser och mellan internt undernät. Programgateway, belastningsutjämnare, och nätverk säkerhetshändelse grupp och diagnostiska loggar samlas in av logganalys att kunden övervakning. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800 53 kontrollen SC-7.b

#### <a name="boundary-protection"></a>Gräns-skydd

**SC-7.b** informationssystem implementerar undernätverk för offentligt tillgänglig systemkomponenter som [markeringen: fysiskt; logiskt] separeras från interna organisationens nätverk.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar resurser i en arkitektur med ett separat undernät, databasen undernät, Active Directory-undernät och undernät för hantering. Undernät logiskt avgränsade med regler för nätverkssäkerhetsgrupper används för att begränsa trafik mellan undernät att endast som krävs för system-och hanteringsfunktioner individuella undernät (t.ex. externa trafiken kan inte komma åt databasen, hantering, eller Active Directory-undernät). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800 53 kontrollen SC-7.c

#### <a name="boundary-protection"></a>Gräns-skydd

**SC-7.c** information anslutning till externa nätverk eller informationssystem endast via hanterade gränssnitt som består av gräns enheter ordnade i enlighet med en organisations Säkerhetsarkitektur.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar en Programgateway för att hantera externa anslutningar till ett kunden distribuerade program. Externa anslutningar för av hanteringsåtkomst är begränsad till en skyddsmiljö värd- / jumpbox som distribueras i ett undernät för hantering med Nätverkssäkerhetsregler som används för att begränsa externa anslutningar till auktoriserade IP-adresser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800 53 kontrollen SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Gräns skydd | Åtkomstpunkter

**SC-7.3** organisationen begränsar antalet externa anslutningar till systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar två offentliga IP-adresser: en som associeras med Programgatewayen; en kopplad till värden för hantering av skyddsmiljön / jumpbox. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800 53 kontrollen SC-7.4 schemanivån

#### <a name="boundary-protection--external-telecommunications-services"></a>Gräns skydd | Externa telekommunikationstjänster

**SC-7.4 schemanivån** organisationen implementerar ett gränssnitt för hanterade för varje externa telekommunikation-tjänst.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar två offentliga IP-adresser: en som associeras med Programgatewayen; en kopplad till värden för hantering av skyddsmiljön / jumpbox. Hantering av dessa gränssnitt aktiveras via programvarudefinierat nätverk. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800 53 kontrollen SC-7.4 .b

#### <a name="boundary-protection--external-telecommunications-services"></a>Gräns skydd | Externa telekommunikationstjänster

**SC-7.4 .b** organisationen upprättar en princip för flödet av trafik för varje hanterad gränssnitt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar två offentliga IP-adresser: en som associeras med Programgatewayen; en kopplad till värden för hantering av skyddsmiljön / jumpbox. Hantering av dessa gränssnitt aktiveras via programvarudefinierat nätverk. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800 53 kontrollen SC-7.4 .c

#### <a name="boundary-protection--external-telecommunications-services"></a>Gräns skydd | Externa telekommunikationstjänster

**SC-7.4 .c** organisationen skyddar sekretess och integriteten hos den information som skickas över varje gränssnitt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Web Programgateway distribueras med den här modell har konfigurerats med en HTTPS-lyssnare följande sekretess och integriteten för kommunikationssessioner. Anslutning till fjärrskrivbord till jumpbox krypteras också att tillhandahålla sekretess och integritet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800 53 kontrollen SC-7.4 .d

#### <a name="boundary-protection--external-telecommunications-services"></a>Gräns skydd | Externa telekommunikationstjänster

**SC-7.4 .d** organisationen dokument varje undantag till principen trafik flöde med stöder verksamhetskritisk/affärsbehov och varaktighet som måste.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunder ansvarar inte för datacenter-åtgärder (till exempel telekommunikation services). Alla telekommunikation tillhandahålls och hanteras av Microsoft Azure. Den här kontrollen har ärvts från Azure. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800 53 kontrollen SC-7.4 .e

#### <a name="boundary-protection--external-telecommunications-services"></a>Gräns skydd | Externa telekommunikationstjänster

**SC-7.4 .e** Organisationen granskar undantag till principen trafik flödet [tilldelning: organisation definierats frekvens] och tar bort undantag som inte längre stöds av en explicit uppdrag/affärsbehov.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunder ansvarar inte för datacenter-åtgärder (till exempel telekommunikation services). Alla telekommunikation tillhandahålls och hanteras av Microsoft Azure. Den här kontrollen har ärvts från Azure. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800 53 kontrollen SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Gräns skydd | Neka som standard / tillåta med undantag

**SC-7.5** informationssystem på hanterade gränssnitt nekar nätverkstrafik för kommunikation som standard och tillåter kommunikation nätverkstrafik som undantag (d.v.s. neka alla, tillåter av undantag).

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Regeluppsättningar för nätverkssäkerhetsgrupper som distribueras med den här utkast konfigureras med ett neka standardschema. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800 53 kontrollen SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Gräns skydd | Förhindra att delade tunnlar för fjärranslutna enheter

**SC-7 (7)** informationssystem, tillsammans med en fjärransluten enhet förhindrar enheten från samtidigt upprätta – remote anslutningar med systemet och kommunikation via en anslutning till resurser i externa nätverk.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Konfigurationsprincip för kundens företagsnivå fjärranslutna enheter kan hantera delade tunnlar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800 53 kontrollen SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Gräns skydd | Vidarebefordra trafik till autentiserade proxyservrar

**SC-7 (8)** systemvägar information [tilldelning: organisation definierats intern kommunikation trafik] till [tilldelning: organisation definierats externa nätverk] via autentiserade proxyservrar på hanterade gränssnitt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för kunddefinierade routningsinformation via en autentiserad proxyserver till ett externt nätverk. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800 53 kontrollen SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Gräns skydd | Förhindra att obehöriga Exfiltration

**SC-7 (10)** organisationen förhindrar obehörig exfiltration information över hanterade gränssnitt.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att förhindra obehörig exfiltration information över hanterade gränssnitt. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800 53 kontrollen SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Gräns skydd | Skydd på värden

**SC-7 (12)** organisation implementerar [tilldelning: organisation definierats värdbaserad gräns skyddet] vid [tilldelning: organisation definierats information systemkomponenter].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Virtuella datorer som distribueras med den här modell har konfigurerats med en värdbaserad brandvägg aktiverad. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800 53 kontrollen SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Gräns skydd | Isolering av säkerhetsverktyg / metoder / Support komponenter

**SC-7 (13)** organisation isolerar [tilldelning: organisation definierats information säkerhetsverktyg, metoder och support komponenter] från andra systemkomponenter för intern information genom att implementera fysiskt separata undernätverk med Hantera gränssnitt till andra komponenter i systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar resurser i en arkitektur med en separat management undernät kunden distribution av säkerhetsverktyg information och support-komponenter. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800 53 kontrollen SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Gräns skydd | Misslyckas säkra

**SC-7 (18)** informationssystem på ett säkert sätt misslyckas om en operativa kraschar för en gräns skydd enhet.

**Ansvarsområden:** `Azure Only`

|||
|---|---|
| **Kunden** | Det finns inga fysiska gränsen skydd enheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Microsoft Azure distribuerar geografiskt separat och redundanta Gateway-servrar och SSL VPN. När en Gateway-system misslyckas, misslyckas på ett säkert sätt och åtkomsten är begränsad till miljön. En användare måste upprätta en separat anslutning till en aktiv gatewayserver som hanteras av Microsoft Azure för att upprätta en anslutning till Microsoft Azure-miljön. <br /> Dessutom om Microsoft Azure network-enheter (inklusive routrar i utkanten åtkomst routrar, belastningsutjämnare, aggregering växlar och TORS) misslyckas, blir berörda kretsen frånkopplat, vilket misslyckas på ett säkert sätt. Fel på en nätverksenhet i Microsoft Azure kan leda till eller låta information utanför systemet för att ange enheten eller ett fel kan bevilja obehöriga information versionen. Inbyggd redundans kan Microsoft Azure tillgångar kan gå sönder utan att påverka tillgängligheten. |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800 53 kontrollen SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Gräns skydd | Dynamisk isolering / ansvarsfördelning

**SC-7 (20)** informationssystem ger möjlighet att dynamiskt isolera/särskilja [tilldelning: organisation definierats information systemkomponenter] från andra komponenter i systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att säkerställa att systemet har möjlighet att dynamiskt isolera kunden distribuerade resurser. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800 53 kontrollen SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Gräns skydd | Isolering av Information systemkomponenter

**SC-7 (21)** organisationen använder gräns skyddsmekanismer för att avgränsa [tilldelning: organisation definierats information systemkomponenter] stöder [tilldelning: organisation definierats uppdrag och/eller business funktioner].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar resurser i en arkitektur med ett separat undernät, databasen undernät, Active Directory-undernät och undernät för hantering. Undernät är logiskt åtskilda av reglerna för nätverkssäkerhetsgrupper tillämpas på individuella undernät för att begränsa trafik mellan undernät att endast som krävs för system-och hanteringsfunktioner. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800 53 kontrollen SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Överföring sekretess och integritet

**SC-8** informationssystem skyddar den [markeringen (en eller flera): sekretess, integritet] för den överförda informationen.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | SI 8 (1) implementeringen uppfyller detta krav för kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800 53 kontrollen SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Överföring sekretess och integritet | Kryptografiska eller en annan fysisk skydd

**SC-8.1** informationssystem implementerar kryptografiska mekanismer för att [markeringen (en eller flera): förhindra obehörig avslöjande av information, identifiera ändringar av informationen] under överföringen såvida inte annat skyddas av [tilldelning: organisation användardefinierade alternativa fysiska skydd].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet konfigurerar resurser för att kommunicera med hjälp av endast säkra protokoll. Komponenten Brandvägg i Programgatewayen har konfigurerats att acceptera kommunikationen från externa användare via HTTPS/TLS och kommunicera med serverdelspoolen endast över HTTPS/TLS. SQL Server är konfigurerad för att kommunicera endast över HTTPS/TLS. Fjärrskrivbordstjänster är konfigurerade för att använda säkra anslutningar. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800 53 kontrollen SC-10

#### <a name="network-disconnect"></a>Koppla från nätverket

**SC-10** informationssystem avslutar på nätverksanslutningen som associeras med ett session-kommunikation i slutet av sessionen eller efter [tilldelning: definierad tidsperiod organisation] av inaktivitet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Autentisering för fjärrskrivbordssessioner hanteras av Active Directory. När åtkomst är inaktiverat för en användare i Active Directory, fjärrsessioner omedelbart att avslutas. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800 53 kontrollen SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Kryptografisk nyckel etablering och hantering

**SC-12** upprättar och hanterar kryptografiska nycklar för krävs kryptering med anställda inom informationssystem i enlighet med [tilldelning: organisation definierats kraven för generering av nycklar, distribution, lagring, åtkomst och destruktion].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det här utkastet distribuerar ett Azure Key Vault. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Azure Key Vault kan generera nycklar med hjälp av en FIPS 140-2-nivå 2 maskinvara security module (HSM) nyckelgenerering kapaciteten. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800 53 kontrollen SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Kryptografisk nyckel etablering och hantering | Tillgänglighet

**SC-12.1** organisationen underhåller tillgänglighet i händelse av förlust av kryptografiska nycklar av användare.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure Key Vault används för att lagra kryptografiska nycklar och hemligheter som används i det här utkastet. Key Vault förenklar nyckelhanteringen för nycklar som kommer åt och krypterar data. Följande autentiserare lagras i Nyckelvalvet: Azure-lösenord för distribuera kontot, virtuella administratörslösenord och lösenordet för SQL Server-tjänsten. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800 53 kontrollen SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Kryptografisk nyckel etablering och hantering | Symmetriska nycklar

**SC-12 (2)** organisationen producerar, styr och distribuerar symmetriska kryptografiska nycklar med hjälp av [markeringen: NIST FIPS-kompatibla; NSA godkända] nyckelhantering teknik och processer.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Azure Key Vault används för att skapa, styra och distribuera kryptografiska nycklar. Azure Key Vault kan generera nycklar med hjälp av en FIPS 140-2-nivå 2 maskinvara security module (HSM) nyckelgenerering kapaciteten. Nycklar lagras och hanteras i säkert krypterade behållare i Azure Key Vault. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800 53 kontrollen SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Kryptografisk nyckel etablering och hantering | Asymmetriska nycklar

**SC-12 (3)** organisationen producerar, styr och distribuerar asymmetriska kryptografiska nycklar med hjälp av [markeringen: NSA godkända nyckelhantering teknik och processer; godkända PKI klass 3-certifikat eller prepositioned nyckelmaterial; godkända PKI klass 3 eller 4 för klassen certifikat och maskinvaru-säkerhetstoken som skyddar den privata nyckeln].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för producerar, styra och distribuerar asymmetriska kryptografiska nycklar (om de används i kunden distribuerade resurser). |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800 53 kontrollen SC-13

#### <a name="cryptographic-protection"></a>Kryptografiska skydd

**SC-13** informationssystem implementerar [tilldelning: organisation definierats kryptografiska använder och typ av kryptering som krävs för varje användning] i enlighet med tillämpliga federal lagar Executive order direktiven, principer, regler, och standarder.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Windows-autentisering, fjärrskrivbord och BitLocker är anställda av det här utkastet. Dessa komponenter kan konfigureras för att förlita sig på FIPS 140 validerade kryptografiska modulerna. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800 53 kontrollen SC-15.a

#### <a name="collaborative-computing-devices"></a>Samarbetsfunktioner Computing enheter

**SC-15.a** informationssystemet tillåter inte fjärraktivering av samarbetsfunktioner datorenheter med följande undantag: [tilldelning: organisation definierats undantag där fjärraktivering ska tillåtas].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns inga samarbetsfunktioner datorenheter som distribueras som en del av det här utkastet. Obs: Det finns fysiska samarbetsfunktioner datorenheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800 53 kontrollen SC-15.b

#### <a name="collaborative-computing-devices"></a>Samarbetsfunktioner Computing enheter

**SC-15.b** informationssystem ger explicit uppgift om användning för användare som är fysiskt närvarande vid enheterna.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns inga samarbetsfunktioner datorenheter som distribueras som en del av det här utkastet. Obs: Det finns fysiska samarbetsfunktioner datorenheter inom omfånget för system som distribueras på Azure. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800 53 kontrollen SC-17

#### <a name="public-key-infrastructure-certificates"></a>PKI-certifikat

**SC-17** organisationen utfärdar certifikat för offentlig nyckel under en [tilldelning: organisation definierats certifikatprincip] eller hämtar certifikat med offentlig nyckel från en godkänd-leverantör.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan förlita sig på en företagsnivå public key infrastructure för utfärdande av certifikat. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800 53 kontrollen SC-18.a

#### <a name="mobile-code"></a>Mobil kod

**SC-18.a** organisationen definierar acceptabelt och oacceptabelt mobil kod och tekniker för mobil kod.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå system och procedurer för skydd av kommunikation kan definiera acceptabelt och oacceptabelt mobil kod. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800 53 kontrollen SC-18.b

#### <a name="mobile-code"></a>Mobil kod

**SC-18.b** organisationen upprättar implementering vägledning för godkända mobil kod och mobil kod tekniker och användningsbegränsningar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kundens på företagsnivå system och kommunikation skydd procedurer kan upprätta begränsningar för användning av mobil kod. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800 53 kontrollen SC-18.c

#### <a name="mobile-code"></a>Mobil kod

**SC-18.c** organisationen godkänner, övervakar och styr användningen av mobil kod inom systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden kan vara beroende av en process på företagsnivå för auktorisering, övervakning och kontroll av användningen av mobil kod. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800 53 kontrollen SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.a** organisationen upprättar implementering vägledning för röst över Internet Protocol (VoIP)-teknik baserat på kan orsaka skada informationssystemet om vissa ändringar och användningsbegränsningar.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen Röst via internet protocol-teknik som distribueras som en del av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800 53 kontrollen SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.b** organisationen godkänner, övervakar och styr användningen av VoIP inom systemet.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Det finns ingen Röst via internet protocol-teknik som distribueras som en del av det här utkastet. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800 53 kontrollen SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Skydda namn / adress namnmatchningstjänst (auktoritativ datakälla)

**SC-20.a** informationssystem ger ytterligare dataursprung autentisering och integritet verifiering artefakter auktoritära namn upplösning data systemet återgår som svar på frågor för matchning av extern namn/adress.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för en säker namn och adress namnmatchningstjänst. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800 53 kontrollen SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Skydda namn / adress namnmatchningstjänst (auktoritativ datakälla)

**SC-20.b** informationssystem innebär att du visar säkerhetsstatusen för underordnade zoner och (om underordnat har stöd för säker lösning services) att aktivera kontroll av en kedja av förtroende mellan överordnade och underordnade domäner, när som ingår i en distribuerad, hierarkiskt namnområde.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden är ansvarig för en säker namn och adress namnmatchningstjänst. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800 53 kontrollen SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Skydda namn / adress namnmatchningstjänst (rekursiv eller cachelagring matcharen)

**SC-21** informationssystemet begär och utför autentisering av dataursprung och kontrollera dataintegriteten namn/adress upplösning svaren systemet tar emot från auktoritära källor.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att konfigurera kunden distribuerade resurser för att begära och utföra autentisering av dataursprung och kontrollera dataintegriteten i namn/adress upplösning svar som tagits emot från auktoritära källor. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800 53 kontrollen SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Arkitektur och etablerar för namn / adress namnmatchningstjänst

**SC-22** informationssystem som tillsammans ger namn/adress namnmatchningstjänst för en organisation är feltolerant och implementera intern/extern separering.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att säkerställa att de system som tillhandahåller adress lösning för kunden distribuerade resurser feltoleranta och genomföra intern/extern separering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800 53 kontrollen SC-23

#### <a name="session-authenticity"></a>Sessionen äkthetsbeviset

**SC-23** informationssystem skyddar kommunikationssessioner är äkta.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Fjärråtkomst till resurser som distribueras med den här modell, inklusive Azure-portalen, fjärrskrivbord och web Programgateway skyddas med hjälp av TLS. TLS innehåller äkthetsbeviset för kommunikation på nivån session. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800 53 kontrollen SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Sessionen äkthetsbeviset | Ogiltigförklara sessions-ID: N på Logga ut

**SC-23 (1)** informationssystem upphäver session identifierare användaren logga ut eller andra sessionen avslutas.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Fjärråtkomst till resurser som distribueras med den här modell, inklusive Azure-portalen, fjärrskrivbord och web Programgateway skyddas med hjälp av TLS. Azure-portalen och fjärrskrivbordssessioner ogiltig identifierare för sessionen på Logga ut. Web session ogiltigförklarade tillämpas via Azure Application Gateway - Web Application Firewall (Brandvägg) regler. Brandvägg gäller cookie mappning mellan sessioner och utför sessionstidsgränsen efter 30 minuter (konfigureras efter distribution av organisation särskilda regler) inaktivitet från klienten. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800 53 kontrollen SC-24

#### <a name="fail-in-known-state"></a>Fel i fungerande tillstånd

**SC-24** informationssystemet inte en [tilldelning: organisation definierats fungerande tillstånd] för [tilldelning: organisation användardefinierade typer av fel] bevarar [tilldelning: organisation definierats tillstånd Systeminformation] i fel.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Kunden ansvarar för att säkerställa kunden distribuerade resurser misslyckas i ett fungerande tillstånd. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800 53 kontrollen SC-28

#### <a name="protection-of-information-at-rest"></a>Skydd av Information i vila

**SC-28** informationssystem skyddar den [markeringen (en eller flera): sekretess, integritet] av [tilldelning: organisation definierats information vilande].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | SC-28 (1) implementeringen uppfyller detta krav för kontrollen. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800 53 kontrollen SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Skydd av Information i vila | Kryptografiska skydd

**SC-28 (1)** informationssystem implementerar kryptografiska metoder för att förhindra obehörig avslöjande och ändring av [tilldelning: organisation definierats information] på [tilldelning: organisation definierats informationssystem komponenter].

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | Virtuella datorer som distribueras med den här utkast implementera diskkryptering för att skydda sekretess och integriteten för information i vila. Azure disk encryption för Windows implementeras med hjälp av funktionen BitLocker i Windows. SQL Server är konfigurerad att använda Transparent Data kryptering (TDE), som utför realtid kryptering och dekryptering av data och loggfiler för att skydda information i vila. TDE ger försäkran om att lagrade data inte har omfattas obehörig åtkomst. Kunden kan välja att implementera ytterligare programnivå kontroller för att skydda informationen. Sekretess och integriteten hos alla storage-blobbar som distribueras med den här utkast skyddas med hjälp av Azure Storage Service kryptering (SSE). SSE skyddar data i vila i Azure storage-konton med hjälp av 256-bitars AES-kryptering. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800 53 kontrollen SC-39

#### <a name="process-isolation"></a>Processisolering av

**SC-39** informationssystemet har en separat körning domän för varje process som körs.

**Ansvarsområden:** `Customer Only`

|||
|---|---|
| **Kunden** | De virtuella datorerna som distribueras med den här utkast kör Windows-operativsystem. Windows underhåller separat körning domäner för varje process som körs genom att tilldela varje process ett privat virtuellt adressutrymme. |
| **Providern (Microsoft Azure)** | Inte tillämpligt |
