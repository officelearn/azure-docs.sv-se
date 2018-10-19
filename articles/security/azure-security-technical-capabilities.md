---
title: Tekniska funktioner för Azure-säkerhet | Microsoft Docs
description: Läs mer om molnbaserad databehandling tjänster som omfattar ett brett urval av beräkningsinstanser och tjänster som kan skalas upp och ned automatiskt för att uppfylla behoven i ditt program eller enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 862143aec0ac54abb3c62677c5a1ab1c15007edb
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405573"
---
# <a name="azure-security-technical-capabilities"></a>Tekniska funktioner för Azure-säkerhet

För att hjälpa Azure aktuella och potentiella kunder förstå och använda olika säkerhetsrelaterade funktioner som ingår i och med Azure-plattformen Microsoft har utvecklat en serie White Paper-faktablad, Security översikter, bästa praxis och Checklistor. Ämnena variera beroende på bredden och djupet och uppdateras regelbundet. Det här dokumentet är en del av serien som sammanfattas i abstrakt avsnittet nedan. Mer information om den här serien med Azure-säkerhet finns i (URL).

## <a name="azure-platform"></a>Azure-plattformen

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) är en molnplattform som består av infrastruktur och programtjänster, med integrerad datatjänster och avancerad analys och utvecklingsverktyg och tjänster, som finns i Microsofts offentliga molndata datacenter. Kunder kan använder Azure för många olika kapaciteter och scenarier, från grundläggande beräkning, nätverk och lagring, till mobila enheter och web app services till fullständiga moln-scenarier som Internet of Things, och användas med tekniker för öppen källkod, och distribueras som hybridmoln eller värdbaserade inom en kunds datacenter. Azure tillhandahåller molnteknik som byggblock för att hjälpa företag att spara pengar, snabbt utveckla och hantera system proaktivt. När du bygger på eller migrerar IT-tillgångar till en molnleverantör lita du på den organisationens förmåga att skydda dina program och data med tjänsterna och de kontroller som de tillhandahåller för att hantera säkerheten för dina molnbaserade tillgångar.

Microsoft Azure är den enda databehandling molnleverantören som erbjuder en säker, enhetlig plattform och infrastruktur-as-a-service för team att arbeta i sina annat moln kompetens och komplexitetsnivåer projekt, med integrerad datatjänster och analys som avslöja information från data oavsett var den finns i både Microsoft och icke-Microsoft-plattformar, öppna ramverk och verktyg för att tillhandahålla val för att integrera cloud med lokala samt distribuera Azure-molntjänster i lokala datacenter. Som en del av betrodda Microsoft-molnet kan lita kunderna på Azure för branschledande säkerhet, tillförlitlighet, efterlevnad, sekretess och vårt stora nätverk av personer, partner och processer för organisationer i molnet.

Med Microsoft Azure kan du:

- Innovera snabbare med molnet.

- Power affärsbeslut & appar med insikter.

- Skapa fritt och distribuera var som helst.

- Skydda sin verksamhet.

## <a name="scope"></a>Omfång

