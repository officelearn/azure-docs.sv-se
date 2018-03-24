---
title: Token, session och enkel inloggning-konfiguration – Azure AD B2C | Microsoft Docs
description: Token, session och enkel inloggning-konfiguration i Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 925313b6f2a00826f2ec8086457315c60f70b007
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Token, session och konfiguration för enkel inloggning

Den här funktionen ger dig finmaskig kontroll på en [per princip bas](active-directory-b2c-reference-policies.md), av:

1. Livslängd för säkerhetstoken som sänds av Azure Active Directory (AD Azure) B2C.
2. Livslängd för web application-sessioner som hanteras av Azure AD B2C.
3. Format för viktiga anspråk i de säkerhetstoken som sänds av Azure AD B2C.
4. Enkel inloggning (SSO) beteendet över flera appar och principer i din B2C-klient.

För inbyggda principer, kan du använda den här funktionen i Azure AD B2C-katalogen på följande sätt:

1. Följ dessa steg för att [gå till menyn B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
2. Klicka på **principer för registrering eller inloggning**. * Obs: Du kan använda den här funktionen på alla principtypen, inte bara på ** registrering eller inloggning principer ***.
3. Öppna en princip genom att klicka på den. Klicka till exempel på **B2C_1_SiUpIn**.
4. Klicka på **redigera** längst upp på menyn.
5. Klicka på **Token, session och enkel inloggning config**.
6. Gör ändringarna du. Läs mer om tillgängliga egenskaper i nedanstående avsnitt.
7. Klicka på **OK**.
8. Klicka på **spara** överst på menyn.

## <a name="token-lifetimes-configuration"></a>Livslängd för token-konfiguration

Azure AD B2C stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-b2c-reference-protocols.md) för att aktivera säker åtkomst till skyddade resurser. Om du vill implementera detta stöd för Azure AD B2C avger olika [säkerhetstoken](active-directory-b2c-reference-tokens.md). Dessa är de egenskaper som du kan använda för att hantera livslängd av säkerhetstoken som sänds av Azure AD B2C:

* **Få åtkomst till & ID token livstid (minuter)**: livslängden för OAuth 2.0-ägar-token som används för att få åtkomst till en skyddad resurs.
  * Standard = 60 minuter.
  * Minsta (sammanlagt) = 5 minuter.
  * Maximalt (sammanlagt) = 1 440 minuter.
* **Uppdatera livslängd för token (dagar)**: den maximala tidsperiod som en uppdateringstoken kan användas för att skaffa en ny åtkomst eller ID-token (och eventuellt en ny uppdateringstoken, om ditt program har beviljats den `offline_access` omfattning).
  * Standard = 14 dagar.
  * Minsta (sammanlagt) = 1 dag.
  * Maximalt (sammanlagt) = 90 dagar.
* **Uppdatera livslängd för token glidande fönstret (dagar)**: efter denna tidsperiod användaren tvingas återautentisera, oavsett giltighetsperioden för den senaste uppdatera token som skapats av programmet. Det kan endast anges om parametern är inställd på **Bounded**. Det måste vara större än eller lika med den **uppdatering livslängd för token (dagar)** värde. Om växeln har angetts till **Unbounded**, du kan inte ange ett specifikt värde.
  * Standard = 90 dagar.
  * Minsta (sammanlagt) = 1 dag.
  * Maximalt (sammanlagt) = 365 dagar.

Det här är några användningsområden som kan aktiveras med hjälp av egenskaperna:

* Att en användare ska vara signerade i ett mobilt program på obestämd tid som han eller hon är kontinuerligt aktiv på programmet. Du kan göra detta genom att ange den **uppdatering glidande fönstret livslängd för token (dagar)** växla till **Unbounded** i din inloggningsprincip.
* Uppfyller din bransch säkerhets- och efterlevnadskrav genom att ange lämplig åtkomst-token livslängd.

    > [!NOTE]
    > De här inställningarna är inte tillgängliga för principer för återställning av lösenord.
    > 
    > 

## <a name="token-compatibility-settings"></a>Inställningar för tokenkompatibilitet

Vi har gjort ändringar på viktiga anspråk i säkerhetstoken som sänds av Azure AD B2C. Detta gjordes för att förbättra vår standardprotokoll support och för bättre samverkan med tredjeparts-identity-bibliotek. Om du vill behålla befintliga appar kan vi dock skapas om du vill att kunder kan anmäla sig vid behov följande egenskaper:

