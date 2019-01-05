---
title: Säkerhet – Azure Cosmos DB-databas
description: Lär dig hur Azure Cosmos DB ger skydd och data databassäkerhet för dina data.
author: rafats
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: rafats
ms.openlocfilehash: 5fcf04c57f957b670d813de8ffe2d945f70cb3c5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038780"
---
# <a name="azure-cosmos-db-database-security"></a>Azure Cosmos DB database-säkerhet

Den här artikeln beskrivs säkerhetsmetoder för databasen och viktiga funktioner som erbjuds av Azure Cosmos DB för att förhindra, upptäcka och svara på databasen överträdelser.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Vad är nytt i Azure Cosmos DB security?

Vilande kryptering är nu tillgängligt för dokument och säkerhetskopior som lagras i Azure Cosmos DB i alla Azure-regioner. Kryptering i vila tillämpas automatiskt för både nya och befintliga kunder i dessa regioner. Behöver inte konfigurera något; Du får samma fantastiska svarstid, dataflöde, tillgänglighet och funktioner som innan med fördelen med vetskapen om att dina data är skyddad och säker med kryptering i vila.

## <a name="how-do-i-secure-my-database"></a>Hur skyddar jag min databas? 

Datasäkerhet är ett delat ansvar mellan dig, kunden och leverantören databas. Mängden ansvar som du utför kan variera beroende på vilken databasprovider som du väljer. Om du väljer en lokal lösning kan behöva du har allt från slutpunktsskydd till fysisk säkerhet maskinvara – vilket är ingen enkel aktivitet. Om du väljer en PaaS-databas leverantör, till exempel Azure Cosmos DB, minskar problem område avsevärt. Följande bild, lånar från Microsofts [delat ansvar för molnbaserad databehandling](https://aka.ms/sharedresponsibility) faktablad, visar hur ditt ansvar minskar med en PaaS-provider som Azure Cosmos DB.

![Kund- och databasen providern ansvarsområden](./media/database-security/nosql-database-security-responsibilities.png)

Det föregående diagrammet visar övergripande molnet säkerhetskomponenter, men vilka objekt du behöver bekymra dig om specifikt för din databaslösning? Och hur kan man jämföra lösningar till varandra? 

Vi rekommenderar följande kontrollista om kraven som du vill jämföra databassystem:

- Nätverkssäkerhet och brandväggsinställningar
- Autentisering av användare och de detaljerade användarkontroller
- Möjligheten att replikera data globalt för regionala fel
- Möjligheten att utföra redundans från ett datacenter till en annan
- Replikering av lokala data inom ett datacenter
- Automatiska säkerhetskopior
- Återställning av borttagna data från säkerhetskopior
- Skydda och isolera känsliga data
- Övervakning för attacker
- Svara på attacker
- Möjlighet att geo-avgränsningstecken data följa databegränsningar för styrning
- Fysiskt skydd av servrar i skyddade data centers
- Certifieringar

Och även om det kan verka uppenbart, senaste [storskalig databas överträdelser](https://thehackernews.com/2017/01/mongodb-database-security.html) påminna oss enkla men kritiska vikten av följande krav:
- Korrigerade servrar som hålls uppdaterade
- HTTPS som standard/SSL-kryptering
- Administrativa konton med starka lösenord

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hur skyddar min databas i Azure Cosmos DB?

Nu ska vi gå tillbaka till föregående lista – hur många av dessa säkerhetskrav Azure Cosmos DB tillhandahåller? Varje enskild en.

Låt oss gå till var och en i detalj.

|Säkerhetskrav|Metoden för Azure Cosmos DB-säkerhet|
|---|---|---|
|Nätverkssäkerhet|Det första lagret för att skydda din databas är att använda en IP-brandvägg. Azure Cosmos DB stöder driven IP-baserade åtkomstkontroller för Brandvägg för inkommande trafik support-princip. IP-baserade åtkomstkontroller liknar brandväggsregler som används av traditionella databassystem, men de visas så att ett Azure Cosmos DB-databaskonto kan endast nås från en godkänd uppsättning datorer eller molntjänster. <br><br>Azure Cosmos DB kan du aktivera en specifik IP-adress (168.61.48.0), ett IP-adressintervall (168.61.48.0/8) och kombinationer av IP-adresser och intervall. <br><br>Alla begäranden från datorer utanför den här listan blockeras av Azure Cosmos DB. Begäranden från godkända datorer och molntjänster sedan slutföra autentiseringsprocessen för att få åtkomstkontroll till resurser.<br><br>Läs mer i [Azure Cosmos DB-brandväggsstöd](firewall-support.md).|
|Auktorisering|Azure Cosmos DB använder hashbaserad meddelandeautentiseringskod (HMAC) för auktorisering. <br><br>Varje begäran hashas med den hemliga nyckeln och efterföljande Base64-kodad Hash-värdet skickas med varje anrop till Azure Cosmos DB. För att verifiera begäran kan Azure Cosmos DB-tjänsten som använder rätt hemlig nyckel och egenskaper för att generera en hash och sedan den jämför värdet med det i begäran. Om de två värdena matchar, åtgärden är auktoriseringen är klar och begäran har bearbetats, annars är det ett autentiseringsfel och begäran avvisas.<br><br>Du kan använda antingen en [huvudnyckeln](secure-access-to-data.md#master-keys), eller en [Resurstoken](secure-access-to-data.md#resource-tokens) detaljerad åtkomst till en resurs, till exempel ett dokument.<br><br>Läs mer i [skydda åtkomst till Azure Cosmos DB-resurserna](secure-access-to-data.md).|
|Användare och behörigheter|Med hjälp av den [huvudnyckeln](#master-key) för kontot, kan du skapa användarresurser och behörigheten resurser per databas. En [Resurstoken](#resource-token) är associerad med en behörighet i en databas och avgör om användaren har åtkomst (skrivskyddad, skrivskyddad, eller ingen åtkomst) till en resurs för en i databasen. Resurser för programmet är behållare, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er. Resurstoken används sedan under autentiseringen för att tillhandahålla eller neka åtkomst till resursen.<br><br>Läs mer i [skydda åtkomst till Azure Cosmos DB-resurserna](secure-access-to-data.md).|
|Active directory-integrering (RBAC)| Du kan också ge åtkomst till det konto med hjälp av åtkomstkontroll (IAM) i Azure-portalen som visas i skärmbilden som följer den här tabellen. IAM ger rollbaserad åtkomstkontroll och integreras med Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper som du ser i följande bild.|
|Global replikering|Azure Cosmos DB erbjuder nyckelfärdig global distribution, där du kan replikera dina data till en av Azures globalt Datacenter genom att klicka på en knapp. Global replikering kan du skala globalt och ge låg latens åtkomst till dina data över hela världen.<br><br>I samband med säkerhet garanterar global replikering dataskydd mot regionala fel.<br><br>Läs mer i [Distribuera data globalt](distribute-data-globally.md).|
|Regionala redundanstestningar|Om du har replikerat data i flera datacenter, samlar Azure Cosmos DB automatiskt över din verksamhet bör ett regionala Datacenter går offline. Du kan skapa en prioriterad lista över regioner för redundans med hjälp av de regioner där dina data replikeras. <br><br>Läs mer i [Regional redundans i Azure Cosmos DB](high-availability.md).|
|Lokal replikering|Även i ett datacenter, Azure Cosmos DB automatiskt replikerar data för hög tillgänglighet, vilket ger dig valet av [konsekvensnivåer](consistency-levels.md). Detta garanterar en 99,99% [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) för alla och alla konton i flera regioner med restriktiva konsekvens och 99,999% läsningstillgänglighet för alla databaskonton för flera regioner konton.|
|Automatiserad onlinesäkerhetskopieringar|Azure Cosmos DB-databaser säkerhetskopieras regelbundet och lagras i ett Arkiv för georedundant. <br><br>Läs mer i [automatisk online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md).|
|Återställa borttagna data|Automatiserad onlinesäkerhetskopieringar kan användas för att återställa data som du kanske har råkat ta bort upp till cirka 30 dagar efter evenemanget. <br><br>Läs mer i [automatisk online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md)|
|Skydda och isolera känsliga data|Alla data i de regioner som anges i [nyheter?](#whats-new) nu krypteras i vila.<br><br>Personliga data och andra känsliga data kan isoleras specifika behållaren och Skriv- eller läsbehörighet kan begränsas till specifika användare.|
|Övervakare för attacker|Med hjälp av [loggning och aktivitet granskningsloggar](logging.md), kan du övervaka ditt konto för normalt och onormalt aktivitet. Du kan visa vilka åtgärder som vidtogs för dina resurser, vem som initierade åtgärden när åtgärden utfördes, status för åtgärden och mycket mer som visas i skärmbilden nedan.|
|Svara på attacker|När du har kontaktat Azure-supporten om du vill rapportera ett potentiellt angrepp, har en 5-steget incidenthanteringsprocess startats. Målet med den 5 steg är att återställa normal drift säkerhets- och åtgärder så snabbt som möjligt när ett problem har identifierats och en undersökning har startats.<br><br>Läs mer i [Microsoft Azure Security Response i molnet](https://aka.ms/securityresponsepaper).|
|Geografiska avgränsningar|Azure Cosmos DB garanterar datastyrning för landsbaserade regioner (exempelvis Tyskland, Kina, USA-förvaltad region).|
|Skyddade lokaler|Data i Azure Cosmos DB lagras på SSD: er i Azures skyddade datacenter.<br><br>Läs mer i [Microsofts globala Datacenter](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-kryptering|Alla klient-till-tjänst Azure Cosmos DB-interaktioner är SSL/TLS 1.2-kompatibla. All intra datacenter och mellan datacenter replikering är också SSL/TLS 1.2 tillämpas.|
|Vilande kryptering|Alla data som lagras i Azure Cosmos DB krypteras i vila. Läs mer i [Azure Cosmos DB-kryptering i vila](./database-encryption-at-rest.md)|
|Korrigerade servrar|Som en hanterad databas eliminerar Azure Cosmos DB behovet av att hantera och uppdatera servrar, som har utförts för dig, automatiskt.|
|Administrativa konton med starka lösenord|Det är svårt att tro måste vi även nämner det här kravet, men till skillnad från vissa av våra konkurrenter, är det omöjligt att ha ett administrativt konto utan lösenord i Azure Cosmos DB.<br><br> Säkerhet via SSL och HMAC hemliga formulärbaserad autentisering är inbyggd i som standard.|
|Säkerhet och data protection-certifieringar|För de flesta upp till datalista över certifieringar, se totala [webbplatsen Azure efterföljande](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) samt senaste [Azure efterlevnad dokumentet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) med alla certifieringar (Sök efter Cosmos). En mer fokuserad finns Kolla in den 25 April 2018 inlägget [Azure #CosmosDB: Säker, privata och kompatibel](https://azure.microsoft.com/blog/azure-cosmosdb-secure-private-compliant/) som innehåller SOC 1/2 typ 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High och många andra.

I följande skärmbild visas Active directory-integrering (RBAC) med hjälp av åtkomstkontroll (IAM) i Azure portal: ![Åtkomstkontroll (IAM) i Azure portal – visar databassäkerhet](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Följande skärmbild visar hur du kan använda loggning och aktivitet granskningsloggar för att övervaka ditt konto: ![Aktivitetsloggar för Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Nästa steg

Mer information om huvudnycklar och resurstokens finns i [skydda åtkomst till Azure Cosmos DB data](secure-access-to-data.md).

Mer information om granskningsloggning finns i [Diagnostisk loggning för Azure Cosmos DB](logging.md).

Mer information om Microsoft-certifieringar finns i [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