I centrum av detta White Paper gäller säkerhetsfunktioner och funktioner som stöder kärnkomponenterna i Microsoft Azure, nämligen [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction), [Microsoft Azure SQL Database](https://docs.microsoft.com/azure/sql-database/), [ Modellen för Microsoft Azure VM](https://docs.microsoft.com/azure/virtual-machines/), och de verktyg och infrastruktur som hanterar allt. Den här vitboken fokus på Microsoft Azure tekniska funktioner tillgängliga för dig som kunder att uppfylla deras roll i att skydda säkerheten och sekretessen för sina data.

Vikten av att förstå den här modellen delat ansvar är nödvändigt för kunder som migrerar till molnet. Molnleverantörer erbjuder betydande fördelar för säkerhet och efterlevnad, men dessa fördelar dessutom inte kunden från att skydda sina användare, program och erbjudanden.

För IaaS-lösningar, kunden ansvarar eller har ett delat ansvar för att skydda och hantera operativsystemet, nätverkskonfiguration, program, identitet, klienter och data.  Build-PaaS-lösningar på IaaS-distributioner, kunden är fortfarande ansvarig eller ett delat ansvar för att skydda och hantera program, identitet, klienter och data. Kunden fortsätter att vara ansvariga för SaaS-lösningar, Nonetheless. De måste se till att data har klassificerats korrekt och de delar ett ansvar för att hantera användare och enheter för slutpunkt.

Det här dokumentet ger inte detaljerade täckning av någon av de relaterade Microsoft Azure-Plattformskomponenter som Azure Web Sites, Azure Active Directory, HDInsight, Media Services och andra tjänster som är lagrade på kärnkomponenterna. Även om det finns en lägsta nivå av allmän information, antas läsare bekant med grundläggande begrepp för Azure, enligt beskrivningen i andra referenser som tillhandahålls av Microsoft och ingår i länkarna i detta dokument.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Tillgängliga säkerhetsuppdateringar tekniska funktioner för att uppfylla ansvar för användare (kund) - helheten

Microsoft Azure tillhandahåller tjänster som kan hjälpa kunder att uppfylla behoven för säkerhet, sekretess och efterlevnad. Följande bild kan förklara olika Azure-tjänster tillgängliga för användare att skapa en säker och kompatibel programinfrastruktur baserat på branschstandarder.

![Tillgängliga säkerhetsuppdateringar tekniska funktioner för Big-bild](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Hantera och kontrollera identitet och användaråtkomst åtkomst (skydda)

Azure hjälper dig att skydda företags- och personlig information genom att hantera användaridentiteter och autentiseringsuppgifter och åtkomstkontroll.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft identitets- och hantering av lösningar kan IT-avdelningen skydda åtkomsten till program och resurser i företagets datacenter och i molnet, aktiverar extra kontrollnivåer verifiering, till exempel multifaktorautentisering och villkorlig åtkomst principer. Övervakning av misstänkt aktivitet genom avancerad säkerhetsrapportering, -granskning och -avisering hjälper till att minska potentiella säkerhetsproblem. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) tillhandahåller enkel inloggning till tusentals molnappar (SaaS) och åtkomst till webbappar som du kör lokalt.

Säkerhetsfördelarna med Azure Active Directory (Azure AD) är möjligheten att:

- Skapa och hantera en enda identitet för varje användare i din hybrid-företag, synkronisera användare, grupper och enheter.

- Ger enkel inloggning för åtkomst till dina program inklusive tusentals förintegrerade SaaS-appar.

- Aktivera säkerhet för åtkomst genom att tillämpa regelbaserad Multi-Factor Authentication för både lokala och molnprogram.

- Etablera säker fjärråtkomst till lokala webbprogram via Azure AD-programproxy.

Den [Azure Active Directory-portalen](http://aad.portal.azure.com/) finns en del av Azure-portalen. Från den här instrumentpanelen kan du få en översikt över tillståndet för din organisation och enkelt fördjupa dig i Hantera directory, användare eller programåtkomst.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Här följer kärnfunktioner Azure Identity management:

- Enkel inloggning

- Multi-Factor Authentication

- Säkerhetsövervakning, varningar och maskininlärningsbaserade rapporter

- Hantering av konsumentidentitet och åtkomst

- Enhetsregistrering

- Privileged identity management

- Identitetsskydd

#### <a name="single-sign-on"></a>Enkel inloggning

[Enkel inloggning (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) innebär att de har åtkomst till alla program och resurser som du behöver för att göra affärer, genom att logga in endast en gång med ett enda användarkonto. När du har loggat in, du kan komma åt alla program som du behöver utan att vara krävs för att autentisera (exempel: Skriv ett lösenord) en andra gång.

Många organisationer förlita sig på programvara som en tjänst (SaaS)-program som Office 365, Box och Salesforce för slutanvändarnas produktivitet. Historiskt sett har IT-personal som behövs för att skapa och uppdatera användarkonton i varje SaaS-program separat, och var användarna tvungna att komma ihåg ett lösenord för varje SaaS-program.

[Azure AD utökar den lokala Active Directory till molnet](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), vilket gör att användarna kan använda sina primära organisationskonto inte bara logga in på sina domänanslutna enheter och företagets resurser, men även alla webb- och SaaS-program som behövs för sitt arbete.

Inte bara användare behöver inte hantera flera uppsättningar med användarnamn och lösenord, programåtkomst kan vara automatiskt etablerade eller enhetstiden baserat på organisationsgrupper och deras status som en anställd. [Azure AD introducerar säkerhet och styrning åtkomstkontroller](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) som gör att du centralt hantera användarnas åtkomst mellan SaaS-program.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett viktigt andra säkerhetslager till användarinloggningar och transaktioner. [MFA hjälper dig att skydda](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) åtkomst till data och program och tillgodoser samtidigt användarens önskemål för en enkel inloggningsprocess. Du får stark autentisering via en rad verifieringsalternativ – telefonsamtal, SMS eller mobilapp-meddelande eller verifieringen kod och tredje parts OAuth-token.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, varningar och maskininlärningsbaserade rapporter

Säkerhetsövervakning och varningar och maskininlärningsbaserade rapporter som visar avvikande åtkomstmönster kan hjälpa dig att skydda ditt företag. Du kan använda Azure Active Directory-åtkomst och användningsrapporter för att få insyn i integriteten och säkerheten i din organisations katalog. Med den här informationen kan kan en directory-administratör bättre avgöra var potentiella säkerhetsrisker finns så att de kan planera på lämpligt sätt du minskar riskerna.

I Azure portal eller via den [Azure Active Directory-portalen](http://aad.portal.azure.com/), [rapporter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) kategoriseras på följande sätt:

- Rapporter för avvikelseidentifiering – innehåller händelser som vi inte avvikande inloggning. Vårt mål är att se till att du är medveten om sådan aktivitet och gör att du kan bestämma om huruvida en händelse är misstänkta.

- Rapporter över integrerade program – ge insikter om hur molnappar används i din organisation. Azure Active Directory möjliggör integrering med tusentals molnprogram.

- Felrapporter – visar fel som kan uppstå vid etablering av konton för att externa program.

- Användarspecifika rapporter – visa enheten/logga i aktivitetsdata för en viss användare.

- Aktivitetsloggar – innehåller en post för alla granskade händelser inom de senaste 24 timmarna, senaste 7 dagarna eller senaste 30 dagarna, och ändringar av aktivitet och aktiviteten för återställning och registrering av lösenord.

#### <a name="consumer-identity-and-access-management"></a>Hantering av konsumentidentitet och åtkomst

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en högtillgänglig, global identitetshanteringstjänst för konsumentorienterade program som kan skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Konsumenterna kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

I de tidigare programutvecklare som ville [registrera dig och logga in användare](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) i sina program skulle ha skriva egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure Active Directory B2C erbjuder din organisation ett bättre sätt att integrera identitetshanteringen i program med hjälp av en säker, standardbaserad plattform och en stor uppsättning utökningsbara principer.

När du använder Azure Active Directory B2C kan kan dina användare registrera dig för dina program med hjälp av sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord).

#### <a name="device-registration"></a>Enhetsregistrering

[Azure AD-enhetsregistrering](https://docs.microsoft.com/azure/active-directory/device-management-introduction) är grunden för enhetsbaserad [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) scenarier. När en enhet registreras ger Azure AD-enhetsregistrering enheten med en identitet som används för att autentisera enheten när användaren loggar in. Den autentiserade enheten och attributen för enheten kan sedan användas för att genomdriva principer för villkorlig åtkomst för program som finns i molnet och lokalt.

När de kombineras med en [hantering av mobilenheter (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) lösning, till exempel Intune, enhetens egenskaper i Azure Active Directory har uppdaterats med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter uppfyller dina säkerhets- och efterlevnadskrav.

#### <a name="privileged-identity-management"></a>Privileged identity management

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) kan du hantera, styr, och övervaka Privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.

Ibland behöver användare att utföra Privilegierade åtgärder i Azure eller Office 365-resurser eller andra SaaS-appar. Detta innebär ofta organisationer behöver ge dem permanent privilegierad åtkomst i Azure AD. Detta är en växande säkerhetsrisk för molnbaserade resurser eftersom organisationer inte kan tillräckligt övervaka vad användarna gör med sina administratörsprivilegier. Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom att ett intrång påverka deras övergripande molnsäkerhet. Azure AD Privileged Identity Management hjälper dig för att lösa den här risken.

Azure AD Privileged Identity Management kan du:

- Se vilka användare som är Azure AD-administratörer

- Aktivera på begäran, just-in-time ”administrativ åtkomst till Microsofts onlinetjänster som Office 365 och Intune

- Få rapporter om administratören åtkomsthistorik och ändringar i administratör tilldelningar

- Få aviseringar om åtkomst till en privilegierad roll

#### <a name="identity-protection"></a>Identitetsskydd

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en säkerhetstjänst som tillhandahåller en samlad vy över riskhändelser och potentiella problem som påverkar organisationens identiteter. Identity Protection använder befintliga Azure Active Directorys avvikelseidentifiering identifieringsfunktioner (tillgängligt via Azure Active Directorys avvikande aktivitetsrapporter) och introducerar nya typer av riskhändelser som kan identifiera avvikelser i realtid.

## <a name="secured-resource-access-in-azure"></a>Åtkomst till skyddade resurser i Azure

Åtkomstkontroll i Azure startar från ett. Ägaren till ett Azure-konto som används genom att besöka den [Azure Kontocenter](https://account.windowsazure.com/subscriptions), är kontot Administratör (AA). Prenumerationer är en behållare för fakturering, men de fungerar även som en säkerhetsgräns: varje prenumeration har en tjänst systemadministratörskontot (SA) som kan lägga till, ta bort och ändra Azure-resurser i prenumerationen med hjälp av Azure-portalen. Standard-SA av en ny prenumeration är AA, men AA kan ändra SA i Azure Kontocenter.

![Åtkomst till skyddade resurser i Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Prenumerationer har också en association med en katalog. Katalogen definierar en uppsättning användare. Det kan vara användare från arbetet eller skolan som skapade katalogen eller de kan vara externa användare (det vill säga Microsoft Accounts). Prenumerationer är tillgängliga för en delmängd av de directory-användare som har tilldelats som tjänsten systemadministratörskontot (SA) eller delad administratör (CA); Det enda undantaget är att äldre skäl, Microsoft-Accounts (tidigare Windows Live ID) kan tilldelas som SA eller CA: N utan att vara närvarande i katalogen.

Säkerhetsinriktade företag bör fokusera på att ge anställda exakta behörigheter de behöver. För många behörigheter kan exponera ett konto för att angripare. För få behörigheter innebär att anställda inte kan utföra sitt arbete effektivt. [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) hjälper dig att lösa problemet genom att erbjuda detaljerad åtkomsthantering för Azure.

![Säker åtkomst till ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete. Istället för att ge alla obegränsad behörighet i din Azure-prenumeration eller resurser kan du tillåta enbart vissa åtgärder. Exempelvis kan du använda RBAC för att låta en anställd hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration.

![Åtkomst till skyddade resurser i Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Azure datasäkerhet och kryptering (skydda)

En av nycklarna för dataskydd i molnet redovisning för möjliga tillstånd som kan uppstå i dina data och vilka kontroller som finns för det aktuella tillståndet. Azure data säkerhet och kryptering Metodtips för vara rekommendationerna kring följande data tillstånd.

- I vila: Detta innehåller all information som lagringsobjekt, behållare och typer som statiskt finns på fysiska media som ska vara den magnetiska eller optisk disk.

- Under överföring: När data överförs mellan komponenter, platser eller program, till exempel som över nätverket, i en service bus (från lokal plats till molnet och tvärtom, inklusive hybridanslutningar, till exempel ExpressRoute), eller under en in-/ utdata-process , det är betraktas som i rörelse.

### <a name="encryption-at-rest"></a>Vilande kryptering

För att uppnå kryptering i vila, gör du följande:

Stöd för minst en av de rekommendera kryptering modellerna som beskrivs i följande tabell för att kryptera data.

| Kryptering modeller |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Krypteringen av Server | Krypteringen av Server | Krypteringen av Server | Klient-kryptering
| Serversidan datakryptering med hjälp av tjänsten hanterade nycklar | Server side encryption med Customer-Managed nycklar i Azure Key Vault | Serversidan datakryptering med hjälp av en lokal Kundhanterade nycklar |
| • Azure Resursprovidrar utför åtgärder för kryptering och dekryptering <br> • Microsoft hanterar nycklarna <br>• Fullständig molnfunktioner | • Azure Resursprovidrar utför åtgärder för kryptering och dekryptering<br>• Kunden styr nycklar via Azure Key Vault<br>• Fullständig molnfunktioner | • Azure Resursprovidrar utför åtgärder för kryptering och dekryptering <br>• Kunden styr nycklar On-Premises <br> • Fullständig molnfunktioner| • Azure-tjänster inte kan se dekrypterade data <br>• Kunder behålla nycklar på lokalt (eller skydda butiker i andra). Nycklarna är inte tillgängliga för Azure-tjänster <br>• Minskas molnfunktioner|

### <a name="enabling-encryption-at-rest"></a>Aktivering av kryptering i vila

**Identifiera alla platserna lagrar Data**

Målet med kryptering i vila är att kryptera alla data. Detta eliminerar risken att du saknar viktiga data eller alla beständiga platser. Räkna upp alla data som lagras av ditt program. 

> [!Note] 
> Inte bara ”application data” eller ”personligt identifierbar information, men alla data som rör program, inklusive kontot metadata (prenumeration mappningar, kontrakt information, personligt identifierbar information).

Överväg vilka butiker som du använder för att lagra data. Exempel:

- Extern lagring (till exempel SQL Azure, Document DB, HDInsights, Data Lake osv.)

- Temporär lagring (alla lokala cacheminne som innehåller klientdata)

- Minnescache (kan sättas i växlingsfilen.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Utnyttja befintliga kryptering i rest-stöd i Azure

Utnyttja befintliga kryptering i Rest-stöd för varje butik som du använder.

- Azure Storage: Se [Azure Storage Service Encryption för vilande Data](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Se [Transparent datakryptering (TDE), SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Disklagring för virtuella datorer och lokala ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Använda Azure Disk Encryption för virtuella datorer och lokala disklagring där de stöds:

#### <a name="iaas"></a>IaaS

Tjänster med IaaS-datorer (Windows eller Linux) ska använda [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) att kryptera volymer som innehåller kundinformation.

#### <a name="paas-v2"></a>PaaS v2

Tjänster som körs på PaaS-v2 med hjälp av Service Fabric kan använda Azure-diskkryptering för Virtual Machine Scale Sets [VMSS] att kryptera sina PaaS v2-datorer.

#### <a name="paas-v1"></a>PaaS v1

Azure Disk Encryption stöds för närvarande inte på PaaS v1. Därför måste du använda programmet filnivåkryptering för att kryptera beständiga data i vila.  Detta omfattar, men är inte begränsat till programdata, temporära filer, loggar och kraschdumpar.

De flesta tjänster bör försöka använda kryptering av en lagringsresursprovidern. Vissa tjänster behöver explicit kryptering, till exempel någon beständig nyckelmaterial (certifikat, root / master-nycklar) måste lagras i Key Vault.

Om du stöder tjänstsidan kryptering med Kundhanterade nycklar måste det finnas ett sätt för kunden att hämta nyckeln till oss. Stöds och rekommenderade sättet att göra det genom att integrera med Azure Key Vault (AKV). I det här fallet kan kunderna lägga till och hantera sina nycklar i Azure Key Vault. En kund kan lära dig hur du använder AKV via [komma igång med Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Om du vill integrera med Azure Key Vault, skulle du lägga till kod för att begära en nyckel från AKV när det behövs för dekryptering.

- Se [Azure Key Vault – steg för steg](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) information om hur du integrerar med AKV.

Om du stöder Kundhanterade nycklar, måste du tillhandahålla en UX för kunden att ange vilka Key Vault (eller Key Vault-URI) att använda.

Kryptering i vila innebär krypteringen av värden, infrastruktur- och klienttrafik data, förlust av nycklar på grund av systemfel eller skadlig aktivitet kan innebära alla krypterade data förloras. Det är därför viktigt att din kryptering i Rest-lösningen har en omfattande disaster recovery historia motståndskraftig mot systemfel och skadlig aktivitet.

Tjänster som implementerar kryptering i vila är vanligtvis fortfarande sårbara för krypteringsnycklarna eller data som lämnas okrypterade på värdenheten (till exempel i växlingsfilen för värdoperativsystemet.) Tjänsterna måste därför kontrollera värdvolym för sina tjänster är krypterad. Team har aktiverat distributionen av värden kryptering, som använder för att underlätta den här beräkningen [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP och tillägg till DCM-tjänsten och agenten att kryptera värdvolymen.

De flesta tjänster är implementerade på Azure VM: ar. Dessa tjänster bör få [värden kryptering](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automatiskt när beräkning gör det möjligt. För tjänster som körs i beräkning är hanterade kluster värd kryptering aktiverat automatiskt som Windows Server 2016 distribueras.

### <a name="encryption-in-transit"></a>Kryptering under överföring

Bör vara väsentlig del av din strategi för att skydda data genom att skydda data under överföring. Eftersom data flyttas fram och tillbaka från flera platser, är allmänt rekommenderar vi att du alltid använder SSL/TLS-protokollen för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikationskanalen mellan din lokala och molnbaserade infrastruktur med hjälp av ett virtuellt privat nätverk (VPN).

För data som flyttas mellan den lokala infrastrukturen och Azure, bör du överväga lämpliga säkerhetsåtgärder, till exempel HTTPS- eller VPN.

För organisationer som behöver för att skydda åtkomst från flera arbetsstationer som finns lokalt till Azure måste du använda [Azure plats-till-plats-VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

För organisationer som behöver för att skydda åtkomst från en dator som finns på en plats till Azure måste du använda [punkt-till-plats VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Större datauppsättningar kan flyttas över en dedikerad höghastighetsnätverk WAN-länken som [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om du väljer att använda ExpressRoute, du kan också kryptera data på programnivå med [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för extra skydd.

Om du interagerar med Azure Storage via Azure Portal, inträffar alla transaktioner via HTTPS. [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) över HTTPS kan också användas för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som inte vill skydda data under överföring är svårare för [man-in-the-middle-attacker](https://technet.microsoft.com/library/gg195821.aspx), [avlyssning](https://technet.microsoft.com/library/gg195641.aspx), och sessionskapning. Dessa attacker kan vara det första steget i att komma åt känsliga data.

Du kan lära dig mer om Azure VPN-alternativet genom att läsa artikeln [planering och design för VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Framtvinga data på filkryptering

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) använder kryptering, identitet och auktoriseringsprinciper för att skydda dina filer och e-post. Azure RMS fungerar på flera olika enheter – telefoner, surfplattor och datorer genom att skydda både inom organisationen och utanför organisationen. Den här funktionen är möjligt eftersom Azure RMS lägger till en nivå av skydd som följer med informationen, även när de lämnar organisationens gränser.

När du använder Azure RMS för att skydda dina filer, använder du branschstandardkryptografi med fullständig support av [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). När du använder Azure RMS för dataskydd, har du säkra på att skyddet kvar för filen även om de kopieras till lagring som inte kontrolleras av IT-avdelningen, till exempel en molntjänst för lagring. Samma inträffar för filer som delas via e-post, filen skyddas som en bifogad fil till ett e-postmeddelande med instruktioner för hur du öppnar den skyddade bifogade filen.
När du planerar för Azure RMS-införande rekommenderar vi följande:

- Installera den [RMS-delningsappen](https://technet.microsoft.com/library/dn339006.aspx). Den här appen kan integreras med Office program genom att installera ett Office-tillägget så att användarna kan enkelt skydda filer direkt.

- Konfigurera program och tjänster för att stödja Azure RMS

- Skapa [anpassade mallar](https://technet.microsoft.com/library/dn642472.aspx) som motsvarar dina affärsbehov. Till exempel: en mall för övre hemliga data som ska tillämpas i alla översta hemlighet relaterade e-postmeddelanden.

Organisationer som har svag på [dataklassificering](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och Filskydd kan vara svårare att dataläckage. Utan rätt Filskydd organisationer inte att hämta affärsinsikter, övervaka missbruk och förhindra obehörig åtkomst till filer.

> [!Note]
> Du kan lära dig mer om Azure RMS genom att läsa artikeln [komma igång med Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Skydda ditt program (skydda)
Azure är ansvarig för att skydda infrastruktur och plattform som ditt program körs på, är ditt ansvar att skydda ditt program själva. Du behöver med andra ord att utveckla, distribuera och hantera din programkod och innehåll på ett säkert sätt. Utan detta vara din programkod eller innehåll fortfarande sårbar för attacker.

### <a name="web-application-firewall-waf"></a>Brandvägg för webbaserade program (WAF)
[Brandvägg för webbaserade program (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) är en funktion i [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) som ger ett centraliserat skydd för dina webbprogram mot vanliga kryphål och säkerhetsproblem.

Brandväggen använder regler från [OWASP Core Rule Sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Några vanliga säkerhetshot som brandväggen för webbaserade program skyddar mot:

- Skydd mot SQL-inmatning

- Skydd mot skriptkörning över flera webbplatser

- Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

- Skydd mot åtgärder som inte följer HTTP-protokollet

- Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

- Skydd mot robotar, crawlers och skannrar

- Identifiering av vanliga felkonfigureringar för programmet (det vill säga Apache, IIS osv.)

> [!Note]
> För en mer detaljerad lista över regler och deras skydd finns följande [Core regeluppsättningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Azure tillhandahåller också flera enkel att använda funktioner för att skydda både inkommande och utgående trafik för din app. Azure hjälper kunder att skydda sina programkod genom att tillhandahålla externt får också funktioner för genomsökning av ditt webbprogram efter säkerhetsrisker.

- [Konfigurera Azure Active Directory-autentisering för din app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Säker trafik till din app genom att aktivera Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Tvinga all inkommande trafik över HTTPS-anslutning](http://microsoftazurewebsitescheatsheet.info/)

  - [Aktivera strikt Transport Security (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Begränsa åtkomsten till din app med klientens IP-adress](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Begränsa åtkomsten till din app med klientens beteende - begäran frekvens och samtidighet](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Skanna webbappkoden för säkerhetsproblem med Tinfoil Security Scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurera ömsesidig TLS-autentisering för att kräva klientcertifikat för att ansluta till din webbapp](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Konfigurera ett certifikat för användning från din app för att på ett säkert sätt ansluta till externa resurser](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Ta bort standardserver rubriker för att undvika verktyg från fingeravtryck din app](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [På ett säkert sätt ansluta din app med resurser i ett privat nätverk med hjälp av punkt-till-plats-VPN](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [På ett säkert sätt ansluta din app med resurser i ett privat nätverk med hjälp av Hybridanslutningar](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Azure App Service använder samma program mot skadlig kod som används av Azure Cloud Services och virtuella datorer. Lär dig mer om detta finns på vår [dokumentation för program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Skydda ditt nätverk (skydda)
Microsoft Azure innehåller en stabil nätverksinfrastruktur för att stödja din program- och anslutningskrav för tjänsten. Nätverksanslutningen är mellan resurser i Azure, mellan lokala och Azure-värdbaserade resurser, och till och från Internet och Azure.

Den [Azure nätverksinfrastruktur](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) gör det möjligt att på ett säkert sätt ansluta Azure-resurser till varandra med [virtuella nätverk (Vnet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering för nätverket för Azure-molnet som är anpassad efter din prenumeration. Du kan ansluta virtuella nätverk till ditt lokala nätverk.

![Skydda ditt nätverk (skydda)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Om du behöver grundläggande nivå nätverksåtkomstkontroll (baserat på IP-adress och TCP eller UDP-protokoll), så kan du använda [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). En Nätverkssäkerhetsgrupp (NSG) är en grundläggande tillståndskänsliga paket filtrering brandvägg och gör att du kan styra åtkomst baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple).

Azure-nätverk stöder möjligheten att anpassa beteendet routning för nätverkstrafik på dina virtuella Azure-nätverk. Du kan göra detta genom att konfigurera [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) i Azure.

[Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på Internet.

Azure har stöd för dedikerade WAN-länken anslutning till ditt lokala nätverk och Azure Virtual Network med [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Länken mellan Azure och din webbplats använder en dedikerad anslutning som inte går via det offentliga Internet. Om ditt Azure-program körs i flera datacenter, kan du använda [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) för begäranden från användare smart i instanser av programmet. Du kan också dirigera trafik till tjänster som inte körs i Azure om de är tillgänglig från Internet.

## <a name="virtual-machine-security-protect"></a>Säkerhet för virtuella datorer (skydda)

[Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/) kan du distribuera en lång rad databehandlingslösningar på ett smidigt sätt. Med support för Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services kan du distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

Med Azure, kan du använda [program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware) från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro och Kaspersky för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure Cloud Services och virtuella datorer är en realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Microsoft Antimalware innehåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på din Azure-system.

[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är en skalbar lösning som skyddar dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) hjälper till att dirigera replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats kraschar.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Kontrollera efterlevnad: molntjänster på grund av noggrannhet checklista (skydda)

Microsoft har utvecklat [Cloud Services förfaller noggrannhet checklista](https://aka.ms/cloudchecklist.download) hjälper organisationer att utöva sistone de överväger att flytta till molnet. Det finns en struktur för en organisation med valfri storlek och typ, privata företag och offentliga organisationer, inklusive myndigheter på alla nivåer och ideella organisationer – att identifiera sina egna prestanda, tjänst, datahantering och styrning mål och krav. Detta innebär att de ska jämföras erbjudanden av olika molntjänstleverantörer, slutligen som utgör basen för ett serviceavtal för molnet.

Checklistan tillhandahåller ett ramverk som justerar satsen av satsen med en nya internationella standarden för molnet serviceavtal, ISO/IEC 19086. Den här standarden erbjuder en enhetlig uppsättning överväganden för organisationer som hjälper dem att fatta beslut om molnanvändning och skapa en common grunden för att jämföra cloud service-erbjudanden.

Checklistan främjar noggrant granskade flytta till molnet, vilket ger vägledning för strukturerade och en konsekvent och repeterbar metod för att välja en molntjänstleverantör.

Molnet är inte längre bara ett teknik beslut. Eftersom checklista krav touch på alla aspekter av en organisation, avser att kalla alla viktiga interna-beslutsfattare – den CIO och IT-chef samt eventuella juridiska krav, riskerar tekniker för hantering, anskaffning och efterlevnad. Detta ökar effektiviteten för beslutsfattande processen och grunden beslut i ljud resonemang, vilket minskar risken för oförutsedda förbi vägspärrar implementeringen.

Dessutom checklistan:

- Visar viktiga diskussionsämnen för beslutsfattare i början av implementeringsprocessen för molnet.

- Stöder omfattande business diskussioner om bestämmelser och organisationens egen målen för sekretess, personligt identifierbar information (PII) och datasäkerhet.

- Hjälper organisationer att identifiera alla eventuella problem som kan påverka en cloud-projektet.

- Ger en konsekvent uppsättning frågor, med samma villkor, definitioner, mått och slutprodukter för varje leverantör för att förenkla processen för att jämföra erbjudanden från olika molntjänstleverantörer.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure Säkerhetsverifieringen för infrastruktur och program (identifiera)

[Azure driftsäkerhet](https://docs.microsoft.com/azure/security/azure-operational-security) refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure.

![Säkerhetsverifieringen (identifiera)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure driftsäkerhet bygger på ett ramverk som innehåller den kunskap som en olika funktioner som är unika för Microsoft, inklusive på Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program , och djup medvetenhet om hotlandskapet.

### <a name="microsoft-azure-log-analytics"></a>Microsoft Azure Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är IT-hanteringslösningen för hybridmoln. Används fristående eller om du vill utöka den befintliga distributionen av System Center Log Analytics får du maximal flexibilitet och kontroll för molnbaserad hantering av infrastrukturen.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Du kan hantera en instans i alla moln, inklusive lokala, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrenskraftiga lösningar med Log Analytics. Byggd för den molnbaserade världen, tillhandahåller Log Analytics en ny metod för att hantera ditt företag är det snabbaste och mest kostnadseffektiva sättet att uppfylla nya utmaningar och hantera nya arbetsbelastningar, program och molnmiljöer.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakning genom att samla in data från hanterade resurser i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Den här metoden kan du konsolidera data från olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.

### <a name="azure-security-center"></a>Azure Security Center

Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Säkerhetstillståndet på resurserna i Azure analyseras i Security Center för upptäckt av eventuella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs.

Exempel på rekommendationer:

- att installera program som kan hitta och ta bort skadlig kod

- Konfigurera nätverkssäkerhetsgrupper och regler för trafiken till virtuella datorer

- att installera brandväggar för webbaserade program för skydd mot angrepp riktade mot webbaserade program

- genomföra alla systemuppdateringar som fattas

- se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar, samlas in, analyseras och integreras automatiskt i Security Center. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

- Komprometterade virtuella datorer som kommunicerar med kända skadliga IP-adresser

- avancerad skadlig kod upptäckt genom felrapporteringen i Windows

- Nyckelsökningsangrepp mot virtuella datorer

- säkerhetsaviseringar från integrerade brandväggar och program mot skadlig kod

### <a name="azure-monitor"></a>Med Azure monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) innehåller tips och information om specifika typer av resurser. Den erbjuder visualisering, fråga, routning, aviseringar, Automatisk skala och automatisering på data både från Azure-infrastrukturen (aktivitetsloggen) och varje enskild Azure-resurs (diagnostikloggar).

Molnprogram är komplexa med alla rörliga delar. Övervakning ger data så att ditt program fungerar och körs i ett felfritt tillstånd. Det hjälper dig också att stave potentiella problem eller felsöka tidigare sådana.

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) dessutom du kan använda övervakningsdata för att få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda och underhåll eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Det är viktigt för att identifiera säkerhetsproblem i nätverket och att säkerställa efterlevnad med din IT-säkerhet och föreskrifter styrning modell granskning nätverkssäkerheten. Du kan hämta den konfigurerade Nätverkssäkerhetsgrupp och säkerhetsregler samt gällande säkerhetsregler med säkerhetsgruppen vy. Du kan fastställa vilka portar som är öppna och ss network säkerhetsproblem med listan över regler som tillämpas.

### <a name="network-watcher"></a>Nätverksbevakare

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) är en regional tjänst som hjälper dig att övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Nätverksdiagnostik- och visualiseringsverktyg för Network Watcher hjälper dig att förstå, diagnostisera och få information om ditt nätverk i Azure. Den här tjänsten innefattar paketinsamling, nästa hopp, IP-flöde verifiera säkerhetsgruppvy, NSG-flödesloggar. Scenariot på övervakning ger en heltäckande vy av nätverksresurser, till skillnad från enskilda resource nätverksövervakning.

### <a name="storage-analytics"></a>Lagringsanalys

[Lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan lagra mätvärden som innehåller aggregerad Transaktionsstatistik och kapacitetsdata om förfrågningar till en lagringstjänst. Transaktioner rapporteras på både den API-åtgärdsnivå samt på tjänstnivå lagring och kapacitet rapporteras på tjänstnivå lagring. Måttdata kan användas för att analysera användning av storage-tjänsten, diagnostisera problem med begäranden som görs mot tjänsten storage och förbättra prestanda för program som använder en tjänst.

### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar tjänst Application Performance Management (APM) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den inkluderar kraftfulla analysverktyg att hjälpa dig att diagnostisera problem och förstå vad användarna gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Den fungerar för appar på en rad olika plattformar, däribland .NET, Node.js och J2EE som finns lokalt eller i molnet. Den integrerar med din devOps-process och har kopplingspunkter till olika utvecklingsverktyg.

Tjänsten övervakar:

- **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem.

- **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.

- **Undantag** – analysera aggregerad statistik, eller Välj specifika instanser och öka detaljnivån i stackspårningen och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

- **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.

- **AJAX-anrop från webbsidor** -frekvens, svarstider och Felfrekvens.

- **Antal användare och sessioner.**

- **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning.

- **Värddiagnostik** från Docker eller Azure.

- **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.

- **Anpassade händelser och mått** som du skriver själv i klient- eller kod, spåra affärshändelser, t.ex. sålda, eller vunna spel.

Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, ett lagringskonto och ett virtuellt nätverk eller en webbapp, en databas, en databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteter, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kan du arbeta med resurserna i din lösning som en grupp.

Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

**Fördelarna med att använda Resource Manager**

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

- Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.

- Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

- Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom rollbaserad åtkomstkontroll (RBAC) är inbyggt i hanteringsplattformen.

- Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

- Du kan tydliggöra din organisations fakturering genom att visa kostnaderna för en grupp av resurser som delar samma tagg.

> [!Note]
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du har använt tidigare distributionsmodellen och vill att lära dig om ändringarna läser [förstå Resource Manager-distribution och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om säkerhet genom att läsa några av våra djupgående säkerhetsfrågor:

- [Granskning och loggning](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cyberbrott](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Design- och driftsäkerhet](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Kryptering](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitets- och åtkomsthantering](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Nätverkssäkerhet](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Hothantering](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
