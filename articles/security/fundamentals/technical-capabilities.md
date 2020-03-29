---
title: Tekniska säkerhetsfunktioner i Azure – Microsoft Azure
description: Introduktion till säkerhetstjänster i Azure som hjälper dig att skydda data, resurser och program i molnet.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845341"
---
# <a name="azure-security-technical-capabilities"></a>Tekniska funktioner för Azure-säkerhet
Den här artikeln innehåller en introduktion till säkerhetstjänster i Azure som hjälper dig att skydda dina data, resurser och program i molnet och uppfylla ditt företags säkerhetsbehov.

## <a name="azure-platform"></a>Azure-plattformen

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) är en molnplattform som består av infrastruktur- och applikationstjänster, med integrerade datatjänster och avancerade analysverktyg och utvecklartjänster som finns i Microsofts offentliga molndatacenter. Kunder använder Azure för många olika kapaciteter och scenarier, från grundläggande beräkning, nätverk och lagring, till mobila och webbapptjänster, till fullständiga molnscenarier som Sakernas Internet, och kan användas med tekniker med öppen källkod och distribueras som hybridmoln eller finns i en kunds datacenter. Azure tillhandahåller molnteknik som byggstenar för att hjälpa företag att spara kostnader, förnya snabbt och hantera system proaktivt. När du bygger på eller migrerar IT-resurser till en molnleverantör förlitar du dig på organisationens förmåga att skydda dina program och data med de tjänster och kontroller de tillhandahåller för att hantera säkerheten för dina molnbaserade tillgångar.

Microsoft Azure är den enda molndatorleverantören som erbjuder en säker, konsekvent programplattform och infrastruktur som en tjänst för team att arbeta inom sina olika molnkunskaper och nivåer av projektkomplexitet, med integrerade datatjänster och analyser som avslöjar information från data var de än finns, på både Microsofts och andra plattformar, öppna ramverk och verktyg, vilket ger val för att integrera molnet med lokala och distribuera Azure-molntjänster inom lokala datacenter. Som en del av Microsoft Trusted Cloud förlitar sig kunderna på Azure för branschledande säkerhet, tillförlitlighet, efterlevnad, sekretess och det stora nätverket av personer, partner och processer för att stödja organisationer i molnet.

Med Microsoft Azure kan du:

- Påskynda innovation med molnet.

- Power affärsbeslut & appar med insikter.

- Bygg fritt och distribuera var som helst.

- Skydda deras affärer.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Säkerhetstekniska möjligheter att fullgöra ditt ansvar

Microsoft Azure tillhandahåller tjänster som hjälper dig att uppfylla dina säkerhets-, sekretess- och efterlevnadsbehov. Följande bild hjälper till att förklara olika Azure-tjänster som är tillgängliga för dig att skapa en säker och kompatibel programinfrastruktur baserat på branschstandarder.

