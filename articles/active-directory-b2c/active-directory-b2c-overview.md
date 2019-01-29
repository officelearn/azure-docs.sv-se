---
title: Vad är Azure Active Directory B2C? | Microsoft Docs
description: Läs mer om hur du skapar och hanterar programmets inloggningsfunktion med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b667934fefe0b9afb5aeca786839f5e105be2c4d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845001"
---
# <a name="what-is-azure-active-directory-b2c"></a>Vad är Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C är en identitetshanteringstjänst som hjälper dig att anpassa och styra hur kunderna registrerar interagerar med ditt program. Den här interaktionen omfattar registrering, inloggning och hantering av deras profiler när kunder använder dina program. Du har valet med program för iOS, Android och .NET med flera. Azure AD B2C möjliggör dessa åtgärder samtidigt som kundernas identiteter skyddas.

Du konfigurerar ett program som registrerats med Azure AD B2C att utföra många åtgärder för identitetshantering. Några exempel är:

- Göra så att en kund kan registrera sig för att använda ditt registrerade program
- Göra så att en registrerad kund kan logga in och börja använda programmet
- Göra så att en registrerad kund kan redigera sin profil
- Aktivera multifaktorautentisering i programmet
- Göra så att en kund kan registrera sig och logga in med specifika identitetsprovidrar
- Bevilja åtkomst från ditt program till API:er som du skapar
- Anpassa utseende och design för registrerings- och inloggningsfunktionerna
- Hantera sessioner för enkel inloggning för programmet

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Vad behöver jag tänka på innan jag använder Azure AD B2C?

- Hur vill jag att kunden ska interagera med mitt program?
- Vilken användarupplevelse för användargränssnitt (UI) vill jag tillhandahålla till kunderna?
- Vilka identitetsprovidrar vill jag att kunderna ska kunna välja mellan i programmet?
- Kräver inloggningsprocessen att ytterligare API:er körs?

### <a name="customer-interaction"></a>Kundinteraktion

