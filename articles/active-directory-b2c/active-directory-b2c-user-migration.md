---
title: Metoder för användarmigrering i Azure Active Directory B2C
description: Diskuterar både grundläggande och avancerade koncept för användarmigrering med hjälp av Azure AD Graph API och om du vill med hjälp av Azure AD B2C anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c922799b650de7f921cc0493eb3feb2ad90b9d92
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183157"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Användarmigrering

När du migrerar identitets leverantören till Azure Active Directory B2C (Azure AD B2C) kan du också behöva migrera användar kontona. I den här artikeln förklaras hur du migrerar befintliga användar konton från vilken identitetsprovider som helst till Azure AD B2C. Artikeln är inte avsedd att vara förväntad, utan i stället beskrivs några få scenarier. Utvecklaren ansvarar för varje strategis lämplighet.

## <a name="user-migration-flows"></a>Flöden för användarmigrering

Med Azure AD B2C kan du migrera användare via [Azure AD-Graph API][B2C-GraphQuickStart]. Migreringen av användaren hamnar i två flöden:

- **Före migrering**: Det här flödet gäller när du antingen har rensa åtkomst till en användares autentiseringsuppgifter (användar namn och lösen ord), eller om autentiseringsuppgifterna är krypterade, men du kan dekryptera dem. För migreringen sker att läsa användare från den gamla identitets leverantören och skapa nya konton i Azure AD B2C-katalogen.

- **Före migrering och lösen ords återställning**: Det här flödet gäller när en användares lösen ord inte är tillgänglig. Exempel:
  - Lösen ordet lagras i HASH-format.
  - Lösen ordet lagras i en identitets leverantör som du inte kan komma åt. Den gamla identitets leverantören verifierar användarens autentiseringsuppgifter genom att anropa en webb tjänst.

I båda flödena kör du först processen före migreringen, läser användarna från din gamla identitetsprovider och skapar nya konton i Azure AD B2C-katalogen. Om du inte har lösen ordet skapar du kontot med ett lösen ord som skapas slumpmässigt. Sedan ber du användaren att ändra lösen ordet eller, när användaren loggar in för första gången, Azure AD B2C ber användaren återställa den.

## <a name="password-policy"></a>Lösenordsprincip

Den Azure AD B2C lösen ords principen (för lokala konton) baseras på Azure AD-principen. Principerna Azure AD B2C för registrering eller inloggning och lösen ords återställning använder lösen ords styrkan "stark" och upphör aldrig att gälla lösen ord. Mer information finns i [lösen ords princip för Azure AD][AD-PasswordPolicies].

Om de konton som du vill migrera använder en svagare lösen ords styrka än den [starka lösen ords styrkan som tillämpas av Azure AD B2C][AD-PasswordPolicies], kan du inaktivera kravet på starkt lösen ord. Ange `passwordPolicies` egenskapen till `DisableStrongPassword`om du vill ändra standard lösen ords principen. Du kan till exempel ändra begäran om att skapa användare på följande sätt:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Steg 1: Använd Azure AD-Graph API för att migrera användare

Du skapar Azure AD B2C användar kontot via Graph API (med lösen ordet eller med ett slumpmässigt lösen ord). I det här avsnittet beskrivs processen för att skapa användar konton i Azure AD B2C Directory med Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Steg 1,1: Registrera ditt program i din klient organisation

För att kunna kommunicera med Graph API måste du först ha ett tjänst konto med administratörs behörighet. I Azure AD registrerar du ett program och autentisering till Azure AD. Programmets autentiseringsuppgifter är **program-ID** och **program hemlighet**. Programmet fungerar som själva, inte som en användare, för att anropa Graph API.

Registrera först ditt program för migrering i Azure AD. Skapa sedan en program nyckel (program hemlighet) och ange programmet med Skriv behörighet.

