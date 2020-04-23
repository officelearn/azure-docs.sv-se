---
title: Utvärdering av kontinuerlig åtkomst i Azure AD
description: Svara på ändringar i användartillstånd snabbare med utvärdering av kontinuerlig åtkomst i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873308"
---
# <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

Microsoft-tjänster, till exempel Azure Active Directory (Azure AD) och Office 365, använder öppna standarder och protokoll för att maximera interoperabiliteten. En av de mest kritiska är Open ID Connect (OIDC). När ett klientprogram som Outlook ansluter till en tjänst som Exchange Online auktoriseras API-begäranden med hjälp av OAuth 2.0-åtkomsttoken. Som standard är dessa åtkomsttoken giltiga i en timme. När de upphör att gälla omdirigeras klienten tillbaka till Azure AD för att uppdatera dem. Det ger också en möjlighet att omvärdera principer för användaråtkomst – vi kan välja att inte uppdatera token på grund av en princip för villkorlig åtkomst eller för att användaren har inaktiverats i katalogen. 

Vi har hört den överväldigande feedback från våra kunder: en timmes fördröjning på grund av åtkomst token livstid för att återanvända principer för villkorlig åtkomst och ändringar i användartillstånd (till exempel: inaktiverad på grund av permission) är inte tillräckligt bra.

