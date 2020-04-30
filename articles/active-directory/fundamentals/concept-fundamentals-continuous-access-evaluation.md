---
title: Utvärdering av kontinuerlig åtkomst i Azure AD
description: Att svara på ändringar i användar tillstånd snabbare med utvärdering av kontinuerlig åtkomst i Azure AD
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
ms.openlocfilehash: 3713901dd3dd5d17c4e1ddcef529c663b68f5b43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112583"
---
# <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

Microsoft-tjänster som Azure Active Directory (Azure AD) och Office 365 använder öppna standarder och protokoll för att maximera samverkan. En av de mest kritiska är öppen ID Connect (OIDC). När ett klient program som Outlook ansluter till en tjänst som Exchange Online, auktoriseras API-begäranden med hjälp av OAuth 2,0-åtkomsttoken. Som standard är dessa åtkomsttoken giltiga i en timme. När de går ut omdirigeras klienten tillbaka till Azure AD för att uppdatera dem. Det ger också möjlighet att utvärdera principer för användar åtkomst – vi kan välja att inte uppdatera token på grund av en princip för villkorlig åtkomst eller på grund av att användaren har inaktiverats i katalogen. 

Förfallo datum för token och uppdatering är en standard mekanism i branschen. I detta fall har kunderna uttalat sig om fördröjningen mellan när risk villkoren ändras för användaren (till exempel: genom att flytta från företags kontoret till det lokala kaféet eller användarautentiseringsuppgifter som identifierats på den svarta marknaden) och när principer kan tillämpas på den ändringen. Vi har experimentat med "Blunt Object"-metoden för minskad token för token men upptäckte att de kan försämra användar upplevelsen och tillförlitligheten utan att eliminera risker.

Svars tiden för princip överträdelser eller säkerhets problem kräver verkligen en "konversation" mellan token Issuer, t. ex. Azure AD och den förlitande parten, t. ex. Exchange Online. Den här dubbelriktade konversationen ger oss två viktiga funktioner. Den förlitande parten kan se när saker har ändrats, till exempel en klient som kommer från en ny plats, och meddela token utfärdaren. Det ger också token utfärdaren ett sätt att säga till att den förlitande parten slutar att följa tokens för en specifik användare på grund av konto kompromisser, inaktivitet eller andra problem. Mekanismen för den här konversationen är en utvärdering av kontinuerlig åtkomst (CAE).

