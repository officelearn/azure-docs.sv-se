---
title: Migrering av användare som närmar sig i Azure Active Directory B2C | Microsoft Docs
description: Diskutera grundläggande och avancerade begrepp på användarmigrering använda Graph API och du kan också använda Azure AD B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c78cb13f9028b2be527794751d5f8ced9bff171
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711349"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Användarmigrering
När du migrerar identitetsprovider till Azure Active Directory B2C (Azure AD B2C) du kan också behöva migrera användarkontot. Den här artikeln förklarar hur du migrerar befintliga användarkonton från alla identitetsleverantören till Azure AD B2C. Artikeln är inte avsett att vara normativ, men i stället beskriver några scenarier. Utvecklaren ansvarar för lämplighet varje metod.

## <a name="user-migration-flows"></a>Flöden för migrering av användare
Med Azure AD B2C kan du migrera användare via [Azure AD Graph API][B2C-GraphQuickStart]. Migreringsprocessen användaren hamnar i två flöden:

* **Före migreringen**: detta flöde gäller när du har antingen avmarkera åtkomst till en användarens autentiseringsuppgifter (användarnamn och lösenord) eller autentiseringsuppgifterna krypteras, men du kan dekryptera dem. Processen innan migrering innefattar läser användare från den gamla identitetsleverantören och skapa nya konton i Azure AD B2C-katalogen.

* **Återställning av före migrering och lösenord**: detta flöde gäller när en användares lösenord inte är tillgänglig. Exempel:
    * Lösenordet lagras i hash-format.
    * Lösenordet lagras i en identitetsleverantör som du inte kan komma åt. Din gamla identitetsleverantör validerar användarens autentiseringsuppgifter genom att anropa en webbtjänst.

I båda flöden du först köra processen innan migrering, läsa användare från ditt gamla identitetsleverantör och skapa nya konton i Azure AD B2C-katalogen. Om du inte har lösenordet kan du skapa kontot genom att använda ett lösenord som har genererats slumpmässigt. Du sedan be användaren att ändra lösenordet, eller när användaren loggar in för första gången, Azure AD B2C ombeds användaren att återställa den.

## <a name="password-policy"></a>Lösenordsprincip
Azure AD B2C lösenordsprincip (för lokala konton) baseras på Azure AD-princip. Azure AD B2C registrering eller inloggning och lösenord Återställ principer användning ”starka” lösenordssäkerhet och ut inte eventuella lösenord. Mer information finns i [Azure AD-lösenordsprincip][AD-PasswordPolicies].

Om de konton som du vill migrera använder en svagare lösenordssäkerhet än den [starka lösenord tillämpas av Azure AD B2C][AD-PasswordPolicies], kan du inaktivera kravet på starkt lösenord. Du kan ändra standardprincipen för lösenord, ange den `passwordPolicies` egenskapen `DisableStrongPassword`. Du kan till exempel ändra begäran om att skapa användare på följande sätt:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Steg 1: Använd Azure AD Graph API för att migrera användare
Du kan skapa Azure AD B2C användarkontot via Graph API (med lösenordet eller med ett slumpmässigt lösenord). Det här avsnittet beskriver hur du skapar användarkonton i Azure AD B2C-katalogen med hjälp av Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Steg 1.1: Registrera ditt program i din klientorganisation
För att kommunicera med Graph API måste måste du först ha ett tjänstkonto med administratörsbehörighet. I Azure AD kan registrera du ett program och autentisering till Azure AD. Autentiseringsuppgifterna som programmet är **program-ID** och **Programhemlighet**. Programmet fungerar som själva inte som en användare att anropa Graph API.

Först registrera ditt program för migrering i Azure AD. Sedan skapar en tangent (programhemlighet) och ställa in programmet med skrivrättigheter.

1. Logga in på [Azure Portal][Portal].

2. Välj din Azure AD **B2C** klient genom att välja ditt konto överst höger i fönstret.

3. I den vänstra rutan, Välj **Azure Active Directory** (inte Azure AD B2C). Om du vill hitta det kanske du måste välja **fler tjänster**.

4. Välj **Appregistreringar**.

