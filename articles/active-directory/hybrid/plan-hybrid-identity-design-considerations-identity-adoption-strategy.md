---
title: Hybrid identity design - införandestrategin för Azure | Microsoft Docs
description: Med villkorlig åtkomstkontroll kontrollerar de specifika villkor som du väljer när du autentiserar användaren och innan åtkomst ges till programmet i Azure Active Directory. När dessa villkor är uppfyllda, användaren autentiseras och får åtkomst till programmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d64cac3812d8daf8ac34b93c91338e1dfab88a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381999"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiera en hybrid identity införandestrategi
I det här steget definierar du hybrid identity-införandestrategin för din hybrididentitetslösning att uppfylla kraven som beskrivs i:

* [Fastställa affärsbehov](plan-hybrid-identity-design-considerations-business-needs.md)
* [Fastställa katalogsynkroniseringskrav](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Fastställa krav för multifaktorautentisering](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiera din strategi för behov
De första uppgift adresserna avgör organisationer företag behöver.  Detta kan vara mycket bred och omfång krypning kan inträffa om du inte är försiktig.  Förenkla allt i början men komma ihåg att planera för en design som ska hantera och underlätta förändringen i framtiden.  Oavsett om det är en enkel design eller en extremt komplexa, Azure Active Directory är Microsoft Identity-plattformen som har stöd för Office 365, Microsoft Online Services och molnet medvetna program.

## <a name="define-an-integration-strategy"></a>Definiera en strategi för integrering
Microsoft har tre huvudsakliga integrationsscenarier är molnidentiteter, synkroniserade identiteter och federerade identiteter.  Du bör planera på börja använda en av strategierna integration.  Vilken strategi du väljer kan variera och beslut när de väljer en kan innehålla vilken typ av användarupplevelse som du vill ge, har du en befintlig infrastruktur och vad är den mest kostnadseffektiva.  

![](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Scenarier som definierades i bilden ovan är:

* **Molnbaserade identiteter**: det här är identiteter som finns i molnet.  När det gäller Azure AD, skulle de finns i Azure AD-katalogen.
* **Synkroniserade**: det här är identiteter som finns lokalt och i molnet.  Med Azure AD Connect dessa användare antingen skapas eller ansluten med befintliga Azure AD-konton.  Användarens lösenords-hash synkroniseras från den lokala miljön till molnet i något som kallas lösenords-hash.  När du använder synkroniserad är ett villkor att om en användare har inaktiverats i den lokala miljön, kan det ta upp till tre timmar innan den kontostatus som ska visas i Azure AD.  Detta beror på tidsintervallet för synkronisering.
* **Federerad**: dessa identiteter finns både lokalt och i molnet.  Med Azure AD Connect dessa användare antingen skapas eller ansluten med befintliga Azure AD-konton.  

> [!NOTE]
> Mer information om alternativ för synkronisering [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Tabellen nedan hjälper dig att fastställa fördelarna och nackdelarna med vart och ett av följande strategier:

| Strategi | Fördelar | Nackdelar |
| --- | --- | --- |
| **Molnidentiteter** |Enklare att hantera för små organisation. <br> Något att installera den lokala. Ingen ytterligare maskinvara krävs<br>Enkelt inaktiveras om användaren lämnar företaget |Användarna måste logga in vid åtkomst av arbetsbelastningar i molnet <br> Lösenord kanske eller kanske inte är samma för molnet och lokala identiteter |
| **Synkroniserad** |Lokala lösenord autentiserar både lokalt och molnet kataloger <br>Enklare att hantera för små, medelstora eller stora organisationer <br>Användare kan ha enkel inloggning (SSO) för vissa resurser <br> Microsoft önskad metod för synkronisering <br> Enklare att hantera |Vissa kunder kan vara ovilliga att synkronisera sina kataloger med molnet på grund av ett visst företag polis |
| **Federerad** |Användare kan ha enkel inloggning (SSO) <br>Om en användare lämnar avslutas och kontot kan inaktiveras omedelbart och återkalla åtkomsten,<br> Har stöd för avancerade scenarier som inte kan uppnås med synkroniseras |Flera steg för att installera och konfigurera <br> Högre Underhåll <br> Kan kräva ytterligare maskinvara för STS-infrastruktur <br> Kan kräva ytterligare maskinvara för att installera federationsservern. Ytterligare programvara krävs om du använder AD FS <br> Kräv omfattande inställningar för enkel inloggning <br> Kritisk felpunkt om federationsservern är nere, användare kommer inte att kunna autentisera |

### <a name="client-experience"></a>Klientupplevelse
Den strategi som du använder bestämmer inloggningsupplevelse för användare.  I tabellerna nedan ger dig information om vilka användarna kan förvänta dig sina inloggningen ska vara.  Inte alla leverantörer för federerad identitet stöder enkel inloggning i alla scenarier.

**Anslutna till domänen och privata nätverksprogram**:

|  | Synkroniserade identiteter | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |enkel inloggning, ibland måste du tillhandahålla organisations-ID |
| Outlook |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Skype för företag (Lync) |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, uppmanas att ange autentiseringsuppgifter för Exchange |
| OneDrive för företag |Fråga efter autentiseringsuppgifter |Enkel inloggning |
| Office Pro Plus prenumeration |Fråga efter autentiseringsuppgifter |Enkel inloggning |

**Externa eller ej betrodda källor**:

|  | Synkroniserade identiteter | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |Formulärbaserad autentisering |
| Outlook, Skype för företag (Lync), OneDrive för företag, Office-prenumeration |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Exchange ActiveSync |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, uppmanas att ange autentiseringsuppgifter för Exchange |
| Mobilappar |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |

Om du har fastställt från uppgift 1 att du har en tredje parts IDP: N eller är kommer att använda en för att tillhandahålla federation med Azure AD, måste du vara medveten om följande stöds funktioner:

* SAML 2.0-providern som är kompatibel för SP-Lite-profilen har stöd för autentisering till Azure AD och associerade program
* Har stöd för passiv autentisering, vilket underlättar autentisering för OWA, SPO osv.
* Exchange Online-klienter stöds via den SAML 2.0 förbättrad klienten profil (ECP)

Du måste också vara medveten om vilka funktioner är inte tillgängliga:

* Utan stöd för WS-Trust/Federation visar bryta alla aktiva klienter
  * Det innebär att inga Lync-klienten, OneDrive-klient, Office-prenumeration, Office Mobile före Office 2016
* Övergång av Office till passiv autentisering låter dem stöder endast SAML 2.0 IDP: er, men stöd kan fortfarande på basis av klient av klient

> [!NOTE]
> Den mest uppdaterade listan finns i artikeln [kompatibilitetslistan för Azure AD-federation](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definiera en strategi för synkronisering
I det här steget definierar du de verktyg som används för att synkronisera företagets lokala data till molnet och vad du bör använda topologi.  Eftersom de flesta organisationer använder Active Directory, har information om hur du använder Azure AD Connect för att adressera frågorna ovan angetts i viss detalj.  Det finns information om hur du använder FIM 2010 R2- eller MIM 2016 för att planera den här strategin för miljöer som inte har Active Directory.  Dock framtida versioner av Azure AD Connect har stöd för LDAP-kataloger bör därför beroende på din tidslinje, den här informationen kan vara möjlighet att hjälpa dig.

### <a name="synchronization-tools"></a>Synkroniseringsverktyg
Under åren har har flera synkroniseringsverktyg fanns och används för olika scenarier.  Azure AD Connect är för närvarande gå till verktyg för alla scenarier som stöds.  AAD Sync och DirSync är också fortfarande runt och kan även finnas i din miljö nu. 

> [!NOTE]
> Den senaste informationen om vilka funktioner som stöds av varje verktyg, läsa [katalogintegreringsverktyg](plan-hybrid-identity-design-considerations-tools-comparison.md) artikeln.  
> 
> 

### <a name="supported-topologies"></a>Topologier som stöds
När du definierar en strategi för synkronisering måste topologin som används för att identifiera. Beroende på vilken information som bestämdes i steg fastställa 2 du vilken topologi som du bör använda. Enkel skog, enskild Azure AD-topologi är den vanligaste och består av en Active Directory-skog och en enda instans av Azure AD.  Detta kommer att användas i en majoritet av scenarier och är den förväntade topologin när du använder Azure AD Connect Express-installationen, enligt bilden nedan.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png) En skog Scenario är det vanligt för stora och även små organisationer att ha flera skogar, som visas i bild 5.

> [!NOTE]
> Mer information om de olika lokala och Azure AD-topologier med Azure AD Connect sync finns i artikeln [topologier för Azure AD Connect](plan-connect-topologies.md).
> 
> 

![](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenario med flera skogar

Om detta är fallet, och sedan flera skogar enda Azure AD-topologi bör övervägas om följande stämmer:

* Användare har endast 1 identitet i alla skogar – unikt identifierande användare nedan beskriver detta i detalj.
* Användaren autentiseras mot den skog som sin identitet finns
* UPN och Källfästpunkt (oföränderligt id) kommer från den här skogen
* Alla skogar som är tillgängliga för Azure AD Connect – detta innebär att det inte behöver vara domänen ansluten och kan placeras i en DMZ om detta förenklar detta.
* Användare har en postlåda
* Den skog som är värd för en användares postlåda har bästa möjliga datakvalitet för för attribut som är synliga i den Exchange globala adresslistan (GAL)
* Om det finns inga postlåda på användaren, kan sedan en skog användas igen för att bidra med dessa värden
* Om du har en länkad postlåda kan sedan finns det också ett annat konto i en annan skog som används för att logga in.

> [!NOTE]
> Objekt som finns i både lokalt och i molnet är ”ansluten” via en unik identifierare. I samband med katalogsynkronisering kallas den här unika identifieraren i SourceAnchor. I kontexten för enkel inloggning kallas detta för ImmutableId. [Designbegrepp för Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) mer information om användningen av SourceAnchor.
> 
> 

Om ovanstående inte är uppfyllda och du har flera aktiva konton eller mer än en postlåda, Välj en Azure AD Connect och ignorera den andra.  Om du har länkat postlådor men inga andra kontot kan dessa konton kommer inte att exporteras till Azure AD och användaren kommer inte vara medlem i några grupper.  Detta skiljer sig från hur den har passerat med DirSync och är avsiktlig att bättre stöd för dessa scenarier med flera skogar. Ett scenario med flera skogar illustreras i bilden nedan.

![](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Flera skogar flera Azure AD-scenario**

Rekommenderar vi att du har bara en enskild katalog i Azure AD för en organisation, men det går det en 1:1-relation sparas mellan en Azure AD Connect-synkroniseringsservern och Azure AD-katalog.  För varje instans av Azure AD kan behöver du en installation av Azure AD Connect.  Dessutom Azure AD, avsiktligt är isolerade och användare i en instans av Azure AD kommer inte att kunna se användare i en annan instans.

Det är möjligt och stöds för att ansluta en lokal instans av Active Directory till flera Azure AD-kataloger som visas i bilden nedan:

![](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Sammanhållna filtrering scenario**

Om du vill göra detta, måste följande vara sant:

* Azure AD Connect sync-servrar måste konfigureras för filtrering så att de har ett ömsesidigt uteslutande uppsättning objekt.  Detta har gjort, till exempel genom att konfigurera varje server till en viss domän eller Organisationsenhet.
* En DNS-domän kan bara registreras i en enda Azure AD-katalog så UPN-namn för användare i lokalt AD måste använda separata namnområden
* Användare i en instans av Azure AD kommer bara att kunna se användare från deras instans.  De kommer inte att kunna se användare i de andra instanserna
* Endast en av Azure AD-kataloger kan du aktivera Exchange-hybrid med lokal AD
* Ömsesidig exklusivitet gäller även för att tillbakaskrivning.  Detta gör vissa återskrivning funktioner stöds inte med den här topologin eftersom dessa förutsätter en enda lokal konfiguration.  Det här omfattar:
  * Gruppera återskrivning med standardkonfiguration
  * Tillbakaskrivning av enhet

Följande stöds inte och bör inte väljas som en implementering:

* Det går inte för att har flera Azure AD Connect-synkroniseringsservrar som ansluter till samma Azure AD-katalog, även om de är konfigurerade för att synkronisera Objektuppsättning objekt
* Det stöds inte för att synkronisera samma användare till flera Azure AD-kataloger. 
* Det är också som inte stöds för att göra en konfigurationsändring så att användare i en Azure AD ska visas som kontakter i en annan Azure AD-katalog. 
* Det är också stöds inte att ändra Azure AD Connect-synkronisering för att ansluta till flera Azure AD-kataloger.
* Azure AD-kataloger är avsiktligt isolerade. Det finns inte stöd för att ändra konfigurationen av Azure AD Connect-synkronisering för att läsa data från en annan Azure AD-katalog i ett försök att skapa en gemensam och enhetlig GAL mellan katalogerna. Det stöds inte även om du vill exportera användare som kontakter till en annan lokala AD med hjälp av Azure AD Connect-synkronisering.

> [!NOTE]
> Om din organisation begränsar datorer i nätverket från att ansluta till Internet, den här artikeln innehåller slutpunkter (FQDN: er, IPv4 och IPv6-adressintervall) som ska inkluderas i din utgående Tillåt listor och Internet Explorer zonen Betrodda platser av klienten datorer för att se till att dina datorer använder Office 365. Mer information finns [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiera en strategi för multifaktorautentisering
I det här steget definierar du multifaktorautentisering-strategi för att använda.  Azure Multi-Factor Authentication finns i två olika versioner.  En är en molnbaserad och den andra är på plats-baserade med hjälp av Azure MFA-servern.  Baserat på tillämpningen du ovan kan du bestämma vilken lösning är korrekt för din strategi.  Använd tabellen nedan för att avgöra vilken design som bäst uppfyller företagets säkerhetskrav:

Flera faktorer designalternativ:

| Tillgången till säker | MFA i molnet | MFA lokalt |
| --- | --- | --- |
| Microsoft-appar |ja |ja |
| Saas-appar i appgalleriet |ja |ja |
| IIS-program publicerade via Azure AD App Proxy |ja |ja |
| IIS-program som inte är publicerade via Azure AD App Proxy |nej |ja |
| Fjärråtkomst som VPN, RDG |nej |ja |

Trots att du kan ha regleras på en lösning för din strategi, behöver du fortfarande använda utvärdering ovan på var användarna finns.  Detta kan leda till lösning för att ändra.  Använd tabellen nedan för att hjälpa dig att fastställa detta:

| Användarplats | Prioriterade designalternativ |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication i molnet |
| Azure AD och lokalt AD med federation med AD FS |Båda |
| Azure AD och lokalt AD med hjälp av Azure AD Connect utan Lösenordssynkronisering |Båda |
| Azure AD och lokalt med Azure AD Connect med Lösenordssynkronisering |Båda |
| Lokala AD |Multi-Factor Authentication Server |

> [!NOTE]
> Du bör också se till att alternativet multifaktorautentisering design som du har valt stöder funktioner som krävs för din design.  Mer information finns [Välj Multi-Factor Authentication-säkerhetslösning för dig](../authentication/concept-mfa-whichversion.md#what-am-i-trying-to-secure).
> 
> 

## <a name="multi-factor-auth-provider"></a>Leverantör av Multifaktorautent
Multi-Factor authentication är tillgängligt som standard för globala administratörer som har en Azure Active Directory-klient. Men om du vill utöka multifaktorautentisering till alla användare och/eller vill att din globala administratörer för att kunna dra nytta funktioner som hanteringsportalen, anpassade hälsningar och rapporter, måste sedan du köpa och konfigurera Multi-Factor Authentication-Provider.

> [!NOTE]
> Du bör också se till att alternativet multifaktorautentisering design som du har valt stöder funktioner som krävs för din design. 
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa kraven på dataskydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Se också
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