1. Logga in på [Azure Portal][Portal].
1. Välj **katalog + prenumerations** filter i det övre högra avsnittet i portalen.
1. Välj den katalog som innehåller Azure AD B2C klienten.
1. I den vänstra menyn väljer du **Azure Active Directory** (*inte* Azure AD B2C). Du kan behöva välja **alla tjänster**för att hitta det.
1. Välj **Appregistreringar (bakåtkompatibelt)** .
1. Välj **Ny programregistrering**.

   ![Azure Active Directory-och Appregistreringar meny alternativ har marker ATS](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

1. Skapa ett nytt program genom att göra följande:

   - För **namn**använder du *B2CUserMigration* eller andra namn som du vill använda.
   - För **program typ**väljer du **webbapp/API**.
   - Använd`https://localhost` (är inte relevant för det här programmet) för **inloggnings-URL**.
   - Välj **Skapa**.

    När programmet har skapats visas sidan **registrerad app** som visar dess egenskaper.
1. Kopiera programmets **program-ID**och spara det för senare.

### <a name="step-12-create-the-application-secret"></a>Steg 1,2: Skapa program hemligheten

1. På sidan **registrerad app** väljer du **Inställningar**.
1. Välj **Nycklar**.
1. Under **lösen ord**lägger du till en ny nyckel (kallas även en klient hemlighet) med namnet *MyClientSecret* eller ett annat namn som du väljer, väljer ett utgångs fönster, väljer **Spara**och kopierar sedan nyckelvärdet för senare användning.

    ![Meny alternativet program-ID-värde och nycklar markerat i Azure Portal](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Steg 1,3: Ge administrativ behörighet till ditt program

1. I menyn **Inställningar** väljer du **nödvändiga behörigheter**.
1. Välj **Windows-Azure Active Directory**.
1. I fönstret **Aktivera åtkomst** , under **program behörigheter**, väljer du **läsa och skriva katalog data**och väljer sedan **Spara**.
1. I fönstret **nödvändiga behörigheter** väljer du **bevilja behörigheter**och väljer sedan **Ja**.

   ![Kryss rutan Läs/skriv katalog, Spara och bevilja behörighet markerad](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Nu har du ett program med behörighet att skapa, läsa och uppdatera användare från din Azure AD B2C klient.

### <a name="step-14-optional-environment-cleanup"></a>Steg 1,4: Valfritt Miljö rensning

Läs-och Skriv behörighet för katalog data inkluderar *inte* behörighet att ta bort användare. För att ge ditt program möjlighet att ta bort användare (för att rensa din miljö) måste du utföra ett extra steg, som inbegriper att köra PowerShell för att ange administratörs behörighet för användar konton. Annars kan du gå vidare till nästa avsnitt.

> [!IMPORTANT]
> Du måste använda ett B2C klient administratörs konto som är *lokalt* för B2C-klienten. Konto namnets syntax är *admin\@contosob2c.onmicrosoft.com*.

I det här PowerShell-skriptet, som kräver [Azure AD PowerShell V2-modulen][AD-Powershell], gör du följande:

1. Anslut till din online tjänst. Det gör du genom att köra `Connect-AzureAD` cmdleten i Windows PowerShell-Kommandotolken och ange dina autentiseringsuppgifter.

1. Använd **program-ID: t** för att tilldela programmet rollen som administratör för användar konton. De här rollerna har välkända identifierare, så allt du behöver göra är att ange ditt **program-ID** i skriptet.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

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

Ändra värdet med ditt Azure AD **-program-ID.** `$AppId`

## <a name="step-2-pre-migration-application-sample"></a>Steg 2: Exempel på program för migrering

Du hittar kod exemplet före migrering i GitHub-lagringsplatsen som hanteras `azure-ad-b2c/user-migration` av gruppen:

[Azure-AD-B2C/User-migration/för migrering][UserMigrationSample-code] GitHub

### <a name="step-21-edit-the-migration-data-file"></a>Steg 2.1: Redigera data filen för migrering

Exempel programmet använder en JSON-fil som innehåller dummy-användarnamn. När du har kört exemplet kan du ändra koden så att den använder data från din egen databas. Alternativt kan du exportera användar profilen till en JSON-fil och sedan ange att appen ska använda filen.

Om du vill redigera JSON-filen öppnar `AADB2C.UserMigration.sln` du Visual Studio-lösningen. Öppna filen i `AADB2C.UserMigration`projektet `UsersData.json` .

![Del av UsersData. JSON-filen som visar JSON-block av två användare](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Som du kan se innehåller filen en lista över användar enheter. Varje entitet för användare har följande egenskaper:

- email
- displayName
- firstName
- lastName
- lösen ord (kan vara tomt)

> [!NOTE]
> Vid kompileringen kopierar Visual Studio filen till `bin` katalogen.

### <a name="step-22-configure-the-application-settings"></a>Steg 2.2: Konfigurera program inställningarna

Öppna filen *app. config* under projektet.`AADB2C.UserMigration` Ersätt följande app-inställningar med dina egna värden:

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
> - Användningen av en Azure Table-anslutningssträng beskrivs i nästa avsnitt.
> - Ditt B2C-klient namn är den domän som du angav när du skapade klienten, och det visas i Azure Portal. Klient namnet slutar vanligt vis med suffixet *. onmicrosoft.com* (till exempel *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Steg 2,3: Kör processen före migrering

Högerklicka på `AADB2C.UserMigration` lösningen och återskapa sedan exemplet. Om du lyckas bör du nu ha en `UserMigration.exe` körbar fil som finns i. `AADB2C.UserMigration\bin\Debug\net461` Om du vill köra migreringsprocessen använder du någon av följande kommando rads parametrar:

- Använd`UserMigration.exe 1` kommandot för att **migrera användare med lösen ord**.

- Om du vill **migrera användare med slumpmässigt lösen ord**använder du `UserMigration.exe 2` kommandot. Den här åtgärden skapar även en Azure Table-entitet. Senare konfigurerar du principen för att anropa tjänsten REST API. Tjänsten använder en Azure-tabell för att spåra och hantera migreringsprocessen.

![Kommando tolks fönstret visar utdata från kommandot UserMigration. exe](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Steg 2,4: Kontrol lera processen före migreringen

Verifiera migreringen med någon av följande två metoder:

- Om du vill söka efter en användare efter visnings namn använder du Azure Portal:

   1. Öppna **Azure AD B2C**och välj sedan **användare**.
   1. I rutan Sök skriver du användarens visnings namn och visar sedan användarens profil.

- Om du vill hämta en användare via inloggnings-e-postadress använder du det här exempel programmet:

   1. Kör följande kommando:

      ```Console
          UserMigration.exe 3 {email address}
      ```

      > [!TIP]
      > Du kan också hämta en användare genom att visa namnet med hjälp av följande kommando `UserMigration.exe 4 "<Display name>"`:.

   1. Öppna filen UserProfile. json i en JSON-redigerare för att se användarens information.

      ![Filen UserProfile. JSON öppnas i Visual Studio Code Editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Steg 2,5: Valfritt Miljö rensning

Om du vill rensa din Azure AD-klient och ta bort användare från Azure AD-katalogen kör du `UserMigration.exe 5` kommandot.

> [!NOTE]
> * Om du vill rensa klienten konfigurerar du användar konto administratörs behörighet för ditt program.
> * I exemplet på migrering rensas alla användare som listas i JSON-filen.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Steg 2,6: Logga in med migrerade användare (med lösen ord)

När du har kört en för inmigrering med användar lösen ord, är kontona redo att användas och användarna kan logga in till ditt program med hjälp av Azure AD B2C. Om du inte har åtkomst till användar lösen ord kan du fortsätta till nästa avsnitt.

## <a name="step-3-help-users-reset-their-password"></a>Steg 3: Hjälp användarna att återställa sina lösen ord

Om du migrerar användare med ett slumpmässigt lösen ord måste de återställa sina lösen ord. Du kan hjälpa dem att återställa lösen ordet genom att skicka ett välkomst meddelande med en länk för att återställa lösen ordet.

Följ dessa steg om du vill hämta en länk till din princip för lösen ords återställning. Den här proceduren förutsätter att du tidigare har skapat en [anpassad princip](active-directory-b2c-get-started-custom.md)för lösen ords återställning.

1. Välj den katalog som innehåller Azure AD B2C klienten med hjälp av filtret för **katalog + prenumeration** i det övre högra avsnittet av [Azure Portal](https://portal.azure.com).
1. Välj **Azure AD B2C** i den vänstra menyn (eller i **alla tjänster**).
1. Under **principer**väljer du **Identity Experience Framework**.
1. Välj din princip för lösen ords återställning. Till exempel *B2C_1A_PasswordReset*.
1. Välj ditt program i list rutan **Välj program** .

    > [!NOTE]
    > **Körning kräver nu** att minst ett program har registrerats i din klient organisation. Information om hur du registrerar program finns i [Självstudier: Registrera ett program i Azure Active Directory B2C][B2C-AppRegister].

1. Kopiera den URL som visas i text rutan **Kör nu-slut punkt** och skicka den sedan till användarna.

    ![Sidan princip för lösen ords återställning med slut punkten för kör nu markerad](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Steg 4: Valfritt Ändra principen för att kontrol lera och ange användar status för migrering

> [!NOTE]
> Om du vill kontrol lera och ändra status för användarmigrering måste du använda en anpassad princip. Konfigurations anvisningarna från [Kom igång med anpassade principer][B2C-GetStartedCustom] måste utföras.

När användarna försöker logga in utan att återställa lösen ordet först bör principen returnera ett eget fel meddelande. Exempel:

> *Ditt lösen ord har upphört att gälla. Om du vill återställa det väljer du länken Återställ lösen ord.*

Det här valfria steget kräver att Azure AD B2C anpassade principer används, enligt beskrivningen i artikeln [komma igång med anpassade principer][B2C-GetStartedCustom] .

I det här avsnittet ändrar du principen för att kontrol lera status för användarmigrering vid inloggning. Om användaren inte har ändrat lösen ordet returnerar du ett HTTP 409-fel meddelande som uppmanar användaren att välja länken **glömt lösen ordet?** .

För att spåra lösen ords ändringen använder du en Azure-tabell. När du kör processen för migrering med kommando rads parametern `2`skapar du en entitet för användare i en Azure-tabell. Tjänsten gör följande:

- Vid inloggning anropar Azure AD B2C principen migrering av RESTful-tjänsten och skickar ett e-postmeddelande som ett inloggat anspråk. Tjänsten söker efter e-postadressen i Azure-tabellen. Om adressen finns, genererar tjänsten ett fel meddelande: *Du måste ändra lösen ordet*.

- När användaren har ändrat lösen ordet tar du bort entiteten från Azure-tabellen.

> [!NOTE]
> Vi använder en Azure-tabell för att förenkla exemplet. Du kan lagra migrerings status i valfri databas eller som en anpassad egenskap i Azure AD B2C-kontot.

### <a name="41-update-your-application-setting"></a>4,1: Uppdatera program inställningen

1. Testa RESTful API-demon genom att öppna `AADB2C.UserMigration.sln` i Visual Studio.
1. Öppna filen *Web. config* i projektet.`AADB2C.UserMigration.API` Ersätt inställningen med den som kon figurer ATS i [steg 2,2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Steg 4,2: Distribuera ditt webb program till Azure App Service

I Solution Explorer högerklickar du `AADB2C.UserMigration.API`på och väljer "publicera...". Följ anvisningarna för att publicera till Azure App Service. Mer information finns i [distribuera din app till Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Steg 4,3: Lägg till en teknisk profil och en teknisk profil validering i principen

1. Expandera "lösnings objekt" i Solution Explorer och öppna princip filen *TrustFrameworkExtensions. XML* .
1. Ändra `TenantId`och fältfrån`yourtenant.onmicrosoft.com` till namnet på din klient organisation. `<TenantId>` `PublicPolicyUri`
1. Under elementet ersätter du alla instanser av `ProxyIdentityExperienceFrameworkAppId` och `IdentityExperienceFrameworkAppId` med de program-ID: n som kon figurer ATS i [komma igång med anpassade principer][B2C-GetStartedCustom]. `<TechnicalProfile Id="login-NonInteractive">`
1. Leta upp följande XML-kodfragment under noden.`<ClaimsProviders>` Ändra värdet för `ServiceUrl` så att det pekar på din Azure App Service-URL.

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

Föregående tekniska profil definierar ett indatamängds- `signInName` anspråk: (skicka som-e-post). Vid inloggning skickas anspråket till din RESTful-slutpunkt.

När du har definierat den tekniska profilen för ditt RESTful-API ska du be din Azure AD B2C policy att anropa den tekniska profilen. XML-kodfragmentet åsidosätter `SelfAsserted-LocalAccountSignin-Email`, som definieras i bas principen. XML-kodfragmentet läggs `ValidationTechnicalProfile`också till, med ReferenceId som pekar på `LocalAccountUserMigration`din tekniska profil.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Steg 4,4: Överför principen till klienten

1. I [Azure Portal][Portal]växlar du till kontexten [för din Azure AD B2C klient][B2C-NavContext]och väljer sedan **Azure AD B2C**.
1. Välj **ramverk för identitets upplevelse**.
1. Välj **alla principer**.
1. Välj **Ladda upp princip**.
1. Markera kryss rutan **Skriv över principen om den finns** .
1. Ladda upp filen *TrustFrameworkExtensions. XML* och kontrol lera att den klarar verifieringen.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Steg 4,5: Testa den anpassade principen med hjälp av kör nu

1. Välj **Azure AD B2C**och välj sedan **Identity Experience Framework**.
1. Öppna *B2C_1A_signup_signin*, den förlitande parten (RP) anpassad princip som du överförde och välj sedan **Kör nu**.
1. Ange autentiseringsuppgifterna för en av de migrerade användarna och välj sedan **Logga in**. Ditt REST API bör utlösa följande fel meddelande:

    ![Inloggnings registrerings sidan som visar fel meddelandet ändra lösen ord](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Steg 4,6: Valfritt Felsöka REST API

Du kan visa och övervaka loggnings information i nära real tid.

1. På RESTful-programmets inställningar-meny, under **övervakning**, väljer du **diagnostikloggar**.
1. Ange **program loggning (fil system)** till **på**.
1. Ange **nivån** **utförlig**.
1. Välj **spara**

    ![Konfigurations sidan för diagnostikloggar i Azure Portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. På menyn **Inställningar** väljer du **logg ström**.
1. Kontrol lera utdata från RESTful-API: et.

> [!IMPORTANT]
> Använd bara diagnostikloggar när du utvecklar och testar. RESTful API-utdata kan innehålla konfidentiell information som inte bör exponeras i produktionen.

## <a name="optional-download-the-complete-policy-files"></a>Valfritt Hämta fullständiga principfiler

När du har slutfört [Kom igång med anpassade principer][B2C-GetStartedCustom] , rekommenderar vi att du skapar ditt scenario genom att använda dina egna anpassade principfiler. Vi har angett [exempel på principfiler][UserMigrationSample-policy]för din referens.

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
