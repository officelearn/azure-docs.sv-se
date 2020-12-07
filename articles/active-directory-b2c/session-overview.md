---
title: SSO-sessioner i Azure Active Directory B2C | Microsoft Docs
description: En översikt över hur du konfigurerar sessionens beteende i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e02323df3a12c4a74de1fb62b36762fc739e9e5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750449"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C session

Enkel inloggning (SSO) lägger till säkerhet och bekvämlighet när användare loggar in mellan program i Azure Active Directory B2C (Azure AD B2C). Den här artikeln beskriver de enkla inloggnings metoderna som används i Azure AD B2C och hjälper dig att välja den lämpligaste SSO-metoden när du konfigurerar principen.

Med enkel inloggning loggar användarna in en gång med ett enda konto och får åtkomst till flera program. Programmet kan vara ett webb-, mobil-eller enda webb program, oavsett plattform eller domän namn.

När användaren loggar in till ett program från början behåller Azure AD B2C en cookie-baserad session. Vid efterföljande autentiseringsbegäranden läser Azure AD B2C och verifierar den cookie-baserade sessionen och utfärdar en åtkomsttoken utan att användaren uppmanas att logga in igen. Om den cookie-baserade sessionen upphör att gälla eller blir ogiltig, uppmanas användaren att logga in igen.  

## <a name="sso-session-types"></a>Typer av SSO-sessioner

Integrering med Azure AD B2C omfattar tre typer av SSO-sessioner:

- **Azure AD B2C** -session hanteras av Azure AD B2C
- **Federerad identitetsprovider** – session som hanteras av identitets leverantören, till exempel Facebook, Salesforce eller Microsoft-konto
- Programsession som hanteras av webb-, mobil-eller enskild sida **-program**

