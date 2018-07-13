---
title: Vad är Azure Active Directory B2C? | Microsoft Docs
description: Läs mer om hur du kan skapa och hantera programmets inloggningsfunktion med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 04/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6949ab89cf806818783c86199e6df334e263b046
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440889"
---
# <a name="what-is-azure-active-directory-b2c"></a>Vad är Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C är en identitetshanteringstjänst som hjälper dig att anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Detta omfattar program som har utvecklas för bland annat iOS, Android och .NET. Azure AD B2C möjliggör dessa åtgärder samtidigt som kundernas identiteter skyddas.

Du kan konfigurera ett program som registrerats med Azure AD B2C att utföra olika åtgärder för identitetshantering. Några exempel är:

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

Azure AD-B2C stöder [OpenID Connect](https://openid.net/connect/) för alla kundupplevelser. I Azure AD B2C-implementeringen av OpenID Connect initierar ditt program denna användarresa genom att utfärda autentiseringsförfrågningar till Azure AD B2C. Resultatet av begäran är en `id_token`. Den här säkerhetstoken representerar kundens identitet.

Alla program som använder Azure AD B2C måste registreras i en Azure AD B2C-klientorganisation med hjälp av Azure-portalen. Registreringsprocessen samlar in och tilldelar värden till ditt program. Dessa värden inkluderar ett program-ID som unikt identifierar programmet och en omdirigerings-URI som kan användas för att dirigera svar tillbaka till den.

Interaktionen för alla program följer ett liknande övergripande mönster:

1. Programmet dirigerar kunden till att köra en princip.
2. Kunden slutför principen enligt principdefinitionen.
3. Programmet tar emot en säkerhetstoken.
4. Programmet använder säkerhetstoken för att försöka komma åt en skyddad resurs.
5. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
6. Programmet uppdaterar säkerhetstoken med jämna mellanrum.

Anvisningarna kan skilja sig något beroende på vilken typ av program som du utvecklar.

Azure AD B2C interagerar med identitetsprovidrar, kunder, andra system och den lokala katalogen i sekvens för att slutföra en identitetsuppgift. Exempel: logga in en kund, registrera en ny kund eller återställa ett lösenord. Den underliggande plattformen som upprättar flerpartsförtroende och slutför de här stegen kallas för Identity Experience Framework (Ramverk för identitetsupplevelse). Det här ramverket och en princip (kallas även en användarresa eller en princip för betrott ramverk) definierar explicit aktörerna, åtgärderna, protokollen och sekvensen med de steg som ska slutföras.

Azure AD B2C skyddar mot överbelastningsattacker (DoS) och lösenordsattacker mot dina program på flera olika sätt. Azure AD B2C använder identifierings- och skyddstekniker som SYN-cookies och begränsningar för hastighet och anslutning för att skydda resurser mot DoS-attacker. Skydd ingår också för råstyrkebaserade lösenordsattacker och ordlisteattacker för lösenord.

#### <a name="built-in-policies"></a>Inbyggda principer

Varje begäran som skickas till Azure AD B2C anger en princip. En princip styr beteendet för hur programmet interagerar med Azure AD B2C. Inbyggda principer är fördefinierade för de vanligaste identitetsuppgifterna, till exempel registrering, inloggning och profilredigering.  Till exempel kan du med en registreringsprincip styra beteenden genom att konfigurera följande inställningar:

- Konton i sociala medier som kunden kan använda för att registrera sig för programmet
- Data som samlas in från kunden, till exempel förnamn eller postnummer
- Multi-Factor Authentication
- Utseendet och designen på alla registreringssidor
- Information som returneras till programmet

#### <a name="custom-policies"></a>Anpassade principer 

[Anpassade principer](active-directory-b2c-overview-custom.md) är konfigurationsfiler som definierar beteendet för Identity Experience Framework (Ramverk för identitetsupplevelse) i din Azure AD B2C-klientorganisation. Anpassade principer kan redigeras fritt för att slutföra många uppgifter. En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. 

Flera anpassade principer av olika typer kan användas i Azure AD B2C-klientorganisationen efter behov och kan återanvändas i olika program. Den här flexibiliteten gör att du kan definiera och ändra kundidentitetsupplevelsen med minimala eller inga ändringar i koden. Principer kan användas genom att lägga till en särskild frågeparameter till HTTP-autentiseringsbegäranden.

Anpassade principer kan användas till att styra användarresor på följande sätt:

- Definiera interaktion med API:er för att samla in ytterligare information, verifiera anspråk som skickas in av kunden eller utlösa externa processer.
- Ändra beteende baserat på anspråk från API:er eller från anspråk i katalogen såsom *migrationStatus*.
- Alla arbetsflöden som inte omfattas av inbyggda principer. Till exempel samla in information från en kund under en inloggningsupplevelse och utföra en auktoriseringskontroll för att komma åt en resurs.

### <a name="identity-providers"></a>Identitetsprovidrar

En identitetsprovider är en tjänst som autentiserar kundidentiteter och utfärdar säkerhetstoken. I Azure AD B2C kan du konfigurera ett antal identitetsprovidrar i din klientorganisation, till exempel ett Microsoft-konto, Facebook eller Amazon med flera. 

Om du vill konfigurera en identitetsprovider i din Azure AD B2C-klientorganisation måste du registrera programidentifieraren eller klientidentifieraren samt lösenordet eller klienthemligheten från det identitetsproviderprogram som du skapar. Den här identifieraren och lösenordet används sedan för att konfigurera ditt program.

### <a name="user-interface-experience"></a>Upplevelse för användargränssnittet

Merparten av det HTML- och CSS-innehåll som presenteras för kunder kan kontrolleras. Genom att använda funktionen för anpassning av sid-UI kan du anpassa utseendet och känslan för vilken princip som helst. Du kan även hålla varumärke och grafik konsekventa mellan programmet och Azure AD B2C.

Azure AD B2C kör koden i kundens webbläsare och använder en modern metod som kallas Cross-Origin Resource Sharing (CORS, Resursdelning mellan ursprung). Först anger du en URL i en princip med anpassat HTML-innehåll. Azure AD B2C sammanfogar UI-element med HTML-innehåll som läses in från din URL och visar sedan sidan för kunden.

Du kan skicka parametrar till Azure AD B2C i en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Hur kommer jag igång med Azure AD B2C?

I Azure AD B2C representerar en klientorganisation din organisation och är en katalog med användare. Varje Azure AD B2C-klientorganisation är separat och åtskild från andra Azure AD B2C-klientorganisationer. En klientorganisation innehåller information om de kunder som har registrerat sig för att använda programmet. Exempel: lösenord, profildata och behörigheter.

Du måste länka din Azure AD B2C-klientorganisation till din Azure-prenumeration för att aktivera alla funktioner och betala för användningsavgifter. Om du vill tillåta att Azure AD B2C-kunder loggar in till ditt program måste du registrera programmet i Azure AD B2C-klientorganisation.

Innan du konfigurerar programmet att använda Azure AD B2C måste du först skapa en Azure AD B2C-klientorganisation och registrera ditt program. För att registrera ditt program slutför du stegen i [Självstudie: Registrera ett program för att aktivera registrering och inloggning med Azure AD B2C](tutorial-register-applications.md).
  
Om du är ASP.NET-webbprogramutvecklare konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett webbprogram för autentisering med konton med Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Om du är utvecklare av skrivbordsprogram konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett skrivbordsprogram för autentisering med konton med Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Om du är utvecklare av ensidesprogram som använder Node.js konfigurerar du programmet för att autentisera konton med hjälp av stegen i [Självstudie: Aktivera ett ensidesprogram för autentisering med konton med Azure AD B2C](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Nästa steg

Börja konfigurera ditt program för registrerings- och inloggningsupplevelsen genom att fortsätta till självstudien.

> [!div class="nextstepaction"]
> [Självstudie: Registrera ett program för att aktivera registrering och inloggning med Azure AD B2C](tutorial-register-applications.md)
