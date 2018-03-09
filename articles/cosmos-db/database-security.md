---
title: "Säkerhet – Azure Cosmos-DB-databas | Microsoft Docs"
description: "Lär dig hur Azure Cosmos DB ger skydd och data databassäkerhet för dina data."
keywords: "nosql-databas säkerhet, informationssäkerhet, datasäkerhet, databaskryptering, databasskyddet och IPSec-principer säkerhet testning"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: f6e05d410a8018f0a88a378e767f7a7e6fde6f0c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-cosmos-db-database-security"></a>Azure DB Cosmos database-säkerhet

Den här artikeln beskrivs databasen Metodtips om säkerhet och viktiga funktioner som erbjuds av Cosmos Azure DB som hjälper dig att förebygga, upptäcka och åtgärda till databasen överträdelser.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Vad är nytt i Azure Cosmos DB säkerhet?

Kryptering i vila är nu tillgänglig för dokument och säkerhetskopior som lagras i Azure Cosmos-DB i alla Azure-regioner. Kryptering i vila används automatiskt för nya och befintliga kunder i dessa regioner. Behöver inte konfigurera något; Du får samma bra svarstid, dataflöde, tillgänglighet och funktioner som innan det har fördelen att veta dina data är säkra med kryptering i vila.

## <a name="how-do-i-secure-my-database"></a>Hur skyddar databasen? 

