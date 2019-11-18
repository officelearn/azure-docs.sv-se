---
title: 'Konfigurera ett program för åtkomst till webb-API: er – Microsoft Identity Platform'
description: Lär dig hur du konfigurerar en app som har registrerats på Microsoft Identity Platform att inkludera omdirigerings-URI, autentiseringsuppgifter eller behörigheter för åtkomst till webb-API:er.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: afa757020ff6de3be23403b78fd9a12c2de97016
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106607"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Snabb start: Konfigurera ett klient program för åtkomst till webb-API: er

För att ett webbklientprogram eller ett konfidentiellt webbprogram ska kunna delta i ett flöde för auktoriseringsbeviljande (och hämta en åtkomsttoken) måste det kunna etablera säkra autentiseringsuppgifter. Standardmetoden för autentisering som stöds av Azure-portalen är klient-ID + hemlig nyckel.

Dessutom, innan en klient kan komma åt ett webb-API som görs tillgängligt av ett resursprogram (till exempel Microsoft Graph API) säkerställer ramverket för medgivande att klienten det behörighetsbeviljande som krävs, baserat på de begärda behörigheterna. Som standard kan alla appar välja behörigheter från Microsoft Graph API. [Graph API-behörigheten ”Logga in och läs användarprofil”](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) väljs som standard. Du kan välja mellan [två typer av behörigheter](developer-glossary.md#permissions) för varje önskat webb-API:

* **Programbehörigheter** – Klientprogrammet behöver komma åt webb-API:et direkt som sig självt (ingen användarkontext). Den här typen av behörighet kräver administratörens godkännande och är inte heller tillgängligt för offentliga klientprogram (dator eller mobil).
* **Delegerade behörigheter** – Klientprogrammet behöver komma åt webb-API:et som den inloggade användaren men med åtkomst som begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare såvida inte behörigheten kräver administratörens godkännande.

  > [!NOTE]
  > Att lägga till en delegerad behörighet till ett program ger automatiskt medgivande till användare i klientorganisationen. Användarna måste fortfarande manuellt ge medgivande för de tillagda delegerade behörigheterna vid körning såvida inte administratören beviljas medgivande för alla användares räkning.

I den här snabbstarten visas hur du konfigurerar din app för att:

* [Lägga till omdirigerings-URI:er för programmet](#add-redirect-uris-to-your-application)
* [Konfigurera avancerade inställningar för ditt program](#configure-advanced-settings-for-your-application)
* [Ändra konto typer som stöds](#modify-supported-account-types)
* [Lägg till autentiseringsuppgifter i ditt webb program](#add-credentials-to-your-web-application)
* [Lägga till behörigheter för att få åtkomst till webb-API:er](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Krav

Innan du börjar kontrollerar du att följande krav är uppfyllda:

* Lär dig mer om [behörigheter och medgivande](v2-permissions-and-consent.md) som stöds, vilket är viktigt att förstå när du skapar appar som måste användas av andra användare eller appar.
* Ha en klientorganisation som har appar som är registrerade till den.
  * Om du inte har några registrerade appar kan du [få information om hur du registrerar appar på Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

Innan du kan konfigurera appen gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet och ställer in din portal-session till önskad Azure AD-klient.
1. Sök efter och välj **Azure Active Directory**. 
1. I den vänstra rutan väljer du **Appregistreringar**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. Följ stegen för att konfigurera appen för åtkomst till webb-API:er:
    * [Lägga till omdirigerings-URI:er för programmet](#add-redirect-uris-to-your-application)
    * [Konfigurera avancerade inställningar för ditt program](#configure-advanced-settings-for-your-application)
    * [Ändra konto typer som stöds](#modify-supported-account-types)
    * [Lägg till autentiseringsuppgifter i ditt webb program](#add-credentials-to-your-web-application)
    * [Lägga till behörigheter för att få åtkomst till webb-API:er](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Lägga till omdirigerings-URI:er för appen

Lägga till en omdirigerings-URI för appen:

1. På appens **översiktssida** väljer du avsnittet **Autentisering**.
1. Gör följande för att lägga till en anpassad omdirigerings-URI för webbappar och offentliga klientprogram:
   1. Leta reda på avsnittet **Omdirigerings-URI**.
   1. Välj den typ av app du skapar **webb** eller **offentlig klient (mobil och dator)** .
   1. Ange omdirigerings-URI för appen.
      * För webbappar anger du grundläggande URL för appen. Till exempel kan `http://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
      * För offentliga appar anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifik för ditt program, till exempel: `https://MyFirstApp`.

1. Gör följande för att välja mellan föreslagna omdirigerings-URI:er för offentliga klienter (mobil, dator):
    1. Leta reda på avsnittet för **föreslagna omdirigerings-URI:er för offentliga klienter (mobil, dator)** .
    1. Välj en eller flera lämpliga omdirigerings-URI:er med hjälp av kryssrutorna. Du kan också ange en anpassad omdirigerings-URI. Om du inte är säker på vad du ska använda kan du läsa biblioteks dokumentationen.

Det finns vissa begränsningar som gäller för omdirigerings-URI: er. Läs mer om [begränsningar och begränsningar för omdirigering av URI](https://docs.microsoft.com/azure/active-directory/develop/reply-url).
> [!NOTE]
> Testa de nya inställningarna för **autentisering** där du kan konfigurera inställningar för ditt program baserat på den plattform eller enhet som du vill använda som mål.
>
> Om du vill se den här vyn väljer du **testa den nya upplevelsen** från vyn **standardautentiserings** -sida.
>
> ![Klicka på "prova den nya upplevelsen" för att se vyn plattforms konfiguration](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Då går du till [sidan nya **plattforms konfiguration** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurera avancerade inställningar för ditt program

Beroende på vilket program du registrerar finns det några ytterligare inställningar som du kan behöva konfigurera, till exempel:

* **Utloggnings-URL**
* För appar med en sida kan du aktivera **implicit beviljande** och välja de token som du vill att behörighets slut punkten ska utfärda.
* För skrivbordsappar som hämtar token med integrerad Windows-autentisering, enhets kod flöde eller användar namn/lösen ord i avsnittet **standard klient typ** konfigurerar du inställningen **behandla program som offentlig klient** till **Ja**.
* För äldre appar som använde Live SDK för att integrera med Microsoft-konto-tjänsten konfigurerar du **Live SDK-stöd**. Nya appar behöver inte den här inställningen.
* **Standard klient typ**

### <a name="modify-supported-account-types"></a>Ändra konto typer som stöds

De **konto typer som stöds** anger vem som kan använda programmet eller åtkomst till API: et.

När du har [konfigurerat de konto typer som stöds](quickstart-register-app.md) när du ursprungligen registrerade programmet kan du bara ändra den här inställningen med hjälp av program manifest redigeraren om:

* Du ändrar konto typer från **AzureADMyOrg** eller **AzureADMultipleOrgs** till **AzureADandPersonalMicrosoftAccount**eller vice versa.
* Du ändrar konto typer från **AzureADMyOrg** till **AzureADMultipleOrgs**eller vice versa.

Ändra de konto typer som stöds för en befintlig registrerad app:

* Se [Konfigurera program manifestet](reference-app-manifest.md) och uppdatera `signInAudience`-nyckeln.

## <a name="configure-platform-settings-for-your-application"></a>Konfigurera plattforms inställningar för ditt program

[![konfigurera inställningar för appen baserat på plattformen eller enheten](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

Om du vill konfigurera program inställningar baserat på plattformen eller enheten är du mål:

1. På sidan **plattforms konfiguration** väljer du **Lägg till en plattform** och väljer bland de tillgängliga alternativen.

   ![Visar sidan Konfigurera plattformar](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Ange inställnings informationen baserat på den plattform som du har valt.

   | Plattform                | Alternativ              | Konfigurationsinställningar            |
   |-------------------------|----------------------|-----------------------------------|
   | **Webbprogram**    | **Webb**              | Ange **omdirigerings-URI** för programmet. |
   | **Mobil program** | **iOS**              | Ange appens **paket-ID**, som du hittar i Xcode i info. plist eller skapa inställningar. Genom att lägga till paket-ID: t skapas automatiskt en omdirigerings-URI för programmet. |
   |                         | **Android**          | * Ange appens **paket namn**, som du hittar i filen AndroidManifest. xml.<br/>* Generera och ange **signaturens hash-värde**. När du lägger till signaturens hash skapas automatiskt en omdirigerings-URI för programmet.  |
   | **Desktop + enheter**   | **Desktop + enheter** | Valfritt. Välj en av de rekommenderade **omdirigerings-URI: erna** om du skapar appar för Station ära datorer och enheter.<br/>Valfritt. Ange en **anpassad omdirigerings-URI**, som används som den plats där Azure AD omdirigerar användare som svar på autentiseringsbegäranden. Till exempel för .NET Core-program där du vill ha interaktionen använder du `https://localhost`. |

   > [!IMPORTANT]
   > För mobila program som inte använder det senaste MSAL-biblioteket eller som inte använder en Service Broker måste du konfigurera omdirigerings-URI: erna för dessa program i **Desktop +-enheter**.

1. Beroende på vilken plattform du väljer kan det finnas ytterligare inställningar som du kan konfigurera. För **Web** Apps kan du:
    * Lägg till fler omdirigerings-URI
    * Konfigurera **implicit beviljande** för att välja de token som du vill ska utfärdas av behörighets slut punkten:
        * För appar med en sida väljer du båda **åtkomst-tokens** och **ID-token**
        * För Web Apps väljer du **ID-token**

## <a name="add-credentials-to-your-web-application"></a>Lägga till autentiseringsuppgifter i webbappen

Så här lägger du till en autentiseringsuppgift för webbappen:

1. På appens **översiktssida** väljer du avsnittet för **certifikat och hemligheter**.

1. Gör följande för att lägga till ett certifikat:

    1. Välj **Ladda upp certifikat**.
    1. Välj den fil som du vill ladda upp. Den måste vara någon av följande filtyper: .cer, .pem eller .crt.
    1. Välj **Lägg till**.

1. Gör följande för att lägga till en klienthemlighet:

    1. Välj **Ny klienthemlighet**.
    1. Lägg till en beskrivning för din klienthemlighet.
    1. Välj en varaktighet.
    1. Välj **Lägg till**.

> [!NOTE]
> Kolumnen längst till höger innehåller nyckelvärdet när du har sparat konfigurationsändringarna. **Se till att kopiera värdet** för användning i din klientprogramkod eftersom den inte är tillgänglig när du har lämnat den här sidan.

## <a name="add-permissions-to-access-web-apis"></a>Lägga till behörigheter för att få åtkomst till webb-API:er

Så lägger du till behörigheter för att komma åt resurs-API:er från klienten:

1. På appens **översiktssida** väljer du **API-behörigheter**.
1. Under avsnittet **konfigurerade behörigheter** väljer du knappen **Lägg till en behörighet** .
1. Som standard kan du i vyn välja mellan **Microsoft-API:er**. Välj det avsnitt med API:er du är intresserad av:
    * **Microsoft-API:er** – Du kan välja behörigheter för Microsoft-API:er som Microsoft Graph.
    * **API:er som min organisation använder** – Du kan välja behörigheter för API:er som har exponerats av din organisation, eller API som din organisation har integrerats med.
    * **Mina API:er** – Du kan välja behörigheter för API:er som du har exponerat.
1. När du har valt API:er visas sidan **Begär API-behörigheter**. Om API:et exponerar både delegerade programbehörigheter. Välj vilken typ av behörighet som appen behöver.
1. Välj **Lägg till behörigheter** när du är klar. Du återgår till sidan **API-behörigheter**, där behörigheterna har sparats och lagts till i tabellen.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Förstå API-behörigheter och gränssnitt för administratörs medgivande

### <a name="configured-permissions"></a>Konfigurerade behörigheter

I det här avsnittet visas de behörigheter som uttryckligen har kon figurer ATS för programobjektet (\the-behörigheter som ingår i appens obligatoriska resurs åtkomst lista). Du kan lägga till eller ta bort behörigheter från den här tabellen. Som administratör kan du också bevilja/återkalla administratörs medgivande för en uppsättning behörigheter för API: er eller enskilda behörigheter i det här avsnittet.

### <a name="other-permissions-granted"></a>Andra behörigheter som har beviljats

Om ditt program är registrerat i en klient kan du se ytterligare ett avsnitt med rubriken **andra behörigheter som har beviljats för klient organisationen**. I det här avsnittet visas behörigheter som har beviljats för klienten, men som inte uttryckligen har kon figurer ATS för programobjektet (t. ex. behörigheter som har begärts dynamiskt och godkänts). Det här avsnittet visas bara om det finns minst en behörighet som gäller.

Du kan lägga till en uppsättning behörigheter för API: er eller enskilda behörigheter som visas i det här avsnittet i avsnittet **konfigurerade behörigheter** . Som administratör kan du också återkalla administratörs medgivande för enskilda API: er eller behörigheter i det här avsnittet.

### <a name="admin-consent-button"></a>Knappen administratörs medgivande

Om ditt program är registrerat i en klient organisation visas knappen **tilldela administratörs medgivande för innehavare** . Den kommer att inaktive ras om du inte är administratör eller om inga behörigheter har kon figurer ATS för programmet.
Med den här knappen kan en administratör enkelt bevilja administrativt medgivande till de behörigheter som har kon figurer ATS för programmet. När du klickar på knappen administratörs medgivande startas ett nytt fönster med en uppstarts-prompt som visar alla konfigurerade behörigheter.

> [!NOTE]
> Det finns en fördröjning mellan behörigheter som konfigureras för programmet och de visas i frågan om medgivande. Om du inte ser alla konfigurerade behörigheter i medgivande meddelandet stänger du den och startar den igen.

Om du har behörighet som har beviljats men inte har kon figurer ATS, uppmanas du att välja hur du ska hantera dessa behörigheter när du klickar på knappen administratörs medgivande. Du kan lägga till dem i konfigurerade behörigheter, eller så kan du ta bort dem.

Medgivande meddelandet ger möjlighet att **godkänna** eller **avbryta**. Om du väljer **Godkänn**beviljas administratörs medgivande. Om du väljer **Avbryt**beviljas inte administratörs medgivande och du får ett fel meddelande om att medgivande har avböjts.

> [!NOTE]
> Det uppstår en fördröjning mellan att bevilja administratörs tillåtelse (att välja **acceptera** i medgivande meddelandet) och statusen för administratörs medgivande visas i användar gränssnittet.

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering:

* [Registrera en app på Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurera en app att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md)
* [Ändra konton som stöds av en app](quickstart-modify-supported-accounts.md)
* [Ta bort en app registrerad på Microsoft Identity Platform](quickstart-remove-app.md)

Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).

Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar program med Azure Active Directory finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).
