---
title: 'Snabbstart: Access-webb-API:er för app – Microsoft identity platform | Azure'
description: I den här snabbstarten konfigurerar du en app som är registrerad på Microsofts identitetsplattform så att den innehåller omdirigerings-autentiseringsuppgifter, autentiseringsuppgifter eller behörigheter för åtkomst till webb-API:er.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 5e628626f2db49ff67d6d7ab425a3a19870b1ebd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240900"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Snabbstart: Konfigurera ett klientprogram för åtkomst till webb-API:er

I den här snabbstarten lägger du till omdirigera urier, autentiseringsuppgifter eller behörigheter för att komma åt webb-API:er för ditt program. Ett webb- eller konfidentiellt klientprogram måste upprätta säkra autentiseringsuppgifter för att delta i ett auktoriseringsbidragsflöde som kräver autentisering. Standardmetoden för autentisering som stöds av Azure-portalen är klient-ID + hemlig nyckel. Appen hämtar en åtkomsttoken under den här processen.

Innan en klient kan komma åt ett webb-API som exponeras av ett resursprogram, till exempel Microsoft Graph API, säkerställer ramverket för medgivande att klienten erhåller det behörighetsbidrag som krävs för de begärda behörigheterna. Som standard kan alla program begära behörigheter från Microsoft Graph API.

## <a name="prerequisites"></a>Krav

* Slutförande av [snabbstart: Registrera ett program med Microsofts identitetsplattform](quickstart-register-app.md).
* Granska [behörigheter och medgivande i slutpunkten för Microsoft-identitetsplattform](v2-permissions-and-consent.md).
* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet. Ställ in portalsessionen till den Azure AD-klient som du vill ha.
1. Sök efter och välj **Azure Active Directory**. Under **Hantera**väljer du **Appregistreringar**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen visas programmets **översikts-** eller huvudregistreringssida.

Använd följande procedurer för att konfigurera programmet för åtkomst till webb-API:er.

## <a name="add-redirect-uris-to-your-application"></a>Lägga till omdirigerings-URI:er för programmet

Du kan lägga till anpassade omdirigerings-URI:er och föreslagna omdirigerings-URI:er till ditt program. Så här lägger du till en anpassad omdirigerings-URI för webb- och offentliga klientprogram:

1. Välj **Autentisering**på sidan **Översikt för** appen .
1. Leta reda på **omdirigerings-URI:er**. Du kan behöva välja **Växla till den gamla upplevelsen**.
1. Välj den typ av program som du skapar: **Webb** eller **Offentlig klient/inbyggt (mobilt & skrivbord).**
1. Ange omdirigerings-URI för appen.

   * För webbappar anger du grundläggande URL för appen. Till exempel kan `http://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
   * För offentliga appar anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifikt `https://MyFirstApp`för ditt program, till exempel: .
1. Välj **Spara**.

Så här väljer du mellan föreslagna omdirigerings-URI:er för offentliga klienter:

1. Välj **Autentisering**på sidan **Översikt för** appen .
1. Leta reda på **föreslagna omdirigerings-URI:er för offentliga klienter (mobil, dator)**. Du kan behöva välja **Växla till den gamla upplevelsen**.
1. Välj en eller flera omdirigerings-URI:er för ditt program. Du kan också ange en anpassad omdirigerings-URI. Om du är osäker på vad du ska använda läser du biblioteksdokumentationen.
1. Välj **Spara**.