Datasäkerhet är en delad ansvar mellan du kunden och databas-provider. Mängden ansvar du har kan variera beroende på den databasprovider som du väljer. Du måste ange allt från slutpunkt skydd till fysisk säkerhet för din maskinvara – finns ingen enkel aktivitet om du väljer en lokal lösning. Om du väljer en PaaS databasen molntjänstleverantör, till exempel Azure Cosmos DB minskar ditt område rör avsevärt. Följande bild, lånar från Microsofts [delade ansvarsområden för Cloud Computing](https://aka.ms/sharedresponsibility) faktabladet, visar hur ditt ansvar minskar med en PaaS-provider som Azure Cosmos DB.

![Kund- och providern ansvarsområden](./media/database-security/nosql-database-security-responsibilities.png)

Föregående diagram visar utförligt molnet säkerhetskomponenter, men vilka objekt du behöver oroa specifikt för din databaslösning? Och hur kan du jämföra lösningar till varandra? 

Vi rekommenderar följande checklista för krav som du vill jämföra databassystem:

- Nätverkssäkerhet och brandväggsinställningar
- Användarautentisering och detaljerade användarkontroller
- Möjlighet att replikera data globalt för regionala fel
- Möjligheten att utföra växling vid fel från ett datacenter till en annan
- Replikering av lokala data inom ett datacenter
- Automatisk säkerhetskopiering
- Återställning av borttagna data från säkerhetskopior
- Skydda och isolera känsliga data
- Övervakning för attacker
- Svara på attacker
- Möjlighet att geo avgränsningstecken data följa databegränsningar för styrning
- Fysiska skydd av servrar i skyddade Datacenter

Och även om det kan verka uppenbara, senaste [storskaliga databasen överträdelser](http://thehackernews.com/2017/01/mongodb-database-security.html) Påminn oss enkla men kritiska vikten av följande krav:
- Korrigerade servrar som är uppdaterade
- HTTPS som standard/SSL-kryptering
- Administrativa konton med starka lösenord

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hur skydda databasen i Azure Cosmos DB?

Nu ska vi gå tillbaka till föregående lista – hur många av dessa säkerhetskrav Azure Cosmos DB tillhandahåller? Varje enskild komponent.

Nu ska vi prova var och en i detalj.

|Säkerhetskrav|Azure Cosmos DB säkerhet metod|
|---|---|---|
|Nätverkssäkerhet|Det första lagret för att säkra din databas är att använda en IP-brandvägg. Azure Cosmos-DB stöder drivs IP-baserade åtkomstkontroller för inkommande brandväggsstöd-princip. IP-baserad åtkomstkontroller liknar de brandväggsregler som används av traditionella databassystem, men de har expanderats så att Azure DB som Cosmos-databaskonto är endast tillgänglig från godkända uppsättning datorer eller molntjänster. <br><br>Azure Cosmos-DB kan du aktivera en specifik IP-adress (168.61.48.0), ett IP-adressintervall (168.61.48.0/8) och kombinationer av IP-adresser och intervall. <br><br>Alla begäranden från datorer utanför den här listan över tillåtna blockeras av Azure Cosmos DB. Begäranden från godkända datorer och molntjänster sedan måste slutföra verifieringsprocessen för att få åtkomstkontroll till resurser.<br><br>Läs mer i [Azure DB som Cosmos-brandväggsstöd](firewall-support.md).|
|Auktorisering|Azure Cosmos-DB använder hashbaserad meddelandeautentiseringskod (HMAC) för auktorisering. <br><br>Varje begäran hashas med den hemliga kontonyckel och efterföljande Base64-kodad Hash-värdet skickas med varje anrop till Azure Cosmos DB. För att verifiera begäran tjänsten Azure Cosmos DB som använder rätt hemliga nyckel och egenskaper för att generera ett hash-värde och sedan den jämför värdet med det i begäran. Om de två värdena matchar varandra åtgärden har korrekt behörighet och begäran har bearbetats, annars är det ett auktoriseringsfel och begäran avvisas.<br><br>Du kan använda antingen en [huvudnyckeln](secure-access-to-data.md#master-keys), eller en [resurs token](secure-access-to-data.md#resource-tokens) detaljerade åtkomst till en resurs, till exempel ett dokument.<br><br>Läs mer i [skydda åtkomsten till resurser i Azure Cosmos DB](secure-access-to-data.md).|
|Användare och behörigheter|Med hjälp av den [huvudnyckeln](#master-key) för kontot, du kan skapa användare och behörigheten resurser per databas. En [resurs token](#resource-token) är associerad med en behörighet i en databas och avgör om användaren har åtkomst (skrivskyddad, skrivskyddad eller ingen åtkomst) till en resurs för programmet i databasen. Programresurser inkludera samlingar, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er. Resurs-token används sedan under autentiseringen för att tillhandahålla eller neka åtkomst till resursen.<br><br>Läs mer i [skydda åtkomsten till resurser i Azure Cosmos DB](secure-access-to-data.md).|
|Active directory-integrering (RBAC)| Du kan också ge åtkomst till det konto använder åtkomstkontroll (IAM) i Azure-portalen som visas i skärmbilden som följer den här tabellen. IAM ger rollbaserad åtkomstkontroll och integreras med Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper som visas i följande bild.|
|Globala replikering|Azure Cosmos-DB erbjuder NYCKELFÄRDIGT global distributionsplatsen som gör det möjligt att replikera data till någon av Azures globalt Datacenter klickar på en knapp. Globala replikering kan du skalanpassa globalt och ange låg latens åtkomst till dina data i världen.<br><br>I samband med säkerhet garanterar globala replikering dataskydd mot regionala fel.<br><br>Läs mer i [distribuera data globalt](distribute-data-globally.md).|
|Regional växling vid fel|Om du har replikerats dina data i flera datacenter, samlar Azure Cosmos DB automatiskt över dina åtgärder ska regionala datacenter i offlineläge. Du kan skapa en prioriterad lista med redundans regioner med hjälp av de regioner där dina data replikeras. <br><br>Läs mer i [Regional växling vid fel i Azure Cosmos DB](regional-failover.md).|
|Lokal replikering|Även inom ett datacenter, Azure Cosmos DB automatiskt replikerar data för hög tillgänglighet ger valet av [konsekvensnivåer](consistency-levels.md). Detta garanterar en 99,99% [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) för alla enskild region och konton för alla flera region med en avvikelse konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen.|
|Automatisk onlinesäkerhetskopieringar|Azure DB Cosmos-databaser säkerhetskopieras regelbundet och lagras i en georedundant store. <br><br>Läs mer i [automatisk online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md).|
|Återställa borttagna data|Automatisk onlinesäkerhetskopieringar kan användas för att återställa data som du kan ha bort upp till cirka 30 dagar efter händelsen av misstag. <br><br>Läs mer i [automatisk online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md)|
|Skydda och isolera känsliga data|Alla data i de regioner som anges i [vad är nytt?](#whats-new) nu krypterat i vila.<br><br>Personligt identifierbar information och andra känsliga data kan vara isolerad till specifika samlingar och skrivskyddad eller skrivskyddad åtkomst kan begränsas till specifika användare.|
|Övervakare för attacker|Med hjälp av [granskningsloggar loggnings- och](logging.md), du kan övervaka ditt konto för normalt och onormalt aktiviteten. Du kan visa vilka åtgärder som utfördes på dina resurser, som initierade åtgärden, när åtgärden utfördes status av åtgärden och mycket mer som visas i skärmbilden nedan.|
|Svara på attacker|När du har kontaktat Azure-supporten om du vill rapportera ett potentiellt angrepp, inletts en process i steg 5 incidenter. Målet med steg 5-processen är att återställa normal drift säkerhet och åtgärder så snabbt som möjligt när ett problem upptäcks och en undersökning har startats.<br><br>Läs mer i [Microsoft Azure Security Response i molnet](https://aka.ms/securityresponsepaper).|
|Geobegränsning|Azure Cosmos-DB säkerställer datastyrning och kompatibilitet för suveräna regioner (till exempel Tyskland Kina oss Gov).|
|Skyddade lokaler|Data i Azure Cosmos databasen lagras på SSD i Azures skyddade datacenter.<br><br>Läs mer i [globala Microsoft-datacenter](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-kryptering|Alla klient-till-tjänst Azure Cosmos DB interaktioner är SSL/TLS 1.2 tillämpas. All inom datacentret och mellan datacenter replikering är också SSL/TLS 1.2 tillämpas.|
|Vilande kryptering|Alla data som lagras i Azure Cosmos DB krypterat i vila. Läs mer i [Azure Cosmos DB kryptering i vila](.\database-encryption-at-rest.md)|
|Korrigeringsfil servrar|Som en hanterad databas eliminerar Azure Cosmos DB behovet av att hantera och korrigering av servrar, som har utförts för dig, automatiskt.|
|Administrativa konton med starka lösenord|Det är svårt att tro behöver vi även nämnt detta krav, men till skillnad från vissa av våra konkurrenter, är det omöjligt att ha ett administrativt konto utan lösenord i Azure Cosmos-databasen.<br><br> Säkerhet via SSL och HMAC hemliga autentisering är inbyggd i som standard.|
|Säkerhet och data protection certifieringar|Azure Cosmos-DB har [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [Europeiska modellen satser (EUMC)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses), och [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) certifikat. Ytterligare certifieringar pågår.|

Följande skärmbild visar Active directory-integrering (RBAC) som använder åtkomstkontroll (IAM) i Azure-portalen: ![åtkomstkontroll (IAM) i Azure portal – visar database-säkerhet](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Följande skärmbild visar hur du kan använda loggning och aktiviteten granskningsloggarna för att övervaka ditt konto: ![aktivitet loggar för Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Nästa steg

Mer information om huvudnycklar och resursen token finns [skydda åtkomsten till Azure Cosmos DB data](secure-access-to-data.md).

Mer information om granskningsloggning finns [Azure Cosmos DB diagnostikloggning](logging.md).

Mer information om Microsoft-certifikat finns [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/).
