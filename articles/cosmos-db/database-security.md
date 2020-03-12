---
title: Säkerhet – Azure Cosmos DB-databas
description: Lär dig hur Azure Cosmos DB ger skydd och data databassäkerhet för dina data.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0c97d70ea3e5c7fdd14b0f97c5e393359f2b948e
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087236"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Säkerhet i Azure Cosmos DB – översikt

I den här artikeln beskrivs metodtips för databassäkerhet och viktiga funktioner som erbjuds av Azure Cosmos DB som hjälper dig att förhindra, identifiera och reagera på databasintrång.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Vad är nytt i Azure Cosmos DB säkerhet

Vilande kryptering är nu tillgängligt för dokument och säkerhetskopior som lagras i Azure Cosmos DB i alla Azure-regioner. Kryptering i vila tillämpas automatiskt för både nya och befintliga kunder i dessa regioner. Behöver inte konfigurera något; Du får samma fantastiska svarstid, dataflöde, tillgänglighet och funktioner som innan med fördelen med vetskapen om att dina data är skyddad och säker med kryptering i vila.

## <a name="how-do-i-secure-my-database"></a>Hur gör jag för att skydda min databas

Datasäkerhet är ett delat ansvar mellan dig, kunden och leverantören databas. Mängden ansvar som du utför kan variera beroende på vilken databasprovider som du väljer. Om du väljer en lokal lösning kan behöva du har allt från slutpunktsskydd till fysisk säkerhet maskinvara – vilket är ingen enkel aktivitet. Om du väljer en PaaS-databas leverantör, till exempel Azure Cosmos DB, minskar problem område avsevärt. Följande bild, som lånas från Microsofts [delade ansvar för molnbaserad data behandling](https://aka.ms/sharedresponsibility) White Paper, visar hur ditt ansvar minskar med en PaaS-provider som Azure Cosmos dB.

![Kund- och databasen providern ansvarsområden](./media/database-security/nosql-database-security-responsibilities.png)

Det föregående diagrammet visar övergripande molnet säkerhetskomponenter, men vilka objekt du behöver bekymra dig om specifikt för din databaslösning? Och hur kan man jämföra lösningar till varandra?

Vi rekommenderar följande kontrollista om kraven som du vill jämföra databassystem:

- Nätverkssäkerhet och brandväggsinställningar
- Autentisering av användare och de detaljerade användarkontroller
- Möjligheten att replikera data globalt för regionala fel
- Möjlighet att redundansväxla ett Data Center till ett annat
- Replikering av lokala data inom ett datacenter
- Automatiska säkerhetskopior
- Återställning av borttagna data från säkerhetskopior
- Skydda och isolera känsliga data
- Övervakning för attacker
- Svara på attacker
- Möjlighet att geo-avgränsningstecken data följa databegränsningar för styrning
- Fysiskt skydd av servrar i skyddade data centers
- Certifieringar

Och även om det kan verka självklart kan den senaste [storskaliga databasen](https://thehackernews.com/2017/01/mongodb-database-security.html) påminna oss om den enkla men viktiga betydelsen av följande krav:

- Korrigerade servrar som hålls aktuella
- HTTPS som standard/SSL-kryptering
- Administrativa konton med starka lösenord

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hur skyddar Azure Cosmos DB databasen

Nu ska vi gå tillbaka till föregående lista – hur många av dessa säkerhetskrav Azure Cosmos DB tillhandahåller? Varje enskild en.

Låt oss gå till var och en i detalj.

|Säkerhetskrav|Metoden för Azure Cosmos DB-säkerhet|
|---|---|
|Nätverkssäkerhet|Det första lagret för att skydda din databas är att använda en IP-brandvägg. Azure Cosmos DB stöder driven IP-baserade åtkomstkontroller för Brandvägg för inkommande trafik support-princip. IP-baserade åtkomst kontroller liknar de brand Väggs regler som används av traditionella databas system, men de expanderas så att ett Azure Cosmos Database-konto endast är tillgängligt från en godkänd uppsättning datorer eller moln tjänster. Läs mer i artikeln om [stöd för Azure Cosmos DB-brandvägg](firewall-support.md) .<br><br>Azure Cosmos DB kan du aktivera en specifik IP-adress (168.61.48.0), ett IP-adressintervall (168.61.48.0/8) och kombinationer av IP-adresser och intervall. <br><br>Alla begäranden från datorer utanför den här listan blockeras av Azure Cosmos DB. Begäranden från godkända datorer och molntjänster sedan slutföra autentiseringsprocessen för att få åtkomstkontroll till resurser.<br><br> Du kan använda [taggar för virtuella nätverks tjänster](../virtual-network/service-tags-overview.md) för att uppnå nätverks isolering och skydda dina Azure Cosmos DB resurser från det allmänna Internet. Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (till exempel AzureCosmosDB) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst.|
|Auktorisering|Azure Cosmos DB använder hashbaserad meddelandeautentiseringskod (HMAC) för auktorisering. <br><br>Varje begäran hashas med den hemliga nyckeln och efterföljande Base64-kodad Hash-värdet skickas med varje anrop till Azure Cosmos DB. För att verifiera begäran kan Azure Cosmos DB-tjänsten som använder rätt hemlig nyckel och egenskaper för att generera en hash och sedan den jämför värdet med det i begäran. Om de två värdena matchar, åtgärden är auktoriseringen är klar och begäran har bearbetats, annars är det ett autentiseringsfel och begäran avvisas.<br><br>Du kan antingen använda en [huvud nyckel](secure-access-to-data.md#master-keys)eller en [resurs-token](secure-access-to-data.md#resource-tokens) som ger detaljerad åtkomst till en resurs, till exempel ett dokument.<br><br>Läs mer om hur [du skyddar åtkomsten till Azure Cosmos DB resurser](secure-access-to-data.md).|
|Användare och behörigheter|Med hjälp av huvud nyckeln för kontot kan du skapa användar resurser och behörighets resurser per databas. En Resource-token är associerad med en behörighet i en databas och avgör om användaren har åtkomst (Read-Write, skrivskyddad eller ingen åtkomst) till en program resurs i databasen. Resurser för programmet är behållare, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er. Resurstoken används sedan under autentiseringen för att tillhandahålla eller neka åtkomst till resursen.<br><br>Läs mer om hur [du skyddar åtkomsten till Azure Cosmos DB resurser](secure-access-to-data.md).|
|Active directory-integrering (RBAC)| Du kan också ange eller begränsa åtkomsten till Cosmos-kontot, databasen, behållaren och erbjudandena (genom strömning) med åtkomst kontroll (IAM) i Azure Portal. IAM ger rollbaserad åtkomstkontroll och integreras med Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper. Mer information finns i [Active Directory integrations](role-based-access-control.md) artikeln.|
|Global replikering|Azure Cosmos DB erbjuder nyckelfärdig global distribution, där du kan replikera dina data till en av Azures globalt Datacenter genom att klicka på en knapp. Global replikering kan du skala globalt och ge låg latens åtkomst till dina data över hela världen.<br><br>I samband med säkerhet garanterar global replikering dataskydd mot regionala fel.<br><br>Läs mer i [Distribuera data globalt](distribute-data-globally.md).|
|Regionala redundanstestningar|Om du har replikerat data i flera datacenter, samlar Azure Cosmos DB automatiskt över din verksamhet bör ett regionala Datacenter går offline. Du kan skapa en prioriterad lista över regioner för redundans med hjälp av de regioner där dina data replikeras. <br><br>Läs mer i [regional redundans i Azure Cosmos DB](high-availability.md).|
|Lokal replikering|I ett enda data Center replikerar Azure Cosmos DB automatiskt data för hög tillgänglighet och ger dig möjlighet att välja [konsekvens nivåer](consistency-levels.md). Den här replikeringen garanterar ett [service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db) på 99,99% tillgänglighet för alla enkla region konton och alla konton med flera regioner med avslappnad konsekvens och 99,999% Läs tillgänglighet för alla databas konton i flera regioner.|
|Automatiserad onlinesäkerhetskopieringar|Azure Cosmos-databaser säkerhets kopie ras regelbundet och lagras i en Geo-redundant lagring. <br><br>Läs mer i [Automatisk säkerhets kopiering och återställning online med Azure Cosmos DB](online-backup-and-restore.md).|
|Återställa borttagna data|Automatiserad onlinesäkerhetskopieringar kan användas för att återställa data som du kanske har råkat ta bort upp till cirka 30 dagar efter evenemanget. <br><br>Läs mer i [Automatisk säkerhets kopiering och återställning online med Azure Cosmos DB](online-backup-and-restore.md)|
|Skydda och isolera känsliga data|Alla data i de regioner som anges i vad är nytt? är nu krypterad i vila.<br><br>Personliga data och andra känsliga data kan isoleras specifika behållaren och Skriv- eller läsbehörighet kan begränsas till specifika användare.|
|Övervakare för attacker|Genom att använda [gransknings loggning och aktivitets loggar](logging.md)kan du övervaka ditt konto för normal och onormal aktivitet. Du kan visa vilka åtgärder som vidtogs för dina resurser, vem som initierade åtgärden när åtgärden utfördes, status för åtgärden och mycket mer som visas i skärmbilden nedan.|
|Svara på attacker|När du har kontaktat Azure-supporten om du vill rapportera ett potentiellt angrepp, har en 5-steget incidenthanteringsprocess startats. Målet med den 5 steg är att återställa normal drift säkerhets- och åtgärder så snabbt som möjligt när ett problem har identifierats och en undersökning har startats.<br><br>Läs mer i [Microsoft Azure säkerhets svar i molnet](https://aka.ms/securityresponsepaper).|
|Geografiska avgränsningar|Azure Cosmos DB garanterar datastyrning för landsbaserade regioner (exempelvis Tyskland, Kina, USA-förvaltad region).|
|Skyddade lokaler|Data i Azure Cosmos DB lagras på SSD: er i Azures skyddade datacenter.<br><br>Läs mer i [Microsofts globala data Center](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-kryptering|Alla anslutningar till Azure Cosmos DB stöd för HTTPS. Azure Cosmos DB stöder även TLS 1,2.<br>Det är möjligt att framtvinga en lägsta TLS-version på Server sidan. Om du vill göra det kontaktar du [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Vilande kryptering|Alla data som lagras i Azure Cosmos DB krypteras i vila. Läs mer i [Azure Cosmos DB kryptering i vila](./database-encryption-at-rest.md)|
|Korrigerade servrar|Som en hanterad databas eliminerar Azure Cosmos DB behovet av att hantera och uppdatera servrar, som har utförts för dig, automatiskt.|
|Administrativa konton med starka lösenord|Det är svårt att tro måste vi även nämner det här kravet, men till skillnad från vissa av våra konkurrenter, är det omöjligt att ha ett administrativt konto utan lösenord i Azure Cosmos DB.<br><br> Säkerhet via SSL och HMAC hemliga formulärbaserad autentisering är inbyggd i som standard.|
|Säkerhet och data protection-certifieringar| Den senaste listan över certifieringar finns i den övergripande [Azure Compliance-webbplatsen](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) samt det senaste [Azure Compliance-dokumentet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) med alla certifieringar (Sök efter Cosmos). För en mer fokuserad läsning kolla den 25 april 2018 post [Azure #CosmosDB: säker, privat, kompatibel med SOCS 1/2 typ 2, HITRUST, PCI DSS nivå 1, ISO 27001, HIPAA, FedRAMP hög och många andra.

Följande skärm bild visar hur du kan använda gransknings loggning och aktivitets loggar för att övervaka ditt konto: ![aktivitets loggar för Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Nästa steg

Mer information om huvud nycklar och resurs-token finns i [Skydda åtkomsten till Azure Cosmos db data](secure-access-to-data.md).

Mer information om gransknings loggning finns i [Azure Cosmos DB diagnostisk loggning](logging.md).

Mer information om Microsoft-certifieringar finns [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/).