Microsoft har varit en tidig deltagare i CAEP-initiativet (preaccess Evaluation Protocol) som en del av arbets gruppen [delade signaler och händelser](https://openid.net/wg/sse/) i OpenID Foundation. Identitets leverantörer och förlitande parter kommer att kunna utnyttja de säkerhets händelser och signaler som definieras av arbets gruppen för att auktorisera eller avsluta åtkomst. Det är spännande arbete och förbättrar säkerheten på många plattformar och program.

Eftersom säkerhets fördelarna är så fantastiska, kommer vi att lansera en Microsoft-bestämd första implementering parallellt med vårt fortsatta arbete inom standard organen. När vi arbetar för att distribuera de här CAE-funktionerna (preaccess Evaluation) i Microsoft-tjänster har vi lärt oss mycket och delar den här informationen med standard communityn. Vi hoppas att vår upplevelse i distributionen kan hjälpa till att informera en ännu bättre bransch standard och är förpliktigad att implementera standarden när den har ratificerats, vilket ger alla deltagande tjänster till förmån.

## <a name="how-does-cae-work-in-microsoft-services"></a>Hur fungerar CAE i Microsoft-tjänster?

Vi fokuserar vår inledande implementering av utvärdering av kontinuerlig åtkomst till Exchange och Teams. Vi hoppas att utöka stödet till andra Microsoft-tjänster i framtiden. Vi kommer att börja aktivera utvärdering av kontinuerlig åtkomst för klienter utan principer för villkorlig åtkomst. Vi kommer att använda våra lär sig från den här fasen av CAE för att informera vår pågående distribution av CAE.

## <a name="service-side-requirements"></a>Krav på service sida

Utvärdering av kontinuerlig åtkomst implementeras genom att aktivera tjänster (resurs leverantörer) för att prenumerera på kritiska händelser i Azure AD så att dessa händelser kan utvärderas och tillämpas nära real tid. Följande händelser kommer att framtvingas i den här inledande CAE-distributionen:

- Användar kontot har tagits bort eller inaktiverats
- Lösen ordet för en användare ändras eller återställs
- Administratören återkallar uttryckligen alla uppdateringstoken för en användare
- Utökad användar risk upptäckt av Azure AD Identity Protection

I framtiden hoppas vi att lägga till fler händelser, inklusive händelser som plats-och enhets tillstånds ändringar. **Även om syftet är att tvångs driften ska vara omedelbar kan svars tiden på upp till 15 minuter observeras på grund av händelse spridningen**. 

## <a name="client-side-claim-challenge"></a>Anspråks kontroll på klient Sidan

Innan utvärderingen av den kontinuerliga åtkomsten görs försöker klienter alltid att spela upp åtkomsttoken från sin cache så länge den inte har upphört att gälla. Med CAE presenterar vi ett nytt ärende som en resurs leverantör kan avvisa en token även när den inte har upphört att gälla. För att informera klienter om att kringgå deras cacheminne även om cachelagrade token inte har gått ut, introducerar vi en mekanism som kallas **anspråks utmaning**. CAE kräver en klient uppdatering för att förstå anspråks utmaningen. Den senaste versionen av följande program nedan stöder anspråks utmaning:

- Outlook för Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Team för Windows
- Team iOS 
- Teams Android 
- Teams Mac 

## <a name="token-lifetime"></a>Tokenlivstid

Eftersom risk och principer utvärderas i real tid, förlitar sig klienterna som förhandlar om att en utvärdering av kontinuerlig åtkomst utvärderas på CAE i stället för befintliga livs längds principer för statisk åtkomst, vilket innebär att den konfigurerbara livs längden för token inte kommer att användas längre för CAE-kompatibla klienter som förhandlar om CAE-medvetna sessioner.

Vi kommer att öka livs längden för åtkomsttoken till 24 timmar i CAE-sessioner. Återkallning sköts av kritiska händelser och princip utvärdering, inte en godtycklig tids period. Den här ändringen ökar stabiliteten för dina program utan att det påverkar din säkerhets position. 

## <a name="example-flows"></a>Exempel flöden

### <a name="user-revocation-event-flow"></a>Händelse flöde för återkallade användare:

![Händelse flöde för återkallning av användare](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. En CAE-kompatibel klient visar autentiseringsuppgifter eller en uppdateringstoken till AAD som efterfrågar en åtkomsttoken för en viss resurs.
1. En åtkomsttoken returneras tillsammans med andra artefakter till klienten.
1. En administratör [återkallar uttryckligen alla uppdateringstoken för användaren](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). En återkallnings händelse kommer att skickas till resurs leverantören från Azure AD.
1. En åtkomsttoken visas för resurs leverantören. Resurs leverantören utvärderar giltigheten hos token och kontrollerar om det finns någon återkallnings händelse för användaren. Resurs leverantören använder den här informationen för att avgöra om du vill bevilja åtkomst till resursen eller inte.
1. I det här fallet nekar resurs leverantören åtkomst och skickar en 401 + anspråks utmaning tillbaka till klienten
1. Den CAE-kompatibla klienten förstår 401 +-anspråks utmaningen. Den kringgår cacheminnena och går tillbaka till steg 1 och skickar dess uppdateringstoken tillsammans med anspråks utmaningen tillbaka till Azure AD. Azure AD kommer sedan att utvärdera alla villkor och uppmana användaren att autentisera om i det här fallet.
 
## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-is-the-lifetime-of-my-access-token"></a>Vad är livs längden för min åtkomsttoken?

Om du inte använder CAE-kompatibla klienter är din standardtoken för åtkomsttoken fortfarande 1 timme, om du inte har konfigurerat din livstid för åtkomsttoken med hjälp av för hands versionen av funktionen för att konfigurera token för [token (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

Om du använder CAE-kompatibla klienter som förhandlar om CAE-medvetna sessioner, kommer dina CTL-inställningar för åtkomsttoken att skrivas över och åtkomst-token är 24 timmar.

### <a name="how-quick-is-enforcement"></a>Hur snabb är tvång?

Även om syftet är att tvångs driften ska vara omedelbar kan svars tiden på upp till 15 minuter observeras på grund av händelse spridningen.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hur fungerar CAE med inloggnings frekvensen?

Inloggnings frekvensen kommer att behållas med eller utan CAE.

## <a name="next-steps"></a>Nästa steg

[Vi presenterar en utvärdering av kontinuerlig åtkomst](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
