---
title: Säker fjärr administration av din gateway i Azure Australien
description: Vägledning om hur du konfigurerar säker fjärr administration inom de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 827dffc1c7544d9373b5f8d4426ea8c448fa25ab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571606"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Säker fjärr administration av din gateway i Azure Australien

Det är viktigt att tillgängligheten och integriteten för alla system som administrativa aktiviteter utförs på ett säkert sätt och som kontrol leras. Administrativa aktiviteter bör utföras från en säker enhet, via en säker anslutning och backas upp av kraftfulla processer för autentisering och auktorisering. Säker fjärr administration säkerställer att endast auktoriserade åtgärder utförs och endast av auktoriserade administratörer.

Den här artikeln innehåller information om hur du implementerar en säker fjärr administrations funktion för ett lättillgängligt Internet-system som finns i Azure och som överensstämmer med den australiska cyberhot Security Centre (ACSC) konsument vägledning och avsikten med ACSC information Säkerhets manuell (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>ACSC-krav (australisk cyberhot Security Centre)

De övergripande säkerhets kraven för samväldet samordnings system definieras i ISM-systemet. För att under lätta samACSCnde entiteter i att tillhandahålla säker [administration har publicerat ACSC Protect: Säker administration](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

Det här dokumentet beskriver vikten av säker administration och föreslår en metod för att implementera en säker administrations miljö. Dokumentet beskriver elementen i en säker administrations lösning på följande sätt:

|Element   |Beskrivning   |
|---|---|
|Privilegie rad åtkomst kontroll   |Att kontrol lera åtkomst till privilegierade konton är en grundläggande säkerhets kontroll som skyddar privilegierade konton från att missbruka. Metoden för åtkomst kontroll omfattar begreppen "minsta behörighet" och "behöver" samt processer och procedurer för att hantera tjänst konton och personal rörelser.   |
|Multifaktorautentisering   |Implementering av ytterligare faktorer för autentisering utöver användar namn och lösen fraser, till exempel fysiska token eller smartkort, kan hjälpa till att skydda kritiska till gångar. Om en angripare komprometterar autentiseringsuppgifter för privilegierade konton, eftersom alla administrativa åtgärder först måste gå igenom någon form av Multi-Factor Authentication, kan följderna minska avsevärt.|
|Privilegierade arbets stationer|Användningen av en känd säker miljö för administrativa uppgifter kan resultera i en mindre risk för att nätverket komprometteras på grund av implementeringen av ytterligare säkerhets kontroller.|
|Loggning och granskning   |Automatisk generering, insamling och analys av säkerhet och administrativa relaterade händelser från arbets stationer, servrar, nätverks enheter och hopp rutor kommer att aktivera identifiering av kompromisser och försök till kompromisser. Automation gör det möjligt för organisationer att svara snabbare, vilket minskar konsekvenserna av en kompromiss.|
|Nätverks segmentering och åtskillnad|Att segmentera ett nätverk i logiska zoner, till exempel olika säkerhets domäner, och ytterligare åtskilja dessa logiska nätverk genom att begränsa de typer av data som flödar från en zon till en annan, begränsar lateral förflyttning. Segmentering förhindrar att en angripare får åtkomst till ytterligare resurser.|
|Hopp rutor|En hopp ruta är en skärpd fjärråtkomstserver som ofta använder Microsofts Fjärrskrivbordstjänster eller SSH-programvara (Secure Shell). Hopp rutor fungerar som en steg-punkt för administratörer som har åtkomst till kritiska system med alla administrativa åtgärder som utförs från den dedikerade värden.|
|

Den här artikeln innehåller en referens arkitektur för hur elementen ovan kan användas för säker administration av system som distribueras i Azure.

## <a name="architecture"></a>Arkitektur

Att tillhandahålla en säker administrations funktion kräver att flera komponenter fungerar tillsammans för att bilda en sammanhängande lösning. I referens arkitekturen som anges mappas komponenterna till elementen som beskrivs i [ACSC skydda: Säker administration](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Azure Secure fjärr administrations arkitektur](media/remote-admin.png)

## <a name="components"></a>Komponenter

Arkitekturen är utformad för att säkerställa att ett privilegierat konto endast beviljas de nödvändiga behörigheterna, identifieras på ett säkert sätt och tillhandahålls sedan enbart åtkomst till administrativa gränssnitt från en auktoriserad enhet och genom säker kommunikation mekanismer som styrs och granskas.

|Lösning| Komponenter|Element|
|---|---|---|
|Säkra enheter |<ul><li>Privilegie rad arbets Station</li><li>Mobil enhet</li><li>Microsoft Intune</li><li>Grupprincip</li><li>Hopp Server/skydds-värd</li><li>JIT-administration (just in Time)</li></ul> |<ul><li>Privilegierade arbets stationer</li><li>Hopp rutor</li></ul>|
|Säker kommunikation |<ul><li>Azure Portal</li><li>Azure VPN Gateway</li><li>Gateway för fjärr skrivbord (RD)</li><li>Nätverks säkerhets grupper (NSG: er)</li></ul> |<ul><li>Nätverks segmentering och åtskillnad</li></ul>|
|Stark autentisering |<ul><li>Domänkontrollant (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Nätverks princip Server (NPS)</li><li>Azure MFA</li></ul> |<ul><li>Multifaktorautentisering</li></ul> |
|Starkt tillstånd |<ul><li>Identitets-och åtkomst hantering (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Villkorad åtkomst</li></ul>|<ul><li>Privilegie rad åtkomst kontroll</li></ul>|
|||

>[!NOTE]
>Mer information om loggnings-och gransknings element finns i artikeln om [Gateway-loggning, granskning och synlighet](gateway-log-audit-visibility.md)

## <a name="administration-workflow"></a>Arbets flöde för administration

Att administrera system som distribueras i Azure är uppdelat i två olika kategorier, administrerar Azure-konfigurationen och administrerar arbets belastningar som distribueras i Azure. Azure-konfigurationen utförs via Azure Portal och arbets belastnings administration slutförs genom administrativa mekanismer som Remote Desktop Protocol (RDP), Secure Shell (SSH) eller för PaaS-funktioner med verktyg som SQL Management Studio.

Att få åtkomst till administration är en process i flera steg som innefattar de komponenter som anges i arkitekturen och som kräver åtkomst till Azure Portal och Azure-konfiguration innan åtkomst kan göras till Azure-arbetsbelastningar.

>[!NOTE]
> Stegen som beskrivs här är den allmänna processen med hjälp av GUI-komponenterna (Graphical User Interface) i Azure. De här stegen kan också utföras med andra gränssnitt, till exempel PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Azure-konfiguration och Azure Portal åtkomst

|Steg |Beskrivning |
|---|---|
|Inloggning för privilegie rad arbets Station |Administratören loggar in på den privilegierade arbets stationen med administratörs behörighet. Grupprincip kontroller förhindrar att icke-administratörskonton autentiseras mot den privilegierade arbets stationen och förhindrar att administrativa konton autentiserar till icke-privilegierade arbets stationer. Microsoft Intune hanterar kompatibiliteten för den privilegierade arbets stationen för att säkerställa att den är uppdaterad med program varu korrigeringar, program mot skadlig kod och andra krav på efterlevnad. |
|Azure Portal inloggning |Administratören öppnar en webbläsare till Azure Portal, som krypteras med hjälp av Transport Layer Security (TLS) och loggar in med administratörs behörighet. Autentiseringsbegäran bearbetas via Azure Active Directory direkt eller genom autentiseringsmekanismer som Active Directory Federation Services (AD FS) (AD FS) eller genom strömnings autentisering. |
|Azure MFA |Azure MFA skickar en autentiseringsbegäran till den registrerade mobila enheten för det privilegierade kontot. Den mobila enheten hanteras av Intune för att säkerställa efterlevnaden av säkerhets kraven. Administratören måste först autentisera den mobila enheten och sedan till Microsoft Authenticator-appen med en PIN-kod eller ett bio metriskt system innan autentiseringsförsök tillåts för Azure MFA. |
|Villkorad åtkomst |Principer för villkorlig åtkomst kontrollerar autentiseringsförsök för att säkerställa att den uppfyller de nödvändiga kraven, till exempel IP-adressen som anslutningen kommer från, grupp medlemskap för det privilegierade kontot och hanterings-och kompatibilitetsstatus för privilegie rad arbets station som rapporteras av Intune. |
|Privileged Identity Management (PIM) |Via Azure Portal kan administratören nu aktivera eller begära aktivering för de privilegierade roller som de har tillstånd till via PIM. PIM ser till att privilegierade konton inte har några stå-administratörs privilegier och att alla begär Anden om privilegie rad åtkomst endast gäller den tid som krävs för att utföra administrativa uppgifter. PIM tillhandahåller även loggning av alla förfrågningar och aktiveringar för gransknings syfte. |
|Identitets- och åtkomsthantering|När det privilegierade kontot har identifierats säkert och roller aktive ras, ges administratören åtkomst till de Azure-prenumerationer och-resurser som de har tilldelats behörigheter till genom identitets-och åtkomst hantering.|
|

När det privilegierade kontot har slutfört stegen för att få administrativ åtkomst till Azure Portal, kan åtkomst till arbets belastningarna konfigureras och administrativa anslutningar kan göras.

### <a name="azure-workload-administration"></a>Azures arbets belastnings administration

|Steg |Beskrivning|
|---|---|
|JIT-åtkomst (just in Time)|För att få åtkomst till virtuella datorer använder administratören JIT för att begära åtkomst till RDP till hopp servern från IP-adressen för fjärrskrivbordsgateway och RDP eller SSH från hopp servern till relevanta virtuella arbets belastnings datorer.|
|Azure VPN Gateway|Administratören skapar nu en VPN-anslutning från punkt-till-plats från sin privilegie rad arbets station till Azure-VPN Gateway, som utför certifikatautentisering för att upprätta anslutningen.|
|RD Gateway|Administratören försöker nu med en RDP-anslutning till hopp servern med den RD Gateway som anges i Anslutning till fjärrskrivbord-konfigurationen. RD Gateway har en privat IP-adress som kan kontaktas via Azure VPN Gateway-anslutningen. Principer på RD Gateway kontrollerar om det privilegierade kontot har behörighet att komma åt den begärda hopp servern. RD Gateway ber administratören om autentiseringsuppgifter och vidarebefordrar autentiseringsbegäran till nätverks princip servern (NPS).|
|Nätverks princip Server (NPS)|NPS tar emot autentiseringsbegäran från Fjärrskrivbordsgateway och validerar användar namnet och lösen ordet mot Active Directory innan en begäran skickas till Azure Active Directory för att utlösa en Azure MFA-autentiseringsbegäran.|
|Azure MFA|Azure MFA skickar en autentiseringsbegäran till den registrerade mobila enheten för det privilegierade kontot. Den mobila enheten hanteras av Intune för att säkerställa efterlevnaden av säkerhets kraven. Administratören måste först autentisera den mobila enheten och sedan till Microsoft Authenticator-appen med en PIN-kod eller ett bio metriskt system innan autentiseringsförsök tillåts för Azure MFA.|
|Hopp Server|När en RDP-anslutning har autentiserats krypteras den med hjälp av Transport Layer Security (TLS) och skickas sedan via den krypterade IPSec-tunneln till Azure-VPN Gateway, via RD Gateway och på hopp servern. Från hopp servern kan administratören nu ha RDP eller SSH till virtuella arbets belastnings datorer som anges i JIT-begäran.|
|

## <a name="general-guidance"></a>Allmänna riktlinjer

När du implementerar de komponenter som anges i den här artikeln gäller följande allmänna rikt linjer:

* Verifiera regionens tillgänglighet för tjänster, och se till att alla data ligger kvar inom godkända platser och distribueras till AU central eller AU Central 2 som första preferens för skyddade arbets belastningar

* Gå till *certifierings rapporten för Azure-ACSC – skyddad 2018* -publikation för att se om det finns certifierings status för enskilda tjänster och utföra själv utvärdering av alla relevanta komponenter som inte ingår i rapporten enligt ACSC-.  *Microsoft Azure vid skyddad*

* Se till att nätverks anslutningen och all nödvändig proxykonfiguration för åtkomst till nödvändiga verifierings komponenter, till exempel Azure AD, ADFS och PTA

* Använd Azure Policy för att övervaka och genomdriva efterlevnad med krav

* Se till att virtuella datorer, särskilt Active Directory-domän kontrollanter, lagras i krypterade lagrings konton och utnyttja Azure Disk Encryption

* Skapa och underhåll kraftfulla identitets-och administratörs behörighets hanterings processer och styrning för att stödja de tekniska kontroller som anges i den här artikeln

|Resource|URL|
|---|---|
|Australiska lagar och principer för regelefterlevnad|[Australiska lagar och principer för regelefterlevnad](https://aka.ms/au-irap)|
|Azure-produkter – australiska regioner och icke-regionala|[Azure-produkter – australiska regioner och icke-regionala](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Strategier för att minimera cyberhot säkerhets incidenter|[Strategier för att minimera cyberhot säkerhets incidenter](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC skydda: Säker administration|[ACSC skydda: Säker administration](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Instruktioner: Integrera fjärrskrivbordsgateway infrastruktur med Network Policy Server (NPS)-tillägget och Azure AD|[Integrera RD Gateway med NPS och Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Vägledning för komponenter

Det här avsnittet innehåller information om syftet med varje komponent och dess roll i den övergripande säkra fjärr administrations arkitekturen. Ytterligare länkar finns för att få åtkomst till användbara resurser, till exempel referens dokumentation, guider och självstudier.

## <a name="secure-devices"></a>Säkra enheter

De fysiska enheter som används av privilegierade användare för att utföra administrativa funktioner är värdefulla mål för skadliga aktörer. Upprätthålla säkerheten och integriteten hos de fysiska enheterna och se till att de är fria från skadlig program vara och skyddar dem mot kompromisser är en viktig del av att tillhandahålla en säker fjärr administrations funktion. Detta omfattar säkerhets konfiguration med hög prioritet enligt vad som anges i ACSCs viktiga åtta strategier för att minimera cyberhot-säkerhetsincidenter som program vit listning, uppdaterings program, program härdning och uppdatering av operativ system. Dessa funktioner måste installeras, konfigureras, granskas, val IDE ras och rapporteras för att säkerställa att enhetens status är kompatibel med organisationens krav.

### <a name="privileged-workstation"></a>Privilegie rad arbets Station

Den privilegierade arbets stationen är en härdad dator som kan användas för att utföra administrativa uppgifter och som endast är tillgänglig för administrativa konton. Den privilegierade arbets stationen bör ha principer och konfiguration på plats för att begränsa den program vara som kan köras, dess åtkomst till nätverks resurser och Internet och autentiseringsuppgifter bör skyddas i händelse av att enheten blir stulen eller komprometteras. |

|Resurser|Länka|
|---|---|
|Översikt över arbets stationer med privilegie rad åtkomst|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|Skydda Privileged Access Reference material|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|
|

### <a name="mobile-device"></a>Mobil enhet

En mobil enhet är större risk för oavsiktlig förlust eller stöld på grund av dess portabilitet och storlek och måste skyddas på lämpligt sätt. Den mobila enheten ger en stark ytterligare faktor för autentisering med möjlighet att genomdriva autentisering för enhets åtkomst, spårning genom plats tjänster, krypterings funktioner och möjlighet att fjärrrensa. När du använder en mobil enhet som ytterligare en autentiseringsnivå för Azure ska enheten konfigureras att använda Microsoft Authenticator-appen med PIN-kod eller bio metrisk autentisering och inte via telefonsamtal eller textmeddelanden.

|Resurser|Länka|
|---|---|
|Autentiseringsmetoder för Azure AD|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Använda appen Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|
|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune är komponenten i Enterprise Mobility + Security som hanterar mobila enheter och appar. Den integreras nära andra komponenter som Azure Active Directory för identitets-och åtkomst kontroll och Azure Information Protection för data skydd. Intune tillhandahåller principer för arbets stationer och mobila enheter för att ställa in krav på efterlevnad för åtkomst till resurser och ger rapporterings-och gransknings funktioner för att få insyn i status för administrativa enheter.

|Resurser|Länka|
|---|---|
|Microsoft Intune dokumentation|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Kom igång med enhetens efterlevnad i Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|
|

### <a name="group-policy"></a>Grupprincip

Grupprincip används för att kontrol lera konfigurationen av operativ system och program. Säkerhets principer styr inställningarna för autentisering, auktorisering och granskning i ett system. Grupprincip används för att torka den privilegierade arbets stationen, skydda administrativa autentiseringsuppgifter och begränsa icke-privilegierade konton från åtkomst till privilegierade enheter.

|Resurser|Länka|
|---|---|
|Inställningen Tillåt inloggning lokalt grupprincip|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|
|

### <a name="jump-server--bastion-host"></a>Hopp Server/skydds-värd

Hopp servern/skydds-värden är en centraliserad punkt för administration. Den har de verktyg som krävs för att utföra administrativa uppgifter, men har även den nätverks åtkomst som krävs för att ansluta till resurser på administrativa portar. Hopp servern är den centrala punkten för administration av arbets belastningar för virtuella datorer i den här artikeln, men den kan också konfigureras som godkänd punkt för att administrera PaaS-funktioner (Platform as a Service), till exempel SQL. Åtkomst till PaaS-funktioner kan begränsas baserat på per tjänst med hjälp av identitets-och nätverks kontroller.

|Resurser|Länka|
|---|---|
|Implementera säkra administrativa värdar|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|
|

### <a name="just-in-time-jit-access"></a>JIT-åtkomst (just in Time)

JIT är en Azure Security Center-funktion som använder nätverks säkerhets grupper (NSG: er) för att blockera åtkomst till administrations protokoll som RDP och SSH på Virtual Machines. Program som finns på Virtual Machines fortsätta att fungera som vanligt, men för att administrativ åtkomst ska kunna erhållas måste den begäras bara beviljas för en angiven tids period. Alla begär Anden loggas för gransknings syfte.

|Resurser |Länka |
|---|---|
|Hantera just-in-Time (JIT)-åtkomst|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Automatisera Azures just-in-Time-åtkomst till virtuella datorer|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|
|

## <a name="secure-communication"></a>Säker kommunikation

Kommunikations trafik för administrations aktiviteter kan innehålla mycket känslig information, till exempel administrativa autentiseringsuppgifter och måste hanteras och skyddas i enlighet med detta. Att tillhandahålla säker kommunikation innebär pålitliga krypterings möjligheter för att förhindra avlyssning och nätverks segmentering och begränsningar som begränsar administrativ trafik till godkända slut punkter och styr lateral förflyttning om ett system komprometteras.

### <a name="azure-portal"></a>Azure Portal

Kommunikation till Azure Portal krypteras med hjälp av Transport Layer Security (TLS) och användningen av Azure Portal har certifierats av ACSC. Samväldet entiteter bör följa rekommendationerna i *ACSC-hand boken* och konfigurera sina webbläsare så att de använder den senaste versionen av TLS och med krypterade algoritmer som stöds.

|Resurser |Länka |
|---|---|
|Översikt över Azure-kryptering – kryptering under överföring|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|
|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Azure-VPN Gateway tillhandahåller en säker krypterad anslutning från den privilegierade arbets stationen till Azure. Azure-VPN Gateway har certifierats av ACSC för att tillhandahålla säker IPSec-kommunikation. Samväldet-entiteter bör konfigurera Azure-VPN Gateway i enlighet med ACSC-hand boken, ACSC certifierings rapport och annan speciell vägledning.

|Resurser |Länka |
|---|---|
|Om punkt-till-plats-anslutningar|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Azure VPN Gateway kryptografisk information|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Konfiguration av Azure-VPN Gateway|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="remote-desktop-rd-gateway"></a>Gateway för fjärr skrivbord (RD)

RD Gateway är en säker mekanism för att kontrol lera och godkänna RDP-anslutningar till system. Det fungerar genom att kapsla in RDP-trafik i HyperText Transfer Protocol Secure (HTTPS) och krypteras med TLS. TLS ger ett extra säkerhets lager för administrativ trafik.

|Resurser |Länka |
|---|---|
|Fjärrskrivbordstjänster arkitektur|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|
|

### <a name="network-security-groups-nsgs"></a>Nätverks säkerhets grupper (NSG: er)

NSG: er fungerar som Access Control listor (ACL: er) för nätverks trafik som anger eller lämnar undernät eller virtuella datorer. NSG: er tillhandahåller nätverks segmentering och ger en mekanism för att styra och begränsa de kommunikations flöden som tillåts mellan system. NSG: er är en kärn komponent i just-in-Time-administration (JIT) för att tillåta eller neka åtkomst till administrations protokoll.

|Resurser |Länka |
|---|---|
|Översikt över Azures säkerhets grupper|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Anvisningar: Planera virtuella nätverk|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
|

## <a name="strong-authentication"></a>Stark autentisering

Att identifiera privilegierade användare på ett säkert sätt innan du beviljar åtkomst till system är en kärn komponent i säker administration. Det måste finnas mekanismer för att skydda de autentiseringsuppgifter som är associerade med ett privilegierat konto och för att förhindra att obehöriga aktörer får åtkomst till system genom personifiering eller stöld av autentiseringsuppgifter.

### <a name="domain-controller-dc"></a>Domänkontrollant (DC)

På en hög nivå är en DOMÄNKONTROLLANT en kopia av den Active Directory databasen som innehåller alla användare, datorer och grupper i en domän. DCs utför autentisering för användare och datorer. DCs i den här arkitekturen är värd för virtuella datorer i Azure och tillhandahåller autentiseringstjänster för privilegierade konton som ansluter till hopp servrar och virtuella arbets belastnings datorer.

|Resurser |Länka |
|---|---|
|Översikt över Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|
|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD är autentiseringstjänsten för Azure. Den innehåller molnet

identiteter och tillhandahåller autentisering och auktorisering för en Azure-miljö. Azure AD kan synkroniserad med Active Directory via Azure AD Connect och kan tillhandahålla federerad autentisering via Active Directory Federation Services (AD FS) (AD FS) och Azure AD Connect. Azure AD är en kärn komponent i säker administration.

|Resurser |Länka |
|---|---|
|Dokumentation om Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Dokumentation om hybrid identitet|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|
|

### <a name="network-policy-server-nps"></a>Nätverks princip Server (NPS)

En NPS är en autentiserings-och princip server som tillhandahåller avancerade processer för autentisering och auktorisering. NPS-servern i den här arkitekturen är avsedd att integrera Azure MFA-autentisering med autentiseringsbegäranden för fjärrskrivbordsgateway. NPS har ett särskilt plugin-program för att stödja integrering med Azure MFA i Azure AD.

|Resurser |Länka |
|---|---|
|Dokumentation om nätverks Policy Server|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|
|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA är en autentiseringstjänst i Azure Active Directory för att aktivera autentiseringsbegäranden utöver ett användar namn och lösen ord för åtkomst till moln resurser som Azure Portal. Azure MFA stöder en mängd autentiseringsmetoder och den här arkitekturen använder Microsoft Authenticator-appen för förbättrad säkerhet och integrering med NPS.

|Resurser |Länka |
|---|---|
|Så här fungerar det: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Anvisningar: Distribuera molnbaserad Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|
|

## <a name="strong-authorisation"></a>Starkt tillstånd

När ett privilegierat konto har identifierats på ett säkert sätt kan det beviljas åtkomst till resurser. Auktorisering styr och hanterar de privilegier som är kopplade till ett särskilt konto. Starka godkännande processer överensstämmer med ACSC: s viktigaste åtta strategi för att minimera cyberhot-säkerhetsincidenter för att begränsa administrativa privilegier.

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Åtkomst för att utföra privilegierade åtgärder i Azure baseras på roller som tilldelats kontot. Azure innehåller en omfattande och detaljerad uppsättning roller med vissa behörigheter för att utföra vissa uppgifter. Dessa roller kan beviljas på flera nivåer, till exempel en prenumeration eller resurs grupp. Roll tilldelning och behörighets hantering baseras på konton och grupper i Azure Active Directory och hanteras via Access Control (IAM) i Azure.

|Resurser |Länka |
|---|---|
|Azure-roll baserad Access Control|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|Förstå roll definitioner|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|
|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM är en Azure Active Directory-komponent som styr åtkomst till privilegierade roller. Privilegierade konton kräver inte permanent eller ständig privilegie rad åtkomst, men kan i stället ges möjlighet att begära privilegie rad åtkomst under en viss tids period för att kunna slutföra privilegierade aktiviteter. PIM ger ytterligare kontroller för att underhålla och begränsa privilegie rad åtkomst samt loggning och granskning för att spåra instanser av privilegie rad användning.

|Resurser |Länka |
|---|---|
|Dokumentation om Privileged Identity Management (PIM)|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Börja använda PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|
|

### <a name="conditional-access"></a>Villkorlig åtkomst

Villkorlig åtkomst är en komponent i Azure Active Directory som tillåter eller nekar åtkomst till resurser baserat på villkor. Dessa villkor kan vara nätverks plats baserad, enhets typ, kompatibilitetsstatus, grupp medlemskap och mer. Villkorlig åtkomst används för att genomdriva MFA, enhets hantering och efterlevnad genom Intune och grupp medlemskap i administrativa konton.

|Resurser |Länka |
|---|---|
|Dokumentation för villkorsstyrd åtkomst|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Anvisningar: Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|
|

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [Gateway intränger trafik hantering och kontroll](gateway-ingress-traffic.md) för att se mer information om hur du styr trafik flöden genom dina gateway-komponenter i Azure.
