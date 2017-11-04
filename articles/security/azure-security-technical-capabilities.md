---
title: De tekniska funktionerna i Azure security | Microsoft Docs
description: "Läs mer om molnbaserad databearbetning tjänster som omfattar ett brett urval av compute-instanser och tjänster som kan skalas upp och ned automatiskt så att den passar ditt program-eller enterprise."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 7288466cf31e180a16db18f8ddfe02ace3588a8d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="azure-security-technical-capabilities"></a>De tekniska funktionerna i Azure-säkerhet

För att hjälpa Azure aktuella och potentiella kunder förstå och använda olika säkerhetsrelaterade funktioner tillgängliga i och omgivande Azure-plattformen Microsoft har utvecklat en serie faktablad, säkerhet översikter, bästa praxis och checklistor. Avsnitten intervallet vad gäller bredd och djup och uppdateras regelbundet. Det här dokumentet är en del av serien som sammanfattas i följande avsnitt som abstrakt. Mer information om den här serien i Azure-säkerhet finns i (URL).

## <a name="azure-platform"></a>Azure-plattformen

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) en molnplattform som består av infrastruktur och programtjänster, med integrerad datatjänster och avancerade analyser och utvecklingsverktyg och tjänster, som finns i Microsofts offentligt molndata datacenter. Kunder kan använder Azure för många olika kapacitet och scenarier, från enkla beräkning, nätverk och lagring, till mobila och webbtjänster app fullständig molnet scenarier, t.ex. Sakernas Internet, och användas med öppen källkod tekniker och distribueras som hybridmoln eller finns inom en kund datacenter. Azure tillhandahåller molnteknik som byggblock för att hjälpa företag att spara kostnader, förnya snabbt och hantera system proaktivt. När du bygger på eller migrera IT tillgångar till en molntjänstleverantör, måste den organisationens förmåga att skydda dina program och data med tjänster och de kontroller som de tillhandahåller för att hantera säkerheten för din molnbaserade tillgångar.

Microsoft Azure är den enda moln databehandling providern som erbjuder en säker och konsekvent plattform och infrastructure-as-a-service där team kan arbeta i sina kunskaper i annat moln och nivåer av projektet komplexiteten med integrerad datatjänster och analyser som upptäcka intelligence från data oavsett var den finns i både Microsoft och icke-Microsoft-plattformar, öppna ramverk och verktyg för att tillhandahålla alternativ för att integrera moln med lokala samt distribuera Azure-molntjänster i lokala datacenter. Som en del av Microsoft betrodda molnet beroende kunder Azure för branschledande säkerhet, tillförlitlighet, kompatibilitet, sekretess och stora nätverk personer, partners och processer för att stödja organisationer i molnet.

Med Microsoft Azure kan du:

- Accelerera innovation med molnet.

- Power affärsbeslut och appar med insikter.

- Skapa fritt och distribuera var som helst.

- Skydda verksamheten.

## <a name="scope"></a>Omfång

