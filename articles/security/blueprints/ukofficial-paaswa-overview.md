---
title: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS webb program som värd för de officiella arbets belastningarna i Storbritannien
description: Handlingsplan för säkerhet och efterlevnad i Azure-PaaS webb program som värd för de officiella arbets belastningarna i Storbritannien
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 1f6eeea85a348bb8e88a387fa0fc6bed55e41a5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262775"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure Security and Compliance Blueprint (Handlingsplan för säkerhet och efterlevnad i Azure): PaaS webb program som värd för offentliga Storbritannien-arbetsbelastningar

## <a name="azure-security-and-compliance-blueprints"></a>Skisser för säkerhet och efterlevnad i Azure

Azure-ritningar består av väglednings dokument och automatiserings mallar som distribuerar molnbaserade arkitekturer för att erbjuda lösningar till scenarier som har ackrediterings-eller efterlevnads krav. Azure-ritningar är vägledning och samlingar med automatiserings mallar som gör det möjligt för Microsoft Azure kunder att påskynda leverans av sina affärs mål genom att tillhandahålla en grund arkitektur som kan utökas för att uppfylla eventuella ytterligare krav.

## <a name="overview"></a>Översikt

Den här Handlingsplan för säkerhet och efterlevnad i Azure ger vägledning och automatiserings skript för att leverera en Microsoft Azure [plattform som en tjänst (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) värd för webb program som är avsedd för hantering av arbets belastningar som klassificeras som [brittiskt officiellt ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Den här säkerhets klassificeringen omfattar merparten av den information som skapas eller bearbetas av den offentliga sektorn. Detta omfattar rutinmässiga affärs åtgärder och tjänster, som om de tappas bort, blir stulna eller publiceras på mediet, vilket kan ha skadat konsekvenser. Den vanliga hot profilen för den officiella klassificeringen är mycket samma som en privat verksamhet som tillhandahåller värdefull information och tjänster. UK förväntar sig att skydda data från Storbritannien eller andra tjänster i Storbritannien mot hot eller komprometterade av angripare med begränsade möjligheter och resurser, till exempel (men är inte begränsat till) hactivists, uttrycks grupper med en enda utgåva, undersökande journalister, behöriga enskilda hackare och majoriteten av kriminella individer och grupper.

Den här skissen har granskats av Storbritannien National cyberhot Security Centre (NCSC) och överensstämmer med NCSC 14 Cloud Security-principer.

Arkitekturen använder Azure [-plattformen som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) komponent för att leverera en miljö som gör det möjligt för kunderna att undvika kostnader och komplexitet vid köp av program varu licenser, hantering av underliggande program infrastruktur och mellanprogram eller utvecklingsverktyg och andra resurser. Kunderna hanterar de program och tjänster som de utvecklar, vilket fokuserar på att leverera affärs värde, medan Microsoft Azure hanterar de andra Azure-resurserna, till exempel virtuella datorer, lagring och nätverk, och placerar mer av [delningen av ansvar](../fundamentals/paas-deployments.md) för infrastruktur hantering på Azure-plattformen. [Azure App Services](https://azure.microsoft.com/services/app-service/) erbjuder automatisk skalning, hög tillgänglighet, stöder Windows och Linux och möjliggör automatiserade distributioner från GitHub, Azure DevOps eller git-lagringsplats som standard tjänster. Genom att använda App Services kan utvecklare koncentrera sig på att leverera affärs värde utan att behöva hantera infrastrukturen. Det är möjligt att bygga Bygg nya Java, PHP, Node. js, python, HTML eller C# webb program eller även för att migrera befintliga moln eller lokala webb program till Azure App tjänster (även om det finns en grundlig noggrannhet och testning för att bekräfta prestandan krävs).

Den här skissen fokuserar på etableringen av en säker Foundation- [plattform som ett](https://azure.microsoft.com/overview/what-is-paas/) webbaserat gränssnitt för tjänsten för offentliga och även Back-Office-användare. Det här skiss design scenariot betraktar användningen av Azure-värdbaserade webbaserade tjänster där en offentlig användare kan skicka, Visa och hantera känsliga data på ett säkert sätt. Dessutom kan en back kontor eller myndighets operatör på ett säkert sätt bearbeta känsliga data som den offentliga användaren har skickat. Användnings fall för det här scenariot kan vara:

- En användare som lämnar in en skatte avkastning, med en myndighets operatör som bearbetar sändningen.
- En användare som begär en tjänst via ett webbaserat program, med en Back Office-användare som verifierar och levererar tjänsten. eller
- En användare som söker efter och visar hjälp information om offentliga domäner om en myndighets tjänst.

Med hjälp av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallar och Azure kommando rads gränssnitts skript distribuerar skissen en miljö som motsvarar de nationella cyberhot Security Center (NCSC) 14- [moln säkerhets principer](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) och Center för Internet Säkerhet (CIS) [kritiska säkerhets kontroller](https://www.cisecurity.org/critical-controls.cfm). NCSC rekommenderar att deras moln säkerhets principer används av kunder för att utvärdera tjänstens säkerhets egenskaper och för att hjälpa till att förstå ansvars fördelningen mellan kunden och leverantören. Microsoft tillhandahåller information om var och en av dessa principer för att bättre förstå delning av ansvars områden. Den här arkitekturen och motsvarande Azure Resource Manager mallar stöds av Microsoft whitepaper, [14 Cloud Security-kontroller för brittiskt moln med Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Den här arkitekturen har granskats av NCSC och överensstämmer med NCSC 14-molnets säkerhets principer, vilket gör det möjligt för offentliga organisationer att snabbt spåra sin förmåga att uppfylla efterlevnads skyldigheter med hjälp av molnbaserade tjänster globalt och i Storbritannien på Microsoft Azure molnet. Den här mallen distribuerar infrastrukturen för arbets belastningen. Program kod och stöd för affärs nivå och program vara för datanivå måste installeras och konfigureras av kunderna. Detaljerade distributions anvisningar finns [här](https://aka.ms/ukofficial-paaswa-repo/).

Den här skissen är en grund arkitektur. Våra kunder kan använda den här skissen som grund för sina officiella klassificerings-baserade arbets belastningar och utöka de mallar och resurser som har sina egna krav. Den här skissen bygger på principerna för [OFFICAL i IaaS webb program i tre skikt](https://aka.ms/ukofficial-iaaswa) för att erbjuda våra kunder både [infrastruktur som en tjänst (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) och PaaS implementerings alternativ för att vara värd för webbaserade arbets belastningar.

För att distribuera den här skissen krävs en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du snabbt och enkelt registrera dig utan kostnad: Kom igång med Azure. Klicka [här](https://aka.ms/ukofficial-paaswa-repo/) om du vill ha distributions anvisningar.

## <a name="architecture-and-components"></a>Arkitektur och komponenter

Den här skissen levererar en webb program värd lösning i en Azure Cloud-miljö som stöder officiella arbets belastningar i Storbritannien. Arkitekturen ger en säker miljö som utnyttjar Azure-plattformen som tjänst funktioner. I miljön distribueras två App Service webbappar (en för offentliga användare och en för Back-Office-användare), med en API-app-nivå för att tillhandahålla företags tjänster för webb klient delen. En Azure SQL Database distribueras som ett hanterat Relations data lager för programmet. Anslutning till dessa komponenter utanför plattformen och mellan alla dessa komponenter krypteras via TLS 1,2 för att säkerställa att data transporteras med åtkomst som autentiseras av Azure Active Directory.

![PaaS-webbprogram som är värd för referens arkitektur diagram för Förenade arbets belastningar i Storbritannien](images/ukofficial-paaswa-architecture.png?raw=true "PaaS-webbprogram som är värd för referens arkitektur diagram för Förenade arbets belastningar i Storbritannien")

Som en del av distributions arkitekturen, distribueras etablering av säkra lagrings enheter, övervakning & loggning, enhetlig säkerhets hantering & Avancerat skydd av hot och hanterings funktioner distribueras också för att säkerställa att kunderna har alla verktyg som krävs för att skydda och övervaka deras miljö för den här lösningen.

Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

- Azure Active Directory
- App Service
- Webbapp
- API-app
- Azure DNS
- Key Vault
- Azure Monitor (loggar)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Distributions arkitektur

I följande avsnitt beskrivs distributions-och implementerings elementen.

### <a name="security"></a>Säkerhet

#### <a name="identity-and-authentication"></a>Identitet och autentisering

Den här skissen säkerställer att åtkomst till resurser skyddas via katalog-och identitets hanterings tjänster. Den här arkitekturen ger full användning av [identitet som säkerhetsperimeter](../fundamentals/paas-deployments.md). 

Följande tekniker tillhandahåller funktioner för identitets hantering i Azure-miljön:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Alla användare för lösningen har skapats i Azure Active Directory, inklusive användare som har åtkomst till SQL Database.
- Autentisering till operatören riktad mot webb program och åtkomst för administration av Azure-resurser utförs med hjälp av Azure AD. Mer information finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- Databas kolumn kryptering använder Azure AD för att autentisera programmet för att Azure SQL Database. Mer information finns i [Always Encrypted: Skydda känsliga data i SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Det medborgare-riktade webb programmet är konfigurerat för offentlig åtkomst. För att kunna skapa och autentisera konton via Active Directory eller sociala nätverks identitets leverantörer [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) kan integreras vid behov.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) identifierar potentiella sårbarheter och riskfyllda konton ger rekommendationer för att förbättra säkerhets position i organisationens identiteter, konfigurerar automatiska svar på identifierade misstänkta åtgärder relaterade till din organisations identiteter och undersöker misstänkta incidenter och vidtar lämpliga åtgärder för att lösa dem.
- Med [Azure Role-baserade Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) kan du prioriterad åtkomst hantering för Azure. Åtkomst till prenumerationen är begränsad till prenumerations administratören och Azure Key Vault åtkomst begränsas endast till användare som behöver åtkomst till nyckel hantering.
- Genom att [utnyttja Azure Active Directory villkorliga åtkomst](../../active-directory/active-directory-conditional-access-azure-portal.md) kunder kan använda ytterligare säkerhets kontroller för åtkomst till appar eller användare i deras miljö baserat på vissa villkor, till exempel plats, enhet, tillstånd och inloggnings risk.
- [Azure DDoS Protection](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) kombineras med bästa praxis för program design, ger skydd mot DDoS-attacker, med Always on trafikövervakning och real tids minskning av vanliga attacker på nätverks nivå. Med en PaaS-arkitektur är plattforms nivå DDoS-skyddet transparent för kunden och ingår i plattformen, men det är viktigt att notera att säkerhets design ansvaret för program säkerhet ligger hos kunden.

#### <a name="data-in-transit"></a>Data under överföring

Data överförs från utsidan och mellan Azure-komponenter skyddas med hjälp av [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), som använder symmetrisk kryptering baserat på en delad hemlighet för att kryptera kommunikation när de reser över nätverket. Som standard skyddas nätverks trafik med TLS 1,2.

#### <a name="security-and-malware-protection"></a>Säkerhet och skydd mot skadlig kod

[Azure Security Center](https://azure.microsoft.com/services/security-center/) ger en centraliserad vy över säkerhets läget för alla dina Azure-resurser. Du kan snabbt kontrol lera att rätt säkerhets kontroller är på plats och korrekt konfigurerade, och du kan snabbt identifiera eventuella resurser som kräver uppmärksamhet.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) är en anpassad moln konsult som hjälper dig att följa bästa praxis för att optimera dina Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) är en real tids skydds funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Detta är som standard installerat på den underliggande PaaS-infrastrukturen för virtuella datorer och hanteras av Azure Fabric transparent till kunden.

### <a name="paas-services-in-this-blueprint"></a>PaaS Services i den här skissen

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service tillhandahåller en fullständigt hanterad webb värd miljö för webb program som utvecklats i Java, PHP, Node. js python, C# HTML och utan att behöva hantera infrastrukturen. Den erbjuder automatisk skalning och hög tillgänglighet, stöder både Windows och Linux och möjliggör automatiserade distributioner från [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) eller git-baserade lagrings platsen.

App Service är [ISO, SOC och PCI-kompatibel](https://www.microsoft.com/TrustCenter/) och kan autentisera användare med [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) eller med social inloggning ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)och [Microsoft Authentication](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft).

Basic-, standard-och Premium-planer är för produktions arbets belastningar och körs på dedikerade instanser av virtuella datorer. Varje instans har stöd för flera program och domäner. App Services stöder också [IP-adressbegränsningar](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) för att skydda trafik till betrodda IP-adresser om det behövs och även [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/app-service/overview-managed-identity) för säker anslutning till andra PaaS-tjänster, till exempel [Key Vault](https://azure.microsoft.com/services/key-vault/) och [Azure SQL Databas](https://azure.microsoft.com/services/sql-database/). Om ytterligare säkerhet krävs är det en isolerad plan som är värd för dina appar i en privat, dedikerad Azure-miljö och är idealisk för appar som kräver säkra anslutningar med ditt lokala nätverk, eller ytterligare prestanda och skalning.

Den här mallen distribuerar följande App Service funktioner:

- [Standard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service plans nivå
- Flera App Service [distributions fack](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Dev, Preview, frågor och svar, UAT och kurs produktion (standard fack).
- [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/app-service/overview-managed-identity) för att ansluta till [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (detta kan också användas för att ge åtkomst till [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integrering med [Azure Application insikter](../../azure-monitor/app/azure-web-apps.md) för att övervaka prestanda
- [Diagnostikloggar](../../azure-monitor/platform/resource-logs-overview.md) 
- Mått [varningar](../../azure-monitor/app/alerts.md) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL-databasen är en hanterad, allmän relationsdatabastjänst i Microsoft Azure som har stöd för strukturer som relationsdata, JSON, spatial och XML. SQL Database erbjuder hanterade enskilda SQL-databaser, hanterade SQL-databaser i en [elastisk pool](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)och SQL- [hanterade instanser](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (i offentlig för hands version). Ger [dynamiskt skalbar prestanda](../../sql-database/sql-database-purchase-models.md) och innehåller alternativ som [kolumnlagringsindex](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) för extremanalys och rapporter, samt [minnesintern OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) för extrem transaktionell bearbetning. Microsoft hanterar alla korrigeringar och uppdateringar av SQL-kodbasen sömlöst och avlägsnar all hantering av underliggande den infrastrukturen.

Azure SQL Database i den här skissen

Azure SQL Database-instansen använder följande säkerhets åtgärder för databasen:

- [Brand Väggs regler på server nivå och databas nivå](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), eller via [Virtual Network tjänst slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) med hjälp av [regler för virtuella nätverk](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) hjälper till att skydda Azure SQL Database och Azure Data Warehouse mot hot från skadlig aktivitet. Den utför kryptering och dekryptering i real tid av databasen, tillhör ande säkerhets kopior och transaktionsloggfiler i vila utan att det krävs några ändringar i programmet.
- [Azure AD-autentisering](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster på en central plats. Hantering av central-ID är en enda plats för att hantera databas användare och för att förenkla behörighets hanteringen.
- Användning av Azure Active Directory för databas administration
- [Gransknings loggar](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) till lagrings konton
- Mått [aviseringar](../../azure-monitor/app/alerts.md) för MISSLYCKAde DB-anslutningar
- [Identifiering av SQL-hot](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted kolumner](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) är en Microsoft-hanterad moln tjänst som tillhandahåller lagring som är mycket tillgänglig, säker, hållbar, skalbar och redundant. Azure Storage består av Blob Storage, File Storage och Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Azure Storage i den här skissen

Den här mallen använder följande Azure Storages komponenter:

- [Kryptering för lagringstjänst](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Tillåt endast HTTPS-anslutningar

#### <a name="data-at-rest"></a>Vilande data

Genom att [kryptering för lagringstjänst](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) alla data som skrivs till Azure Storage krypteras med 256-bitars AES-kryptering, är en av de starkaste block krypteringarna tillgängliga. Du kan använda Microsoft-hanterade krypterings nycklar med SSE eller så kan du använda [dina egna krypterings nycklar](../../storage/common/storage-encryption-keys-portal.md).

Lagrings konton kan skyddas via [Virtual Network tjänst slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) med hjälp av [regler för virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Detaljerad information om hur du skyddar Azure Storage finns i [säkerhets hand boken](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Hemligheter, hantering

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) används för att skydda program nycklar och hemligheter för att säkerställa att de inte är tillgängliga av tredje part. Key Vault är inte avsett att användas för lagring av användarlösenord. Du kan skapa flera säkra behållare, som kallas valv. De här valven stöds av säkerhetsmoduler på maskinvarunivå (HSM:er). Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Key Vaults styr och loggar dessutom åtkomsten till informationen du lagrar. Azure Key Vault kan hantera förfrågningar om och förnyande av TLS-certifikat (Transport Layer Security), och du har tillgång till alla de funktioner som krävs för en robust livscykelhantering av certifikat.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault i den här skissen

- Innehåller lagrings åtkomst nyckeln med Läs behörighet till den [hanterade identiteten](https://docs.microsoft.com/azure/app-service/overview-managed-identity) för kundens webb program
- Innehåller SQL Server DBA-lösenord (i ett separat valv)
- Diagnostikloggning

### <a name="monitoring-logging-and-audit"></a>Övervakning, loggning och granskning

#### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) är en tjänst i Azure som hjälper dig att samla in och analysera data som genererats av resurser i molnet och i lokala miljöer.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Azure Monitor loggar i den här skissen

- SQL-bedömning
- Key Vault diagnostik
- Application Insights anslutning
- Azure-aktivitetsloggen

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utöknings bar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Används för att övervaka Live-webbprogram den identifierar automatiskt prestanda avvikelser, analyserar prestanda, diagnostiserar problem och förstår hur användare interagerar med appen. Application Insights kan distribueras på plattformar, inklusive .NET, Node. js och Java-EE, som finns lokalt eller i molnet. Den integrerar med din DevOps-process och kan användas med en rad olika utvecklingsverktyg.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights i den här skissen

Den här mallen använder följande Application Insightss komponenter:

- Application Insights instrument panel per webbplats (operatör, kund och API)

#### <a name="azure-activity-logs"></a>Azure aktivitets loggar

[Azure aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) granskar kontroll Plans händelser för dina prenumerationer. Med aktivitets loggen kan du bestämma vad, vem och när som ska användas för alla Skriv åtgärder (skicka, skicka och ta bort) på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) möjliggör kärn övervakning av Azure-tjänster genom att tillåta insamling av mått, aktivitets loggar och diagnostikloggar. Azure Monitor ger grundläggande infrastruktursmått och loggfiler för de flesta tjänster i Microsoft Azure.

## <a name="threat-model"></a>Hot modell

Data flödes diagrammet för den här referens arkitekturen är tillgängligt för [hämtning](https://aka.ms/ukofficial-paaswa-tm) eller finns nedan. Den här modellen kan hjälpa kunder att förstå punkter av potentiell risk i system infrastrukturen när de gör ändringar.

![PaaS-webbprogram som är värd för de officiella, officiella arbets belastningarna hot modell](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS-webbprogram som är värd för de officiella, officiella arbets belastningarna hot modell")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentation om NCSC Cloud Security princips Compliance

Den kommersiella tjänsten för Kron tjänster (ett organ som arbetar för att förbättra företagets handels-och anskaffnings aktivitet) har förnyat klassificeringen av Microsofts moln tjänster i företags klass till G-Cloud V6, som omfattar alla sina erbjudanden på den officiella nivån. Information om Azure och G-Cloud finns i [sammanfattningen av Azures utvärdering av säkerhets utvärderingen](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Den här skissen motsvarar de 14 moln säkerhets principer som dokumenteras i NCSC [Cloud Security-principer](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) för att säkerställa en miljö som stöder arbets belastningar som klassificeras som brittisk tjänsteman.

I [matrisen handlingsplan för säkerhet och efterlevnad i Azure-Storbritannien officiella kund ansvar](https://aka.ms/ukofficial-crm) (Excel-arbetsbok) visas alla 14 säkerhets principer för molnet och eventuella anmärkningar för varje princip (eller princip kapitel), om princip implementeringen är ansvaret hos Microsoft, kunden eller delas mellan de två.

[PaaS webb program för handlingsplan för säkerhet och efterlevnad i Azure den officiella princip implementerings mat ris](https://aka.ms/ukofficial-paaswa-pim) (Excel-arbetsbok) listar alla 14 moln säkerhets principer och anmärkningar för varje princip (eller princip kapitel) som är angiven som en kund ansvar i matrisen för kund ansvar, 1) om skissen implementerar principen och 2) en beskrivning av hur implementeringen överensstämmer med princip kraven.  

Dessutom publicerade Cloud Security Alliance (CSA) moln kontroll mat ris som stöd för kunder i utvärderingen av moln leverantörer och för att identifiera frågor som ska besvaras innan flytt till moln tjänster. Microsoft Azure besvarade CSA-enkäten för CSA bedömning av initiativ ([CSA-CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), som beskriver hur Microsoft hanterar de föreslagna principerna.

## <a name="third-party-assessment"></a>Utvärdering från tredje part

Den här skissen har granskats av Storbritannien National cyberhot Security Centre (NCSC) och överensstämmer med NCSC 14 Cloud Security-principer

Automation-mallarna har testats av Storbritannien kund framgångs rik Azure Cloud Solution arkitekt-grupp och av vår Microsoft-partner, [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Distribuera lösningen

Den här Handlingsplan för säkerhet och efterlevnad i Azure Automation består av JSON-konfigurationsfiler och PowerShell-skript som hanteras av Azure Resource Managers API-tjänst för att distribuera resurser i Azure. Detaljerade distributions anvisningar finns [här](https://aka.ms/ukofficial-paaswa-repo).

Det finns tre metoder för distribution. Ett enkelt "Express" [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) är lämpligt för att snabbt skapa en test miljö. en parameter för [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -metod som ger bättre konfiguration för arbets belastnings miljöer. och en Azure Portal baserad distribution där operatören kan ange distributions parametrar via Azure Portal. 

1.  Klona eller ladda ned [den här](https://aka.ms/ukofficial-paaswa-repo) GitHub-lagringsplatsen till din lokala arbets Station.
2.  Granska [metod 1: Azure CLI 2 (Express-version](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) ) och kör de tillhandahållna kommandona.
3.  Granska [metod 1a: Azure CLI 2 (konfigurera distributionen via skript argument)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) och kör de tillhandahållna kommandona
4.  Granska [metod 2: Azure Portal distributions](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) process och köra de listade kommandona

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="api-management"></a>API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) kan användas framför API-app service för att tillhandahålla ytterligare lager av säkerhet, begränsning och kontroller för att exponera, proxy och skydda API: er.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) kan implementeras som en kontroll för att tillåta att användare registrerar, skapar en identitet och aktiverar auktorisering och åtkomst kontroll för det offentliga webb programmet.

## <a name="disclaimer"></a>Ansvarsfriskrivning

- Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
- Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
- Kunder får kopiera och använda det här dokumentet för intern referens.
- Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
- Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
- Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