![Tillgängliga tekniska säkerhetsfunktioner- Stor bild](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Hantera och kontrollera identitet och användaråtkomst

Azure hjälper dig att skydda affärsinformation och personlig information genom att du kan hantera användaridentiteter och autentiseringsuppgifter och kontrollera åtkomsten.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsofts identitets- och åtkomsthanteringslösningar hjälper IT att skydda åtkomsten till program och resurser i företagets datacenter och in i molnet, vilket möjliggör ytterligare valideringsnivåer, till exempel multifaktorautentisering och villkorlig åtkomst Politik. Övervakning av misstänkt aktivitet genom avancerad säkerhetsrapportering, -granskning och -avisering hjälper till att minska potentiella säkerhetsproblem. [Azure Active Directory Premium](../../active-directory/active-directory-whatis.md) ger enkel inloggning till tusentals molnappar och åtkomst till webbappar som du kör lokalt.

Säkerhetsfördelar med Azure Active Directory (Azure AD) inkluderar möjligheten att:

- Skapa och hantera en enda identitet för varje användare i hybridföretaget och synkronisera användare, grupper och enheter.

- Ge enkel inloggning till dina program, inklusive tusentals förintegrerade SaaS-appar.

- Skydda programåtkomsten genom att tillämpa regelbaserad multifaktorautentisering får både lokala program och molnprogram.

- Etablera säker fjärråtkomst till lokala webbprogram via Azure AD Application Proxy.

[Azure Active Directory-portalen](https://aad.portal.azure.com/) är tillgänglig som en del av Azure-portalen. Från den här instrumentpanelen kan du få en översikt över tillståndet i din organisation och enkelt hantera katalogen, användarna eller programåtkomsten.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Följande är grundläggande Azure identity management-funktioner:

- Enkel inloggning

- Multi-Factor Authentication

- Säkerhetsövervakning, aviseringar och maskininlärningsbaserade rapporter

- Hantering av konsumentidentitet och åtkomst

- Enhetsregistrering

- Privileged Identity Management

- Identitetsskydd

#### <a name="single-sign-on"></a>Enkel inloggning

[Enkel inloggning (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) innebär att kunna komma åt alla program och resurser som du behöver göra affärer, genom att logga in endast en gång med ett enda användarkonto. När du har loggat in kan du komma åt alla program du behöver utan att behöva autentisera (till exempel skriva ett lösenord) en andra gång.

Många organisationer förlitar sig på saaS-program (Software as a Service) som Office 365, Box och Salesforce för slutanvändarens produktivitet. Historiskt sett måste IT-personal skapa och uppdatera användarkonton individuellt i varje SaaS-program, och användarna var tvungna att komma ihåg ett lösenord för varje SaaS-program.

[Azure AD utökar lokal Active Directory till molnet,](../../active-directory/manage-apps/what-is-single-sign-on.md)vilket gör det möjligt för användare att använda sitt primära organisationskonto för att inte bara logga in på sina domänanslutna enheter och företagsresurser, utan även alla webb- och SaaS-program som behövs för jobbet.

Inte bara behöver användarna inte hantera flera uppsättningar användarnamn och lösenord, kan programåtkomst automatiskt etableras eller avetablerings baserat på organisationsgrupper och deras status som anställd. [Azure AD introducerar säkerhets- och åtkomststyrningskontroller](../../active-directory/active-directory-enterprise-apps-manage-sso.md) som gör att du kan hantera användarnas åtkomst centralt för SaaS-program.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA)](../../active-directory/authentication/multi-factor-authentication.md) är en autentiseringsmetod som kräver användning av mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager i användarloggningar och transaktioner. [MFA hjälper till](../../active-directory/authentication/concept-mfa-howitworks.md) att skydda åtkomsten till data och program samtidigt som användarnas efterfrågan på en enkel inloggningsprocess tillgodoses. Den ger stark autentisering via en rad verifieringsalternativ – telefonsamtal, sms eller mobilappsmeddelanden eller verifieringskod och OAuth-token från tredje part.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, aviseringar och maskininlärningsbaserade rapporter

Säkerhetsövervakning och aviseringar och maskininlärningsbaserade rapporter som identifierar inkonsekventa åtkomstmönster kan hjälpa dig att skydda ditt företag. Du kan använda Azure Active Directorys åtkomst- och användningsrapporter för att få insyn i integriteten och säkerheten i organisationens katalog. Med den här informationen kan en katalogadministratör bättre avgöra var eventuella säkerhetsrisker kan ligga så att de kan planera för att minska dessa risker på lämpligt sätt.

I Azure-portalen eller via [Azure Active Directory-portalen](https://aad.portal.azure.com/)kategoriseras [rapporter](../../active-directory/active-directory-reporting-azure-portal.md) på följande sätt:

- Avvikelserapporter – innehåller inloggningshändelser som vi fann vara avvikande. Vårt mål är att göra dig medveten om sådan aktivitet och göra det möjligt för dig att kunna avgöra om en händelse är misstänkt.

- Integrerade programrapporter – ge insikter om hur molnprogram används i din organisation. Azure Active Directory erbjuder integrering med tusentals molnprogram.

- Felrapporter – indikerar fel som kan uppstå vid etablering av konton till externa program.

- Användarspecifika rapporter – visa enhet och logga in aktivitetsdata för en viss användare.

- Aktivitetsloggar – innehåller en post över alla granskade händelser under de senaste 24 timmarna, de senaste 7 dagarna eller de senaste 30 dagarna och gruppaktivitetsändringar samt lösenordsåterställning och registreringsaktivitet.

#### <a name="consumer-identity-and-access-management"></a>Hantering av konsumentidentitet och åtkomst

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en mycket tillgänglig, global identitetshanteringstjänst för konsumentinriktade program som skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Konsumenterna kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

Tidigare skulle programutvecklare som ville [registrera sig och logga in konsumenter](../../active-directory-b2c/overview.md) i sina program ha skrivit sin egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure Active Directory B2C erbjuder din organisation ett bättre sätt att integrera hantering av konsumentidentiteter i program med hjälp av en säker, standardbaserad plattform och en stor uppsättning utökningsbara principer.

När du använder Azure Active Directory B2C kan dina konsumenter registrera sig för dina program genom att använda sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord).

#### <a name="device-registration"></a>Enhetsregistrering

[Azure AD-enhetsregistrering](../../active-directory/devices/overview.md) är grunden för [enhetsbaserade scenarier för villkorlig åtkomst.](../../active-directory/devices/overview.md) När en enhet har registrerats förser Azure AD-enhetsregistrering enheten med en identitet som används för att autentisera enheten när användaren loggar in. Den autentiserade enheten och enhetens attribut kan sedan användas för att tillämpa principer för villkorlig åtkomst för program som finns i molnet och lokalt.

I kombination med en [MDM-lösning (Mobile Device Management),](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) till exempel Intune, uppdateras enhetsattributen i Azure Active Directory med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som framtvingar åtkomst från enheter för att uppfylla dina standarder för säkerhet och efterlevnad.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Med Azure Active Directory (AD) Privilegierad identitetshantering](../../active-directory/privileged-identity-management/pim-configure.md) kan du hantera, kontrollera och övervaka dina privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.