![Session för enkel inloggning](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

När en användare autentiseras med ett lokalt eller socialt konto lagrar Azure AD B2C en cookie-baserad session i användarens webbläsare. Cookien lagras under Azure AD B2C klient domän namnet, till exempel `https://contoso.b2clogin.com` .

Om en användare ursprungligen loggar in med ett federerat konto och sedan loggar in på samma app eller i en annan app, Azure AD B2C försöker hämta en ny åtkomsttoken från den federerade identitets leverantören under tids perioden för sessionen (Time to Live eller TTL). Om den federerade identitets leverantören har upphört att gälla eller är ogiltig, uppmanas användaren att ange sina autentiseringsuppgifter för den federerade identitets leverantören. Om sessionen fortfarande är aktiv (eller om användaren har loggat in med ett lokalt konto i stället för ett federerat konto), auktoriserar Azure AD B2C användaren och eliminerar ytterligare prompter.

Du kan konfigurera sessionens beteende, inklusive sessions-TTL och hur Azure AD B2C delar sessionen över principer och program.

### <a name="federated-identity-provider-sso"></a>Federerad autentiseringsprovider för identitetsprovider

En social-eller företags identitets leverantör hanterar sin egen session. Cookien lagras under identitets leverantörens domän namn, till exempel `https://login.salesforce.com` . Azure AD B2C styr inte den federerade identitets leverantören. I stället bestäms sessionens beteende av den federerade identitets leverantören. 

Föreställ dig följande scenario:

1. En användare loggar in på Facebook för att kontrol lera deras flöde.
2. Senare öppnar användaren ditt program och påbörjar inloggnings processen. Programmet omdirigerar användaren till Azure AD B2C för att slutföra inloggnings processen.
3. På sidan Azure AD B2C registrering eller inloggning väljer användaren att logga in med sitt Facebook-konto. Användaren omdirigeras till Facebook. Om det finns en aktiv session på Facebook uppmanas användaren inte att ange sina autentiseringsuppgifter och dirigeras omedelbart om till Azure AD B2C med en Facebook-token.

### <a name="application-sso"></a>SSO för program

En webb-, mobil-eller enskild sida-applikation kan skyddas av OAuth-åtkomst, ID-token eller SAML-token. När en användare försöker komma åt en skyddad resurs i appen kontrollerar appen om det finns en aktiv session på program sidan. Om det inte finns någon app-session eller om sessionen har gått ut, tar appen användaren Azure AD B2C till inloggnings sidan.

Programsessionen kan vara en cookie-baserad session som lagras under programmets domän namn, till exempel `https://contoso.com` . Mobila program kan lagra sessionen på ett annat sätt, men med hjälp av en liknande metod.

## <a name="azure-ad-b2c-session-configuration"></a>Konfiguration av Azure AD B2C-session

### <a name="session-scope"></a>Sessionens omfång

Azure AD B2C-sessionen kan konfigureras med följande omfång:

- **Klient** – den här inställningen är standard. Med den här inställningen kan flera program och användare flöda i B2C-klienten för att dela med sig av samma användarsession. När en användare till exempel loggar in på ett program, kan användaren också sömlöst logga in på en annan vid åtkomst till den.
- **Program** – med den här inställningen kan du underhålla en användarsession exklusivt för ett program, oberoende av andra program. Du kan till exempel använda den här inställningen om du vill att användaren ska logga in på Contoso apotek oavsett om användaren redan har loggat in på Contosos inköp.
- **Princip** – med den här inställningen kan du underhålla en användarsession exklusivt för ett användar flöde, oberoende av de program som använder den. Om användaren till exempel redan har loggat in och slutfört ett Multi-Factor Authentication (MFA)-steg, kan användaren få åtkomst till högre säkerhets delar av flera program, förutsatt att sessionen som är kopplad till användar flödet inte upphör att gälla.
- **Disabled** – inställningen tvingar användaren att köra genom hela användar flödet vid varje körning av principen.

### <a name="session-life-time"></a>Tid för session

**Tiden för sessionens livs längd** är den tid som den Azure AD B2C sessions-cookien lagras i användarens webbläsare efter lyckad autentisering. Du kan ställa in tiden för sessionens livs längd till ett värde mellan 15 och 720 minuter.

### <a name="keep-me-signed-in"></a>Håll mig inloggad

Funktionen [Håll mig inloggad](custom-policy-keep-me-signed-in.md) utökar sessionens livs längd genom att använda en beständig cookie. Sessionen förblir aktiv när användaren har stängt och öppnat webbläsaren igen. Sessionen återkallas bara när en användare loggar ut. Funktionen Håll mig inloggad gäller bara för inloggning med lokala konton.

Funktionen Håll mig inloggad har företräde framför sessionens livs längd. Om funktionen Håll mig inloggad är aktive rad och användaren väljer den, avgör den här funktionen när sessionen upphör att gälla. 

### <a name="session-expiry-type"></a>Utgångs typ för session

**Utgångs typen session** anger hur en session utökas av tids inställningen för sessionens livs längd eller inställningen Håll mig inloggad.

- **Rullande** – anger att sessionen är utökad varje gång användaren utför en cookie-baserad autentisering (standard).
- **Absolut** -visar att användaren tvingas att autentisera igen efter den angivna tids perioden.

## <a name="sign-out"></a>Logga ut

När du vill signera användaren från programmet räcker det inte att ta bort programmets cookies eller på annat sätt avsluta sessionen med användaren. Du måste omdirigera användaren till Azure AD B2C för att kunna logga ut. Annars kan användaren kunna autentisera till dina program igen utan att ange sina autentiseringsuppgifter igen.

Vid en inloggningsbegäran Azure AD B2C:

1. Invaliderar den Azure AD B2C cookie-baserade sessionen.
1. Försök att logga ut från federerade identitets leverantörer:
   - OpenId Connect – om den välkända konfigurations slut punkten för identitets leverantör anger en `end_session_endpoint` plats.
   - OAuth2 – om [identitets leverantörens metadata](oauth2-technical-profile.md#metadata) innehåller `end_session_endpoint` platsen.
   - SAML – om [identitets leverantörens metadata](saml-identity-provider-technical-profile.md#metadata) innehåller `SingleLogoutService` platsen.
1. Du kan också logga ut från andra program. Mer information finns i avsnittet om [enkel inloggning](#single-sign-out) .

> [!NOTE]
> Genom att använda [anpassade principer](custom-policy-overview.md)kan du inaktivera utloggningen från federerade identitets leverantörer, genom att ange metadata för den tekniska profilen för identitets leverantören `SingleLogoutEnabled` till `false` .

Utloggningen rensar användarens läge för enkel inloggning med Azure AD B2C, men det är inte säkert att användaren kan logga ut från sin session med social identitetsprovider. Om användaren väljer samma identitetsprovider vid en efterföljande inloggning, kan de autentiseras igen utan att ange sina autentiseringsuppgifter. Om en användare vill logga ut från programmet betyder det inte nödvändigt vis att de vill logga ut från sitt Facebook-konto. Men om lokala konton används avslutas användarens session korrekt.

### <a name="single-sign-out"></a>Enkel utloggning 


> [!NOTE]
> Den här funktionen är begränsad till [anpassade principer](custom-policy-overview.md).

När du omdirigerar användaren till Azure AD B2C utloggnings slut punkt (för både OAuth2-och SAML-protokoll) rensar Azure AD B2C användarens session från webbläsaren. Användaren kan dock fortfarande vara inloggad i andra program som använder Azure AD B2C för autentisering. Om du vill att dessa program ska kunna signera användaren samtidigt, skickar Azure AD B2C en HTTP GET-begäran till registrerade `LogoutUrl` för alla program som användaren för närvarande är inloggad på.


Program måste svara på den här begäran genom att rensa alla sessioner som identifierar användaren och returnera ett `200` svar. Om du vill stödja enkel utloggning i ditt program måste du implementera en `LogoutUrl` i program koden. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar sessionens beteende i användar flödet](session-behavior.md).
- Lär dig hur du [konfigurerar sessionens beteende i anpassade principer](session-behavior-custom-policy.md).
