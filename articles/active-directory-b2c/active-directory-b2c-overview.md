---
title: Vad är Azure Active Directory B2C? | Microsoft Docs
description: Läs mer om hur du kan skapa och hantera dina program inloggningen med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.openlocfilehash: ca9e45a214639da86cf8e0c4a39b3e3d6b6d6491
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory-b2c"></a>Vad är Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C är en identitetshanteringstjänst som hjälper dig att anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Detta omfattar program som har utvecklas för bland annat iOS, Android och .NET. Azure AD B2C kan åtgärderna samtidigt skydda kunden identiteter på samma gång.

Du kan konfigurera ett program som är registrerad med Azure AD B2C att utföra olika identity management-åtgärder. Några exempel är:

- Aktivera en kund för att registrera dig för att använda registrerade programmet
- Aktivera en kund registrerat dig för att logga in och börja använda programmet
- Aktivera loggat in kunder att redigera sin profil
- Aktivera Multi-Factor authentication i ditt program
- Aktivera av kunden att registrera och logga in med specifika identitetsleverantörer
- Bevilja åtkomst från ditt program till API: er som du skapar
- Anpassa utseendet och känslan av registrering och inloggning
- Hantera enkel inloggning-sessioner för ditt program

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Vad behöver jag tänka på innan du använder Azure AD B2C?

- Hur vill kunden att interagera med mitt program?
- Vad är användarupplevelsen för användargränssnittet (UI) som du vill ge företagets kunder tillgång?
- Vilka identitetsleverantörer jag vill låta kunder i mitt program välja?
- Kräver ytterligare API: er att köra i min inloggningsprocessen?

### <a name="customer-interaction"></a>Kundinteraktion

