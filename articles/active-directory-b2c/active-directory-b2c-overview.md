---
title: Vad är Azure Active Directory B2C? | Microsoft Docs
description: Läs mer om hur du skapar och hanterar identitetsupplevelser, till exempel registrering, inloggning och profilhantering i ditt program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 33c6f9e06439ebeb17608aa374532ac4f7a020cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701361"
---
# <a name="what-is-azure-active-directory-b2c"></a>Vad är Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C är en business-to-consumer identity management-tjänsten. Med den här tjänsten kan du anpassa och styra hur användarna på ett säkert sätt samverkar med webb-, skrivbords-, mobil- eller ensidesapplikationer. Med Azure AD B2C kan användarna registrera sig, logga in, återställa lösenord och redigera profiler. Azure AD B2C implementerar en form av OpenID Connect- och OAuth 2.0-protokoll. Det viktigaste i implementeringen av dessa protokoll är de säkerhetstokens och deras anspråk som gör det möjligt att ge säker åtkomst till resurser.

En *användarresa* är en begäran som anger en princip, vilken styr beteendet för hur användaren och programmet interagerar med Azure AD B2C. Det finns två tillgängliga sökvägar som du kan använda för att definiera användarresor i Azure AD B2C. 

Om du är programutvecklare, med eller utan kunskaper om identitet, kan du välja att definiera användarflöden med vanlig identitet med hjälp av Azure-portalen. Om du är identitetsexpert, systemintegrerare, konsult eller ett internt identitetsteam, om du känner till OpenID Connect-flöden samt förstår identitetsproviders och anspråksbaserad autentisering, kan du välja XML-baserade anpassade principer.

Innan du börjar definiera en användarresa, måste du skapa en Azure AD B2C-klientorganisation samt registrera ditt program och API i klientorganisationen. När du har slutfört åtgärderna kan du börja definiera en användarresa med användarflöden eller anpassade principer. Du kan också välja att lägga till eller ändra identitetsproviders, eller anpassa hur användaren upplever resan.

## <a name="protocols-and-tokens"></a>Protokoll och token

Azure AD B2C stöder [OpenID Connect- och OAuth 2.0-protokollen](active-directory-b2c-reference-protocols.md) för användarresor. I Azure AD B2C-implementeringen av OpenID Connect startar ditt program användarresan genom att utfärda autentiseringsbegäranden till Azure AD B2C. 

Resultatet av en begäran till Azure AD B2C är en säkerhetstoken som t.ex. en [ID-token eller åtkomsttoken](active-directory-b2c-reference-tokens.md). Denna säkerhetstoken definierar användarens identitet. Tokens tas emot från Azure AD B2C-slutpunkter, till exempel en `/token`- eller `/authorize`-slutpunkt. Med dessa tokens får du tillgång till anspråk som kan användas för att verifiera en identitet och tillåta åtkomst till säkra resurser.

## <a name="tenants-and-applications"></a>Klientorganisationer och program

I Azure AD B2C representerar en *klientorganisation* din organisation och är en katalog med användare. Varje Azure AD B2C-klientorganisation är separat och åtskild från andra Azure AD B2C-klientorganisationer. Du kanske redan har en Azure Active Directory-klientorganisation, men Azure AD B2C-klientorganisationen är en annan klientorganisation. En klientorganisation innehåller information om de användare som har registrerat sig för att använda programmet. Exempel: lösenord, profildata och behörigheter. Mer information finns i [Självstudie: Skapa en Azure Active Directory B2C-klientorganisation](tutorial-create-tenant.md).

Innan du konfigurerar programmet till att använda Azure AD B2C, måste du först registrera det i klientorganisationen med Azure-portalen. Registreringsprocessen samlar in och tilldelar värden till ditt program. Dessa värden inkluderar ett program-ID som unikt identifierar programmet och en omdirigerings-URI som används för att dirigera svar tillbaka till programmet.

Interaktionen för alla program följer ett liknande övergripande mönster:

1. Programmet dirigerar användaren till att köra en princip.
2. Användaren uppfyller principen enligt principdefinitionen.
3. Programmet tar emot en token.
4. Programmet använder denna token till att försöka komma åt en resurs.
5. Resursservern verifierar token för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar token med jämna mellanrum.

Registrera webbappen genom att slutföra stegen i [Självstudie: Registrera ett program för att aktivera registrering och inloggning med hjälp av Azure AD B2C](tutorial-register-applications.md). Du kan också [lägga till ett webb-API-program i din Azure Active Directory B2C-klientorganisation](add-web-application.md) eller [lägga till ett internt klientprogram i din Azure Active Directory B2C-klientorganisation](add-native-application.md).

## <a name="user-journeys"></a>Användarresor

Principen i en användarresa kan definieras som ett [användarflöde](active-directory-b2c-reference-policies.md) eller en [anpassad princip](active-directory-b2c-overview-custom.md). Fördefinierade användarflöden för de flesta identitetsuppgifter, till exempel registrering och profilredigering, är tillgängliga i Azure-portalen.

Med användarresor kan du styra beteenden genom att konfigurera följande inställningar:

- Konton i sociala medier som användaren använder för att registrera sig för programmet
- Data som samlas in från användaren, till exempel förnamn eller postnummer
- Multi-Factor Authentication
- Utseendet på sidor
- Information som returneras till programmet

Anpassade principer är konfigurationsfiler som definierar beteendet för [Identity Experience Framework](trustframeworkpolicy.md) i din Azure AD B2C-klientorganisation. Identity Experience Framework är den underliggande plattform som upprättar flerpartsförtroende och slutför stegen i en användarresa. 

