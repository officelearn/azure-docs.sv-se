---
title: Token, session och enkel inloggning-konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Token, session och enkel inloggning-konfiguration i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 35210a8e93b8437ea4d8c3b5f002c81c549d3afe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444844"
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Token, session och konfiguration för enkel inloggning

Den här funktionen ger dig detaljerad kontroll på en [per princip basis](active-directory-b2c-reference-policies.md), av:

1. Livslängd för säkerhetstoken som orsakats av Azure Active Directory (Azure AD) B2C.
2. Livslängd för web application sessioner som hanteras av Azure AD B2C.
3. Format för viktiga anspråk i säkerhetstoken som orsakats av Azure AD B2C.
4. Enkel inloggning (SSO) beteende för flera appar och principer i din B2C-klient.

För inbyggda principer kan använda du den här funktionen i din Azure AD B2C-katalog på följande sätt:

1. Följ dessa steg för att [navigerar du till menyn för B2C-funktioner](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
2. Klicka på **registrerings-eller logga in**. * Obs: Du kan använda den här funktionen på alla principtypen, inte bara på ** registrering eller inloggning principer ***.
3. Öppna en princip genom att klicka på den. Klicka exempelvis på **B2C_1_SiUpIn**.
4. Klicka på **redigera** högst upp på menyn.
5. Klicka på **Token, session och enkel inloggning config**.
6. Gör dina önskade ändringar. Läs mer om tillgängliga egenskaper i följande avsnitt.
7. Klicka på **OK**.
8. Klicka på **spara** överst på menyn.

## <a name="token-lifetimes-configuration"></a>Konfiguration av tokenlivslängder

Azure AD-B2C stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-b2c-reference-protocols.md) för att aktivera säker åtkomst till skyddade resurser. För att implementera det här stödet, genererar Azure AD B2C olika [säkerhetstoken](active-directory-b2c-reference-tokens.md). Det här är de egenskaper som du kan använda för att hantera livslängd av säkerhetstoken som orsakats av Azure AD B2C:

* **Åtkomst och ID-token livstid (minuter)**: livslängden för OAuth 2.0-ägartoken som används för att få åtkomst till en skyddad resurs.
  * Standard = 60 minuter.
  * Minsta (inklusivt) = 5 minuter.
  * Maximalt (inklusivt) = 1 440 minuter.
* **Livslängd för uppdateringstoken (dagar)**: den maximala tidsperiod innan vilken en uppdateringstoken kan användas för att hämta en ny åtkomst- eller ID-token (och eventuellt en ny uppdateringstoken, om ditt program har beviljats den `offline_access` omfattning).
  * Standard = 14 dagar.
  * Minsta (inklusivt) = 1 dag.
  * Maximalt (inklusivt) = 90 dagar.
* **Token livslängd för skjutfönster (dagar)**: när den här tidsperioden har gått ut måste användaren tvingas att återautentisera, oavsett giltighetsperioden hos den senaste uppdateringstoken som införskaffats av programmet. Det kan endast anges om växeln har angetts till **begränsad**. Det måste vara större än eller lika med den **livslängd för uppdateringstoken (dagar)** värde. Om växeln har angetts till **Ramavgränsare**, du kan inte ange ett specifikt värde.
  * Standard = 90 dagar.
  * Minsta (inklusivt) = 1 dag.
  * Maximalt (inklusivt) = 365 dagar.

Det här är några användningsområden som du kan aktivera med hjälp av dessa egenskaper:

* Tillåt en användare att hålla dig inloggad i en hanteringsprincip för mobilprogram på obestämd tid, så länge som han eller hon är ständigt aktiv på programmet. Du kan göra detta genom att ange den **uppdatering token livslängd för skjutfönster för (dagar)** växla till **Ramavgränsare** i din inloggningsprincip.
* Uppfyll din bransch säkerhets- och efterlevnadskrav genom att ange lämplig åtkomst tokenlivslängder.

    > [!NOTE]
    > De här inställningarna är inte tillgängliga för principer för lösenordsåterställning.
    > 
    > 

## <a name="token-compatibility-settings"></a>Inställningar för tokenkompatibilitet

Vi har gjort ändringar viktiga anspråk i säkerhetstoken som orsakats av Azure AD B2C. Detta gjordes för att förbättra vår standardprotokollet support och för bättre interoperabilitet med tredje parts identity-bibliotek. Om du vill behålla befintliga appar, kan vi dock skapat följande egenskaper så att kunder kan anmäla sig efter behov:

* **Utfärdaren (iss) anspråk**: identifierar den här kolumnen den Azure AD B2C-klient som utfärdade token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: Det här är standardvärdet.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Det här värdet innehåller ID: N för både B2C-klient och den princip som används i token-begäran. Om din app eller -biblioteket måste Azure AD B2C ska vara kompatibel med den [OpenID Connect Discovery 1.0-specifikationen](http://openid.net/specs/openid-connect-discovery-1_0.html), Använd det här värdet.
* **Anspråk för ämne (sub)**: identifierar den här kolumnen entiteten, dvs, den användaren, som token kontrollerar information.
  * **ObjectID**: Detta är standardvärdet. Fylls i objekt-ID för användaren i katalogen i den `sub` anspråk i token.
  * **Stöds inte**: det finns bara för bakåtkompatibilitet och vi rekommenderar att du växlar till **ObjectID** så fort du.
* **Anspråk som representerar princip-ID**: identifierar den här kolumnen Anspråkstypen som princip-ID som används i token begäran har fyllts i.
  * **tfp**: Detta är standardvärdet.
  * **ACR**: det finns bara för bakåtkompatibilitet och vi rekommenderar att du växlar till `tfp` så fort du.

## <a name="session-behavior"></a>Sessionsbeteende

Azure AD-B2C stöder den [autentiseringsprotokollet OpenID Connect](active-directory-b2c-reference-oidc.md) för att aktivera säker inloggning till webbprogram. Det här är de egenskaper som du kan använda för att hantera web application sessioner:

* **Webbappssession (minuter)**: livslängden för Azure AD B2C-sessioncookie som lagras i användarens webbläsare efter lyckad autentisering.
  * Standard = 1 440 minuter.
  * Minsta (inklusivt) = 15 minuter.
  * Maximalt (inklusivt) = 1 440 minuter.
* **Webbappssession**: om den här växeln anges till **absolut**, om användaren tvingas att autentisera igen efter hur lång tid som anges av **webbappssession (minuter)** nätverksförbindelse. Om den här växeln anges till **rullande** (standardinställningen), användaren förblir inloggad så länge användaren är aktiv kontinuerligt i webbprogrammet.

Det här är några användningsområden som du kan aktivera med hjälp av dessa egenskaper:

* Uppfyller kraven för din bransch säkerhet och efterlevnad genom att ange rätt program webbsessionen livslängd.
* Tvinga autentiseras igen efter en viss tidsperiod under användaren interagerar med en hög säkerhet som en del av ditt webbprogram. 

    > [!NOTE]
    > De här inställningarna är inte tillgängliga för principer för lösenordsåterställning.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Enkel inloggning (SSO) konfiguration
Om du har flera program och principer i din B2C-klient kan du kan hantera användarinteraktioner över dem med hjälp av den **konfigurationen för enkel inloggning** egenskapen. Du kan ange egenskapen till någon av följande inställningar:

* **Klient**: det här är standardinställningen. Om du använder den här inställningen kan flera program och principer i din B2C-klient för att dela samma användarsessionen. Till exempel när en användare loggar in i ett program, Contoso Shopping han eller hon kan också sömlöst logga in på en annan en, Contoso-farmaci, vid åtkomst till den.
* **Programmet**: Detta kan du behålla en användarsession exklusivt för ett program, oberoende av andra program. Till exempel om du vill att användaren att logga in på Contoso farmaci (med samma autentiseringsuppgifter), även om han eller hon är redan inloggad på Contoso Shopping, ett annat program på samma B2C-klient. 
* **Principen**: Detta kan du behålla en användarsession exklusivt för en princip, oberoende av de program som använder. Till exempel om användaren har redan loggat in och slutföra ett steg med multi factor authentication (MFA), kan han eller hon få tillgång till högre säkerhet delar av flera program så länge som den session som är kopplad till principen inte upphör att gälla.
* **Inaktiverad**: Detta tvingar användaren att köra genom hela användarresa på varje körning av principen. Till exempel på så sätt kan flera användare att logga in på ditt program (i ett delat skrivbord scenario), även när en enskild användare förblir inloggade under hela tiden.

    > [!NOTE]
    > De här inställningarna är inte tillgängliga för principer för lösenordsåterställning.
    > 
    > 