Vissa begränsningar gäller för omdirigerings-URI:er. Mer information finns i [Omdirigera URL-begränsningar och begränsningar för URI/svar.](https://docs.microsoft.com/azure/active-directory/develop/reply-url)

> [!NOTE]
> Prova den nya **autentiseringsinställningsupplevelsen** där du kan konfigurera inställningar för ditt program baserat på den plattform eller enhet som du vill rikta in dig på.
>
> Om du vill se den här vyn väljer du **Prova den nya upplevelsen** på sidan **Autentisering.**
>
> ![Klicka på "Prova den nya upplevelsen" för att se plattformskonfigurationsvyn](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Detta tar dig till den [nya **sidan Plattformskonfigurationer** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurera avancerade inställningar för ditt program

Beroende på vilket program du registrerar finns det några ytterligare inställningar som du kan behöva konfigurera, till exempel:

* **Url för utloggning**.
* För ensidiga appar kan du aktivera **implicit beviljande** och välja de token som du vill att auktoriseringsslutpunkten ska utfärda.
* För skrivbordsappar som hämtar token med integrerad Windows-autentisering, enhetskodflöde eller användarnamn/lösenord i avsnittet **Standardklienttyp** anger du **inställningen Behandla som offentlig klientinställning** till **Ja**.
* Konfigurera **Live SDK-stöd**för äldre appar som använde Live SDK för att integrera med Microsoft-kontotjänsten . Nya appar behöver inte den här inställningen.
* **Standardklienttyp**.
* **Kontotyper som stöds**.

### <a name="modify-supported-account-types"></a>Ändra kontotyper som stöds

Kontotyperna **Som stöds** anger vem som kan använda programmet eller komma åt API:et.

Om du har konfigurerat de kontotyper som stöds när du registrerade programmet kan du bara ändra den här inställningen med hjälp av programmanifestredigeraren om:

* Du ändrar kontotyper från **AzureADMyOrg** eller **AzureADMultipleOrgs** till **AzureADandPersonalMicrosoftAccount**, eller tvärtom, eller
* Du ändrar kontotyper från **AzureADMyOrg** till **AzureADMultipleOrgs**eller tvärtom.

Om du vill ändra kontotyperna som stöds `signInAudience` för en befintlig appregistrering uppdaterar du nyckeln. Mer information finns i [Konfigurera programmanifestet](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Konfigurera plattformsinställningar för ditt program

![Konfigurera inställningar för din app baserat på plattformen eller enheten](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Om du vill konfigurera programinställningar baserat på plattformen eller enheten inriktar du dig på följande:

1. På sidan **Plattformskonfigurationer** väljer du **Lägg till en plattform** och väljer bland de tillgängliga alternativen.

   ![Visar sidan Konfigurera plattformar](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Ange inställningsinformationen baserat på den plattform du valt.

   | Plattform                | Konfigurationsinställningar            |
   |-------------------------|-----------------------------------|
   | **Webb**              | Ange **Redirect URI** för ditt program. |
   | **iOS / macOS**              | Ange **apppaket-ID**, som du hittar i XCode i Info.plist eller Bygginställningar. Om du lägger till bunt-ID:et skapas automatiskt en omdirigerings-URI för programmet. |
   | **Android**          | Ange appen **Paketnamn**, som du hittar i filen AndroidManifest.xml.<br/>Generera och ange **signaturhhen**. Om du lägger till signaturhhen skapas automatiskt en omdirigerings-URI för programmet.  |
   | **Mobila och stationära program**  | Valfri. Välj en av de rekommenderade **föreslagna omdirigerings-URI:erna** om du skapar appar för stationära datorer och enheter.<br/>Valfri. Ange en **anpassad omdirigerings-URI**, som används som den plats där Azure AD omdirigerar användare som svar på autentiseringsbegäranden. För .NET Core-program där du vill `https://localhost`ha interaktion använder du till exempel . |

   > [!IMPORTANT]
   > För mobila program som inte använder det senaste MSAL-biblioteket (Microsoft Authentication Library) eller inte använder en mäklare måste du konfigurera omdirigerings-URI:erna för dessa program i **Stationära + enheter**.

Beroende på vilken plattform du väljer kan det finnas ytterligare inställningar som du kan konfigurera. För **webbappar** kan du:

* Lägga till fler omdirigerings-URI:er
* Konfigurera **implicit beviljande** för att välja de token som du vill ska utfärdas av auktoriseringsslutpunkten:

  * För ensidiga appar väljer du både **Access-token** och **ID-token**
  * För webbappar väljer du **ID-token**

## <a name="add-credentials-to-your-web-application"></a>Lägga till autentiseringsuppgifter i webbappen

Om du vill lägga till en autentiseringsinformation i webbprogrammet lägger du antingen till ett certifikat eller skapar en klienthemlighet. Så här lägger du till ett certifikat:

1. På sidan Översikt **över** appen väljer du avsnittet **Certifikat & hemligheter.**
1. Välj **Ladda upp certifikat**.
1. Välj den fil som du vill ladda upp. Den måste vara någon av följande filtyper: .cer, .pem eller .crt.
1. Välj **Lägg till**.

Så här lägger du till en klienthemlighet:

1. På sidan Översikt **över** appen väljer du avsnittet **Certifikat & hemligheter.**
1. Välj **Ny klienthemlighet**.
1. Lägg till en beskrivning för din klienthemlighet.
1. Välj en varaktighet.
1. Välj **Lägg till**.

> [!NOTE]
> Kolumnen längst till höger innehåller nyckelvärdet när du har sparat konfigurationsändringarna. **Se till att kopiera värdet** för användning i din klientprogramkod eftersom den inte är tillgänglig när du har lämnat den här sidan.

## <a name="add-permissions-to-access-web-apis"></a>Lägga till behörigheter för att få åtkomst till webb-API:er

[Behörigheten Graph API-inloggning och läsa användarprofil](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) är markerad som standard. Du kan välja mellan [två typer av behörigheter](developer-glossary.md#permissions) för varje webb-API:

* **Programbehörigheter**. Klientprogrammet måste komma åt webb-API:et direkt som sig själv, utan användarkontext. Den här typen av behörighet kräver administratörsgodkännande. Den här behörigheten är inte tillgänglig för stationära och mobila klientprogram.
* **Delegerade behörigheter**. Klientprogrammet behöver komma åt webb-API:et som den inloggade användaren men med åtkomst som begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare såvida inte behörigheten kräver administratörens godkännande.

  > [!NOTE]
  > Att lägga till en delegerad behörighet till ett program ger automatiskt medgivande till användare i klientorganisationen. Användarna måste fortfarande manuellt ge medgivande för de tillagda delegerade behörigheterna vid körning såvida inte administratören beviljas medgivande för alla användares räkning.

Så här lägger du till behörigheter för åtkomst till resurs-API:er från klienten:

1. Välj **API-behörigheter**på sidan **Översikt** för appen .
1. Under **Konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Som standard kan du i vyn välja mellan **Microsoft-API:er**. Välj det avsnitt med API:er du är intresserad av:

    * **Microsoft-API:er**. Här kan du välja behörigheter för Microsoft API:er som Microsoft Graph.
    * **API:er som min organisation använder**. Här kan du välja behörigheter för API:er som din organisation exponerar eller API:er som din organisation har integrerat med.
    * **Mina API:er**. Här kan du välja behörigheter för API:er som du exponerar.

1. När du har valt API:er visas sidan **Begär API-behörigheter**. Om API:et exponerar både delegerade programbehörigheter. Välj vilken typ av behörighet som appen behöver.
1. Välj **Lägg till behörigheter** när du är klar.

Du går tillbaka till **api-behörighetssidan.** Behörigheterna har sparats och lagts till i tabellen.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Förstå API-behörigheter och användargränssnittet för administratörsmedgivande

### <a name="configured-permissions"></a>Konfigurerade behörigheter

I det här avsnittet visas de behörigheter som uttryckligen har konfigurerats för programobjektet. Dessa behörigheter är en del av appens nödvändiga resursåtkomstlista. Du kan lägga till eller ta bort behörigheter från den här tabellen. Som administratör kan du också bevilja eller återkalla administratörsgodkännande för en uppsättning behörigheter eller enskilda behörigheter för ett API.

### <a name="other-permissions-granted"></a>Andra beviljade behörigheter

Om ditt program är registrerat i en klient kan du se ytterligare ett avsnitt med namnet **Andra behörigheter som beviljats för klient .** Det här avsnittet visar behörigheter som beviljats för klienten och som inte uttryckligen har konfigurerats för programobjektet. Dessa behörigheter begärdes dynamiskt och samtyckte. Det här avsnittet visas bara om det finns minst en behörighet som gäller.

Du kan lägga till en uppsättning behörigheter eller enskilda behörigheter som visas i det här avsnittet i avsnittet **Konfigurerade behörigheter.** Som administratör kan du också återkalla administratörsgodkännande för enskilda API:er eller behörigheter i det här avsnittet.

### <a name="admin-consent-button"></a>Knappen Administratörsmedgivande

Om ditt program är registrerat i en klient kan du se en **bidragsadministratörsgodkännande för klientknappen.** Den är inaktiverad om du inte är administratör eller om inga behörigheter har konfigurerats för programmet.
Med den här knappen kan en administratör ge administratören tillstånd till de behörigheter som konfigurerats för programmet. Om du klickar på knappen Administratör medgivande startas ett nytt fönster med en medgivandefråga som visar alla konfigurerade behörigheter.

> [!NOTE]
> Det finns en fördröjning mellan behörigheter som konfigureras för programmet och dem som visas på medgivande prompten. Om du inte ser alla konfigurerade behörigheter i medgivandeprompten stänger du den och startar den igen.

Om du har behörigheter som har beviljats men inte konfigurerats uppmanas du att hantera dessa behörigheter med knappen Administratörs medgivande. Du kan lägga till dem i konfigurerade behörigheter eller så kan du ta bort dem.

Medgivandeprompten ger möjlighet att **acceptera** eller **avbryta**. Välj **Acceptera** om du vill bevilja administratörsmedgivande. Om du väljer **Avbryt**beviljas inte administratörsgodkännande. Ett felmeddelande anger att samtycket har avvisats.

> [!NOTE]
> Det finns en fördröjning mellan att bevilja administratörsgodkännande genom att välja **Acceptera** på samtyckesprompten och statusen för administratörssamtycke som återspeglas i portalen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du exponerar webb-API:er.
> [!div class="nextstepaction"]
> [Snabbstart: Konfigurera ett program för att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md)

* Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

* Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar program med Azure Active Directory finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).

* [Snabbstart: Registrera ett program med Microsofts identitetsplattform](quickstart-register-app.md)

* [Snabbstart: Ändra konton som stöds av ett program](quickstart-modify-supported-accounts.md)

* [Snabbstart: Ta bort ett program som registrerats med Microsofts identitetsplattform](quickstart-remove-app.md)