Anpassade principer kan ändras för att slutföra många uppgifter. En anpassad princip är en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. Det finns ett [startpaket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) för anpassade principer som kan användas till att aktivera vanliga identitetsuppgifter. 

Anpassade principer eller användarflöden av olika typer används i din Azure AD B2C-klientorganisation efter behov och kan återanvändas i olika program. Den här flexibiliteten gör att du kan definiera och ändra användarens identitetsupplevelse med minimala eller inga ändringar i koden. Principer används genom att en särskild frågeparameter läggs till i HTTP-autentiseringsbegäranden. Om du vill skapa en egen anpassad princip kan du läsa [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Identitetsprovidrar 

Du kanske vill att användarna ska kunna logga in på dina program med olika identitetsprovidrar. En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. Du kan lägga till identitetsprovidrar som stöds av Azure AD B2C med hjälp av Azure-portalen. 

Du använder vanligtvis bara en identitetsprovider i ditt program, men du har möjlighet att lägga till fler. Om du vill konfigurera en identitetsprovider i din Azure AD B2C-klientorganisation måste du först skapa ett program på identitetsproviderns utvecklarwebbplats och sedan registrera programidentifieraren eller klientidentifieraren samt lösenordet eller klienthemligheten från det identitetsproviderprogram som du skapar. Den här identifieraren och lösenordet används sedan för att konfigurera ditt program. 

I följande artiklar beskrivs stegen för att lägga till några av de vanliga identitetsprovidrarna i användarflöden:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft-konto](active-directory-b2c-setup-msa-app.md)

I följande artiklar beskrivs stegen för att lägga till några av de vanliga identitetsprovidrarna i anpassade principer:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft-konto](active-directory-b2c-custom-setup-msa-idp.md)

Mer information finns i [Självstudie: Lägga till identitetsprovidrar i dina program i Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Anpassning av sida

Merparten av det HTML- och CSS-innehåll som presenteras för kunderna i en användarresa är kontrollerbart. Du kan anpassa utseendet på alla anpassade principer eller användarnas flöden med hjälp av sidoanpassning. Du håller varumärke och grafik konsekventa mellan programmet och Azure AD B2C genom att använda den här anpassningsfunktionen. 

Azure AD B2C kör koden i användarens webbläsare och använder en modern metod som kallas Resursdelning för korsande ursprung (CORS). Först anger du en URL i en princip med anpassat HTML-innehåll. Azure AD B2C sammanfogar användargränssnittets element med HTML-innehåll som läses in från din URL och visar sedan sidan för användaren.

Du skickar parametrar till Azure AD B2C i en frågesträng. Genom att skicka parametern till HTML-slutpunkten ändras sidinnehållet dynamiskt. Till exempel kan du ändra bakgrundsbilden på registrerings- eller inloggningssidan baserat på en parameter som du skickar från ditt webb- eller mobilprogram.

Om du vill anpassa sidor i ett användarflöde kan du gå vidare till [Självstudie: Anpassa gränssnittet för användarupplevelser i Azure Active Directory B2C](tutorial-customize-ui.md). Om du vill anpassa sidor i en anpassad princip kan du läsa mer i [Anpassa användargränssnittet i ditt program med en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) eller [Konfigurera användargränssnittet med dynamiskt innehåll med hjälp av anpassade principer i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Utvecklarresurser

### <a name="client-applications"></a>Klientprogram

Du kan välja mellan program för [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) och .NET med flera. Azure AD B2C möjliggör dessa åtgärder samtidigt som användarnas identiteter skyddas.

Om du är ASP.NET-webbappsutvecklare konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett webbprogram för autentisering med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Om du utvecklar skrivbordsprogram konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett skrivbordsprogram för autentisering med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Om du utvecklar ensidesapplikationer med hjälp av Node.js konfigurerar du programmet att autentisera konton via stegen i [Självstudie: Aktivera en ensidesapplikation för autentisering med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>API:er
Om dina klient- eller webbappar behöver anropa API:er kan du konfigurera säker åtkomst till dessa resurser i Azure AD B2C.

Om du är ASP.NET-webbappsutvecklare konfigurerar du programmet till att anropa ett skyddat API med hjälp av stegen i [Självstudie: Bevilja åtkomst till ett ASP.NET webb-API med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Om du är programutvecklare konfigurerar du programmet till att anropa ett skyddat API med hjälp av stegen i [Självstudie: Ge åtkomst till ett Node.js-webb-API från en skrivbordsapp med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Om du utvecklar ensidesapplikationer med hjälp av Node.js konfigurerar du programmet att autentisera kontor via stegen i [Självstudie: Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapplikation med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Du kan lägga till egen JavaScript-kod på klientsidan i dina program i Azure AD B2C. Om du vill konfigurera JavaScript i ditt program måste du definiera ett [sidkontrakt](page-contract.md) och aktivera [JavaScript](javascript-samples.md) i dina användarflöden eller anpassade principer.

### <a name="user-accounts"></a>Användarkonton

Många vanliga klienthanteringsuppgifter måste utföras via programmering. En primärt exempel är användarhantering. Du kan behöva migrera ett befintligt användararkiv till en Azure AD B2C-klientorganisation. Du kanske vill vara värd för användarregistrering på sidan och skapa användarkonton i Azure AD B2C-katalogen i bakgrunden. Dessa typer av uppgifter kräver möjligheten att skapa, läsa, uppdatera och ta bort användarkonton. Du kan utföra dessa uppgifter med hjälp av [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Nästa steg

Börja konfigurera ditt program för registrerings- och inloggningsupplevelsen genom att fortsätta till självstudien.

> [!div class="nextstepaction"]
> [Självstudie: Skapa en Azure Active Directory B2C-klientorganisation](tutorial-create-tenant.md)
