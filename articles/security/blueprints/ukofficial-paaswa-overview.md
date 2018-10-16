---
title: Azure-säkerhet och efterlevnad skissen - PaaS-webbprogram som är värd för Storbritannien officiella arbetsbelastningar
description: Azure-säkerhet och efterlevnad skissen - PaaS-webbprogram som är värd för Storbritannien officiella arbetsbelastningar
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 1c2294004245e0ef64b9b708a5b57ec0d34cc45f
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321996"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure-säkerhet och efterlevnad skiss: PaaS Web program som är värd för Storbritannien officiella arbetsbelastningar

## <a name="azure-security-and-compliance-blueprints"></a>Skisser för säkerhet och efterlevnad i Azure

Azure skisser bestå av vägledning dokument och automation-mallar som distribuerar molnbaserade arkitekturer för att erbjuda lösningar på scenarier där ackreditering eller efterlevnadskrav. Azure skisser är vägledning och automation-mall som möjligt för Microsoft Azure-kunder att leveransen av deras affärsmål genom att etablera en foundation-arkitektur som kan utökas för att uppfylla eventuella ytterligare krav.

## <a name="overview"></a>Översikt

Den här Azure Blueprint för säkerhet och efterlevnad ger vägledning och automation-skript för att leverera en Microsoft Azure [plattform som en tjänst (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) webbprogramarkitektur som är lämpliga för att hantera arbetsbelastningar som klassificeras som värd som [UK-OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Den här säkerhetsklassificering omfattar merparten av information skapas eller bearbetas av den offentliga sektorn. Detta inkluderar rutinmässig verksamheten och tjänster, vilka om tappas bort, blir stulen eller publicerats i mediet, vilket kan ha skadas. Vanliga hot profilen för den officiella klassificeringen är ungefär samma sätt som ett privat företag som ger värdefull information och tjänster. Storbritannien officiella förutse behovet av att försvara Brittiska myndigheterna data eller tjänster mot hot eller kompromettering av angripare med bunden funktioner och resurser som (men inte begränsat till) hactivists single-issue tryck grupper, undersökande journalister behöriga enskilda hackare och flesta av straffrättsliga personer och grupper.

Den här skissen har granskats av den Storbritannien nationella Cyberhot Security Center (NCSC) och stämmer överens med Molnsäkerhetsprinciper för NCSC 14.

Arkitekturen använder Azure [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) komponenter för att leverera en miljö som gör att kunderna slipper du kostnaderna och besväret med att köpa programvarulicenser för att hantera underliggande programinfrastruktur och middleware eller utvecklingsverktygen och andra resurser. Kunderna hantera program och tjänster som de utvecklar fokusera på att leverera affärsvärde, även om Microsoft Azure hanterar andra Azure-resurser, till exempel virtuella datorer, lagring och nätverk, placera flera av de [divisionen av ansvar](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) för infrastrukturhantering in på Azure-plattformen. [Azure App Services](https://azure.microsoft.com/services/app-service/) ger automatisk skalning, hög tillgänglighet, har stöd för Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats som standardtjänster. Med hjälp av App Services, kan utvecklare koncentrera dig på att leverera värde utan att behöva hantera infrastrukturen. Det är möjligt att bygg nya webbprogram i Java, PHP, Node.js, Python, HTML eller C# eller också migrera befintliga molnet eller på lokala webbprogram till Azure App Services, (även om omfattande förfallodatum noggrannhet och testningen bekräfta prestanda krävs).

Den här skissen fokuserar på etablering av en säker grund [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) webbaserat gränssnitt för offentliga och även bakåt office-användare. Det här designscenariot skissen tar hänsyn till användningen av Azure värdbaserade webbaserade tjänster där en offentlig användare kan på ett säkert sätt skicka, visa och hantera känsliga data. också att en backend office eller government-operator på ett säkert sätt kan bearbeta känsliga data som offentliga användaren har skickat. Användningsområden för det här scenariot kan omfatta:

- En användare skickar en deklarationen, med en government operator bearbetning av bidrag;
- En användare som begär en tjänst via ett webbaserat program med en BackOffice-användare verifierar och leverera tjänsten; eller
- En användare som vill ha och visa den offentliga domänen hjälpinformation om en tjänst för myndigheter.

Med hjälp av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallar och Azure Command Line Interface skript skissen distribuerar en miljö som är knutet till de Storbritannien nationella Cyberhot Security Center (NCSC) 14 [Molnsäkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) och Center för Internetsäkerhet (CIS) [kritiskt säkerhetskontroller](https://www.cisecurity.org/critical-controls.cfm). NCSC rekommenderar sina Molnsäkerhetsprinciper används av kunder för att utvärdera säkerhetsegenskaperna för tjänsten och för att förstå divisionen av ansvar mellan kunden och leverantören. Microsoft har tillhandahållit information mot alla dessa principer för att bättre förstå delningen av ansvarsområden. Den här arkitekturen och motsvarande Azure Resource Manager-mallar stöds av Microsoft-faktabladet [14 molnet säkerhetskontroller för Storbritannien cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Den här arkitekturen har granskats av NCSC och överensstämmer med den Storbritannien NCSC 14 Molnsäkerhetsprinciper, vilket gör att organisationer inom offentlig sektor snabb åtkomst till sina möjligheter att uppfylla efterlevnad skyldigheter med molnbaserade tjänster globalt och i Storbritannien på den Microsoft Azure-molnet. Den här mallen distribuerar infrastrukturen för arbetsbelastningen. Programkod och stöd för affärsnivå och programvara för nivå måste installeras och konfigureras av kunder. Distribution av detaljerade instruktioner finns [här](https://aka.ms/ukofficial-paaswa-repo/).

Den här skissen är en foundation-arkitektur. Våra kunder kan använda den här skissen som en grund för sina officiella klassificering webbaserade arbetsbelastningar och expandera de mallar och resurser med sina egna krav. Den här skissen bygger på principerna för den [Storbritannien officiella Trelagers-IaaS-webbprogram skiss](https://aka.ms/ukofficial-iaaswa) att erbjuda våra kunder, både [infrastruktur som en tjänst (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) och PaaS som redan för att hantera webbaserade arbetsbelastningar.

Om du vill distribuera den här skissen, krävs en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du registrera dig snabbt och enkelt utan kostnad: Kom igång med Azure. Klicka på [här](https://aka.ms/ukofficial-paaswa-repo/) anvisningar för distribution.

## <a name="architecture-and-components"></a>Arkitektur och komponenter

Denna skiss tillhandahåller ett webbprogram som värd-lösning i en miljö för Azure-molnet som har stöd för Storbritannien officiella arbetsbelastningar. Arkitekturen ger en säker miljö som utnyttjar Azure-plattformen som en tjänstfunktioner. I miljön, två App Service-webbappar som är distribuerade (en för offentliga användare) och en för BackOffice användare, med en API-App-nivå att tillhandahålla företagstjänster för klientdelen. En Azure SQL Database distribueras som en hanterad relationsdatabas för programmet. Anslutningen till dessa komponenter från utanför plattformen och mellan dessa komponenter är krypterad via TLS 1.2 ska se till att data i transport sekretess med åtkomst som autentiseras av Azure Active Directory.

![PaaS Webbprogramsvärden för Storbritannien officiella arbetsbelastningar referens för Arkitekturdiagram](images/ukofficial-paaswa-architecture.png?raw=true "PaaS Webbprogramsvärden för Arkitekturdiagram för Storbritannien officiella arbetsbelastningar referens")

Som en del av distributionsarkitektur, säker lagring etablera, övervakning & loggning, enhetlig säkerhetshantering och Avancerat skydd mot hot och management distribueras också funktioner för att se till att kunderna har alla verktyg som krävs för att skydda och övervaka deras miljö för den här lösningen.

Den här lösningen använder följande Azure-tjänster. Mer information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

- Azure Active Directory
- App Service
- Webbapp
- API-app
- Azure DNS
- Key Vault
- Azure Monitor
- Application Insights
- Log Analytics
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Distributionsarkitektur för

Följande avsnitt beskriver elementen distribution och implementering.

### <a name="security"></a>Säkerhet

#### <a name="identity-and-authentication"></a>Enhetsidentitet samt autentisering

Den här skissen säkerställer att åtkomsten till resurser är skyddade genom företagskatalogen och identitetshanteringen hanteringstjänster. Den här arkitekturen gör fullt ut utnyttja [identitet som säkerhetsperimeter](https://docs.microsoft.com/azure/security/security-paas-deployments#identity-as-the-primary-security-perimeter). 

Följande tekniker hanteringsfunktioner identitet i Azure-miljön:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) Microsofts molnbaserade katalog- och identitetstjänst management multiklienttjänst. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare med åtkomst till SQL-databasen.
- Det utförs autentiseringen till operatorn mot webbprogram och åtkomst för administrationen av Azure-resurser med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Kolumnen databaskryptering använder Azure AD för att autentisera program till Azure SQL Database. Mer information finns i [Always Encrypted: skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Kundorienterade webbprogram har konfigurerats för offentlig åtkomst. Att tillåta för nätverks-kontoskapandet och autentisering via active directory eller sociala identitetsleverantörer [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) kan integreras om det behövs.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) identifierar potentiella säkerhetsproblem och riskfyllda konton ger rekommendationer för att förbättra säkerhetspositionen för din organisations identiteter, konfigurerar automatiska svar till har identifierats misstänkta åtgärder relaterade till din organisations identiteter, och undersöker misstänkta incidenter och vidtar lämpliga åtgärder du kan åtgärda detta.
- [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) aktiverar exakt fokuserade åtkomsthantering för Azure. Prenumerationsåtkomst är begränsad till administratör för prenumerationen och Azure Key Vault-åtkomst begränsas till användare som behöver åtkomst för nyckelhantering.
- Genom att utnyttja [Azure Active Directory villkorsstyrd åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) kunder kan tillämpa ytterligare säkerhetsåtgärder på åtkomst till appar eller användare i deras miljö utifrån särskilda villkor, till exempel plats, enhet, tillstånd och inloggning i risk.
- [Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) kombineras med metodtips för design av program, ger skydd mot DDoS-attacker med ständigt aktiverad övervakning, och i realtid minskning av vanliga attacker på nätverksnivå. Plattformsnivå DDoS protection är transparent för kunden och inbyggda i plattformen med en arkitektur för PaaS, men det är viktigt att notera att programmet security design ansvar ligger hos kunden.

#### <a name="data-in-transit"></a>Data under överföring

Data överförs från utanför och mellan Azure-komponenter är skyddat med hjälp av [Transport Layer Security/Secure Sockets Layer (SSL/TLS)](https://www.microsoft.com/TrustCenter/Security/Encryption), som använder symmetrisk kryptering som baseras på en delad hemlighet för att kryptera kommunikation som de överföras via nätverket. Som standard skyddas nätverkstrafik med hjälp av TLS 1.2.

#### <a name="security-and-malware-protection"></a>Säkerhet och skadlig kod protection

[Azure Security Center](https://azure.microsoft.com/services/security-center/) ger en central vy över säkerhetsläget hos alla dina Azure-resurser. På ett ögonblick, kan du kontrollera att rätt säkerhetskontroller är på plats och korrekt konfigurerad och att du snabbt kan identifiera eventuella resurser som kräver uppmärksamhet.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) är en personligt anpassad molnrådgivare som hjälper dig att följa bästa praxis för att optimera dina Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) är en realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Detta som standard är installerad på den underliggande PaaS-infrastrukturen för virtuell dator och hanteras av Azure-infrastrukturen transparent till kunden, säkerställer

### <a name="paas-services-in-this-blueprint"></a>PaaS-tjänster i den här skissen

#### <a name="azure-app-service"></a>Azure App Service

Azure Web Apps får du en fullständigt hanterad värdmiljö för webbprogram som har utvecklats i Java, PHP, Node.js, Python, HTML- och C# utan att behöva hantera infrastrukturen. Den tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) eller valfri Git-baserade lagringsplats.

App Service är [ISO, SOC och PCI-kompatibel](https://www.microsoft.com/TrustCenter/) och kan autentisera användare med [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) eller med social inloggning ([Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication), [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication), [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication), och [Microsoft authentication](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication).

Basic, Standard och Premium-prenumerationer avser produktionsarbetsbelastningar och körs på särskilda virtuella datorinstanser. Varje instans har stöd för flera program och domäner. App services också stöd för [IP-adressbegränsningar](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) att skydda trafik till betrodda IP-adresser om det behövs och även [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) för säker anslutning till andra PaaS-tjänster till exempel [Nyckelvalv](https://azure.microsoft.com/services/key-vault/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Om det krävs ytterligare säkerhet vår isolerad är värd för dina appar i en privat, dedikerad miljö för Azure och är perfekt för appar som kräver säkra anslutningar till ditt lokala nätverk eller ytterligare prestanda och skalning.

Den här mallen distribuerar följande App Service-funktioner:

- [Standard](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) nivå för App Service-Plan
- Flera Web App [distributionsfack](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing): utveckling, förhandsgranskning, QA, UAT och naturligtvis produktion (standard-port).
- [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) att ansluta till [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (Detta kan också användas för att ge åtkomst till [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integrering med [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) att övervaka prestanda
- [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- Mått [aviseringar](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL-databasen är en hanterad, allmän relationsdatabastjänst i Microsoft Azure som har stöd för strukturer som relationsdata, JSON, spatial och XML. SQL Database erbjuder hanterade enskild SQL-databaser, hanterade SQL-databaser i en [elastisk pool](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool), och SQL [instanser som hanteras av](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (i allmänt tillgänglig förhandsversion). Det ger [dynamiskt skalbar prestanda])https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) och innehåller alternativ som [kolumnlagringsindex](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) för extremanalys och rapporter, och [minnesintern OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) för extreme transaktionsbearbetning. Microsoft hanterar alla korrigeringar och uppdateringar av SQL-kodbasen sömlöst och avlägsnar all hantering av underliggande den infrastrukturen.

Azure SQL-databas i den här skissen

Azure SQL Database-instans använder följande säkerhetsåtgärder för databasen:

- [Brandväggsregler på servernivå och databasnivå](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), eller via [tjänstslutpunkter i virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) med [virtuella Nätverksregler](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) hjälper dig att skydda Azure SQL Database och Azure Data Warehouse mot skadlig aktivitet. Den utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet.
- [Azure AD-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera databasanvändare och förenklar hanteringen av behörighet.
- Användning av Azure Active Directory för databasadministration
- [Granskningsloggar](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) till storage-konton
- Mått [aviseringar](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) för misslyckade DB-anslutningar
- [SQL-Hotidentifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Alltid krypterad kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) är en Microsoft-hanterad molntjänst som tillhandahåller lagring som har hög tillgänglighet, säker, beständig, skalbar, och redundant. Azure Storage består av Blob Storage, File Storage och Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Azure Storage i denna skiss

Den här mallen använder följande komponenter i Azure Storage:

- [Kryptering av lagringstjänst](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Tillåt endast HTTPS-anslutningar

#### <a name="data-at-rest"></a>Vilande data

Via [Lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) alla data som skrivs till Azure Storage krypteras med 256-bitars AES-kryptering, en av de starkaste blockchiffer som är tillgängliga. Du kan använda Microsoft-hanterade krypteringsnycklarna med SSE eller så kan du använda [era egna krypteringsnycklar](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Storage-konton kan skyddas [tjänstslutpunkter i virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) med [virtuella Nätverksregler](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Detaljerad information om hur du skyddar Azure Storage finns i den [säkerhetsguiden](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Hantering av hemligheter

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) används för att skydda programnycklar och hemligheter så att de inte är tillgänglig av tredje part. Key Vault är inte avsett att användas för lagring av användarlösenord. Det kan du skapa flera säkra behållare, som kallas valv. De här valven stöds av säkerhetsmoduler på maskinvarunivå (HSM:er). Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Key Vaults styr och loggar dessutom åtkomsten till informationen du lagrar. Azure Key Vault kan hantera förfrågningar om och förnyande av TLS-certifikat (Transport Layer Security), och du har tillgång till alla de funktioner som krävs för en robust livscykelhantering av certifikat.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault i den här skissen

- Innehåller lagringsåtkomstnyckel med läsbehörighet för den [hanterad identitet](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) kunden riktade webbappens
- Innehåller DBA-lösenord för SQL Server (i ett separat valv)
- Diagnostikloggning

### <a name="monitoring-logging-and-audit"></a>Övervakning, loggning och granskning

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure som hjälper dig att samla in och analysera data som genereras av resurser i molnet och lokala miljöer.

#### <a name="log-analytics-in-this-blueprint"></a>Log Analytics i denna skiss

- SQL-bedömning
- Key Vault-diagnostik
- Application Insights-anslutning
- Azure-aktivitetsloggen

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar tjänst Application Performance Management (APM) för webbutvecklare på flera plattformar. Används för att övervaka live webbprogram det automatiskt ska identifiera prestandaavvikelser, analysera prestanda och diagnostisera problem och förstå hur användarna samverkar med appen. Application Insights kan distribueras på plattformar, inklusive .NET, Node.js och J2EE, finns lokalt eller i molnet. Den integrerar med din DevOps-process och kan användas med en rad olika utvecklingsverktyg.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights i den här skissen

Den här mallen använder följande Application Insights-komponenter:

- Instrumentpanel för program per plats (operatör, kund- och API)

#### <a name="azure-activity-logs"></a>Azure-aktivitetsloggar

[Azure-aktivitetslogg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) granskningar kontrollplanet händelser för dina prenumerationer. Med aktivitetsloggen kan du fastställa den ”vad, vem, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) aktiverar Kärnövervakning för Azure-tjänster genom att låta insamling av mått, aktivitetsloggar och diagnostikloggar. Azure Monitor ger grundläggande infrastruktursmått och loggfiler för de flesta tjänster i Microsoft Azure.

## <a name="threat-model"></a>Hotmodell

Dataflödesdiagram för denna Referensarkitektur är tillgängligt för [hämta](https://aka.ms/ukofficial-paaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunderna att förstå punkterna i risken i system-infrastruktur när du gör ändringar.

![PaaS Webbprogramsvärden för Storbritannien officiella arbetsbelastningar hotmodell](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS Webbprogramsvärden för Storbritannien officiella arbetsbelastningar hotmodell")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC Molnsäkerhetsprinciper efterlevnad dokumentation

Den kommersiella krona-tjänsten (en myndighet som fungerar för att förbättra kommersiell och inköp av regeringen) förnyas klassificeringen av Microsofts omfattande enterprise molntjänster till G-Cloud v6, som omfattar alla erbjudanden på OFFICIAL-nivå. Information om Azure och G-Cloud finns i den [Azure UK G-Cloud security utvärderingssammanfattning](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Den här skissen stämmer överens med de 14 molnsäkerhetsprinciper som finns dokumenterade i NCSC [Molnsäkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) för att säkerställa en miljö som har stöd för arbetsbelastningar som klassificeras som officiella Storbritannien.

Den [Azure säkerhet och efterlevnad skissen - Storbritannien officiella kundens ansvar matrisen](https://aka.ms/ukofficial-crm) (Excel-arbetsboken) visar en lista över alla 14 molnsäkerhetsprinciper och anger för varje princip (eller princip kapitel), om principen implementering ansvarar för Microsoft, kunden, eller delas mellan två.

Den [Azure säkerhet och efterlevnad skissen - PaaS webbprogram för Storbritannien officiella princip implementering matrisen](https://aka.ms/ukofficial-paaswa-pim) (Excel-arbetsboken) visar en lista över alla 14 molnsäkerhetsprinciper och anger för varje princip (eller princip kapitel) som är utsedd ett kund-ansvar i matrisen kundens ansvarsområden (1) om skissen implementerar principen och 2) en beskrivning av hur implementeringen ska justeras med unika krav.  

Dessutom publicerade Cloud Security Alliance (CSA) Cloud Control matrisen stöd till kunder i utvärderingen av molnleverantörer och för att identifiera frågor som ska besvaras innan du går till molntjänster. I svaret, Microsoft Azure besvarade CSA konsensus utvärdering initiativ enkäten ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), som beskriver hur Microsoft behandlar föreslagna principerna.

## <a name="third-party-assessment"></a>Tredjeparts-utvärdering

Den här skissen har granskats av den Storbritannien nationella Cyberhot Security Center (NCSC) och stämmer överens med Molnsäkerhetsprinciper för NCSC 14

Automation-mallar har testats av Storbritannien kundernas framgång enhet Azure Cloud Solution Architect-teamet och av våra Microsoft-partner [Ampliphae](http://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Distribuera lösningen

Den här Azure-säkerhet och efterlevnad skissen Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Manager API-tjänsten för att distribuera resurser i Azure. Distribution av detaljerade instruktioner finns [här](https://aka.ms/ukofficial-paaswa-repo).

Tre metoder har angetts för distribution; En enkel ”express” [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lämplig för att snabbt skapa ett test miljö; en parametriserade [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) metod för att tillhandahålla större konfiguration för arbetsbelastningen-miljöer och en Azure-portalen baserad distribution där operatören kan ange distributionsparametrarna via Azure portal. 

1.  Klona eller ladda ned [detta](https://aka.ms/ukofficial-paaswa-repo) GitHub-lagringsplatsen till den lokala arbetsstationen.
2.  Granska [metod 1: Azure CLI-2 (Express-version)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) och kör angivna kommandon.
3.  Granska [metoden 1a: Azure CLI-2 (Konfigurera distribution via skriptargument)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) och kör angivna kommandon
4.  Granska [metod 2: processen för distribution av Azure Portal](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) och kör kommandon för listan

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="api-management"></a>API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) skulle kunna användas framför API App Service för att tillhandahålla ytterligare lager av säkerhet, begränsning och kontroller för att exponera, proxy och skydda API: er.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) kan implementeras som en kontroll så att användarna kan registrera, skapa en identitet och aktivera auktorisering och åtkomstkontroll för offentliga webbprogrammet.

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
- Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
- Kunderna får kopiera och använda det här dokumentet som intern referens.
- Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
- Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
- Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
