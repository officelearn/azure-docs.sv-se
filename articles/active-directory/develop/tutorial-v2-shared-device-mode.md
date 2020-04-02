---
title: Använda läget delad enhet med MSAL Android | Azure
description: Lär dig hur du förbereder en Android-enhet för att köras i delat läge och kör en förstalinjearbetsapp.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e74ff320f26a4b6fa7d1caf3d4effca5e10669f4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546218"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Självstudiekurs: Använd läget delad enhet i Android-programmet

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Utvecklarguide

Den här guiden innehåller utvecklarvägledning för att implementera läget för delade enheter i ett Android-program med hjälp av Microsoft Authentication Library (MSAL). Se [msal Android-självstudien](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) för att se hur du integrerar MSAL med din Android-app, loggar in en användare, anropar Microsoft-diagram och loggar ut en användare.

### <a name="download-the-sample"></a>Hämta exemplet

Klona [exempelprogrammet](https://github.com/Azure-Samples/ms-identity-android-java/) från GitHub. Exemplet har möjlighet att arbeta i [ett eller flera kontoläge](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Lägga till MSAL SDK i din lokala Maven-databas

Om du inte använder exempelappen lägger du till MSAL-biblioteket som ett beroende i filen build.gradle, så här:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Konfigurera appen så att den använder läget för delade enheter

Mer information om hur du konfigurerar konfigurationsfilen finns i [konfigurationsdokumentationen.](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)

Ställ `"shared_device_mode_supported"` `true` in i konfigurationsfilen för MSAL.

Du kanske inte planerar att stödja läget för flera konton. Det kan vara om du inte använder en delad enhet och användaren kan logga in på appen med mer än ett konto samtidigt. Om så `"account_mode"` är `"SINGLE"`fallet, ställ in på . Detta garanterar att din `ISingleAccountPublicClientApplication`app alltid kommer att få, och avsevärt förenklar din MSAL integration. Standardvärdet `"account_mode"` för `"MULTIPLE"`är , så det är viktigt att ändra det här `"single account"` värdet i konfigurationsfilen om du använder läget.

Här är ett exempel på filen auth_config.json som ingår**raw** i **appens**>**res**>**huvudkatalog**>för exempelappen:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Identifiera läget för delade enheter

Med läget med delade enheter kan du konfigurera Android-enheter som ska delas av flera anställda, samtidigt som Microsoft Identity-säkerhetskopierad hantering av enheten tillhandahålls. Anställda kan logga in på sina enheter och komma åt kundinformation snabbt. När de är klara med skiftet eller uppgiften kan de logga ut från alla appar på den delade enheten med ett enda klick och enheten är omedelbart redo för nästa medarbetare att använda.

Används `isSharedDevice()` för att avgöra om en app körs på en enhet som är i läget delad enhet. Din app kan använda den här flaggan för att avgöra om den ska ändra användarupplevelsen i enlighet med detta.

Här är ett kodavsnitt som visar hur `isSharedDevice()`du kan använda .  Det är från `SingleAccountModeFragment` klassen i exempelappen:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Initiera publicclientApplication-objektet

Om du `"account_mode":"SINGLE"` anger i MSAL-config-filen kan du på ett `ISingleAccountPublicCLientApplication`säkert sätt casta det returnerade programobjektet som ett .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Identifiera ett eller flera konton

Om du skriver en app som bara används för förstahandsarbetare på en delad enhet rekommenderar vi att du skriver din app för att bara stödja ett kontoläge. Detta inkluderar de flesta program som är uppgiftsfokuserade, till exempel journalappar, fakturaappar och de flesta affärsappar. Detta kommer att förenkla din utveckling eftersom många funktioner i SDK inte behöver tillgodoses.

Om appen stöder flera konton och läget för delade enheter måste du utföra en typkontroll och casta till rätt gränssnitt enligt nedan.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Hämta den inloggade användaren och ta reda på om en användare har ändrats på enheten

Metoden `loadAccount` hämtar kontot för den inloggade användaren. Metoden `onAccountChanged` avgör om den inloggade användaren har ändrats och i så fall rensa:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Logga in en användare globalt

Följande loggar in en användare över enheten till andra appar som använder MSAL med Authenticator-appen:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Logga ut en användare globalt

Följande tar bort det inloggade kontot och rensar cachelagrade token från inte bara appen utan även från enheten som är i läget för delade enheter:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Administratörsguide

I följande steg beskrivs hur du konfigurerar ditt program i Azure-portalen och placerar enheten i läget för delade enheter.

### <a name="register-your-application-in-azure-active-directory"></a>Registrera ditt program i Azure Active Directory

Registrera först ditt program i organisationens klientorganisation. Ange sedan dessa värden nedan i auth_config.json för att ditt program ska fungera korrekt.

Mer information om hur du gör detta finns [i Registrera din ansökan](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> När du registrerar din app använder du snabbstartsguiden till vänster och väljer sedan **Android**. Detta leder dig till en sida där du blir ombedd att ange **paketnamn** och **signatur hash** för din app. Dessa är mycket viktiga för att säkerställa att din appkonfiguration fungerar. Du får sedan ett konfigurationsobjekt som du kan använda för din app som du ska klippa ut och klistra in i filen auth_config.json.

![Skärmen](media/tutorial-v2-shared-device-mode/register-app.png) För appregistrering Du bör välja **Gör den här ändringen åt mig** och sedan ange de värden som snabbstarten efterfrågar i Azure-portalen. När det är gjort kommer vi att generera alla konfigurationsfiler du behöver.

![Visa informationsskärmen för App config](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Konfigurera en klient

Konfigurera följande i din klientorganisation i testsyfte: minst två anställda, en molnenhetsadministratör och en global administratör. Ange molnenhetsadministratören i Azure-portalen genom att ändra organisationsroller. I Azure-portalen får du åtkomst till dina organisationsroller genom att välja **Azure Active Directory** > **Roles och Administrators** > **Cloud Device Administrator**. Lägg till de användare som kan placera en enhet i delat läge.

## <a name="set-up-an-android-device-in-shared-mode"></a>Konfigurera en Android-enhet i delat läge

### <a name="download-the-authenticator-app"></a>Ladda ner Authenticator-appen

Ladda ned Microsoft Authenticator-appen från Google Play Store. Om du redan har laddat ned appen kontrollerar du att den är den senaste versionen.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Inställningar för Autentiseringsappar & registrera enheten i molnet

Starta Authenticator-appen och navigera till huvudkontosidan. När du ser sidan **Lägg till konto** är du redo att göra enheten delad.

![Skärmen Autentiseringsperson för lägg till konto](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Gå till fönstret **Inställningar** med hjälp av den högra menyraden. Välj **Enhetsregistrering** under **Konton & arbete.**

 ![Skärmen Autentiseringsperson för lägg till konto](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 När du klickar på den här knappen blir du ombedd att auktorisera åtkomst till enhetskontakter. Detta beror på Androids kontointegration på enheten. Välj **tillåt**.

 ![Skärmen Autentiseringsperson för lägg till konto](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

Cloud-enhetsadministratören ska ange sin organisations-e-postadress under **Eller registrera sig som en delad enhet**. Klicka sedan på **knappen Registrera som delad enhet** och ange deras autentiseringsuppgifter.

![skärmen registrera enheter](media/tutorial-v2-shared-device-mode/register-device.png)

![sign-in](media/tutorial-v2-shared-device-mode/sign-in.png)

Enheten är nu i delat läge.

![skärmen registrera enheter](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Alla inloggningar och ut signeringar på enheten kommer att vara globala, vilket innebär att de gäller för alla appar som är integrerade med MSAL och Microsoft Authenticator på enheten. Du kan nu distribuera program till den enhet som använder funktioner för delad enhetsläge.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Visa den delade enheten i Azure-portalen

När du har placerat en enhet i delat läge blir den känd för din organisation och spåras i organisationens klientorganisation. Du kan visa dina delade enheter genom att titta på **kopplingstypen** i Azure Active Directory-bladet på din Azure-portal.

![Alla enheter blad i Azure-portalen](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Köra exempelappen

Exempelprogrammet är en enkel app som anropar graph-API:et för din organisation. Vid första körningen uppmanas du att godkänna när programmet är nytt för ditt medarbetarkonto.

![Visa informationsskärmen för App config](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om delat läge i [läget Delad enhet för Android-enheter](msal-android-shared-devices.md)