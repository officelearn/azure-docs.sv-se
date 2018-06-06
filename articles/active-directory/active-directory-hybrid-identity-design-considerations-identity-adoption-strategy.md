---
title: Identitet hybridutformning - införandestrategin Azure | Microsoft Docs
description: Med villkorlig åtkomstkontroll kontrollerar de särskilda villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet i Azure Active Directory. När dessa villkor är uppfyllda, autentiserade användaren och få tillgång till programmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 4b1fefafb34dcbfdced5c978aa235e56cb7fa513
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801804"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiera en strategi för införandet en hybrid identity
I den här uppgiften definierar du hybrid identity införandestrategin för din hybrididentitetslösning att uppfylla kraven som beskrivs i:

* [Fastställa affärsbehov](active-directory-hybrid-identity-design-considerations-business-needs.md)
* [Ange krav för directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Ange krav för multifaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiera behov affärsstrategi
De första uppgift adresserna fastställa organisationer företag behöver.  Detta kan vara mycket bred och scope krypning kan inträffa om du inte är försiktig.  Enkelhet i början, men komma ihåg att planera för en design som ska anpassas och underlätta förändringen i framtiden.  Oavsett om det är en enkel design eller en extremt komplexa, Azure Active Directory är Microsoft Identity-plattform som stöder Office 365, Microsoft Online Services och molnet medvetna program.

## <a name="define-an-integration-strategy"></a>Definiera en strategi för integrering
Microsoft har tre huvudsakliga integrationsscenarier är molnidentiteter, synkroniserade identiteter och federerade identiteter.  Du bör planera på införandet av en av dessa strategier för integrering.  Den strategi som du väljer kan variera och beslut att välja en kan innehålla vilken typ av användarupplevelse som du vill ge, har du en befintlig infrastruktur och vad är den mest kostnadseffektiva.  

![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Scenarier som definierades i ovan är:

* **Molnet identiteter**: dessa är identiteter som finns enbart i molnet.  När det gäller Azure AD, skulle de finns i Azure AD-katalogen.
* **Synkroniserade**: dessa är identiteter som finns lokalt och i molnet.  Med Azure AD Connect användarna antingen skapas eller kopplas till befintlig Azure AD-konton.  Användarens lösenords-hash synkroniseras från den lokala miljön till molnet i något som kallas lösenords-hash.  När du synkroniserar med är ett villkor att om en användare har inaktiverats i den lokala miljön kan det ta upp till tre timmar för att kontostatus som ska visas i Azure AD.  Detta beror på tidsintervallet för synkronisering.
* **Federerad**: dessa identiteter finns både lokalt och i molnet.  Med Azure AD Connect användarna antingen skapas eller kopplas till befintlig Azure AD-konton.  

> [!NOTE]
> Mer information om alternativ för synkronisering [integrera dina lokala identiteter med Azure Active Directory](connect/active-directory-aadconnect.md).
> 
> 

I följande tabell kan fastställa fördelarna och nackdelarna med vart och ett av följande strategier:

| Strategi | Fördelar | Nackdelar |
| --- | --- | --- |
| **Molnidentiteter** |Enklare att hantera för små företag. <br> Inget att installera lokalt. Ingen ytterligare maskinvara krävs<br>Enkelt inaktiveras om användaren lämnar företaget |Användare behöver logga in vid åtkomst av arbetsbelastningar i molnet <br> Lösenord kan eller kan inte vara samma för molnet och lokala identiteter |
| **Synkroniserade** |Lokala lösenord autentiserar både lokalt och i molnet kataloger <br>Enklare att hantera för små, medelstora eller stora organisationer <br>Användare kan ha enkel inloggning (SSO) för vissa resurser <br> Microsoft önskad metod för synkronisering <br> Enklare att hantera |Vissa kunder kan vara ovilliga att synkronisera sina kataloger med molnet på grund av ett visst företag polis |
| **Federerad** |Användare kan ha enkel inloggning (SSO) <br>Om en användare lämnar avslutas och kontot kan inaktiveras omedelbart och behörighet återkallats<br> Stöd för avancerade scenarier som kan utföras med synkroniserade |Flera steg för att installera och konfigurera <br> Högre Underhåll <br> Kan kräva ytterligare maskinvara för STS-infrastruktur <br> Kan kräva ytterligare maskinvara för att installera federationsservern. Det krävs ytterligare programvara om AD FS används <br> Kräv omfattande inställningar för enkel inloggning <br> Kritisk felpunkt om federationsservern är nere, användare kommer inte att kunna autentisera |

### <a name="client-experience"></a>Klientupplevelse
Den strategi som du använder styr inloggning för användaren.  Följande tabeller ger dig information om sina erfarenheter ska förvänta dig vad användare.  Inte alla federerade identitetsleverantörer kan använda enkel inloggning i samtliga scenarier.

**Ansluten till domänen och privata nätverksprogram**:

|  | Synkroniserade identiteter | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |enkel inloggning, ibland måste du ange organisations-ID |
| Outlook |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Skype för företag (Lync) |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, uppmanas att ange autentiseringsuppgifter för Exchange |
| SkyDrive Pro |Fråga efter autentiseringsuppgifter |Enkel inloggning |
| Office Pro Plus prenumeration |Fråga efter autentiseringsuppgifter |Enkel inloggning |

**Externa eller ej betrodda källor**:

|  | Synkroniserade identiteter | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |Formulärbaserad autentisering |
| Outlook, Skype för företag (Lync) Skydrive Pro, Office-prenumeration |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Exchange ActiveSync |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, uppmanas att ange autentiseringsuppgifter för Exchange |
| Mobilappar |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |

Om du har konstaterat från aktivitet 1 att du har en tredje parts IdP eller är kommer att använda en för att ge federation med Azure AD, måste du vara medveten om följande stöds:

* SAML 2.0-providern som är godkända för SP-Lite profilen har stöd för autentisering till Azure AD och associerade program
* Har stöd för passiv autentisering, vilket underlättar autentisering till OWA, SPO osv.
* Exchange Online-klienter kan användas via den SAML 2.0 förbättrad klienten profil (ECP)

Du måste också vara medveten om vilka funktioner är inte tillgängliga:

* Utan stöd för identitetsfederation-WS-Trust Bryt aktiva klienter
  * Det innebär att inga Lync-klienten, OneDrive-klient, Office-prenumeration, Office Mobile innan Office 2016
* Övergången av Office till passiv autentisering kan användas för att stödja ren SAML 2.0 IdPs, men stöd kommer fortfarande att klienten av klient

> [!NOTE]
> För senaste uppdaterade listan artikeln https://aka.ms/ssoproviders.
> 
> 

## <a name="define-synchronization-strategy"></a>Definiera en strategi för synkronisering
I den här uppgiften definierar du de verktyg som används för att synkronisera organisationens lokala data till molnet och vad du bör använda topologi.  Eftersom de flesta organisationer använder Active Directory, har information om hur du använder Azure AD Connect för att adressera på ovanstående frågor angetts i viss detalj.  Det finns information om hur du använder FIM 2010 R2- eller MIM 2016 för att planera den här strategin för miljöer som inte har Active Directory.  Emellertid framtida versioner av Azure AD Connect har stöd för LDAP-kataloger, så beroende på tidslinjen, den här informationen kan hjälpa få.

### <a name="synchronization-tools"></a>Synkroniseringsverktyg
Åren har har flera synkroniseringsverktyg fanns och används för olika scenarier.  Azure AD Connect är för närvarande gå till verktyg för alla scenarier som stöds.  AAD Sync och DirSync är också fortfarande runt och kan även finnas i din miljö nu. 

> [!NOTE]
> Den senaste informationen om vilka funktioner som stöds av varje verktyg läsa [katalogintegreringsverktyg](active-directory-hybrid-identity-design-considerations-tools-comparison.md) artikel.  
> 
> 

### <a name="supported-topologies"></a>Topologier som stöds
När du definierar en strategi för synkronisering, måste topologin som används bestämmas. Beroende på den information som fastställdes i steg bestämma 2 du vilka topologin är rätt som ska användas. Enkel skog, enkel topologi för Azure AD är de vanligaste och består av en Active Directory-skog och en enda instans av Azure AD.  Det här kommer att användas i en majoritet av scenarier och den förväntade topologin när du använder Azure AD Connect Snabbinstallation som visas i bilden nedan.

![](./media/hybrid-id-design-considerations/single-forest.png) En skog Scenario är det vanligt att små och även stora organisationer har flera skogar som visas i bild 5.

> [!NOTE]
> Mer information om olika lokalt och Azure AD-topologier med Azure AD Connect sync artikeln [topologier för Azure AD Connect](connect/active-directory-aadconnect-topologies.md).
> 
> 

![](./media/hybrid-id-design-considerations/multi-forest.png) 

Scenario med flera skogar

Om detta är fallet och sedan flera skogar enda Azure AD-topologi bör övervägas om följande är sant:

* Användare har bara 1 identitet i alla skogar – unikt identifiera användarna avsnittet nedan beskriver detta i detalj.
* Användaren autentiseras mot den skog som sin identitet finns
* Källfästpunkten (ändras id) och UPN hämtas från den här skogen
* Alla skogar som är tillgängliga för Azure AD Connect – det innebär att den inte behöver vara domän ansluten och kan placeras i en DMZ om detta förenklar detta.
* Användare har en postlåda
* Skogen som är värd för en användares postlåda har bästa data kvalitet för attribut som visas i den Exchange globala adresslistan (GAL)
* Om det inte finns någon postlåda på användare, kan en skog användas att bidra med dessa värden
* Om du har en länkad postlåda sedan finns det också ett annat konto i en annan skog som används för att logga in.

> [!NOTE]
> Objekt som finns i både lokalt och i molnet är ”ansluten” via en unik identifierare. I samband med katalogsynkronisering kallas den unika identifieraren i SourceAnchor. I kontexten för enkel inloggning kallas det för ImmutableId. [Designbegreppen för Azure AD Connect](connect/active-directory-aadconnect-design-concepts.md#sourceanchor) för mer information om användningen av SourceAnchor.
> 
> 

Om detta inte är uppfyllda och du har mer än en aktiv konto eller mer än en postlåda, Välj en Azure AD Connect och ignorera den andra.  Om du har länkat postlådor, men inga andra konto, dessa konton kommer inte att exporteras till Azure AD och användaren är inte en medlem av en grupp.  Detta skiljer sig från hur den var tidigare med DirSync och är avsiktlig för bättre stöd för dessa scenarier med flera skogar. Ett scenario med flera skogar visas i bilden nedan.

![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 

**Flera skogar flera Azure AD-scenarier**

Det rekommenderas att du har bara en katalog i Azure AD för en organisation men stöds den en 1:1-relation förvaras mellan en Azure AD Connect sync-server och Azure AD-katalog.  För varje instans av Azure AD behöver du en installation av Azure AD Connect.  Dessutom Azure AD avsiktligt är isolerad och användare i en instans av Azure AD kommer inte att kunna visa användare i en annan instans.

Det är möjligt och stöds för att ansluta en lokal instans av Active Directory till flera Azure AD-kataloger som visas i bilden nedan:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 

**En skog filtrering scenario**

Följande detta gör det måste vara sant:

* Azure AD Connect sync-servrar måste konfigureras för att filtrera så att de har ett ömsesidigt uteslutande uppsättning objekt.  Detta har gjort, till exempel genom att ange omfång för varje server till en viss domän eller Organisationsenhet.
* En DNS-domän kan bara registreras i en enda Azure AD-katalog därför UPN för användarna i lokalt AD måste använda separata namnområden
* Användare i en instans av Azure AD kommer bara att kunna se användare från deras instans.  De kommer inte att visa användare i andra instanser
* Endast en av Azure AD-kataloger kan du aktivera Exchange hybrid med lokalt AD
* Ömsesidig ensamrätt gäller även för tillbakaskrivning.  Det gör att vissa återskrivning funktioner stöds inte med den här topologin eftersom dessa förutsätter en enda lokal konfiguration.  Det här omfattar:
  * Gruppera återskrivning med standardkonfiguration
  * Tillbakaskrivning av enhet

Följande stöds inte och bör inte väljas som en implementering:

* Det går inte för att ha flera servrar för Azure AD Connect-synkronisering ansluter till samma Azure AD-katalog, även om de är konfigurerade för att synkronisera ömsesidigt uteslutande uppsättning objekt
* Den stöds inte för att synkronisera flera Azure AD-kataloger samma användare. 
* Den stöds inte även om du vill göra en konfigurationsändring om du vill att användare i en Azure AD ska visas som kontakter i en annan Azure AD-katalog. 
* Det är också stöds inte för att ändra Azure AD Connect-synkronisering för att ansluta till flera Azure AD-kataloger.
* Azure AD-kataloger är avsiktligt isolerat. Det finns inte stöd att ändra konfigurationen av Azure AD Connect-synkronisering för att läsa data från en annan Azure AD-katalog i ett försök att skapa en gemensam och enhetlig GAL mellan kataloger. Stöds också för att exportera användare som kontakter till en annan lokal AD med hjälp av Azure AD Connect-synkronisering.

> [!NOTE]
> Om din organisation begränsar datorer i nätverket från att ansluta till Internet, den här artikeln innehåller slutpunkter (FQDN: er, IPv4 och IPv6-adressintervall) att du ska inkludera i din utgående Tillåt listor och Internet Explorer zonen Betrodda platser för klient datorer för att se till att dina datorer kan använda Office 365. Mer information finns [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiera en strategi för multifaktorautentisering
I den här uppgiften definierar du multifaktorautentisering strategin för att använda.  Azure Multi-Factor Authentication finns i två olika versioner.  En är en molnbaserad och den andra är lokalt baserad med Azure MFA-Server.  Baserat på tillämpningen du ovan du kan bestämma vilken lösning som är korrekt för din strategi.  Använd tabellen nedan för att avgöra vilket designalternativ för som bäst uppfyller företagets säkerhetskrav:

Multi-Factor designalternativen:

| Tillgångsinformation för att skydda | MFA i molnet | MFA lokalt |
| --- | --- | --- |
| Microsoft-appar |ja |ja |
| Saas-appar i appgalleriet |ja |ja |
| IIS-program publicerade via Azure AD App Proxy |ja |ja |
| IIS-program som inte publicerats via Azure AD App-Proxy |nej |ja |
| Fjärråtkomst som VPN, Fjärrskrivbordsgateway |nej |ja |

Trots att du kan ha regleras på en lösning för din strategi, behöver du fortfarande använda utvärdering ovan på var användarna finns.  Detta kan orsaka lösning för att ändra.  Använd tabellen nedan för att hjälpa dig att fastställa detta:

| Användarplats | Alternativet föredragna designen |
| --- | --- |
| Azure Active Directory |Flera FactorAuthentication i molnet |
| Azure AD och lokalt AD med federation med AD FS |Både |
| Azure AD och lokala AD med Azure AD Connect inga Lösenordssynkronisering |Både |
| Azure AD och lokala med Azure AD Connect med Lösenordssynkronisering |Både |
| Lokala AD |Multi-Factor Authentication Server |

> [!NOTE]
> Du bör också se till att alternativet multifaktorautentisering design som du har valt stöder funktioner som krävs för din design.  Mer information finns [Multi-Factor säkerhetslösning väljer du](authentication/concept-mfa-whichversion.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Leverantör av Multifaktorautent
Multifaktorautentisering är som standard för globala administratörer som har en Azure Active Directory-klient. Men om du vill utöka multifaktorautentisering till alla användare och/eller vill dina globala administratörer för att kunna dra nytta funktioner, till exempel hanteringsportalen, anpassade helg och rapporter måste sedan du köpa och konfigurera Multi-Factor Authentication-leverantör.

> [!NOTE]
> Du bör också se till att alternativet multifaktorautentisering design som du har valt stöder funktioner som krävs för din design. 
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa kraven på dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