Microsoft har varit en tidig deltagare i CAEP-initiativet (Continuous Access Evaluation Protocol) som en del av arbetsgruppen [för delade signaler och händelser](https://openid.net/wg/sse/) vid OpenID Foundation. Identitetsleverantörer och förlitande parter kommer att kunna utnyttja de säkerhetshändelser och signaler som definieras av arbetsgruppen för att omauktorisera eller avsluta åtkomsten. Det är ett spännande arbete och kommer att förbättra säkerheten på många plattformar och applikationer.

Eftersom säkerhetsfördelarna är så stora lanserar vi en Microsoft-specifik inledande implementering parallellt med vårt fortsatta arbete inom standardiseringsorganen. När vi arbetar med att distribuera dessa funktionerna för utvärdering av kontinuerlig åtkomst (CAE) i Microsoft-tjänster har vi lärt oss mycket och delar den här informationen med standardiseringsgemenskapen. Vi hoppas att vår erfarenhet av driftsättning kan bidra till att informera en ännu bättre branschstandard och är fast beslutna att genomföra denna standard när den har ratificerats, så att alla deltagande tjänster kan dra nytta av detta.

## <a name="how-does-cae-work-in-microsoft-services"></a>Hur fungerar CAE i Microsoft-tjänster?

Vi fokuserar vårt första genomförande av kontinuerlig åtkomstutvärdering till Exchange och Teams. Vi hoppas kunna utöka stödet till andra Microsoft-tjänster i framtiden. Vi kommer att börja aktivera utvärdering av kontinuerlig åtkomst endast för klienter utan principer för villkorlig åtkomst. Vi kommer att använda våra lärdomar från denna fas av CAE för att informera vår pågående utbyggnad av CAE.

## <a name="service-side-requirements"></a>Krav på servicesidan

Utvärdering av kontinuerlig åtkomst implementeras genom att tjänster (resursleverantörer) kan prenumerera på kritiska händelser i Azure AD så att dessa händelser kan utvärderas och tillämpas i nära realtid. Följande händelser kommer att tillämpas i den här inledande CAE-distributionen:

- Användarkontot tas bort eller inaktiveras
- Lösenordet för en användare ändras eller återställs
- Admin återkallar uttryckligen alla uppdateringstoken för en användare
- Förhöjd användarrisk som identifieras av Azure AD Identity Protection

I framtiden hoppas vi kunna lägga till fler händelser, inklusive händelser som plats- och enhetstillståndsändringar. **Medan vårt mål är att verkställigheten ska vara omedelbar, i vissa fall latens på upp till 15 minuter kan observeras på grund av händelse förökningstid**. 

## <a name="client-side-claim-challenge"></a>Anspråksutmaning på klientsidan

Innan utvärdering av kontinuerlig åtkomst skulle klienter alltid försöka spela upp åtkomsttoken igen från cacheminnet så länge den inte har upphört att gälla. Med CAE introducerar vi ett nytt ärende som en resursprovider kan avvisa en token även när den inte har upphört att gälla. För att informera klienter att kringgå deras cache även om de cachelagrade tokens inte har löpt ut, introducerar vi en mekanism som kallas **anspråksutmaning**. CAE kräver en klientuppdatering för att förstå anspråksutmaning. Den senaste versionen av följande program nedan stöder anspråk utmaning:

- Outlook för Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Team för Windows
- Lag iOS 
- Lag Android 
- Lag Mac 

## <a name="token-lifetime"></a>Tokenlivstid

Eftersom risk och princip utvärderas i realtid, kunder som förhandlar kontinuerlig åtkomst utvärdering medvetna sessioner kommer att förlita sig på CAE i stället för befintliga statisk åtkomst token livstid principer, vilket innebär att konfigurerbara token livstid politik inte kommer att hedras längre för CAE-kompatibla klienter som förhandlar CAE-medvetna sessioner.

Vi ökar åtkomsttokens livstid till 24 timmar i CAE-sessioner. Återkallande drivs av kritiska händelser och principutvärdering, inte en godtycklig tidsperiod. Den här ändringen ökar stabiliteten i dina program utan att påverka din säkerhetsposition. 

## <a name="example-flows"></a>Exempel på flöden

### <a name="user-revocation-event-flow"></a>Händelseflöde för återkallade användare:

![Händelseflöde för återkallade användare](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. En CAE-kompatibel klient presenterar autentiseringsuppgifter eller en uppdateringstoken till AAD och ber om en åtkomsttoken för vissa resurser.
1. En åtkomsttoken returneras tillsammans med andra artefakter till klienten.
1. En administratör återkallar uttryckligen [alla uppdateringstoken för användaren](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). En återkallningshändelse skickas till resursprovidern från Azure AD.
1. En åtkomsttoken visas för resursprovidern. Resursleverantören utvärderar tokens giltighet och kontrollerar om det finns någon återkallningshändelse för användaren. Resursleverantören använder den här informationen för att besluta att bevilja åtkomst till resursen eller inte.
1. I det här fallet nekar resursprovidern åtkomst och skickar tillbaka en 401+-anspråksutmaning till klienten
1. Den CAE-kapabla klienten förstår 401+ anspråksutmaningen. Det kringgår cacheminnena och går tillbaka till steg 1, skicka dess uppdateringstoken tillsammans med anspråksutmaningen tillbaka till Azure AD. Azure AD kommer sedan att omvärdera alla villkor och uppmana användaren att omauktorisera i det här fallet.
 
## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-is-the-lifetime-of-my-access-token"></a>Vad är livstiden för min åtkomsttoken?

Om du inte använder KLIENT-kompatibla klienter med CAE är din livstid för åtkomsttoken fortfarande 1 timme om du inte har konfigurerat din åtkomsttokens livstid med förhandsgranskningsfunktionen [för konfigurerbar tokenlivstid (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

Om du använder CAE-kompatibla klienter som förhandlar om CAE-medvetna sessioner skrivs dina CTL-inställningar för åtkomsttokens livstid över och åtkomsttokens livstid kommer att vara 24 timmar.

### <a name="how-quick-is-enforcement"></a>Hur snabb är verkställighet?

Medan vårt mål är att verkställighet ska vara omedelbar, i vissa fall latens på upp till 15 minuter kan observeras på grund av händelse spridning tid.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hur fungerar CAE med inloggningsfrekvens?

Inloggningsfrekvensen kommer att respekteras med eller utan CAE.

## <a name="next-steps"></a>Nästa steg

[Tillkännage utvärdering av kontinuerlig åtkomst](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