5. Välj **Ny programregistrering**.

    ![Ny programregistrering](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Skapa ett nytt program genom att göra följande:
    * För **namn**, använda **B2CUserMigration** eller ett annat namn som du vill använda.
    * För **programtyp**, använda **webb-app/API**.
    * För **inloggnings-URL**, använda **https://localhost** (eftersom den inte är relevanta för det här programmet).
    * Välj **Skapa**.

7. När programmet har skapats i den **program** väljer du den nyligen skapade **B2CUserMigration** program.

8. Välj **egenskaper**, kopiera den **program-ID**, och spara den för senare.

### <a name="step-12-create-the-application-secret"></a>Steg 1.2: Skapa hemligheten som programmet
1. I Azure portal **registrerad App** väljer **nycklar**.

2. Lägg till en ny nyckel (även kallat klienthemlighet) och sedan kopiera nyckeln för senare användning.

    ![Program-ID och nycklar](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Steg 1.3: Ge administrativ behörighet till ditt program
1. I Azure portal **registrerad App** väljer **nödvändiga behörigheter**.

2. Välj **Windows Azure Active Directory**.

3. I den **Aktivera åtkomst** rutan under **programbehörigheter**väljer **läsning och skrivning katalogdata**, och välj sedan **spara**.

4. I den **nödvändiga behörigheter** väljer **bevilja med**.

    ![Behörigheter för program](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Nu har du ett program med behörighet att skapa, läsa och uppdatera användare från din Azure AD B2C-klient.

### <a name="step-14-optional-environment-cleanup"></a>Steg 1.4: (Valfritt) miljö rensning
Läsa och skriva data katalogbehörigheter gör *inte* inkludera behörighet att ta bort användare. Om du vill ge ditt program möjlighet att ta bort användare (för att rensa din miljö), måste du utföra ytterligare ett steg, som omfattar att köra PowerShell för att ange behörighet för användaren kontoadministratör. Annars kan du gå vidare till nästa avsnitt.

> [!IMPORTANT]
> Du måste använda ett administratörskonto för B2C-klient som är *lokala* till B2C-klient. Namnsyntaxen konto är *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Följande PowerShell-skript kräver [Azure Active Directory PowerShell Version 2][AD-Powershell].

I det här PowerShell-skriptet kan du göra följande:
1. Ansluta till din online-tjänst. Det gör du genom att köra den `Connect-AzureAD` cmdlet i Windows PowerShell kommandotolk och ange dina autentiseringsuppgifter.

2. Använd den **program-ID** att tilldela rollen administratör konto för programmet. De här rollerna har välkända identifierare, så att allt du behöver göra är att ange din **program-ID** i skriptet.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Ändra den `$AppId` värdet med din Azure AD **program-ID**.

## <a name="step-2-pre-migration-application-sample"></a>Steg 2: Före migrering programmet exempel
[Hämta och kör exempelkoden][UserMigrationSample]. Du kan hämta den som en .zip-fil.

### <a name="step-21-edit-the-migration-data-file"></a>Steg 2.1: Redigera migreringsfilen
Exempelappen använder en JSON-fil som innehåller dummy användardata. När du har köra exemplet kan ändra du koden för att använda data från en egen databas. Eller exportera användarprofilen till en JSON-fil och sedan ange att appen använder filen.

Redigera JSON-filen genom att öppna den `AADB2C.UserMigration.sln` Visual Studio-lösning. I den `AADB2C.UserMigration` projektet öppnar den `UsersData.json` filen.

![Datafil för användaren](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Som du kan se innehåller filen en lista över enheter som användaren. Varje enhet som användaren har följande egenskaper:
* e-post
* Visningsnamn
* Förnamn
* Efternamn
* lösenord (kan vara tom)

> [!NOTE]
> Vid kompileringen, Visual Studio kopieras filen till den `bin` directory.

### <a name="step-22-configure-the-application-settings"></a>Steg 2.2: Konfigurera inställningar för program
Under den `AADB2C.UserMigration` projektet öppnar den *App.config* fil. Ersätt följande appinställningar med egna värden:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> * Användning av en anslutningssträng för Azure-tabellen beskrivs i nästa avsnitt.
> * Din B2C-klientnamn är den domän som du angav när du skapade klienten och den visas i Azure-portalen. Innehavarens namn vanligtvis slutar med suffixet *. onmicrosoft.com* (till exempel *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>Steg 2.3: Kör processen innan migrering
Högerklicka på den `AADB2C.UserMigration` lösningen och sedan återskapa exemplet. Om du lyckas kan du bör nu ha en `UserMigration.exe` körbar fil som finns i `AADB2C.UserMigration\bin\Debug\net461`. Om du vill köra migreringen använder du någon av följande parametrar:

* Att **Migrera användare med lösenord**, använda den `UserMigration.exe 1` kommando.

* Att **Migrera användare med slumpmässigt lösenord**, använda den `UserMigration.exe 2` kommando. Den här åtgärden skapar även en Azure-tabellentiteten. Senare kan konfigurera du principen för att anropa REST API-tjänsten. Tjänsten använder en Azure-tabellen för att spåra och hantera migreringsprocessen.

![Migrering processen demo](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Steg 2.4: Kontrollera processen innan migrering
Använd någon av följande två metoder för att validera migreringen:

* Använd Azure-portalen om du vill söka efter en användare enligt visningsnamn:

    a. Öppna **Azure AD B2C**, och välj sedan **användare och grupper**.

    b. I sökrutan skriver användarens namn och visa användarens profil.

* Använd det här exempelprogrammet för att hämta en användare genom att logga in e-postadress:

    a. Kör följande kommando:

    ```Console
        UserMigration.exe 3 {email address}
    ```

    > [!TIP]
    > Du kan också hämta en användare med visningsnamn med hjälp av följande kommando: `UserMigration.exe 4 "<Display name>"`.

    b. Öppna filen UserProfile.json i en JSON-redigerare för att se användarens information.

    ![Filen UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Steg 2.5: (Valfritt) miljö rensning
Om du vill rensa upp din Azure AD-klient och ta bort användare från Azure AD-katalog som kör den `UserMigration.exe 5` kommando.

> [!NOTE]
> * Konfigurera kontot Användaradministratör behörigheter för ditt program för att rensa din klient.
> * Migrering exempelapp rensar alla användare som anges i JSON-filen.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Steg 2.6: Logga in med migrerade användarna (med lösenord)
Konton som är redo att använda när du har kört processen innan migrering med lösenord och användare kan logga in på ditt program med hjälp av Azure AD B2C. Om du inte har åtkomst till användarlösenord fortsätta till nästa avsnitt.

## <a name="step-3-help-users-reset-their-password"></a>Steg 3: Hjälper användarna att återställa sina lösenord
Om du migrerar användare med ett slumpmässigt lösenord, måste de återställa sina lösenord. Skicka ett välkomstmeddelande med en länk för att återställa lösenordet för att hjälpa dem återställa lösenordet.

Om du vill hämta en länk till din princip för lösenordsåterställning gör du följande:

1. Välj **Azure AD B2C inställningar**, och välj sedan **Återställ lösenord** egenskaper.

2. Välj ditt program.

    > [!NOTE]
    > Kör nu kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång] [ B2C-GetStarted] artikel eller [appregistrering] [ B2C-AppRegister] artikel.

3. Välj **kör nu**, och kontrollera sedan principen.

4. I den **kör nu endpoint** rutan, Kopiera URL-Adressen och skicka den till dina användare.

    ![Ange diagnostik loggar](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Steg 4: (Valfritt) Ändra din princip för att kontrollera och användarstatus för migrering

> [!NOTE]
> Om du vill kontrollera och ändra användarstatus för migreringen, måste du använda en anpassad princip. Ställa in instruktioner från [komma igång med anpassade principer] [ B2C-GetStartedCustom] måste fyllas i.
>

När användarna försöker logga in utan att återställa lösenordet först ska principen returnera ett eget felmeddelande. Exempel:
>*Ditt lösenord har upphört att gälla. Välj länken Återställ lösenord för att återställa den.*

Det här valfria steget kräver användning av Azure AD B2C anpassade principer, enligt beskrivningen i den [komma igång med anpassade principer] [ B2C-GetStartedCustom] artikel.

I det här avsnittet kan du ändra principen för att kontrollera migrering användarstatus på Logga in. Om användaren inte ändra lösenordet, returnera ett HTTP 409 felmeddelande som ber användaren att välja den **har du glömt lösenordet?** länk.

För att spåra ändra lösenordet måste använda du en Azure-tabellen. När du kör processen innan migrering med kommandoradsparametern `2`, du skapar en användare och enhet i en Azure-tabellen. Tjänsten gör följande:

* På inloggning anropar Azure AD B2C-principen migreringen RESTful-tjänst, skickar ett e-postmeddelande som ett inkommande anspråk. Tjänsten söker efter e-postadressen i Azure-tabellen. Om adressen finns tjänsten utlöser ett felmeddelande visas: *du måste ändra lösenord*.

* När användaren ändrar har lösenordet, kan du ta bort entiteten från Azure-tabellen.

>[!NOTE]
>Vi använder en Azure-tabellen för att förenkla exemplet. Du kan lagra Migreringsstatus i en databas eller som en anpassad egenskap i Azure AD B2C-konto.

### <a name="41-update-your-application-setting"></a>4.1: uppdatera din tillämpningsinställning
1. Om du vill testa RESTful-API-demo öppna `AADB2C.UserMigration.sln` i Visual Studio.

2. I den `AADB2C.UserMigration.API` projektet öppnar den *appsettings.json* fil. Ersätta inställningen med det konfigurerade i [steg 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Steg 4.2: Distribuera ditt webbprogram till Azure App Service
I Solution Explorer högerklickar du på den `AADB2C.UserMigration.API`, Välj ”publicera...”. Följ instruktionerna för att publicera till Azure App Service. Mer information finns i [distribuera din app till Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Steg 4.3: Lägga till en teknisk profil och tekniska profil validering din princip
1. Expandera ”lösningsobjekt” i Solution Explorer och öppna den *TrustFrameworkExtensions.xml* principfil.
2. Ändra `TenantId`, `PublicPolicyUri` och `<TenantId>` fält från `yourtenant.onmicrosoft.com` till namnet på din klient.
3. Under den `<TechnicalProfile Id="login-NonInteractive">` element, Ersätt alla förekomster av `ProxyIdentityExperienceFrameworkAppId` och `IdentityExperienceFrameworkAppId` med program-ID: N som konfigurerats i [komma igång med anpassade principer][B2C-GetStartedCustom].
4. Under den `<ClaimsProviders>` nod, söka efter följande XML-fragment. Ändra värdet för `ServiceUrl` att peka till Azure App Service-URL.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Föregående tekniska profil definierar ett inkommande anspråk: `signInName` (skicka som e-post). Vid inloggning, anspråket som skickas till RESTful-slutpunkten.

När du har definierat tekniska profilen för RESTful-API berätta för din Azure AD B2C-princip för att anropa tekniska profilen. XML-kodstycke åsidosätter `SelfAsserted-LocalAccountSignin-Email`, som har definierats i den grundläggande principen. XML-kodstycke lägger också till `ValidationTechnicalProfile`, med ReferenceId som pekar på profilen tekniska `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Steg 4.4: Överför principen till din klient
1. I den [Azure-portalen][Portal], växla till den [kontext för din Azure AD B2C-klient][B2C-NavContext], och välj sedan **Azure AD B2C**.

2. Välj **identitet upplevelse Framework**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **skriva över principen om den finns** kryssrutan.

6. Överför den *TrustFrameworkExtensions.xml* filen och se till att den har klarat valideringen.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Steg 4.5: Testa den anpassade principen genom att använda Kör nu
1. Välj **Azure AD B2C inställningar**, och gå till **identitet upplevelse Framework**.

2. Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du överfört och välj sedan **kör nu**.

3. Försök att logga in med något av de migrerade användarna autentiseringsuppgifter och välj sedan **logga In**. REST-API utlösa följande felmeddelande visas:

    ![Ange diagnostik loggar](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Steg 4.6: (Valfritt) felsöka REST-API
Du kan visa och övervaka loggningsinformation i nära realtid.

1. På inställningsmenyn för tillämpningsprogrammet RESTful under **övervakning**väljer **diagnostikloggar**.

2. Ange **programloggning (filsystem)** till **på**.

3. Ange den **nivå** till **utförlig**.

4. Välj **spara**

    ![Ange diagnostik loggar](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. På den **inställningar** väljer du **loggström**.

6. Kontrollera resultatet av RESTful-API.

Mer information finns i [loggar och konsolen][AppService-Log].

> [!IMPORTANT]
> Använd diagnostik loggar endast under utveckling och testning. RESTful-API-utdata innehåller konfidentiell information som inte ska visas i produktion.
>

## <a name="optional-download-the-complete-policy-files"></a>(Valfritt) Hämta de fullständiga principfilerna
När du har slutfört den [komma igång med anpassade principer] [ B2C-GetStartedCustom] genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[AppService-Log]: https://docs.microsoft.com/azure/active-directory-b2c/app-service-web/web-sites-streaming-logs-and-console
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration