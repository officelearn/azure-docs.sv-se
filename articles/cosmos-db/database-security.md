---
title: Databassäkerhet - Azure Cosmos DB
description: Lär dig mer om hur Azure Cosmos DB tillhandahåller databasskydd och datasäkerhet för dina data.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mjbrown
ms.openlocfilehash: c92f045f2c8d4443d596697596363bda3d0df975
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985294"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Säkerhet i Azure Cosmos DB – översikt

I den här artikeln beskrivs metodtips för databassäkerhet och viktiga funktioner som erbjuds av Azure Cosmos DB som hjälper dig att förhindra, identifiera och reagera på databasintrång.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Nyheter i Azure Cosmos DB-säkerhet

Kryptering i vila är nu tillgänglig för dokument och säkerhetskopior som lagras i Azure Cosmos DB i alla Azure-regioner. Kryptering i vila tillämpas automatiskt för både nya och befintliga kunder i dessa regioner. Det finns ingen anledning att konfigurera något; och du får samma stora latens, dataflöde, tillgänglighet och funktionalitet som tidigare med fördelen att veta att dina data är säkra och säkra med kryptering i vila.

## <a name="how-do-i-secure-my-database"></a>Hur skyddar jag min databas

Datasäkerhet är ett delat ansvar mellan dig, kunden och databasleverantören. Beroende på vilken databasleverantör du väljer kan det ansvar du bär variera. Om du väljer en lokal lösning måste du tillhandahålla allt från slutpunktsskydd till fysisk säkerhet för maskinvaran – vilket inte är någon lätt uppgift. Om du väljer en PaaS-molndatabasleverantör som Azure Cosmos DB minskar ditt problemområde avsevärt. Följande avbildning, som lånats från Microsofts [delade ansvar för cloud computing-faktablad,](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) visar hur ditt ansvar minskar med en PaaS-leverantör som Azure Cosmos DB.

![Ansvar för kund- och databasleverantörer](./media/database-security/nosql-database-security-responsibilities.png)

Det föregående diagrammet visar säkerhetskomponenter på hög nivå, men vilka objekt behöver du oroa dig specifikt för databaslösningen? Och hur kan ni jämföra lösningar med varandra?

Vi rekommenderar följande checklista över krav som du kan jämföra databassystem på:

- Inställningar för nätverkssäkerhet och brandvägg
- Användarautentisering och detaljerade användarkontroller
- Möjlighet att replikera data globalt för regionala fel
- Möjlighet att växla över från ett datacenter till ett annat
- Lokal datareplikering i ett datacenter
- Automatiska säkerhetskopieringar av data
- Återställande av borttagna data från säkerhetskopior
- Skydda och isolera känsliga data
- Övervakning av attacker
- Svara på attacker
- Möjlighet att geo-fence data för att följa begränsningar för datastyrning
- Fysiskt skydd av servrar i skyddade datacenter
- Certifieringar