Azure AD B2C stöder [OpenID Connect](https://openid.net/connect/) alla kunden inträffar. I Azure AD B2C-implementeringen av OpenID Connect initierar programmet den här användaren resa genom att utfärda autentiseringsbegäranden till Azure AD B2C. Resultatet av begäran är en `id_token`. Den här säkerhetstoken representerar kundens identitet.

Alla program som använder Azure AD B2C måste registreras i en Azure AD B2C-klient som använder Azure portal. Registreringsprocessen samlar in och tilldelar värden till ditt program. Dessa värden är ett program-ID som unikt identifierar programmet och en omdirigerings-URI som kan användas för att dirigera svar tillbaka till den.

Interaktion mellan varje program följer ett liknande övergripande mönster:

1. Programmet anger att kunden ska köra en princip.
2. Kunden uppfyller principen enligt principdefinitionen.
3. Programmet tar emot en säkerhetstoken.
4. Programmet använder säkerhetstoken att få åtkomst till en skyddad resurs.
5. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar säkerhetstoken med jämna mellanrum.

De här stegen kan skilja sig något beroende på vilken typ av program som du skapar.

Azure AD B2C samverkar med identitetsleverantörer, kunder, andra system och den lokala katalogen i följd att slutföra en identity-aktivitet. Till exempel logga in en kund, registrera en ny kund eller återställa ett lösenord. Den underliggande plattformen som upprättar flerparti förtroende och slutför stegen kallas identitet upplevelse ramen. Det här ramverket och en princip (kallas även för en användare resa eller en princip för förtroende Framework) definierar aktörer, åtgärder, protokoll och ordningsföljden för att slutföra.

Azure AD B2C skyddar mot denial of service och lösenord attacker mot dina program på flera olika sätt. Azure AD B2C använder identifiering och minskning tekniker som SYN cookies och hastighet och anslutningen gränserna för att skydda resurser mot denial of service-attacker. Minskning ingår också för lösenord brute force-attacker och lösenord för ordlisteattacker.

#### <a name="built-in-policies"></a>Inbyggda principer

Varje begäran som skickas till Azure AD B2C anger en princip. En princip styr beteendet för hur programmet samverkar med Azure AD B2C. Inbyggda principerna är fördefinierade för de vanligaste uppgifterna i identitet som registrering, inloggning, och Redigera profil.  Till exempel kan en princip för registrering du styra beteenden genom att konfigurera följande inställningar:

- Sociala konton som kunden kan använda för att registrera dig för programmet
- Data som samlas in från kunden, till exempel förnamn eller postnummer
- Multi-Factor Authentication
- Utseendet på alla sidor för registrering
- Informationen som returneras till programmet

#### <a name="custom-policies"></a>Anpassade principer 

[Anpassade principer](active-directory-b2c-overview-custom.md) är konfigurationsfiler som definierar beteendet för ramverket identitet upplevelse i din Azure AD B2C-klient. Anpassade principer kan redigeras fullständigt för att slutföra många aktiviteter. En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. 

Flera anpassade principer för olika typer kan användas i Azure AD B2C-klienten efter behov och kan återanvändas i program. Den här flexibiliteten gör att du kan definiera och ändra kunden identitetsupplevelser med minimal eller utan ändringar av din kod. Principer kan användas genom att lägga till en särskild frågeparameter begäranden för HTTP-autentisering.

Anpassade principer kan användas för att styra användaren resor på följande sätt:

- Definiera interaktion med API: er för att samla in ytterligare information, kontrollera kunden tillhandahålls anspråk eller utlösa externa processer.
- Ändra beteende utifrån anspråk från API: er eller anspråk i katalogen som *migrationStatus*.
- Alla arbetsflöden som inte omfattas av inbyggda principer. Till exempel kontrollera samla in mer information från en kund under en inloggning och auktorisering en för att komma åt en resurs.

### <a name="identity-providers"></a>Identitetsprovidrar

En identitetsleverantör är en tjänst som autentiserar kunden identiteter och utfärdar säkerhetstoken. Du kan konfigurera ett antal identitetsleverantörer i din klientorganisation, till exempel ett Microsoft-konto, Facebook eller Amazon bland andra Azure AD B2C. 

Om du vill konfigurera en identitetsleverantör i din Azure AD B2C-klient, måste du registrera program-ID eller klient-ID och lösenord eller klienten hemlighet från providern identity-programmet som du skapar. Detta ID och lösenord används sedan för att konfigurera ditt program.

### <a name="user-interface-experience"></a>Gränssnittet användarupplevelse

De flesta av HTML- och CSS-innehåll som presenteras för kunder kan kontrolleras. Med hjälp av sidan anpassning gränssnittsfunktionen kan anpassa du utseendet och känslan av en princip. Du kan också upprätthålla märke och visual konsekvensen mellan dina program och Azure AD B2C.

Azure AD B2C Kör koden i kundens webbläsare och använder en modern metod som kallas Cross-Origin Resource Sharing (CORS). Först måste ange du en URL i en princip med anpassade HTML-innehåll. Azure AD B2C sammanfogas UI-element med HTML-innehåll som har lästs in från URL: en och visar sidan till kunden.

Du kan skicka parametrar till Azure AD B2C i en frågesträng. Genom att ange parametern till din HTML-slutpunkt, kan du ändra sidinnehållet dynamiskt. Du kan till exempel ändra bakgrundsbilden på Azure AD B2C registrering eller inloggning sidan baserat på en parameter som överförs från dina webb- eller mobila program.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Hur kommer jag igång med Azure AD B2C?

En klient representerar organisationen i Azure AD B2C och är en katalog med användare. Varje Azure AD B2C-klient är separat och åtskild från andra Azure AD B2C-klienter. En klient innehåller information om de kunder som har registrerat sig för att använda programmet. Till exempel lösenord, profildata och behörigheter.

Du måste länka din Azure AD B2C-klient till din Azure-prenumeration att aktivera alla funktioner och betalar avgifter för användning. Om du vill att Azure AD B2C kunder att logga in på ditt program måste du registrera ditt program i en Azure AD B2C-klient.

Innan du konfigurerar programmet ska använda Azure AD B2C måste du först skapa en Azure AD B2C-klient och registrera ditt program. För att registrera ditt program måste du slutföra stegen i [Självstudier: registrera ett program för att aktivera registrering och inloggning med Azure AD B2C](tutorial-register-applications.md).
  
Om du är programutvecklare ASP.NET web konfigurera ditt program för att autentisera konton med hjälp av stegen i [Självstudier: aktivera ett webbprogram att autentisera med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Om du skrivbordsprogram utvecklare kan skapa ditt program för att autentisera konton med hjälp av stegen i [Självstudier: aktivera ett skrivbordsprogram att autentisera med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Om du är en enkel sida programutvecklaren med Node.js, konfigurera ditt program för att autentisera konton med hjälp av stegen i [Självstudier: aktivera en enda sida-program för att autentisera med hjälp av Azure AD B2C-konton](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Nästa steg

Börja konfigurera ditt program för registrering och inloggning användarupplevelse genom att fortsätta att kursen.

> [!div class="nextstepaction"]
> [Självstudier: Registrera ett program för att aktivera registrering och inloggning med Azure AD B2C](tutorial-register-applications.md)