Ibland måste användare utföra privilegierade åtgärder i Azure- eller Office 365-resurser eller andra SaaS-appar. Detta innebär ofta att organisationer måste ge dem permanent privilegierad åtkomst i Azure AD. Detta är en växande säkerhetsrisk för molnbaserade resurser eftersom organisationer inte kan övervaka vad dessa användare gör med sina administratörsbehörighet. Om ett användarkonto med privilegierad åtkomst äventyras kan dessutom en överträdelse påverka deras totala molnsäkerhet. Azure AD Privileged Identity Management hjälper till att lösa den här risken.

Med Azure AD-privilegierad identitetshantering kan du:

- Se vilka användare som är Azure AD-administratörer

- Aktivera administrativ åtkomst på begäran till Microsoft Online Services som Office 365 och Intune

- Hämta rapporter om administratörsåtkomsthistorik och ändringar i administratörstilldelningar

- Få aviseringar om åtkomst till en privilegierad roll

#### <a name="identity-protection"></a>Identitetsskydd

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) är en säkerhetstjänst som ger en konsoliderad vy över riskidentifieringar och potentiella sårbarheter som påverkar organisationens identiteter. Identity Protection använder befintliga Azure Active Directorys avvikelseidentifieringsfunktioner (tillgängliga via Azure AD:s avvikande aktivitetsrapporter) och introducerar nya riskidentifieringstyper som kan identifiera avvikelser i realtid.

## <a name="secure-resource-access"></a>Säker åtkomst till resurser

