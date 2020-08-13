---
title: 'Snabb start: Konfigurera en app för att få åtkomst till ett webb-API | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här snabb starten konfigurerar du en app som registrerats med Microsoft Identity Platform för att inkludera omdirigerings-URI: er, autentiseringsuppgifter eller behörigheter för åtkomst till webb-API: er'
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 87c21587567ffe3462e4b702985114ac10454886
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140810"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Snabb start: Konfigurera ett klient program för att få åtkomst till ett webb-API

I den här snabb starten lägger du till omdirigerings-URI: er, autentiseringsuppgifter eller behörigheter för åtkomst till webb-API: er för programmet Ett webb-eller konfidentiellt klient program måste upprätta säkra autentiseringsuppgifter för att delta i ett flöde för utfärdande av auktorisering som kräver autentisering. Standardmetoden för autentisering som stöds av Azure-portalen är klient-ID + hemlig nyckel. Appen erhåller en åtkomsttoken under den här processen.

Innan en klient kan komma åt ett webb-API som exponeras av ett resurs program, t. ex. Microsoft Graph-API, ser medgivande ramverket till att klienten erhåller den behörighets beviljande som krävs för de begärda behörigheterna. Som standard kan alla program begära behörigheter från Microsoft Graph-API: et.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Slut för ande av [snabb start: Konfigurera ett program för att exponera ett webb-API](quickstart-configure-app-expose-web-apis.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto i det övre högra hörnet. Ange din portal-session till den Azure AD-klient som du vill använda.
1. Sök efter och välj **Azure Active Directory**. Välj **Appregistreringar** under **Hantera**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du programmets **Översikt** eller huvud registrerings sidan.

Använd följande procedurer för att konfigurera ditt program för att få åtkomst till webb-API: er.

## <a name="add-redirect-uris-to-your-application"></a>Lägga till omdirigerings-URI:er för programmet

Du kan lägga till anpassade omdirigerings-URI: er och föreslagna omdirigerings-URI: er Så här lägger du till en anpassad omdirigerings-URI för webb-och offentliga klient program:

1. På sidan **Översikt över** appen väljer du **autentisering**.
1. Hitta **omdirigerings-URI: er**. Du kan behöva välja **Växla till den gamla upplevelsen**.
1. Välj den typ av program som du skapar: **webb** eller **offentlig klient/ursprunglig (mobil & Desktop)**.
1. Ange omdirigerings-URI för appen.

   * För webbappar anger du grundläggande URL för appen. Till exempel kan `http://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
   * För offentliga appar anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifik för ditt program, till exempel: `https://MyFirstApp` .
1. Välj **Spara**.

Följ dessa steg om du vill välja mellan föreslagna omdirigerings-URI: er för offentliga klienter:

1. På sidan **Översikt över** appen väljer du **autentisering**.
1. Hitta **föreslagna omdirigerings-URI: er för offentliga klienter (mobil, stationär dator)**. Du kan behöva välja **Växla till den gamla upplevelsen**.
1. Välj en eller flera omdirigerings-URI: er för ditt program. Du kan också ange en anpassad omdirigerings-URI. Om du inte är säker på vad du ska använda kan du läsa biblioteks dokumentationen.
1. Välj **Spara**.

Vissa begränsningar gäller för omdirigerings-URI: er. Mer information finns i [OMDIRIGERA URI/svars-URL begränsningar och begränsningar](./reply-url.md).

> [!NOTE]
> Testa de nya inställningarna för **autentisering** där du kan konfigurera inställningar för ditt program baserat på den plattform eller enhet som du vill använda som mål.
>
> Om du vill se den här vyn väljer du **testa den nya upplevelsen** från sidan **autentisering** .
>
> ![Klicka på "prova den nya upplevelsen" för att se vyn plattforms konfiguration](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Då går du till [sidan nya **plattforms konfiguration** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Konfigurera avancerade inställningar för ditt program

Beroende på vilket program du registrerar finns det några ytterligare inställningar som du kan behöva konfigurera, till exempel:

* **Utloggnings-URL**.
* För appar med en sida kan du aktivera **implicit beviljande** och välja de token som du vill att behörighets slut punkten ska utfärda.
* För skrivbordsappar som hämtar token genom att använda integrerad Windows-autentisering, enhets kod flöde eller användar namn/lösen ord i avsnittet **standard klient typ** , ställer du in inställningen **behandla program som offentlig klient** på **Ja**.
* För äldre appar som använde Live SDK för att integrera med Microsoft-konto-tjänsten konfigurerar du **Live SDK-stöd**. Nya appar behöver inte den här inställningen.
* **Standard klient typ**.
* **Konto typer som stöds**.

### <a name="modify-supported-account-types"></a>Ändra konto typer som stöds

De **konto typer som stöds** anger vem som kan använda programmet eller åtkomst till API: et.

Om du har konfigurerat de konto typer som stöds när du registrerade programmet kan du bara ändra den här inställningen med hjälp av program manifest redigeraren om:

* Du ändrar konto typer från **AzureADMyOrg** eller **AzureADMultipleOrgs** till **AzureADandPersonalMicrosoftAccount**, eller till ett annat sätt runt, eller
* Du ändrar konto typer från **AzureADMyOrg** till **AzureADMultipleOrgs**eller tvärtom.

Uppdatera nyckeln för att ändra de konto typer som stöds för en befintlig app-registrering `signInAudience` . Mer information finns i [Konfigurera applikations manifestet](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Konfigurera plattforms inställningar för ditt program

![Konfigurera inställningar för din app baserat på plattformen eller enheten](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Om du vill konfigurera program inställningar baserat på plattformen eller enheten är du mål:

1. På sidan **plattforms konfiguration** väljer du **Lägg till en plattform** och väljer bland de tillgängliga alternativen.

   ![Visar sidan Konfigurera plattformar](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Ange inställnings informationen baserat på den plattform som du har valt.

   | Plattform                | Konfigurationsinställningar            |
   |-------------------------|-----------------------------------|
   | **Webb**              | Ange **omdirigerings-URI** för programmet. |
   | **iOS/macOS**              | Ange **programpaket-ID: t**, som du hittar i Xcode i info. plist eller skapa inställningar. Genom att lägga till paket-ID: t skapas automatiskt en omdirigerings-URI för programmet. |
   | **Android**          | Ange **namnet**på Appaketet, som du hittar i AndroidManifest.xml-filen.<br/>Generera och ange **signaturens hash-värde**. När du lägger till signaturens hash skapas automatiskt en omdirigerings-URI för programmet.  |
   | **Mobil-och skriv bords program**  | Valfritt. Välj en av de rekommenderade **omdirigerings-URI: erna** om du skapar appar för Station ära datorer och enheter.<br/>Valfritt. Ange en **anpassad omdirigerings-URI**, som används som den plats där Azure AD omdirigerar användare som svar på autentiseringsbegäranden. Till exempel för .NET Core-program där du vill använda interaktionen använder du `http://localhost` . |

   > [!NOTE]
   > På Active Directory Federation Services (AD FS) (AD FS) och Azure AD B2C måste du också ange ett port nummer.  Till exempel: `http://localhost:1234`.

   > [!IMPORTANT]
   > För mobila program som inte använder det senaste MSAL (Microsoft Authentication Library) eller inte använder en Service Broker måste du konfigurera omdirigerings-URI: erna för dessa program i **Desktop +-enheter**.

Beroende på vilken plattform du väljer kan det finnas ytterligare inställningar som du kan konfigurera. För **Web** Apps kan du:

* Lägg till fler omdirigerings-URI
* Konfigurera **implicit beviljande** för att välja de token som du vill ska utfärdas av behörighets slut punkten:

  * För appar med en sida väljer du båda **åtkomst-tokens** och **ID-token**
  * För Web Apps väljer du **ID-token**

## <a name="add-credentials-to-your-web-application"></a>Lägga till autentiseringsuppgifter i webbappen

Lägg till ett certifikat eller skapa en klient hemlighet för att lägga till en autentiseringsuppgift i ditt webb program. Så här lägger du till ett certifikat:

1. På sidan **Översikt över** appen väljer du avsnittet **certifikat & hemligheter** .
1. Välj **Ladda upp certifikat**.
1. Välj den fil som du vill ladda upp. Den måste vara någon av följande filtyper: .cer, .pem eller .crt.
1. Välj **Lägg till**.

Så här lägger du till en klient hemlighet:

1. På sidan **Översikt över** appen väljer du avsnittet **certifikat & hemligheter** .
1. Välj **Ny klienthemlighet**.
1. Lägg till en beskrivning för din klienthemlighet.
1. Välj en varaktighet.
1. Välj **Lägg till**.

> [!NOTE]
> Kolumnen längst till höger innehåller nyckelvärdet när du har sparat konfigurationsändringarna. **Se till att kopiera värdet** för användning i din klientprogramkod eftersom den inte är tillgänglig när du har lämnat den här sidan.

## <a name="add-permissions-to-access-web-apis"></a>Lägga till behörigheter för att få åtkomst till webb-API:er

[Behörigheten Graph API inloggning och läsa användar profil](/graph/permissions-reference#user-permissions) är markerad som standard. Du kan välja mellan [två typer av behörigheter](developer-glossary.md#permissions) för varje webb-API:

* **Program behörigheter**. Klient programmet måste ha åtkomst till webb-API: et direkt, utan användar kontext. Den här typen av behörighet kräver administratörs medgivande. Den här behörigheten är inte tillgänglig för Skriv bords-och mobil klient program.
* **Delegerade behörigheter**. Klientprogrammet behöver komma åt webb-API:et som den inloggade användaren men med åtkomst som begränsas av den valda behörigheten. Den här typen av behörighet kan beviljas av en användare såvida inte behörigheten kräver administratörens godkännande.

  > [!NOTE]
  > Att lägga till en delegerad behörighet till ett program ger automatiskt medgivande till användare i klientorganisationen. Användarna måste fortfarande manuellt ge medgivande för de tillagda delegerade behörigheterna vid körning såvida inte administratören beviljas medgivande för alla användares räkning.

Så här lägger du till behörigheter för åtkomst till resurs-API: er från klienten:

1. På sidan **Översikt över** app väljer du **API-behörigheter**.
1. Under **konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Som standard kan du i vyn välja mellan **Microsoft-API:er**. Välj det avsnitt med API:er du är intresserad av:

    * **Microsoft API: er**. Låter dig välja behörigheter för Microsoft API: er som Microsoft Graph.
    * **API: er som min organisation använder**. Låter dig välja behörigheter för API: er som din organisation exponerar, eller API: er som din organisation har integrerat med.
    * **Mina API: er**. Låter dig välja behörigheter för de API: er som du exponerar.

1. När du har valt API:er visas sidan **Begär API-behörigheter**. Om API:et exponerar både delegerade programbehörigheter. Välj vilken typ av behörighet som appen behöver.
1. Välj **Lägg till behörigheter** när du är klar.

Du kommer tillbaka till sidan **API-behörigheter** . Behörigheterna har sparats och lagts till i tabellen.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Förstå API-behörigheter och gränssnitt för administratörs medgivande

### <a name="configured-permissions"></a>Konfigurerade behörigheter

I det här avsnittet visas de behörigheter som uttryckligen har kon figurer ATS för programobjektet. De här behörigheterna är en del av appens obligatoriska resurs åtkomst lista. Du kan lägga till eller ta bort behörigheter från den här tabellen. Som administratör kan du också bevilja eller återkalla administratörs medgivande för en uppsättning behörigheter för API eller enskilda behörigheter.

### <a name="other-permissions-granted"></a>Andra behörigheter som har beviljats

Om ditt program är registrerat i en klient kan du se ytterligare ett avsnitt med rubriken **andra behörigheter som har beviljats för klient organisationen**. Det här avsnittet visar behörigheter som har beviljats för klienten som inte har kon figurer ATS uttryckligen för programobjektet. Dessa behörigheter begärdes dynamiskt och samtyckde. Det här avsnittet visas bara om det finns minst en behörighet som gäller.

Du kan lägga till en uppsättning behörigheter för API: er eller enskilda behörigheter som visas i det här avsnittet i avsnittet **konfigurerade behörigheter** . Som administratör kan du också återkalla administratörs medgivande för enskilda API: er eller behörigheter i det här avsnittet.

### <a name="admin-consent-button"></a>Knappen administratörs medgivande

Om ditt program är registrerat i en klient visas knappen **tilldela administratörs medgivande för klient** . Den inaktive ras om du inte är administratör eller om inga behörigheter har kon figurer ATS för programmet.
Med den här knappen kan en administratör bevilja administrativt medgivande till de behörigheter som har kon figurer ATS för programmet. När du klickar på knappen administratörs medgivande startas ett nytt fönster med en uppstarts-prompt som visar alla konfigurerade behörigheter.

> [!NOTE]
> Det finns en fördröjning mellan behörigheter som konfigureras för programmet och de visas i frågan om medgivande. Om du inte ser alla konfigurerade behörigheter i medgivande meddelandet stänger du den och startar den igen.

Om du har behörighet som har beviljats men inte kon figurer ATS, kommer du att be dig att hantera dessa behörigheter. Du kan lägga till dem i konfigurerade behörigheter, eller så kan du ta bort dem.

Medgivande meddelandet ger möjlighet att **godkänna** eller **avbryta**. Välj **acceptera** för att bevilja administratörs tillåtelse. Om du väljer **Avbryt**beviljas inte administratörs medgivande. Ett fel meddelande om att medgivande har nekats.

> [!NOTE]
> Det uppstår en fördröjning mellan att bevilja administratörs medgivande genom att välja **acceptera** i medgivande frågan och statusen för det administrativa medgivande som återspeglas i portalen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa snabb start i serien och lär dig hur du konfigurerar vilka konto typer som har åtkomst till ditt program. Du kanske till exempel vill begränsa åtkomsten enbart till de användare i din organisation (en enskild klient) eller tillåta användare i andra Azure AD-klienter (flera innehavare) och de med personliga Microsoft-konton (MSA).

> [!div class="nextstepaction"]
> [Ändra konton som stöds av en app](quickstart-modify-supported-accounts.md)