I centrum i den här rapporten avser säkerhetsfunktioner och funktioner som stöder kärnkomponenterna i Microsoft Azure, nämligen [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction), [Microsoft Azure SQL-databaser](https://docs.microsoft.com/azure/sql-database/), [Microsoft Azure virtuella modellen](https://docs.microsoft.com/azure/virtual-machines/    ), och de verktyg och den infrastruktur som hanterar allt. Den här vitboken fokus på Microsoft Azure tekniska funktioner tillgängliga som kunder att uppfylla sina roller för att skydda säkerhet och sekretess för sina data.

Vikten av att förstå den här delade ansvar modellen är viktigt för kunder som flyttar till molnet. Molntjänstleverantörer erbjuder betydande fördelar för säkerhet och efterlevnad, men dessa fördelar dessutom inte kunden från att skydda sina användare, program och tjänster.

Kunden är ansvarig för IaaS-lösningar eller har en delad ansvaret för att skydda och hantera operativsystem, konfiguration av nätverk, program, identitet, klienter och data.  PaaS lösningar bygger på IaaS-distributioner, kunden fortfarande ansvarig eller en delad ansvar för att skydda och hantera program, identitet, klienter och data. Kunden fortsätter att vara ansvariga för SaaS-lösningar, Nonetheless. De måste säkerställa att data har klassificerats korrekt och de delar en ansvar för att hantera sina användare och enheter för slutpunkt.

Det här dokumentet ger inte detaljerade täckning för någon av tillhörande Microsoft Azure-Plattformskomponenter som Azure-webbplatser, Azure Active Directory, HDInsight, Media Services och andra tjänster som lager ovanpå kärnkomponenter. Även om det finns en miniminivå för allmän information, antas läsare bekant med Azure grundläggande koncept som beskrivs i andra referenser som tillhandahålls av Microsoft och som ingår i länkarna i detta dokument.


## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Tillgängliga säkerhetsuppdateringar tekniska funktioner för att uppfylla ansvar för användare (kunden) - översikt

Microsoft Azure tillhandahåller tjänster som kan hjälpa kunderna uppfyller behoven för säkerhet, sekretess och kompatibilitet. Följande bild kan förklara olika Azure-tjänster som användarna kan bygga en infrastruktur för skyddade och kompatibla program baserat på branschstandarder.

![Tillgängliga säkerhetsuppdateringar tekniska funktioner Big-bild](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Hantera och styra identitets- och åtkomst (skydda)

Azure hjälper dig att skydda affärsdata och personlig information genom att du kan hantera användaridentiteter och autentiseringsuppgifter och styr åtkomsten.

### <a name="azure-active-directory"></a>Azure active directory

Microsoft identitets- och lösningar hjälp IT skydda åtkomst till program och resurser i företagets datacenter och i molnet, aktivera nivåer av verifiering, till exempel multifaktorautentisering och principer för villkorlig åtkomst. Misstänkt aktivitet med avancerad säkerhet rapportering, granskning och aviseringar hjälper dig att minimera potentiella säkerhetsproblem. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) ger enkel inloggning till tusentals molnet (SaaS)-appar och åtkomst till webbprogram som du kör lokalt.

Säkerhet fördelar för Azure Active Directory (AD) möjlighet att:

- Skapa och hantera en enstaka identitet för varje användare inom företaget hybrid synkronisera användare, grupper och enheter.

- Tillhandahålla enkel inloggning åtkomst till ditt program, bland annat tusentals förintegrerade SaaS-appar.

- Aktivera åtkomst programsäkerhet genom att tillämpa regelbaserad Multifaktorautentisering för både lokala program och molnprogram.

- Etablera säker fjärråtkomst till lokala webbprogram via Azure AD Application Proxy.

[Azure active directory-portalen](http://aad.portal.azure.com/) finns en del av azure-portalen. Från den här instrumentpanelen kan du få en översikt över statusen för din organisation och enkelt fördjupa dig i Hantera directory, användare eller programåtkomst.

![Azure active directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Följande är kärnfunktioner Azure Identity management:

- Enkel inloggning

- Multi-Factor Authentication

- Säkerhetsövervakning, aviseringar och machine learning-baserade rapporter

- Hantering av konsumentidentitet och åtkomst

- Enhetsregistrering

- Privileged identity management

- Identitetsskydd

#### <a name="single-sign-on"></a>Enkel inloggning

[Enkel inloggning (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) innebär får tillgång till alla program och resurser som du behöver göra affärer, genom att logga in en gång med ett enda användarkonto. När du är inloggad du har åtkomst till alla de program som du behöver, utan som krävs för att autentisera (Skriv till exempel ett lösenord) en andra gång.

Många organisationer förlita sig på programvara som en tjänst (SaaS)-program, till exempel Office 365, rutan och Salesforce för slutanvändarens produktivitet. Tidigare IT-personal som behövs för att skapa och uppdatera användarkonton i varje SaaS-program separat, och var användarna tvungna att ett lösenord för varje SaaS-program.

[Azure AD utökar Active Directory för lokalt till molnet](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), vilket ger användarna möjlighet att använda sina primära organisationskonto inte bara logga in på sina domänanslutna enheter och företagets resurser, men även alla webb- och SaaS-program som behövs för sitt jobb.

Inte bara användare behöver inte hantera flera uppsättningar av användarnamn och lösenord, programåtkomst kan vara automatiskt etablerade eller frigör etablerade utifrån organisationens grupper och deras status som en medarbetare. [Azure AD introducerar säkerhets- och styrning kontroller](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) som gör att du centralt hantera användarnas åtkomst i SaaS-program.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure Multi-Factor authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) är en autentiseringsmetod som kräver att mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager till användarinloggningar och transaktioner. [MFA hjälper dig att skydda](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering via en mängd alternativ för verifiering – telefonsamtal, textmeddelande eller mobilapp meddelande eller verifiering kod och tredje parts OAuth token.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, aviseringar och machine learning-baserade rapporter

Säkerhetsövervakning och aviseringar och machine learning-baserade rapporter som identifierar inkonsekventa åtkomstmönster kan hjälpa dig att skydda din verksamhet. Du kan använda Azure Active Directory-åtkomst och användningsrapporter insyn i integritet och säkerheten för din organisations katalog. Med den här informationen kan fastställa en katalogadministratör bättre där möjliga säkerhetsrisker kan ligga så att de kan på lämpligt sätt att minska riskerna.

I Azure-portalen eller via [Azure Active directory-portalen](http://aad.portal.azure.com/), [rapporter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) kategoriseras på följande sätt:

- Avvikelseidentifiering rapporter – innehålla händelser som vi avvikande inloggning. Vårt mål är att se till att du är medveten om sådan aktivitet och gör att du ska kunna avgöra om en händelse är misstänkt.

- Integrerade program rapporter – tillhandahåller insikter om hur molnprogram används i din organisation. Azure Active Directory möjliggör integrering med tusentals molnprogram.

- Felrapporter – indikera fel som kan uppstå vid etablering av konton till externa program.

- Användarspecifika rapporter – visa enheten/logga i aktivitetsdata för en viss användare.

- Aktivitetsloggar – innehåller en post på alla granskade händelser inom de senaste 24 timmarna, senaste 7 dagarna eller senaste 30 dagarna, och ändringar av aktiviteten och aktiviteten för återställning och registrering av lösenord.

#### <a name="consumer-identity-and-access-management"></a>Hantering av konsumentidentitet och åtkomst

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en hög tillgänglighet, globala identity management-tjänsten för konsumentinriktade program som kan skalas till flera hundra miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Konsumenterna kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

I de senaste, programutvecklare som ville [registrera och logga in användare](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) i sina program tvungna att skriva egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure Active Directory B2C erbjuder organisationen ett bättre sätt att integrera identitetshanteringen i program med hjälp av en säker, standardbaserad plattform och en stor uppsättning utökningsbara principer.

När du använder Azure Active Directory B2C kan kan dina användare registrera dig för ditt program med hjälp av sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord).

Enhetsregistrering

[Azure AD Device Registration](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) är grunden för enhetsbaserad [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) scenarier. När en enhet registreras, tillhandahåller Azure Active Directory Device Registration en identitet som används för att autentisera enheten när användaren loggar in till enheten. Den autentiserade enheten och attributen för enheten kan sedan användas för att genomdriva principer för villkorlig åtkomst för program som finns i molnet och lokalt.

När den kombineras med en [hantering av mobila enheter (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) lösning, till exempel Intune, attributen i Azure Active Directory uppdateras med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter uppfyller dina säkerhets- och efterlevnadskrav.

#### <a name="privileged-identity-management"></a>Privileged identity management

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) låter dig hantera, styr, och övervaka Privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.

Ibland behöver användare att utföra Privilegierade åtgärder i Azure eller Office 365 resurser eller andra SaaS-appar. Detta innebär ofta organisationer behöver ge dem permanent privilegierad åtkomst i Azure AD. Detta är en allt större säkerhetsrisk för molnbaserade resurser eftersom organisationer inte tillräckligt övervaka vad användarna gör med deras administratörsrättigheter. Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom som en överträdelse påverka deras övergripande säkerheten för molnet. Azure AD Privileged Identity Management kan lösa denna risk.

Azure AD Privileged Identity Management kan du:

- Se vilka användare som är administratörer för Azure AD

- Aktivera på begäran, just-in-time ”administrativ åtkomst till Microsofts onlinetjänster som Office 365 och Intune

- Hämta rapporter om administratören åtkomsthistorik och ändringar i administratörstilldelningar

- Få meddelanden om åtkomst till en privilegierad roll

#### <a name="identity-protection"></a>Identitetsskydd

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en security-tjänst som tillhandahåller en samlad vy riskhändelser och potentiella säkerhetsproblem som påverkar din organisations identiteter. Identity Protection använder befintliga Azure Active Directorys avvikelseidentifiering identifieringsfunktionerna (tillgängligt genom Azure AD avvikande aktivitetsrapporter) och ger nya risk händelsetyper som kan identifiera avvikelser i realtid.

## <a name="secured-resource-access-in-azure"></a>Åtkomst till skyddade resurser i Azure

Åtkomstkontroll i Azure startar ur faktureringsinformation. Ägaren till ett Azure-konto som nås genom att besöka den [Azure Accounts Center](https://account.windowsazure.com/subscriptions), är kontot Administratör (AA). Prenumerationer är en behållare för fakturering, men de fungerar som en säkerhetsgräns: varje prenumeration har en Service systemadministratörskontot (SA) som kan lägga till, ta bort och ändra Azure-resurser i den prenumerationen med hjälp av Azure portal. Standard-SA för en ny prenumeration är AA, men AA kan ändra SA i Azure Accounts Center.

![Åtkomst till skyddade resurser i Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Prenumerationer har också en association med en katalog. Katalogen definierar en uppsättning användare. Det kan vara användare från arbetet eller skolan som skapade katalogen eller de kan vara externa användare (det vill säga Microsoft Accounts). Prenumerationer är tillgängliga för en delmängd av de directory-användare som har tilldelats som tjänsten systemadministratörskontot (SA) eller Medadministratör (CA); Det enda undantaget är att äldre skäl Microsoft Accounts (tidigare Windows Live ID) kan tilldelas som SA eller Certifikatutfärdare utan att vara närvarande i katalogen.

Säkerhet indatavärdena företag bör tänka på att ge anställda behörigheterna exakt som de behöver. För många behörigheter kan exponera ett konto för attacker. För få behörigheter innebär att anställda kan få arbetet gjort effektivt. [Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) hjälper dig att lösa problemet genom att erbjuda detaljerad åtkomsthantering för Azure.

![Åtkomst till skyddade resurser ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Med RBAC kan du särskilja uppgifter i din grupp och ge bara mängden åtkomst till användare som de behöver för att utföra sitt arbete. Obegränsad istället för att ge alla behörigheter i din Azure-prenumeration eller resurser, kan du tillåta endast vissa åtgärder. Till exempel använda RBAC så att en medarbetare som hanterar virtuella datorer i en prenumeration medan en annan kan hantera SQL-databaser inom samma prenumeration.

![Åtkomst till skyddade resurser i Azure(RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Azure datasäkerhet och -kryptering (skydda)

En av nycklar på dataskydd i molnet redovisning för möjliga tillstånd som kan uppstå i dina data och vilka kontroller som är tillgängliga för det aktuella tillståndet. För Azure data säkerhets- och bästa praxis vara rekommendationer runt följande data tillstånd.

- I vila: Detta innehåller all information som lagringsobjekt, behållare och typer som finns statiskt på fysiska media som ska vara det magnetiska eller optical disk.

- Under överföring: När data överförs mellan komponenter, platser eller program, som i nätverket via en service bus (från lokalt till molnet och vice versa, inklusive hybridanslutningar, till exempel ExpressRoute), eller under en in-/ utdata-process kan anses av som i rörelse.

### <a name="encryption--rest"></a>Kryptering @ rest

Att uppnå kryptering i vila av följande:

Stöd för minst en av de rekommenderade kryptering modeller som beskrivs i följande tabell för att kryptera data.

| Kryptering modeller |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Krypteringen av Server | Krypteringen av Server | Krypteringen av Server | Klienten kryptering
| Kryptering för serversidan med tjänsten hanterade nycklar | Kryptering för serversidan med Customer-Managed nycklar i Azure Key Vault | Kryptering för serversidan med hjälp av lokal kunden hanterade nycklar |
| • Azure Resursproviders utför åtgärder för kryptering och dekryptering <br> • Microsoft hanterar nycklarna <br>• Fullständig molnet funktioner | • Azure Resursproviders utför åtgärder för kryptering och dekryptering<br>• Kunden styr nycklar via Azure Key Vault<br>• Fullständig molnet funktioner | • Azure Resursproviders utför åtgärder för kryptering och dekryptering <br>• Kunden styr nycklar lokal <br> • Fullständig molnet funktioner| • Azure-tjänster inte kan se dekrypterade data <br>• Kunder behålla nycklar på lokalt (eller i andra säkra lagrar). Nycklarna är inte tillgänglig för Azure-tjänster <br>• Minskas molnet funktioner|

### <a name="enabling-encryption-at-rest"></a>Aktivera kryptering i vila

**Identifiera alla platser lagrar-Data**

Målet med kryptering i vila är att kryptera alla data. Detta eliminerar möjligheten att viktiga data eller alla beständiga platser saknas. Räkna upp alla data som lagras av ditt program. 

> [!Note] 
> Inte bara ”application data” eller ”personligt identifierbar information, men alla data som rör program inklusive konto metadata (prenumeration mappningar kontraktet information, personligt identifierbar information).

Överväg att vilka butiker som du använder för att lagra data. Exempel:

- Extern lagringsenhet (till exempel SQL Azure-dokumentet DB, HDInsights, Data Lake, etc.)

- Tillfälligt lagringsutrymme (alla lokala cacheminnet som innehåller klientdata)

- I cacheminnet (kan användas i växlingsfilen.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Utnyttja befintliga kryptering i rest-stöd i Azure

Utnyttja befintliga kryptering i vila stöd för varje butik som du använder.

- Azure Storage: Se [Azure Storage Service-kryptering av vilande Data](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Finns [Transparent datakryptering (TDE), SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Virtuella och lokala disklagring ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Använd Azure Disk Encryption för lagring av Virtuella och lokala diskar där de stöds:

IaaS

Tjänster med IaaS-VM (Windows eller Linux) ska använda [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) att kryptera volymer som innehåller kundens data.

PaaS v2

Tjänster som körs på PaaS v2 med hjälp av Service Fabric kan använda Azure disk encryption för Skaluppsättning för virtuell dator [VMSS] att kryptera deras PaaS v2 virtuella datorer.

PaaS v1

Azure Disk Encryption stöds för närvarande inte på PaaS v1. Därför måste du använda programmet kryptering för kryptering av sparade data i vila.  Detta inkluderar, men är inte begränsat till programdata, temporära filer, loggar och minnesdumpar krascher.

De flesta tjänster ska försöka använda kryptering av en lagringsresursprovidern. Vissa tjänster behöver göra explicit kryptering, till exempel alla sparade nyckelmaterial (certifikat, root / master-nycklar) måste vara lagrad i Nyckelvalvet.

Om du stöder kryptering för tjänsten på klientsidan med kundhanterad nycklar måste det finnas ett sätt för kunden att hämta nyckeln till oss. Stöds och rekommenderade sätt att göra det genom att integrera med Azure Key Vault (AKV). I det här fallet kan kunder lägga till och hantera sina nycklar i Azure Key Vault. En kund kan lära dig hur du använder AKV via [komma igång med Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Om du vill integrera med Azure Key Vault lägger du till kod för att begära en nyckel från AKV vid behov för dekryptering.

- Se [Azure Key Vault – steg för steg](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) för information om hur du integrerar med AKV.

Om du stöder hanterade Kundnycklar, måste du ange en UX för kund för att ange vilken Key Vault (eller Key Vault-URI) ska användas.

Kryptering av värden, infrastruktur- och data, förlust av nycklar på grund av systemfel innebär att kryptering i vila eller skadliga aktiviteter kan innebära alla krypterade data går förlorade. Det är därför viktigt att krypteringen Rest-lösning har en omfattande disaster recovery artikel motståndskraftiga mot systemfel och skadlig aktivitet.

Tjänster som implementerar kryptering i vila är vanligtvis fortfarande sårbara för krypteringsnycklarna eller data som lämnas okrypterad på värdenheten (till exempel i växlingsfilen för värd-OS.) Tjänster måste du därför kontrollera värdvolym för sina tjänster krypteras. För att underlätta detta beräkning team har aktiverat distributionen av värden kryptering, som använder [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP och tillägg till DCM-tjänsten och agenten att kryptera värdvolymen.

De flesta tjänster implementeras på standard virtuella Azure-datorer. Sådana tjänster ska få [värden kryptering](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) när beräkning aktiveras automatiskt den. För tjänster som körs i beräkning är hanterade kluster värden kryptering aktiverat automatiskt som Windows Server 2016 lyfts.

### <a name="encryption-in-transit"></a>Kryptering under överföring

Skydda data under överföringen ska väsentlig del av strategin för skydd av data. Eftersom data flyttas fram och tillbaka från många platser, vi Allmänt rekommenderar att du alltid använder SSL/TLS-protokoll för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikationskanalen mellan din lokala och moln infrastruktur med hjälp av ett virtuellt privat nätverk (VPN).

För data som flyttas mellan din lokala infrastruktur och Azure, bör du lämpliga skyddsåtgärder, till exempel HTTPS eller VPN.

För organisationer som behöver för att skydda åtkomst från flera arbetsstationer som finns lokalt till Azure kan använda [Azure plats-till-plats-VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

För organisationer som behöver för att skydda åtkomst från en dator som finns på plats till Azure kan använda [punkt-till-plats VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Större datauppsättningar kan flyttas dedikerade snabba WAN-länk som [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om du väljer att använda ExpressRoute kan du också kryptera data på programnivå med hjälp av [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för extra skydd.

Om du interagerar med Azure Storage via Azure Portal, alla transaktioner ska ske via HTTPS. [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) över HTTPS kan också användas för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som inte kan skydda data under överföringen är mer känslig för [man-in-the-middle-attacker](https://technet.microsoft.com/library/gg195821.aspx), [avlyssning](https://technet.microsoft.com/library/gg195641.aspx), och sessionskapning. Dessa attacker kan vara det första steget i att komma åt känsliga data.

Du kan lära dig mer om Azure VPN-alternativet genom att läsa artikeln [planering och design för VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Tillämpa fil nivån datakryptering

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) använder kryptering, identitet och auktorisering för att skydda filer och e-post. Azure RMS fungerar över flera enheter – telefoner, surfplattor och datorer genom att skydda både inom organisationen och utanför organisationen. Den här funktionen är möjligt eftersom Azure RMS lägger till en skyddsnivå som finns kvar med data, även när de lämnar organisationens gränser.

När du använder Azure RMS för att skydda dina filer, använder du branschstandardkryptografi med fullständigt stöd för [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). När du använder Azure RMS för dataskydd, har garantier att skyddet kvar för filen även om de kopieras till lagringsplats som inte kontrolleras av IT-avdelningen, till exempel en molntjänst för lagring. Samma inträffar för filer som delas via e-post, skyddas som en bilaga i ett e-postmeddelande med instruktioner för hur du öppnar den skyddade bifogade filen.
När du planerar för införandet av Azure RMS rekommenderar vi följande:

- Installera den [RMS-delningsappen](https://technet.microsoft.com/library/dn339006.aspx). Den här appen integreras med Office program genom att installera ett Office-tillägget så att användarna kan enkelt skydda filer direkt.

- Konfigurera program och tjänster för att stödja Azure RMS

- Skapa [anpassade mallar](https://technet.microsoft.com/library/dn642472.aspx) som motsvarar dina affärsbehov. Exempel: en mall för övre hemlig information som ska användas i alla översta hemlighet relaterade e-postmeddelanden.

Organisationer som är beroende av på [dataklassificering](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och Filskydd kan vara svårare att data sprids. Utan rätt Filskydd kan organisationer inte hämta affärsinsikter, övervaka missbruk och förhindra obehörig åtkomst till filer.

> [!Note]
> Du kan lära dig mer om Azure RMS genom att läsa artikeln [komma igång med Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Skydda ditt program (skydda)
Medan Azure ansvarar för att skydda infrastruktur och plattform som tillämpningsprogrammet körs på, är ditt ansvar att skydda ditt program sig själv. Du behöver med andra ord att utveckla, distribuera och hantera din programkod och innehåll på ett säkert sätt. Utan detta, kan din programkod eller innehåll definieras sårbar för attacker.

### <a name="web-application-firewall-waf"></a>Brandvägg för webbaserade program (WAF)
[Brandvägg för webbaserade program (Brandvägg)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) är en funktion i [Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) som tillhandahåller centraliserad skyddet av dina webbapplikationer från vanliga kryphål och säkerhetsproblem.

Brandväggen använder regler från [OWASP Core Rule Sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Några vanliga säkerhetshot som brandväggen för webbaserade program skyddar mot:

- Skydd mot SQL-inmatning

- Skydd mot skriptkörning över flera webbplatser

- Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

- Skydd mot åtgärder som inte följer HTTP-protokollet

- Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

- Skydd mot robotar, crawlers och skannrar

- Identifiering av program vanliga felinställningar (det vill säga Apache, IIS osv.)

> [!Note]
> En detaljerad lista över regler och deras skydd finns följande [Core uppsättningar](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Azure tillhandahåller också flera lätt att använda funktioner för att skydda både inkommande och utgående trafik för din app. Azure också hjälper kunder skyddas sina programkod genom ett externt tillhandahålla funktionalitet för genomsökning av ditt webbprogram för säkerhetsproblem.

- [Konfigurera Azure Active Directory-autentisering för din app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)


- [Säker trafik till din app genom att aktivera Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

    - [Tvinga all inkommande trafik via HTTPS-anslutning](http://microsoftazurewebsitescheatsheet.info/)

  - [Aktivera strikt transportsäkerhet (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)


- [Begränsa åtkomsten till din app genom klientens IP-adress](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Begränsa åtkomsten till din app genom klientens beteende - begäran frekvens och samtidighet](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Skanna web app-kod för säkerhetsproblem med Tinfoil Security Scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurera TLS ömsesidig autentisering för att kräva klientcertifikat för att ansluta till ditt webbprogram](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Konfigurera ett certifikat för användning från din app för att ansluta säkert till externa resurser](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Ta bort standard server rubriker för att undvika verktyg från fingeravtryck din app](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [På ett säkert sätt ansluta din app med resurser i ett privat nätverk med punkt-till-plats VPN](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [På ett säkert sätt ansluta din app med resurser i ett privat nätverk med Hybridanslutningar](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Azure Apptjänst använder samma program mot skadlig kod som används av Azure-molntjänster och virtuella datorer. Läs mer om detta finns i vår [program mot skadlig kod dokumentationen](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Skydda nätverket (skydda)
Microsoft Azure innehåller en stabil nätverksinfrastruktur för att stödja dina program och tjänsten anslutningskrav. Nätverksanslutningen är möjlig mellan resurser i Azure, mellan lokala och Azure värdbaserade resurser, och till och från Internet och Azure.

Den [Azure nätverksinfrastruktur](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) kan du ansluta Azure-resurser på ett säkert sätt till varandra med [virtuella nätverk (Vnet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av nätverket Azure-molnet dedikerad till din prenumeration. Du kan ansluta Vnet till ditt lokala nätverk.

![Skydda nätverket (skydda)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Om du behöver grundläggande nivån nätverksåtkomstkontroll (baserat på IP-adress och TCP- eller UDP-protokoll), så du kan använda [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). En Nätverkssäkerhetsgrupp (NSG) är en grundläggande tillståndskänslig paket filtrering brandväggen och du kan styra åtkomsten baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple).

Azure nätverk stöder möjligheten att anpassa dirigeringsbeteendet för nätverkstrafik i dina virtuella Azure-nätverk. Du kan göra detta genom att konfigurera [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) i Azure.

[Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på Internet.

Har stöd för Azure dedikerad WAN-länken anslutning till lokalt nätverk och ett Azure Virtual Network med [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Länken mellan Azure och din plats använder en dedikerad anslutning som inte går via det offentliga Internet. Om din Azure-program körs i flera datacenter, kan du använda [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) på vägen begäranden från användare intelligent i instanser av programmet. Du kan också dirigera trafik till tjänster inte körs i Azure om de är tillgängliga från Internet.

## <a name="virtual-machine-security-protect"></a>Säkerhet för virtuell dator (skydda)

[Virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/) kan du distribuera en mängd olika databehandling lösningar i en flexibel metod. Med support för Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services kan du distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

Med Azure, kan du använda [program mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware) från säkerhet leverantörer, till exempel Microsoft, Symantec, Trend Micro och Kaspersky för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure-molntjänster och virtuella datorer är en realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Microsoft Antimalware innehåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på Azure-system.

[Azure-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är en skalbar lösning som skyddar dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) hjälper till att samordna replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om den primära platsen kraschar.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Kontrollera efterlevnad: molntjänster på grund av fordringar checklista (skydda)

Microsoft har utvecklat [Cloud Services på grund av fordringar checklista](https://aka.ms/cloudchecklist.download) att hjälpa organisationer att utöva vederbörlig möda åt som de anser vara en flytt till molnet. Det finns en struktur för en organisation av storlek och typ, privata företag och offentliga organisationer, inklusive myndigheter på alla nivåer och ideella föreningar – att identifiera sina egna prestanda, tjänst, hantering, och styrning mål och krav. Detta innebär att de ska jämföras erbjudanden för olika molntjänstleverantörer som slutligen utgör grunden för ett moln.

Checklistan innehåller ett ramverk som justerar satsen av sats med en ny internationella standard för molnet serviceavtal ISO/IEC 19086. Standarden erbjuder enhetlig överväganden för organisationer att hjälpa dem att fatta beslut om molnet införande och skapa en gemensamma grunden för att jämföra Tjänsterbjudanden i molnet.

Checklistan befordrar en noggrant vetted flytt till molnet med strukturerade vägledning och ett konsekvent, repeterbara tillvägagångssätt för att välja en molntjänstleverantör.

Molnet antas inte längre bara teknik beslut. Eftersom checklista krav touch på alla aspekter av en organisation, avser att kalla alla viktiga interna-beslutsfattare – chef och CISO samt juridiska, riskerar tekniker för hantering, inköp och kompatibilitet. Detta ökar effektiviteten för beslutsprocesser och grunden beslut i ljud motivationen, vilket minskar risken för oförutsedda förbi vägspärrar implementeringen.

Dessutom checklistan:

- Visar viktiga diskussionsämnen för beslutsfattare i början av implementeringsprocessen för molnet.

- Stöder omfattande business diskussioner om föreskrifter och organisationens egen mål för sekretess och personligt identifierbar information (PII) datasäkerhet.

- Hjälper organisationer att identifiera eventuella problem som kan påverka cloud-projekt.

- Ger en enhetlig uppsättning frågor, samma villkor, definitioner, mått och mål för varje leverantör, förenklar processen för att jämföra erbjudanden från olika molntjänst-providers.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure-infrastrukturen och programmet säkerhetsvalidering (identifiera)

[Azure operativ säkerhet](https://docs.microsoft.com/azure/security/azure-operational-security) refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure.

![Säkerhetsvalidering (identifiera)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure operativ säkerhet bygger på ett ramverk som innehåller kunskap via en olika funktioner som är unika för Microsoft, inklusive Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center program och djup medvetenhet om hotbild cybersecurity.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft operations management suite(OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) är IT-hanteringslösning för hybridmoln. Används fristående eller om du vill utöka din befintliga System Center-distribution OMS ger maximal flexibilitet och kontroll för molnbaserad hantering av infrastrukturen.

![Microsoft operations management suite(OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Du kan hantera valfri instans i något moln, inklusive lokalt, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrenskraftiga lösningar med OMS. Byggt för molnet första världen erbjuder OMS en ny metod för att hantera ditt företag som är det snabbaste och mest kostnadseffektiva sättet att uppfylla nya utmaningar för företag och hantera nya arbetsbelastningar, program och miljöer i molnet.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakning för OMS genom att samla in data från hanterade resurser i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Den här metoden kan du konsolidera data från olika källor, så kan du kombinera data från Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.

### <a name="azure-security-center"></a>Azure security center

Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Säkerhetstillståndet på resurserna i Azure analyseras i Security Center för upptäckt av eventuella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs.

Exempel på rekommendationer:

- att installera program som kan hitta och ta bort skadlig kod

- Konfigurera nätverkssäkerhetsgrupper och regler för trafiken till virtuella datorer

- att installera brandväggar för webbaserade program för skydd mot angrepp riktade mot webbaserade program

- genomföra alla systemuppdateringar som fattas

- se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar, samlas in, analyseras och integreras automatiskt i Security Center. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

- Angripna virtuella datorer som kommunicerar med kända skadliga IP-adresser

- avancerad skadlig kod upptäckt genom felrapporteringen i Windows

- Brute force-attacker mot virtuella datorer

- säkerhetsaviseringar från integrerade brandväggar och program mot skadlig kod

### <a name="azure-monitor"></a>Övervakare för Azure

[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) innehåller tips och information om specifika typer av resurser. Den erbjuder visualisering, fråga, routning, varningar, automatisk skalning och automation på data från Azure-infrastrukturen (aktivitetsloggen) och varje enskild Azure resurs (diagnostikloggar).

Molnprogram är komplicerade med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet in och körs i ett felfritt tillstånd. Det hjälper dig också att stave ut potentiella problem eller felsöka tidigare viktiga.

![Azure-monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) du kan dessutom använda övervakningsdata och få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Det är viktigt för att identifiera säkerhetsproblem i nätverket och att säkerställa kompatibilitet med din IT-säkerhet och regelverk styrning modellen granskning nätverkssäkerheten. Du kan hämta konfigurerade Nätverkssäkerhetsgruppen och säkerhetsregler samt effektiva säkerhetsregler med säkerhetsgruppen vy. Du kan fastställa de portar som är öppna och ss nätverk säkerhetsproblem med listan över regler som används.

### <a name="network-watcher"></a>Nätverksbevakaren

[Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på en nivå i till och från Azure. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure. Den här tjänsten innehåller paketinsamling, nästa hopp, IP-flöde Kontrollera NSG flödet loggar i gruppvyn säkerhet. Scenariot nivån övervakning innehåller en heltäckande vy av nätverksresurser i stället för enskilda resurs nätverksövervakning.

### <a name="storage-analytics"></a>Lagringsanalys

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan lagra mått som innehåller aggregerade statistik och kapacitet transaktionsdata om begäranden till en storage-tjänst. Transaktioner rapporteras på båda API åtgärden nivå samt på tjänstnivå lagring och kapacitet rapporteras på tjänstnivå lagring. Mätvärdesdata kan användas för att analysera lagringskvoten på tjänsten, diagnostisera problem med begäranden som görs mot lagringstjänsten och för att förbättra prestanda för program som använder en tjänst.

### <a name="application-insights"></a>Programinsikter

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) är en utökningsbar Management-program (APM)-tjänst för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den innehåller kraftfulla analytics verktyg som hjälper dig att diagnostisera problem och för att förstå vad användarna göra med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Den fungerar för appar på en rad olika plattformar, däribland .NET, Node.js och J2EE som finns lokalt eller i molnet. Den kan integreras med devOps-processen och har kopplingspunkter till olika utvecklingsverktyg.

Tjänsten övervakar:

- **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem.

- **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.

- **Undantag** – analysera sammanställda statistik, eller välja specifika instanser och detaljerat stackspårning och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

- **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.

- **AJAX-anrop från webbsidorna** -priser svarstider och fel.

- **Användar- och antal.**

- **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning.

- **Värddiagnostik** från Docker eller Azure.

- **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.

- **Anpassade händelser och mått** du skriva själv i klient- eller koden, att spåra affärshändelser som försäljning, eller vunna.
Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, ett lagringskonto och ett virtuellt nätverk eller en webbapp, en databas, en databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteter, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kan du arbeta med resurserna i din lösning som en grupp.

Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

**Fördelarna med att använda Resource Manager**

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

- Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.

- Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

- Du kan definiera beroenden mellan resurser, så att de distribueras i rätt ordning.

- Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom rollbaserad åtkomstkontroll (RBAC) är inbyggt i hanteringsplattformen.

- Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

- Du kan tydliggöra din organisations fakturering genom att visa kostnaderna för en grupp av resurser som delar samma tagg.

> [!Note]
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du använde tidigare distributionsmodellen och vill att lära dig om ändringarna [förstå Resource Manager distribution och klassisk distribution](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om säkerheten genom att läsa in några av våra djupgående säkerhetsfrågor:

- [Granskning och loggning](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cyberbrott](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Design- och operativ säkerhet](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Kryptering](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitets- och åtkomsthantering](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Nätverkssäkerhet](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Hothantering](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