Åtkomstkontroll i Azure startar från ett faktureringsperspektiv. Ägaren till ett Azure-konto, som nås genom att besöka [Azure Account Center](https://account.windowsazure.com/subscriptions), är kontoadministratören (AA). Prenumerationer är en behållare för fakturering, men de fungerar också som en säkerhetsgräns: varje prenumeration har en tjänstadministratör (SA) som kan lägga till, ta bort och ändra Azure-resurser i den prenumerationen med hjälp av Azure-portalen. Standardasa för en ny prenumeration är AA, men AA kan ändra SA i Azure Account Center.

![Säker resursåtkomst i Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Prenumerationer har också en koppling till en katalog. Katalogen definierar en uppsättning användare. Dessa kan vara användare från arbetet eller skolan som skapade katalogen, eller de kan vara externa användare (det vill ha Microsoft-konton). Prenumerationer är tillgängliga för en delmängd av de kataloganvändare som har tilldelats som antingen tjänstadministratör (SA) eller co-administrator (CA). Det enda undantaget är att Microsoft-konton (tidigare Windows Live ID) av äldre skäl kan tilldelas som SA eller CERTIFIKAT utan att finnas i katalogen.

Säkerhetsinriktade företag bör fokusera på att ge medarbetarna de exakta behörigheter de behöver. För många behörigheter kan exponera ett konto för angripare. För få behörigheter innebär att anställda inte kan få sitt arbete gjort effektivt. [Azure Role-Based Access Control (RBAC)](../../role-based-access-control/overview.md) hjälper till att lösa problemet genom att erbjuda finkornig åtkomsthantering för Azure.

![Säker resursåtkomst](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete. I stället för att ge alla obegränsade behörigheter i din Azure-prenumeration eller dina Azure-resurser kan du bara tillåta vissa åtgärder. Använd till exempel RBAC för att låta en anställd hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration.

![Säker resursåtkomst i Azure (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Datasäkerhet och kryptering

En av nycklarna till dataskydd i molnet är att stå för möjliga tillstånd där dina data kan inträffa och vilka kontroller som är tillgängliga för det tillståndet. För Azure-datasäkerhet och kryptering bästa praxis rekommendationerna vara runt följande datas tillstånd.

- I vila: Detta inkluderar alla informationslagringsobjekt, behållare och typer som finns statiskt på fysiska medier, oavsett om det är magnetisk eller optisk disk.

- Under överföring: När data överförs mellan komponenter, platser eller program, till exempel över nätverket, över en servicebuss (från lokal till moln och vice versa, inklusive hybridanslutningar som ExpressRoute) eller under en indata-/utdataprocess, är det tänkt som i rörelse.

### <a name="encryption-at-rest"></a>Vilande kryptering

Gör något av följande för att uppnå kryptering i vila:

Stöd minst en av de rekommenderade krypteringsmodeller som beskrivs i följande tabell för att kryptera data.

| Krypteringsmodeller |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Serverkryptering | Serverkryptering | Serverkryptering | Kryptering av klient
| Kryptering på serversidan med tjänsthanterade nycklar | Kryptering på serversidan med kundhanterade nycklar i Azure Key Vault | Kryptering på serversidan med lokala kundhanterade nycklar |
| • Azure Resource Providers utför krypterings- och dekrypteringsåtgärder <br> • Microsoft hanterar nycklarna <br>• Fullständig molnfunktionalitet | • Azure Resource Providers utför krypterings- och dekrypteringsåtgärder<br>• Kunden styr nycklar via Azure Key Vault<br>• Fullständig molnfunktionalitet | • Azure Resource Providers utför krypterings- och dekrypteringsåtgärder <br>• Kunden kontrollerar nycklar Lokalt <br> • Fullständig molnfunktionalitet| • Azure-tjänster kan inte se dekrypterade data <br>• Kunderna håller nycklar lokalt (eller i andra säkra butiker). Nycklar är inte tillgängliga för Azure-tjänster <br>• Minskad molnfunktionalitet|

### <a name="enabling-encryption-at-rest"></a>Aktivera kryptering i vila

**Identifiera alla platser som lagrar data**

Målet med kryptering i vila är att kryptera alla data. Om du gör det elimineras risken för att viktiga data eller alla kvarstående platser saknas. Räkna upp alla data som lagras av ditt program.

> [!Note]
> Inte bara "programdata" eller "PII" utan alla uppgifter som rör program, inklusive kontometadata (prenumerationsmappningar, kontraktsinformation, PII).

Fundera över vilka butiker du använder för att lagra data. Ett exempel:

- Extern lagring (till exempel SQL Azure, Document DB, HDInsights, Data Lake, etc.)

- Tillfällig lagring (alla lokala cacheminnen som innehåller klientdata)

- Minnesminnescache (kan placeras i sidfilen.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Utnyttja det befintliga krypteringsstödet i vila i Azure

Använd det befintliga krypteringsstödet i vila för varje butik som du använder.

- Azure Storage: Se Azure Storage Service Encryption for Data at Rest , se [Azure Storage Service Encryption for Data at Rest](../../storage/common/storage-service-encryption.md),

- SQL Azure: Se [Transparent datakryptering (TDE), SQL alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx)

- VM & Lokal disklagring[(Azure Disk Encryption)](../azure-security-disk-encryption-overview.md)

För vm och lokal disklagring använder Azure Disk Encryption där det stöds:

#### <a name="iaas"></a>IaaS

Tjänster med virtuella IaaS-datorer (Windows eller Linux) bör använda [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) för att kryptera volymer som innehåller kunddata.

#### <a name="paas-v2"></a>PaaS v2

Tjänster som körs på PaaS v2 med Service Fabric kan använda Azure-diskkryptering för Virtual Machine Scale Set [VMSS] för att kryptera sina Virtuella PaaS v2-datorer.

#### <a name="paas-v1"></a>PaaS v1

Azure Disk Encryption stöds för närvarande inte på PaaS v1. Därför måste du använda kryptering på programnivå för att kryptera beständiga data i vila.  Detta inkluderar, men är inte begränsat till, programdata, temporära filer, loggar och kraschdumpar.

De flesta tjänster bör försöka utnyttja krypteringen av en lagringsresursleverantör. Vissa tjänster måste göra explicit kryptering, till exempel alla kvarstående nyckelmaterial (certifikat, rot / huvudnycklar) måste lagras i Key Vault.

Om du stöder kryptering på servicesidan med kundhanterade nycklar måste det finnas ett sätt för kunden att få nyckeln till oss. Det sätt som stöds och rekommenderas att göra det genom att integrera med Azure Key Vault (AKV). I det här fallet kan kunder lägga till och hantera sina nycklar i Azure Key Vault. En kund kan lära sig att använda AKV via [Komma igång med Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Om du vill integrera med Azure Key Vault lägger du till kod för att begära en nyckel från AKV när det behövs för dekryptering.

- Se [Azure Key Vault – Steg](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) för steg för information om hur du integrerar med AKV.

Om du stöder kundhanterade nycklar måste du tillhandahålla en användarupplevelse för kunden för att ange vilket Key Vault (eller Key Vault URI) som ska användas.

Eftersom Kryptering i vila innebär kryptering av värd-, infrastruktur- och klientdata kan förlust av nycklar på grund av systemfel eller skadlig aktivitet innebära att alla krypterade data går förlorade. Det är därför viktigt att din solution för kryptering vid vila har en omfattande katastrofåterställningshistoria som är motståndskraftig mot systemfel och skadlig aktivitet.

Tjänster som implementerar Kryptering i vila är vanligtvis fortfarande känsliga för krypteringsnycklarna eller data som lämnas okrypterade på värdenheten (till exempel i sidfilen för värdoperativsystemet.) Därför måste tjänsterna se till att värdvolymen för sina tjänster är krypterad. För att underlätta det här beräkningsteamet har aktiverat distributionen av värdkryptering, som använder [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP och tillägg till DCM-tjänsten och agenten för att kryptera värdvolymen.

De flesta tjänster implementeras på vanliga virtuella Azure-datorer. Sådana tjänster bör hämta [värdkryptering](../azure-security-disk-encryption-overview.md) automatiskt när Compute aktiverar den. För tjänster som körs i Compute-hanterade kluster aktiveras värdkryptering automatiskt när Windows Server 2016 distribueras.

### <a name="encryption-in-transit"></a>Kryptering under överföring

Att skydda data under överföring bör vara en viktig del av din dataskyddsstrategi. Eftersom data flyttas fram och tillbaka från många platser är den allmänna rekommendationen att du alltid använder SSL/TLS-protokoll för att utbyta data över olika platser. Under vissa omständigheter kanske du vill isolera hela kommunikationskanalen mellan din lokala infrastruktur och molninfrastruktur med hjälp av ett virtuellt privat nätverk (VPN).

För data som flyttas mellan din lokala infrastruktur och Azure bör du överväga lämpliga skyddsåtgärder som HTTPS eller VPN.

För organisationer som behöver skydda åtkomst från flera arbetsstationer som finns lokalt till Azure använder du [Azure-plats-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

För organisationer som behöver skydda åtkomst från en arbetsstation som finns lokalt till Azure använder du [Point-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Större datauppsättningar kan flyttas över en dedikerad WAN-länk med hög hastighet, till exempel [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om du väljer att använda ExpressRoute kan du också kryptera data på programnivå med [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för extra skydd.

Om du interagerar med Azure Storage via Azure Portal sker alla transaktioner via HTTPS. [REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) för lagring via HTTPS kan också användas för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och Azure [SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som misslyckas med att skydda data i transit är mer mottagliga för [man-in-the-middle-attacker,](https://technet.microsoft.com/library/gg195821.aspx) [avlyssning](https://technet.microsoft.com/library/gg195641.aspx)och session kapning. Sådana attacker kan vara ett första steg i att få åtkomst till konfidentiella data.

Du kan läsa mer om Azure VPN-alternativet genom att läsa artikeln [Planering och design för VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Framtvinga datakryptering på filnivå

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) använder principer för kryptering, identitet och auktorisering för att skydda dina filer och e-post. Azure RMS fungerar på flera enheter – telefoner, surfplattor och datorer genom att skydda både inom organisationen och utanför organisationen. Den här funktionen är möjlig eftersom Azure RMS lägger till en skyddsnivå som finns kvar med data, även när den lämnar organisationens gränser.

När du använder Azure RMS för att skydda dina filer använder du kryptering av branschstandard med fullt stöd för [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). När du använder Azure RMS för dataskydd har du försäkran om att skyddet stannar kvar i filen, även om det kopieras till lagring som inte kontrolleras av IT, till exempel en molnlagringstjänst. Detsamma gäller för filer som delas via e-post, filen är skyddad som en bilaga till ett e-postmeddelande, med instruktioner om hur du öppnar den skyddade bilagan.
När du planerar för Azure RMS-implementering rekommenderar vi följande:

- Installera [RMS-delningsappen](https://technet.microsoft.com/library/dn339006.aspx). Den här appen integreras med Office-program genom att installera ett Office-tillägg så att användarna enkelt kan skydda filer direkt.

- Konfigurera program och tjänster för att stödja Azure RMS

- Skapa [anpassade mallar](https://technet.microsoft.com/library/dn642472.aspx) som återspeglar dina affärsbehov. Till exempel: en mall för topphemliga data som ska tillämpas i alla topphemliga relaterade e-postmeddelanden.

Organisationer som är svaga på [dataklassificering](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och filskydd kan vara mer mottagliga för dataläckage. Utan rätt filskydd kan organisationer inte få affärsinsikter, övervaka missbruk och förhindra skadlig åtkomst till filer.

> [!Note]
> Du kan läsa mer om Azure RMS genom att läsa artikeln [Komma igång med Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application"></a>Säkra din ansökan
Medan Azure ansvarar för att skydda infrastrukturen och plattformen som ditt program körs på, är det ditt ansvar att skydda själva programmet. Med andra ord måste du utveckla, distribuera och hantera programkoden och innehållet på ett säkert sätt. Utan detta kan din programkod eller ditt innehåll fortfarande vara sårbart för hot.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
[Brandvägg för webbprogram (WAF)](../../application-gateway/waf-overview.md) är en funktion i [Application Gateway](../../application-gateway/overview.md) som ger centraliserat skydd av dina webbprogram från vanliga kryphål och sårbarheter.

Brandväggen använder regler från [OWASP Core Rule Sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Några vanliga säkerhetshot som brandväggen för webbaserade program skyddar mot:

- Skydd mot SQL-inmatning

- Skydd mot skriptkörning över flera webbplatser

- Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

- Skydd mot åtgärder som inte följer HTTP-protokollet

- Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

- Skydd mot robotar, crawlers och skannrar

- Identifiering av vanliga felkonfigurationer av programmet (det vill än Apache, IIS osv.)

> [!Note]
> För en mer detaljerad lista över regler och deras skydd se följande [Core regeluppsättningar:](../../application-gateway/waf-overview.md)

Azure innehåller också flera lättanvända funktioner för att skydda både inkommande och utgående trafik för din app. Azure hjälper också kunder att skydda sin programkod genom att tillhandahålla externt angivna funktioner för att söka igenom webbprogrammet efter säkerhetsproblem.

- [Konfigurera Azure Active Directory-autentisering för din app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Säker trafik till din app genom att aktivera Transport Layer Security (TLS/SSL) - HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Tvinga all inkommande trafik över HTTPS-anslutning](http://microsoftazurewebsitescheatsheet.info/)

  - [Aktivera strikt transportsäkerhet (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Begränsa åtkomsten till din app via klientens IP-adress](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Begränsa åtkomsten till din app efter klientens beteende – begär frekvens och samtidighet](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Sök igenom din webbappkod efter säkerhetsproblem med hjälp av konservering av aluminiumfoliesäkerhet](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurera TLS-ömsesidig autentisering så att klientcertifikat kräver klientcertifikat för att ansluta till webbappen](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Konfigurera ett klientcertifikat för användning från appen för att på ett säkert sätt ansluta till externa resurser](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Ta bort standardserverhuvuden för att undvika att verktyg tar fingeravtryck på appen](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Anslut din app säkert med resurser i ett privat nätverk med Point-To-Site VPN](../../app-service/web-sites-integrate-with-vnet.md)

- [Anslut appen säkert med resurser i ett privat nätverk med hybridanslutningar](../../app-service/app-service-hybrid-connections.md)

Azure App Service använder samma Antimalware-lösning som används av Azure Cloud Services och Virtual Machines. Om du vill veta mer om detta hänvisas till vår [Antimalware dokumentation](antimalware.md).

## <a name="secure-your-network"></a>Skydda nätverket
Microsoft Azure innehåller en robust nätverksinfrastruktur för att stödja dina krav på program- och tjänstanslutning. Nätverksanslutning är möjlig mellan resurser som finns i Azure, mellan lokala och Azure-värdbaserade resurser och till och från Internet och Azure.

[Med Azure-nätverksinfrastrukturen](../../virtual-machines/windows/infrastructure-example.md) kan du på ett säkert sätt ansluta Azure-resurser till varandra med [virtuella nätverk (VNets).](../../virtual-network/virtual-networks-overview.md) Ett virtuella nätverk är en representation av ditt eget nätverk i molnet. Ett virtuella nätverk är en logisk isolering av Azure-molnnätverket som är dedikerat till din prenumeration. Du kan ansluta virtuella nätverk till dina lokala nätverk.

![Skydda nätverket (skydda)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Om du behöver grundläggande åtkomstkontroll på nätverksnivå (baserat på IP-adress och TCP- eller UDP-protokollen) kan du använda [Nätverkssäkerhetsgrupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md). En NSG (Network Security Group) är en grundläggande tillståndskänslig paketfiltreringsbrandvägg och gör att du kan styra åtkomsten baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple).

Azure-nätverk stöder möjligheten att anpassa routningsbeteendet för nätverkstrafik i dina Virtuella Azure-nätverk. Du kan göra detta genom att konfigurera [användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md) i Azure.

[Tvingad tunnelning](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts initiera en anslutning till enheter på Internet.

Azure stöder dedikerad WAN-länkanslutning till ditt lokala nätverk och ett Virtuellt Azure-nätverk med [ExpressRoute](../../expressroute/expressroute-introduction.md). Länken mellan Azure och din webbplats använder en dedikerad anslutning som inte går över det offentliga Internet. Om ditt Azure-program körs i flera datacenter kan du använda [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) för att dirigera begäranden från användare på ett intelligent sätt över instanser av programmet. Du kan också dirigera trafik till tjänster som inte körs i Azure om de är tillgängliga från Internet.

## <a name="virtual-machine-security"></a>Säkerhet för virtuella datorer

[Med Virtuella Azure-datorer](../../virtual-machines/index.yml) kan du distribuera ett brett utbud av datorlösningar på ett smidigt sätt. Med stöd för Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services kan du distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

Med Azure kan du använda programvara mot [skadlig kod](antimalware.md) från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro och Kaspersky för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtuella datorer är en realtidsskyddsfunktion som hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Microsoft Antimalware ger konfigurerbara aviseringar när kända skadliga eller oönskade program försöker installera sig själv eller köras på dina Azure-system.

[Azure Backup](../../backup/backup-overview.md) är en skalbar lösning som skyddar dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och mänskliga fel kan införa buggar i dina program. Med Azure Backup är dina virtuella datorer som kör Windows och Linux skyddade.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) hjälper till att dirigera replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats går ner.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Se till att du följer reglerna: Checklista för due diligence-molntjänster

Microsoft har utvecklat [checklistan för due diligence-molntjänster](https://aka.ms/cloudchecklist.download) för att hjälpa organisationer att utöva due diligence när de överväger en flytt till molnet. Det ger en struktur för en organisation av alla storlekar och typer – privata företag och organisationer inom den offentliga sektorn, inklusive myndigheter på alla nivåer och ideella organisationer – för att identifiera sina egna mål för prestanda, tjänst, datahantering och styrning och Krav. Detta gör det möjligt för dem att jämföra erbjudanden från olika molntjänstleverantörer, vilket i slutändan utgör grunden för ett molntjänstavtal.

Checklistan innehåller ett ramverk som justerar sats för sats med en ny internationell standard för molntjänstavtal, ISO/IEC 19086. Den här standarden erbjuder en enhetlig uppsättning överväganden för organisationer som hjälper dem att fatta beslut om molnanvändning och skapa en gemensam grund för att jämföra molntjänsterbjudanden.

Checklistan främjar en grundligt granskad flytt till molnet, vilket ger strukturerad vägledning och en konsekvent, repeterbar metod för att välja en molntjänstleverantör.

Cloud antagande är inte längre bara ett teknikbeslut. Eftersom checklistekrav berör alla aspekter av en organisation, tjänar de till att sammankalla alla viktiga interna beslutsfattare – CIO och CISO samt juridiska, riskhantering, upphandlingar och efterlevnadsexperter. Detta ökar effektiviteten i beslutsprocessen och markbeslut i sunda resonemang, vilket minskar sannolikheten för oförutsedda vägspärrar att antas.

Dessutom checklista:

- Exponerar viktiga diskussionsämnen för beslutsfattare i början av molnanvändningsprocessen.

- Stöder grundliga affärsdiskussioner om regler och organisationens egna mål för sekretess, personligt identifierbar information (PII) och datasäkerhet.

- Hjälper organisationer att identifiera eventuella problem som kan påverka ett molnprojekt.

- Innehåller en konsekvent uppsättning frågor, med samma termer, definitioner, mått och slutprodukter för varje leverantör, för att förenkla processen med att jämföra erbjudanden från olika molntjänstleverantörer.

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure-infrastruktur och programsäkerhetsverifiering

[Azure Operational Security](operational-security.md) refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda deras data, program och andra resurser i Microsoft Azure.

![säkerhetsvalidering (identifiera)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure Operational Security bygger på ett ramverk som innehåller den kunskap som erhållits genom olika funktioner som är unika för Microsoft, inklusive Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Centre-programmet och djup medvetenhet om cybersäkerhetshotlandskapet.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure-övervakare

[Azure Monitor](../../azure-monitor/index.yml) är IT-hanteringslösningen för hybridmolnet. Azure Monitor-loggarna används ensamma eller för att utöka din befintliga System Center-distribution och ger dig maximal flexibilitet och kontroll för molnbaserad hantering av din infrastruktur.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Med Azure Monitor kan du hantera alla instanser i alla moln, inklusive lokala, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrerande lösningar. Azure Monitor är byggt för molnet först och erbjuder en ny metod för att hantera ditt företag som är det snabbaste och mest kostnadseffektiva sättet att möta nya affärsutmaningar och hantera nya arbetsbelastningar, program och molnmiljöer.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](https://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakningstjänster genom att samla in data från hanterade resurser till en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.

![Azure Monitor-loggar](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Med den här metoden kan du konsolidera data från en mängd olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.

### <a name="azure-security-center"></a>Azure Security Center

Med hjälp av [Azure Security Center](../../security-center/security-center-intro.md) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Säkerhetstillståndet på resurserna i Azure analyseras i Security Center för upptäckt av eventuella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs.

Exempel på rekommendationer:

- att installera program som kan hitta och ta bort skadlig kod

- Konfigurera nätverkssäkerhetsgrupper och regler för att styra trafik till virtuella datorer

- att installera brandväggar för webbaserade program för skydd mot angrepp riktade mot webbaserade program

- genomföra alla systemuppdateringar som fattas

- se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar, samlas in, analyseras och integreras automatiskt i Security Center. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

- Komprometterade virtuella datorer som kommunicerar med kända skadliga IP-adresser

- avancerad skadlig kod upptäckt genom felrapporteringen i Windows

- Brute force-attacker mot virtuella datorer

- säkerhetsaviseringar från integrerade brandväggar och program mot skadlig kod

### <a name="azure-monitor"></a>Azure-övervakare

[Azure Monitor](../../azure-monitor/overview.md) ger pekare till information om specifika typer av resurser. Den erbjuder visualisering, fråga, routning, aviseringar, automatisk skalning och automatisering på data både från Azure-infrastrukturen (Aktivitetslogg) och varje enskild Azure-resurs (diagnostikloggar).

Molnprogram är komplexa med många rörliga delar. Övervakning innehåller data för att säkerställa att ditt program förblir igång i felfritt tillstånd. Det hjälper dig också att avvärja potentiella problem eller felsöka tidigare.

![Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) Monitor Dessutom kan du använda övervakningsdata för att få djupa insikter om ditt program. Den kunskapen kan hjälpa dig att förbättra programmets prestanda eller underhåll, eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

Granskning av nätverkssäkerheten är avgörande för att identifiera nätverkssårbarheter och säkerställa efterlevnad av din IT-säkerhets- och regelstyrningsmodell. Med säkerhetsgruppsvyn kan du hämta de konfigurerade nätverkssäkerhetsgruppen och säkerhetsreglerna samt de gällande säkerhetsreglerna. Med listan över regler som tillämpas kan du bestämma vilka portar som är öppna och ss-nätverkssäkerhetsproblem.

### <a name="network-watcher"></a>Nätverk watcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Nätverksdiagnostik och visualiseringsverktyg som är tillgängliga med Network Watcher hjälper dig att förstå, diagnostisera och få insikter i nätverket i Azure. Den här tjänsten innehåller paketfångst, nästa hopp, IP-flödesverifiering, säkerhetsgruppvy, NSG-flödesloggar. Övervakning på scenarionivå ger en end-to-end-vy av nätverksresurser i motsats till individuell övervakning av nätverksresurser.

### <a name="storage-analytics"></a>Lagringsanalys

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan lagra mått som innehåller aggregerad transaktionsstatistik och kapacitetsdata om begäranden till en lagringstjänst. Transaktioner rapporteras både på API-åtgärdsnivå och på lagringstjänstnivå, och kapacitet rapporteras på lagringstjänstnivå. Måttdata kan användas för att analysera användning av lagringstjänster, diagnostisera problem med begäranden som görs mot lagringstjänsten och för att förbättra prestanda för program som använder en tjänst.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den innehåller kraftfulla analysverktyg som hjälper dig att diagnostisera problem och förstå vad användarna gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Det fungerar för appar på en mängd olika plattformar, inklusive .NET, Node.js och Java EE, som finns lokalt eller i molnet. Den integreras med din devOps-process och har anslutningspunkter till olika utvecklingsverktyg.

Tjänsten övervakar:

- **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem.

- **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.

- **Undantag** – Analysera aggregerad statistik eller välj specifika instanser och visa detaljerad information om stackspårningen och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

- **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.

- **AJAX samtal från webbsidor** - priser, svarstider och felfrekvens.

- **Antalet användare och träningspass.**

- **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning.

- **Värddiagnostik** från Docker eller Azure.

- **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.

- **Anpassade händelser och mått** som du skriver själv i klient- eller serverkoden för att spåra affärshändelser som sålda objekt eller vunna spel.

Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, ett lagringskonto och ett virtuellt nätverk eller en webbapp, en databas, en databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteter, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. [Med Azure Resource Manager](../../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i din lösning som grupp.

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
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du har använt den tidigare distributionsmodellen och vill lära dig mer om ändringarna läser du [Förstå Distribution av Resurshanteraren och klassisk distribution](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om säkerhet genom att läsa några av våra djupgående säkerhetsfrågor:

- [Granskning och loggning](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cybercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Design och driftsäkerhet](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Kryptering](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitets- och åtkomsthantering](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Nätverkssäkerhet](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Hothantering](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
