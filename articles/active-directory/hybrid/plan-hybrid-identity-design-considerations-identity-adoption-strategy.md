---
title: Hybrid identitetsdesign - införande strategi Azure | Microsoft-dokument
description: Med kontrollen Villkorlig åtkomst kontrollerar Azure Active Directory de specifika villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet. När dessa villkor är uppfyllda autentiseras användaren och får åtkomst till programmet.
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
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109338"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definiera en strategi för antagande av hybrididentitet
I den här uppgiften definierar du hybrididentitetsutförandestrategin för hybrididentitetslösningen för att uppfylla de affärskrav som diskuterades i:

* [Bestäm affärsbehov](plan-hybrid-identity-design-considerations-business-needs.md)
* [Fastställ krav på katalogsynkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Fastställa autentiseringskrav för flera faktorer](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definiera strategi för affärsbehov
Den första uppgiften behandlar fastställandet av organisationens affärsbehov.  Detta kan vara mycket bred och omfattning krypa kan uppstå om du inte är försiktig.  I början, hålla det enkelt men kom alltid ihåg att planera för en design som kommer att rymma och underlätta förändringar i framtiden.  Oavsett om det är en enkel design eller en extremt komplex sådan är Azure Active Directory den Microsoft Identity-plattform som stöder Office 365, Microsoft Online Services och molnmedvetna program.

## <a name="define-an-integration-strategy"></a>Definiera en integrationsstrategi
Microsoft har tre huvudsakliga integrationsscenarier som är molnidentiteter, synkroniserade identiteter och federerade identiteter.  Du bör planera att anta en av dessa integrationsstrategier.  Den strategi du väljer kan variera och besluten att välja en kan innehålla, vilken typ av användarupplevelse du vill tillhandahålla, har du en befintlig infrastruktur och vad som är mest kostnadseffektivt.  

![integrationsscenarier](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

De scenarier som definieras i ovanstående figur är:

* **Molnidentiteter**: det här är identiteter som bara finns i molnet.  När det gäller Azure AD finns de specifikt i din Azure AD-katalog.
* **Synkroniserad**: det här är identiteter som finns lokalt och i molnet.  Med Hjälp av Azure AD Connect skapas eller kopplas dessa användare antingen till befintliga Azure AD-konton.  Användarens lösenordshöna synkroniseras från den lokala miljön till molnet i vad som kallas ett lösenordshöste.  När du använder synkroniserad en varning är att om en användare är inaktiverad i den lokala miljön, kan det ta upp till tre timmar för att kontostatus visas i Azure AD.  Detta beror på synkroniseringstidintervallet.
* **Federerad**: dessa identiteter finns både lokalt och i molnet.  Med Hjälp av Azure AD Connect skapas eller kopplas dessa användare antingen till befintliga Azure AD-konton.  

> [!NOTE]
> Mer information om synkroniseringsalternativen finns i [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

Följande tabell hjälper till att fastställa fördelarna och nackdelarna med var och en av följande strategier:

| Strategi | Fördelar | Nackdelar |
| --- | --- | --- |
| **Molnidentiteter** |Enklare att hantera för liten organisation. <br> Inget att installera lokalt. Ingen ytterligare maskinvara behövs<br>Lätt inaktiveras om användaren lämnar företaget |Användare måste logga in när de kommer åt arbetsbelastningar i molnet <br> Lösenord kan vara desamma för moln- och lokala identiteter |
| **Synkroniseras** |Lokalt lösenord autentiserar både lokala och molnkataloger <br>Enklare att hantera för små, medelstora eller stora organisationer <br>Användare kan ha enkel inloggning (SSO) för vissa resurser <br> Microsofts metod för synkronisering <br> Enklare att hantera |Vissa kunder kan vara ovilliga att synkronisera sina kataloger med molnet på grund av specifika företagets polis |
| **Federerade** |Användare kan ha enkel inloggning (SSO) <br>Om en användare avslutas eller lämnar, kan kontot omedelbart inaktiveras och åtkomst återkallas,<br> Stöder avancerade scenarier som inte kan utföras med synkroniserade |Fler steg för att konfigurera och konfigurera <br> Högre underhåll <br> Kan kräva ytterligare maskinvara för STS-infrastrukturen <br> Kan kräva ytterligare maskinvara för att installera federationsservern. Ytterligare programvara krävs om AD FS används <br> Kräv omfattande inställningar för SSO <br> Kritisk felpunkt om federationsservern är nere, kan användarna inte autentisera |

### <a name="client-experience"></a>Klientupplevelse
Den strategi som du använder kommer att diktera användarens inloggningsupplevelse.  Följande tabeller ger dig information om vad användarna bör förvänta sig att deras inloggningsupplevelse ska vara.  Alla federerade identitetsleverantörer stöder inte SSO i alla scenarier.

**Doman-sammanfogade och privata nätverksprogram:**

|  | Synkroniserad identitet | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |enkel inloggning, ibland krävs för att tillhandahålla organisations-ID |
| Outlook |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Skype för företag (Lync) |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, snabb autentiseringsuppgifter för Exchange |
| OneDrive för företag |Fråga efter autentiseringsuppgifter |enkel inloggning |
| Office Pro Plus-prenumeration |Fråga efter autentiseringsuppgifter |enkel inloggning |

**Externa eller ej betrodda källor:**

|  | Synkroniserad identitet | Federerade identiteter |
| --- | --- | --- |
| Webbläsare |Formulärbaserad autentisering |Formulärbaserad autentisering |
| Outlook, Skype för företag (Lync), OneDrive för företag, Office-prenumeration |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |
| Exchange ActiveSync |Fråga efter autentiseringsuppgifter |enkel inloggning för Lync, snabb autentiseringsuppgifter för Exchange |
| Mobilappar |Fråga efter autentiseringsuppgifter |Fråga efter autentiseringsuppgifter |

Om du har fastställt från uppgift 1 att du har ett IDP från tredje part eller ska använda en för att tillhandahålla federation med Azure AD, måste du vara medveten om följande funktioner som stöds:

* Alla SAML 2.0-provider som är kompatibla för SP-Lite-profilen kan stödja autentisering till Azure AD och associerade program
* Stöder passiv autentisering, vilket underlättar autentisering till OWA, SPO, etc.
* Exchange Online-klienter kan stödjas via SAML 2.0 Enhanced Client Profile (ECP)

Du måste också vara medveten om vilka funktioner som inte kommer att vara tillgängliga:

* Utan stöd för WS-Trust/Federation går alla andra aktiva klienter sönder
  * Det innebär ingen Lync-klient, OneDrive-klient, Office-prenumeration, Office Mobile före Office 2016
* Övergång av Office till passiv autentisering gör att de kan stödja rena SAML 2.0-IdPs, men stödet kommer fortfarande att vara på klient-för-klient-basis

> [!NOTE]
> För den mest uppdaterade listan läs artikeln [Azure AD federation kompatibilitetslista](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definiera synkroniseringsstrategi
I den här uppgiften definierar du de verktyg som ska användas för att synkronisera organisationens lokala data till molnet och vilken topologi du ska använda.  Eftersom de flesta organisationer använder Active Directory, information om hur du använder Azure AD Connect för att ta itu med frågorna ovan finns i detalj.  För miljöer som inte har Active Directory finns det information om hur du använder FIM 2010 R2 eller MIM 2016 för att planera den här strategin.  Framtida versioner av Azure AD Connect kommer dock att stödja LDAP-kataloger, så beroende på din tidslinje kan den här informationen hjälpa till.

### <a name="synchronization-tools"></a>Synkroniseringsverktyg
Under årens lopp har flera synkroniseringsverktyg funnits och använts för olika scenarier.  För närvarande Azure AD Connect är gå till verktyg för val för alla scenarier som stöds.  AAD Sync och DirSync finns också kvar och kan till och med finnas i din miljö nu. 

> [!NOTE]
> Den senaste informationen om funktionerna för varje verktyg som stöds finns i [jämförelseartikeln för katalogintegrationsverktyg.](plan-hybrid-identity-design-considerations-tools-comparison.md)  
> 
> 

### <a name="supported-topologies"></a>Topologier som stöds
När du definierar en synkroniseringsstrategi måste topologin som används bestämmas. Beroende på den information som fastställdes i steg 2 kan du avgöra vilken topologi som är rätt att använda. Den enda skogen, en enda Azure AD-topologi är den vanligaste och består av en enda Active Directory-skog och en enda instans av Azure AD.  Detta kommer att användas i de flesta scenarier och är den förväntade topologin när du använder Azure AD Connect Express-installationen som visas i figuren nedan.

![Topologier](./media/plan-hybrid-identity-design-considerations/single-forest.png) med stöd För enkel skog Det är vanligt att stora och till och med små organisationer har flera skogar, vilket visas i figur 5.

> [!NOTE]
> Mer information om de olika lokala och Azure AD-topologierna med Azure AD Connect-synkronisering finns i artikeln [Topologies för Azure AD Connect](plan-connect-topologies.md).
> 
> 

![flerskogstopologi](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenario med flera skogar

Om så är fallet bör en azure AD-topologin för flera skogar beaktas om följande objekt är sanna:

* Användare har bara en identitet i alla skogar – avsnittet unikt identifierande användare nedan beskriver detta mer i detalj.
* Användaren autentiserar till skogen där deras identitet finns
* UPN och Source Anchor (oföränderlig id) kommer från denna skog
* Alla skogar är tillgängliga via Azure AD Connect – det innebär att den inte behöver domänkoppling och kan placeras i en DMZ om detta underlättar detta.
* Användarna har bara en postlåda
* Skogen som är värd för en användares postlåda har den bästa datakvaliteten för attribut som visas i DEN globala adresslistan för Exchange (GAL)
* Om det inte finns någon postlåda på användaren kan alla skogar användas för att bidra med dessa värden
* Om du har en länkad postlåda finns det också ett annat konto i en annan skog som används för att logga in.

> [!NOTE]
> Objekt som finns i både lokalt och i molnet är "anslutna" via en unik identifierare. I samband med katalogsynkronisering kallas den här unika identifieraren SourceAnchor. I samband med Enkel inloggning kallas detta ImmutableId. [Designkoncept för Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) för mer information om användningen av SourceAnchor.
> 
> 

Om ovanstående inte är sant och du har mer än ett aktivt konto eller mer än en postlåda, kommer Azure AD Connect att välja det ena och ignorera det andra.  Om du har länkade postlådor men inget annat konto exporteras inte dessa konton till Azure AD och den användaren kommer inte att vara medlem i några grupper.  Detta skiljer sig från hur det var tidigare med DirSync och är avsiktligt att bättre stödja dessa multi-skog scenarier. Ett scenario med flera skogar visas i figuren nedan.

![flera Azure AD-klienter](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Flera Azure AD-scenario för flera skogar**

Vi rekommenderar att du bara har en enda katalog i Azure AD för en organisation, men det stöds en 1:1-relation hålls mellan en Azure AD Connect-synkroniseringsserver och en Azure AD-katalog.  För varje instans av Azure AD behöver du en installation av Azure AD Connect.  Dessutom azure AD, design är isolerad och användare i en instans av Azure AD kommer inte att kunna se användare i en annan instans.

Det är möjligt och stöds för att ansluta en lokal instans av Active Directory till flera Azure AD-kataloger som visas i bilden nedan:

![enstaka skogsfiltrering](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Scenario för filtrering med en skog**

För att göra detta måste följande vara sant:

* Azure AD Connect-synkroniseringsservrar måste konfigureras för filtrering så att de alla har en ömsesidigt exklusiv uppsättning objekt.  Detta görs, till exempel genom att omfånga varje server till en viss domän eller organisationsenhet.
* En DNS-domän kan bara registreras i en enda Azure AD-katalog så ATT-nätverken för användarna i den lokala AD måste använda separata namnområden
* Användare i en instans av Azure AD kan bara se användare från sin instans.  De kommer inte att kunna se användare i andra fall
* Endast en av Azure AD-katalogerna kan aktivera Exchange hybrid med den lokala AD
* Ömsesidig exklusivitet gäller även för nedskrivning.  Detta gör att vissa tillbakaskrivningsfunktioner inte stöds med den här topologin eftersom dessa förutsätter en enda lokal konfiguration.  Det här omfattar:
  * Gruppavskrivning med standardkonfiguration
  * Tillbakaskrivning av enheter

Följande stöds inte och bör inte väljas som en implementering:

* Det går inte att ha flera Azure AD Connect-synkroniseringsservrar som ansluter till samma Azure AD-katalog även om de är konfigurerade för att synkronisera ömsesidigt uteslutande uppsättning objekt
* Det stöds inte att synkronisera samma användare till flera Azure AD-kataloger. 
* Det stöds inte heller att göra en konfigurationsändring för att göra användare i en Azure AD att visas som kontakter i en annan Azure AD-katalog. 
* Det är inte heller stöd att ändra Azure AD Connect-synkronisering för att ansluta till flera Azure AD-kataloger.
* Azure AD-kataloger är avsiktligt isolerade. Det stöds inte att ändra konfigurationen av Azure AD Connect-synkronisering för att läsa data från en annan Azure AD-katalog i ett försök att skapa en gemensam och enhetlig GAL mellan katalogerna. Det stöds inte heller att exportera användare som kontakter till en annan lokal AD med Azure AD Connect-synkronisering.

> [!NOTE]
> Om din organisation begränsar datorer i nätverket från att ansluta till Internet visas de slutpunkter (FQDN-, IPv4- och IPv6-adressintervall) som du bör inkludera i dina utgående tillåta listor och Internet Explorer Trusted Sites Zone of client datorer för att säkerställa att datorerna kan använda Office 365. Mer information finns i [Url:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definiera multifaktorautentiseringsstrategi
I den här uppgiften definierar du den multifaktorautentiseringsstrategi som ska användas.  Azure Multi-Factor Authentication finns i två olika versioner.  Den ena är en molnbaserad och den andra är lokalt baserad med Azure MFA Server.  Baserat på utvärderingen du gjorde ovan kan du avgöra vilken lösning som är den rätta för din strategi.  Använd tabellen nedan för att avgöra vilket designalternativ som bäst uppfyller företagets säkerhetskrav:

Alternativ för flerfaktorsdesign:

| Tillgång för att säkra | MFA i molnet | MFA lokalt |
| --- | --- | --- |
| Microsoft-appar |ja |ja |
| Saas-appar i appgalleriet |ja |ja |
| IIS-program publicerade via Azure AD App Proxy |ja |ja |
| IIS-program som inte publiceras via Azure AD App Proxy |nej |ja |
| Fjärråtkomst som VPN, RDG |nej |ja |

Även om du kan ha fast på en lösning för din strategi, måste du fortfarande använda utvärderingen ovanifrån på var användarna finns.  Detta kan leda till att lösningen ändras.  Använd tabellen nedan för att hjälpa dig att avgöra detta:

| Användarplats | Önskat designalternativ |
| --- | --- |
| Azure Active Directory |Multifaktorautentisering i molnet |
| Azure AD och lokalt AD med federation med AD FS |Båda |
| Azure AD och lokalt AD med Azure AD Connect ingen lösenordssynkronisering |Båda |
| Azure AD och lokalt med Azure AD Connect med lösenordssynkronisering |Båda |
| Lokalt AD |Multi-Factor Authentication Server |

> [!NOTE]
> Du bör också se till att designalternativet för multifaktorautentisering som du har valt stöder de funktioner som krävs för din design.  Mer information finns i [Välj säkerhetslösning för flera faktorer åt dig](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Multifaktor-Auth-provider
Multifaktorautentisering är som standard tillgängligt för globala administratörer som har en Azure Active Directory-klientorganisation. Men om du vill utöka multifaktorautentisering till alla dina användare och/eller vill att dina globala administratörer ska kunna dra nytta av funktioner som hanteringsportalen, anpassade hälsningar och rapporter, måste du köpa och konfigurera Multifaktorautentiseringsprovider.

> [!NOTE]
> Du bör också se till att designalternativet för multifaktorautentisering som du har valt stöder de funktioner som krävs för din design. 
> 
> 

## <a name="next-steps"></a>Nästa steg
[Bestäm dataskyddskrav](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