* **Utfärdaren (iss) anspråk**: identifierar Azure AD B2C-klient som utfärdade token.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: Det är standardvärdet.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Det här värdet omfattar ID: N för både B2C-klient och den princip som används i tokenbegäran. Om din app eller biblioteket måste Azure AD B2C måste vara kompatibel med den [OpenID Connect identifiering 1.0-specifikationen](http://openid.net/specs/openid-connect-discovery-1_0.html), Använd det här värdet.
* **Ämne (under) anspråk**: identifierar entiteten, d.v.s. användaren som Assert token information.
  * **ObjectID**: Detta är standardvärdet. Objekt-ID för användaren i katalogen i fylls den `sub` anspråk i token.
  * **Stöds inte**: det finns bara för bakåtkompatibilitet och vi rekommenderar att du växla till **ObjectID** så snart du har.
* **Anspråk som representerar princip-ID**: identifierar Anspråkstypen som fylls i princip-ID som används i token.
  * **tfp**: Detta är standardvärdet.
  * **ACR**: det finns bara för bakåtkompatibilitet och vi rekommenderar att du växla till `tfp` när du ska kunna.

## <a name="session-behavior"></a>Sessionsbeteende

Azure AD B2C stöder den [autentiseringsprotokollet OpenID Connect](active-directory-b2c-reference-oidc.md) för att aktivera säker inloggning till webbprogram. Dessa är egenskaper som du kan använda för att hantera web-program körs:

* **Webbprogrammet session-livstid (minuter)**: livslängden för Azure AD B2C sessions-cookie som lagras på användarens webbläsare vid autentiseringen.
  * Standard = 1 440 minuter.
  * Minsta (sammanlagt) = 15 minuter.
  * Maximalt (sammanlagt) = 1 440 minuter.
* **Tidsgräns för Web app**: om den här växeln anges till **absolut**, om användaren tvingas att autentisera på nytt efter hur lång tid som anges av **webbprogrammet session-livstid (minuter)** långa. Om den här växeln anges till **rullande** (standardinställningen), användaren förblir inloggad så länge användaren är aktiv kontinuerligt i webbprogrammet.

Det här är några användningsområden som kan aktiveras med hjälp av egenskaperna:

* Uppfyller kraven för din bransch säkerhet och efterlevnad genom att ange lämplig programmet webbsessionen livslängd.
* Tvinga autentiseras igen efter en angiven tidsperiod när användaren interagerar med en hög säkerhet som en del av ditt webbprogram. 

    > [!NOTE]
    > De här inställningarna är inte tillgängliga för principer för återställning av lösenord.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Enkel inloggning (SSO) konfiguration
Om du har flera program och principer i din B2C-klient kan du hantera användarinteraktioner mellan dem med hjälp av den **konfiguration för enkel inloggning** egenskapen. Du kan ange egenskapen till någon av följande inställningar:

* **Klient**: det här är standardinställningen. Den här inställningen tillåter flera program och principer i din B2C-klient för att dela samma användarsessionen. Till exempel när en användare loggar in på ett program, Contoso Shopping kan han eller hon kan också sömlöst logga in på en annan en, Contoso-apotek, vid åtkomst till den.
* **Programmet**: du kan underhålla en användarsession endast för ett program, oberoende av andra program. Till exempel om du vill att användaren att logga in på Contoso apotek (med samma autentiseringsuppgifter), även om han eller hon är redan inloggad Contoso Shopping, ett annat program på samma B2C-klient. 
* **Principen**: du kan underhålla en användarsession endast för en princip, oberoende av de program som använder den. Till exempel om användaren har redan loggat in och slutfört steg en Multi-Factor authentication (MFA), kan han eller hon få tillgång till högre säkerhet delar av flera program så länge sessionen som är knutna till principen inte upphör.
* **Inaktiverade**: Detta tvingar användaren att köra via transporten hela användaren på varje körning av principen. T.ex, på så sätt kan flera användare att logga i tillämpningsprogrammet (i en delad skrivbord scenariot), även när en användare är inloggad under hela tiden.

    > [!NOTE]
    > De här inställningarna är inte tillgängliga för principer för återställning av lösenord.
    > 
    > 