Azure AD-B2C stöder [OpenID Connect](https://openid.net/connect/) för alla kundupplevelser. I Azure AD B2C-implementeringen av OpenID Connect startar ditt program användarresan genom att utfärda autentiseringsbegäranden till Azure AD B2C. Resultatet av begäran är en `id_token`. Den här säkerhetstoken definierar kundens identitet.

Alla program som använder Azure AD B2C måste registreras i en Azure AD B2C-klientorganisation med hjälp av Azure-portalen. Registreringsprocessen samlar in och tilldelar värden till ditt program. Dessa värden innefattar ett program-ID som unikt identifierar programmet. Ett omdirigerings-URI definieras som används för att dirigera svar tillbaka till programmet.

Interaktionen för alla program följer ett liknande övergripande mönster:

1. Programmet dirigerar kunden till att köra en princip.
2. Kunden slutför principen enligt principdefinitionen.
3. Programmet tar emot en säkerhetstoken.
4. Programmet använder säkerhetstoken för att försöka komma åt en skyddad resurs.
5. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar säkerhetstoken med jämna mellanrum.

Dessa steg skiljer sig något beroende på vilken typ av program som du utvecklar.

Azure AD B2C interagerar med identitetsprovidrar, kunder, andra system och den lokala katalogen i sekvens för att slutföra en identitetsuppgift. Exempel: logga in en kund, registrera en ny kund eller återställa ett lösenord. Den underliggande plattformen som upprättar flerpartsförtroende och slutför de här stegen kallas för Identity Experience Framework (Ramverk för identitetsupplevelse). Det här ramverket och en princip (kallas även en användarresa eller en princip för betrott ramverk) definierar explicit aktörerna, åtgärderna, protokollen och sekvensen med de steg som ska slutföras.

Azure AD B2C skyddar mot överbelastningsattacker (DoS) och lösenordsattacker mot dina program. Azure AD B2C använder identifierings- och skyddstekniker som SYN-cookies och begränsningar för hastighet och anslutning för att skydda resurser mot DoS-attacker. Skydd ingår också för råstyrkebaserade lösenordsattacker och ordlisteattacker för lösenord.

#### <a name="user-flows"></a>Användarflöden

Varje begäran som skickas till Azure AD B2C anger ett användarflöde, som är en princip som styr beteendet för hur programmet interagerar med Azure AD B2C. Fördefinierade användarflöden för de flesta identitetsuppgifter, till exempel registrering, är tillgängliga i Azure AD B2C-portalen.  Till exempel kan du med ett registreringsarbetsflöde styra beteenden genom att konfigurera följande inställningar:

- Konton i sociala medier som kunden använder för att registrera sig för programmet
- Data som samlas in från kunden, till exempel förnamn eller postnummer
- Multi-Factor Authentication
- Utseendet och designen på alla registreringssidor
- Information som returneras till programmet

#### <a name="custom-policies"></a>Anpassade principer 

[Anpassade principer](active-directory-b2c-overview-custom.md) är konfigurationsfiler som definierar beteendet för [Identity Experience Framework](trustframeworkpolicy.md) (Ramverk för identitetsupplevelse) i din Azure AD B2C-klientorganisation. Anpassade principer kan ändras för att slutföra många uppgifter. En anpassad princip är en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. 

Anpassade principer av olika typer används i din Azure AD B2C-klientorganisation efter behov och kan återanvändas i olika program. Den här flexibiliteten gör att du kan definiera och ändra kundidentitetsupplevelsen med minimala eller inga ändringar i koden. Principer används genom att en särskild frågeparameter läggs till i HTTP-autentiseringsbegäranden.

Anpassade principer används till att styra användarresor på följande sätt:

- Definiera interaktion med API:er för att samla in ytterligare information, verifiera anspråk som skickas in av kunden eller utlösa externa processer.
- Ändra beteende baserat på anspråk från API:er eller från anspråk i katalogen såsom *migrationStatus*.
- Alla arbetsflöden som inte omfattas av inbyggda principer. Till exempel samla in information från en kund under en inloggningsupplevelse och utföra en auktoriseringskontroll för att komma åt en resurs.

### <a name="identity-providers"></a>Identitetsprovidrar

En identitetsprovider är en tjänst som autentiserar kundidentiteter och utfärdar säkerhetstoken. I Azure AD B2C konfigurerar du ett antal identitetsprovidrar i din klientorganisation, till exempel ett [Microsoft-konto](active-directory-b2c-setup-msa-app.md), [Facebook](active-directory-b2c-setup-fb-app.md) eller [Amazon](active-directory-b2c-setup-amzn-app.md) med flera. 

Om du vill konfigurera en identitetsprovider i din Azure AD B2C-klientorganisation måste du registrera programidentifieraren eller klientidentifieraren samt lösenordet eller klienthemligheten från det identitetsproviderprogram som du skapar. Den här identifieraren och lösenordet används sedan för att konfigurera ditt program.

### <a name="user-interface-experience"></a>Upplevelse för användargränssnittet

Merparten av det HTML- och CSS-innehåll som presenteras för kunder är kontrollerbart. Genom att använda funktionen för anpassning av sid-UI kan du anpassa utseendet och känslan för vilken princip som helst. Du håller varumärke och grafik konsekventa mellan programmet och Azure AD B2C genom att använda den här anpassningsfunktionen.

Azure AD B2C kör koden i kundens webbläsare och använder en modern metod som kallas Cross-Origin Resource Sharing (CORS, Resursdelning mellan ursprung). Först anger du en URL i en princip med anpassat HTML-innehåll. Azure AD B2C sammanfogar UI-element med HTML-innehåll som läses in från din URL och visar sedan sidan för kunden.

Du skickar parametrar till Azure AD B2C i en frågesträng. Genom att skicka parametern till HTML-slutpunkten ändras sidinnehållet dynamiskt. Till exempel ändrar du bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Hur kommer jag igång med Azure AD B2C?

I Azure AD B2C representerar en klientorganisation din organisation och är en katalog med användare. Varje Azure AD B2C-klientorganisation är separat och åtskild från andra Azure AD B2C-klientorganisationer. En klientorganisation innehåller information om de kunder som har registrerat sig för att använda programmet. Exempel: lösenord, profildata och behörigheter.

Länka din Azure AD B2C-klientorganisation till din Azure-prenumeration för att aktivera alla funktioner och betala för användningsavgifter. För att tillåta dina kunder att logga in på ditt program registrerar du det i en Azure AD B2C-klientorganisation.

Innan du konfigurerar programmet att använda Azure AD B2C måste du först skapa en Azure AD B2C-klientorganisation och registrera ditt program. Registrera ditt program genom att slutföra stegen i [Självstudie: Registrera ett program för att aktivera registrering och inloggning med hjälp av Azure AD B2C](tutorial-register-applications.md).
  
Om du är ASP.NET-webbappsutvecklare konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett webbprogram för autentisering med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Om du utvecklar skrivbordsprogram konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett skrivbordsprogram för autentisering med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Om du utvecklar ensidesapplikationer med hjälp av Node.js konfigurerar du programmet att autentisera kontor via stegen i [Självstudie: Aktivera en ensidesapplikation för autentisering med konton med hjälp av Azure AD B2C](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Nästa steg

Börja konfigurera ditt program för registrerings- och inloggningsupplevelsen genom att fortsätta till självstudien.

> [!div class="nextstepaction"]
> [Självstudier: Skapa en Azure Active Directory B2C-klientorganisation](tutorial-create-tenant.md)