Och även om det kan tyckas uppenbart, påminner den senaste tidens [storskaliga databasöverträdelser](https://thehackernews.com/2017/01/mongodb-database-security.html) oss om den enkla men avgörande betydelsen av följande krav:

- Lappade servrar som hålls uppdaterade
- HTTPS som standard/TLS-kryptering
- Administrativa konton med starka lösenord

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hur skyddar Azure Cosmos DB min databas

Låt oss se tillbaka på föregående lista - hur många av dessa säkerhetskrav tillhandahåller Azure Cosmos DB? Varenda en.

Låt oss gräva i var och en i detalj.

|Säkerhetskrav|Azure Cosmos DB:s säkerhetsstrategi|
|---|---|
|Nätverkssäkerhet|Att använda en IP-brandvägg är det första skyddslagret för att skydda databasen. Azure Cosmos DB stöder principdrivna IP-baserade åtkomstkontroller för stöd för inkommande brandvägg. IP-baserade åtkomstkontroller liknar brandväggsreglerna som används av traditionella databassystem, men de expanderas så att ett Azure Cosmos-databaskonto endast är tillgängligt från en godkänd uppsättning datorer eller molntjänster. Läs mer i azure [cosmos DB-brandväggens supportartikel.](firewall-support.md)<br><br>Med Azure Cosmos DB kan du aktivera en specifik IP-adress (168.61.48.0), ett IP-intervall (168.61.48.0/8) och kombinationer av IP-adresser och intervall. <br><br>Alla begäranden som kommer från datorer utanför den här tillåtna listan blockeras av Azure Cosmos DB. Begäranden från godkända datorer och molntjänster måste sedan slutföra autentiseringsprocessen för att få åtkomstkontroll till resurserna.<br><br> Du kan använda taggar för [virtuella nätverkstjänst för](../virtual-network/service-tags-overview.md) att uppnå nätverksisolering och skydda dina Azure Cosmos DB-resurser från det allmänna Internet. Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (till exempel AzureCosmosDB) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst.|
|Auktorisering|Azure Cosmos DB använder hash-baserad meddelandeautentiseringskod (HMAC) för auktorisering. <br><br>Varje begäran hasheras med den hemliga kontonyckeln och den efterföljande bas-64-kodade hash-koden skickas med varje anrop till Azure Cosmos DB. För att validera begäran använder Azure Cosmos DB-tjänsten rätt hemlig nyckel och egenskaper för att generera en hash, sedan jämförs värdet med värdet i begäran. Om de två värdena matchar auktoriseras åtgärden och begäran bearbetas, annars finns det ett auktoriseringsfel och begäran avvisas.<br><br>Du kan använda antingen en [huvudnyckel](secure-access-to-data.md#master-keys)eller en [resurstoken](secure-access-to-data.md#resource-tokens) som ger finkornig åtkomst till en resurs, till exempel ett dokument.<br><br>Läs mer om hur du [skyddar åtkomsten till Azure Cosmos DB-resurser](secure-access-to-data.md).|
|Användare och behörigheter|Med hjälp av huvudnyckeln för kontot kan du skapa användarresurser och behörighetsresurser per databas. En resurstoken associeras med en behörighet i en databas och avgör om användaren har åtkomst (skrivskyddad, skrivskyddad eller ingen åtkomst) till en programresurs i databasen. Programresurser omfattar behållare, dokument, bilagor, lagrade procedurer, utlösare och UDF:er. Resurstoken används sedan under autentisering för att tillhandahålla eller neka åtkomst till resursen.<br><br>Läs mer om hur du [skyddar åtkomsten till Azure Cosmos DB-resurser](secure-access-to-data.md).|
|Active Directory-integrering (RBAC)| Du kan också ge eller begränsa åtkomsten till Cosmos-kontot, databasen, behållaren och erbjudandena (dataflöde) med hjälp av Access-kontrollen (IAM) i Azure-portalen. IAM tillhandahåller rollbaserad åtkomstkontroll och integreras med Active Directory. Du kan använda inbyggda roller eller anpassade roller för individer och grupper. Mer information finns i active [directory-integrationsartikeln.](role-based-access-control.md)|
|Global replikering|Azure Cosmos DB erbjuder total global distribution, vilket gör att du kan replikera dina data till något av Azures globala datacenter med ett klick på en knapp. Med global replikering kan du skala globalt och ge åtkomst med låg latens till dina data runt om i världen.<br><br>När det gäller säkerhet säkerställer global replikering dataskydd mot regionala fel.<br><br>Läs mer i [Distribuera data globalt](distribute-data-globally.md).|
|Regionala redundanstestningar|Om du har replikerat dina data i mer än ett datacenter rullar Azure Cosmos DB automatiskt över dina åtgärder om ett regionalt datacenter kopplas från. Du kan skapa en prioriterad lista över redundansregioner med hjälp av de regioner där dina data replikeras. <br><br>Läs mer i [regionala redundanser i Azure Cosmos DB](high-availability.md).|
|Lokal replikering|Även inom ett enda datacenter replikerar Azure Cosmos DB automatiskt data för hög tillgänglighet, vilket ger dig möjlighet att välja [konsekvensnivåer](consistency-levels.md). Den här replikeringen garanterar ett [serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db) för tillgänglighet på 99,99 % för alla konton i en region och alla konton med flera regioner med avslappnad konsekvens och 99,999 % lästillgänglighet på alla databaskonton i flera regioner.|
|Automatiserade säkerhetskopiering online|Azure Cosmos-databaser säkerhetskopieras regelbundet och lagras i ett geo redundant arkiv. <br><br>Läs mer i [Automatisk säkerhetskopiering och återställning online med Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).|
|Återställa borttagna data|De automatiska online-säkerhetskopiorna kan användas för att återställa data som du av misstag har tagit bort upp till ~ 30 dagar efter händelsen. <br><br>Läs mer i [Automatisk säkerhetskopiering och återställning online med Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)|
|Skydda och isolera känsliga data|Alla data i de regioner som anges i Nyheter? krypteras nu i vila.<br><br>Personuppgifter och andra konfidentiella uppgifter kan isoleras till specifik behållare och läs-och skriv-, eller skrivskyddad åtkomst kan begränsas till specifika användare.|
|Övervaka för attacker|Genom att använda [granskningsloggning och aktivitetsloggar](logging.md)kan du övervaka ditt konto efter normal och onormal aktivitet. Du kan visa vilka åtgärder som utfördes på dina resurser, som initierade åtgärden, när åtgärden inträffade, åtgärdens status och mycket mer som visas i skärmbilden efter den här tabellen.|
|Svara på attacker|När du har kontaktat Azure-supporten för att rapportera en potentiell attack, sparkas en 5-stegs incidentsvarsprocess igång. Målet med 5-stegsprocessen är att återställa normal servicesäkerhet och drift så snabbt som möjligt efter att ett problem har upptäckts och en undersökning har startats.<br><br>Läs mer i [Microsoft Azure Security Response i molnet](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geo-stängsel|Azure Cosmos DB säkerställer datastyrning för suveräna regioner (till exempel Tyskland, Kina, US Gov).|
|Skyddade anläggningar|Data i Azure Cosmos DB lagras på SSD:er i Azures skyddade datacenter.<br><br>Läs mer i [Microsofts globala datacenter](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-kryptering|Alla anslutningar till Azure Cosmos DB stöder HTTPS. Azure Cosmos DB stöder också TLS 1.2.<br>Det är möjligt att framtvinga en minsta TLS-version server-sida. För att göra [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)det, vänligen kontakta .|
|Vilande kryptering|Alla data som lagras i Azure Cosmos DB krypteras i vila. Läs mer i [Azure Cosmos DB-kryptering](./database-encryption-at-rest.md) i vila|
|Lappade servrar|Som en hanterad databas eliminerar Azure Cosmos DB behovet av att hantera och korrigera servrar, som görs för dig automatiskt.|
|Administrativa konton med starka lösenord|Det är svårt att tro att vi ens behöver nämna detta krav, men till skillnad från vissa av våra konkurrenter är det omöjligt att ha ett administrativt konto utan lösenord i Azure Cosmos DB.<br><br> Säkerhet via TLS och HMAC hemlig baserad autentisering bakas in som standard.|
|Säkerhets- och dataskyddscertifieringar| Den senaste [Azure Compliance-webbplatsen](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) finns för den senaste Azure [Compliance-dokumentet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) med alla certifieringar (sök efter Cosmos). För en mer fokuserad läs kolla in inlägget den 25 april 2018 [Azure #CosmosDB: Secure, private, compliant that includes SOCS 1/2 Type 2, HITRUST, PCI DSS Level 1, ISO 27001, HIPAA, FedRAMP High och många andra.

Följande skärmbild visar hur du kan använda granskningsloggning ![och aktivitetsloggar för att övervaka ditt konto: Aktivitetsloggar för Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Nästa steg

Mer information om huvudnycklar och resurstoken finns i [Skydda åtkomsten till Azure Cosmos DB-data](secure-access-to-data.md).

Mer information om granskningsloggning finns i [Azure Cosmos DB diagnostic logging](logging.md).

Mer information om Microsoft-certifieringar finns i [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).