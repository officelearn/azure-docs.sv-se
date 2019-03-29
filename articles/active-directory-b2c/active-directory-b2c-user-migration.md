---
title: Användarmigrering närmar sig i Azure Active Directory B2C | Microsoft Docs
description: Diskutera grundläggande och avancerade koncept på användarmigrering med Graph API och du kan också använda anpassade principer för Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 09b219fe173be9ba2fd515facce9964b5edc67af
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621323"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Användarmigrering
När du migrerar din identitetsprovider till Azure Active Directory B2C (Azure AD B2C) du kan också behöva migrera användarkontot. Den här artikeln förklarar hur du migrerar befintliga användarkonton från alla identitetsprovider till Azure AD B2C. Artikeln är inte avsedd att köras förebyggande, men i stället beskriver några scenarier. Utvecklaren är ansvarig för lämpligheten för varje metod.

## <a name="user-migration-flows"></a>Migrering – användarflöden
Med Azure AD B2C kan du migrera användare via [Azure AD Graph API][B2C-GraphQuickStart]. Migreringsprocessen användaren hamnar i två flöden:

- **Före migreringen**: Det här flödet gäller när du har antingen rensa åtkomst till en användares autentiseringsuppgifter (användarnamn och lösenord) eller autentiseringsuppgifterna krypteras, men du kan dekryptera dem. Processen innan migrering processen läser användarna från den gamla identitetsprovidern och skapa nya konton i Azure AD B2C-katalogen.

- **Före migreringen och lösenordsåterställning**: Det här flödet gäller när en användares lösenord inte kan nås. Exempel:
   - Lösenordet lagras i HASH-format.
   - Lösenordet lagras i en identitetsprovider som du inte kan komma åt. Din gamla identitetsprovider verifierar användarens autentiseringsuppgifter genom att anropa en webbtjänst.

I båda flöden du först köra processen innan migrering, läsa användare från din gamla identitetsprovider och skapa nya konton i Azure AD B2C-katalogen. Om du inte har lösenordet kan skapa du kontot genom att använda ett lösenord som har genererats slumpmässigt. Du sedan be användaren att ändra lösenordet, eller när användaren loggar in för första gången, Azure AD B2C uppmanar användaren att återställa den.

## <a name="password-policy"></a>Lösenordsprincip
Azure AD B2C-lösenordsprincip (för lokala konton) baseras på Azure AD-princip. Azure AD B2C registrering eller inloggning och lösenord återställa principer användning ”starka” lösenordssäkerhet och löper ut eventuella lösenord. Mer information finns i [Azure AD-lösenordsprincip][AD-PasswordPolicies].

Om de konton som du vill migrera använder en svagare lösenordssäkerhet än den [stark lösenordssäkerhet som tillämpas av Azure AD B2C][AD-PasswordPolicies], kan du inaktivera kravet på starkt lösenord. Du kan ändra standardprincipen för lösenord, ange den `passwordPolicies` egenskap `DisableStrongPassword`. Du kan till exempel ändra begäran om att skapa användaren på följande sätt:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Steg 1: Använda Azure AD Graph API för att migrera användare
Du kan skapa Azure AD B2C-användarkonto via Graph API (med lösenordet eller med ett slumpmässigt lösenord). Det här avsnittet beskrivs hur du skapar användarkonton i Azure AD B2C-katalogen med hjälp av Graph API.

### <a name="step-11-register-your-application-in-your-tenant"></a>Steg 1.1: Registrera ditt program i din klient
För att kommunicera med Graph API kan ha du först ett tjänstkonto med administrativa privilegier. I Azure AD kan registrera du ett program och autentisering till Azure AD. Autentiseringsuppgifterna som programmet är **program-ID** och **Programhemlighet**. Programmet fungerar som själva inte som en användare att anropa Graph API.

Först registrera ditt program för migrering i Azure AD. Sedan skapar en programnyckel (programhemlighet) och ange program med skrivbehörighet.

1. Logga in på [Azure Portal][Portal].
   
1. Välj din Azure AD **B2C** klient genom att välja ditt konto längst upp höger i fönstret.
   
1. I den vänstra rutan väljer **Azure Active Directory** (inte Azure AD B2C). Om du vill hitta det du kan behöva välja **fler tjänster**.
   
1. Välj **Appregistreringar**.
   
1. Välj **Ny programregistrering**.
   
   ![Ny programregistrering](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. Skapa ett nytt program genom att göra följande:
   - För **namn**, använda **B2CUserMigration** eller ett annat namn som du vill.
   - För **programtyp**, använda **Web app/API**.
   - För **inloggnings-URL**, använda `https://localhost` (eftersom den inte är relevanta för det här programmet).
   - Välj **Skapa**.
   
1. När programmet har skapats i den **program** väljer du den nyligen skapade **B2CUserMigration** program.
   
1. Välj **egenskaper**, kopiera den **program-ID**, och spara den till senare.

### <a name="step-12-create-the-application-secret"></a>Steg 1.2: Skapa programhemlighet
1. I Azure-portalen **registrerad App** väljer **nycklar**.
   
1. Lägg till en ny nyckel (även kallat en klienthemlighet) och sedan kopiera nyckeln för senare användning.
   
   ![Program-ID och nycklar](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>Steg 1.3: Ge administrativ behörighet till ditt program
1. I Azure-portalen **registrerad App** väljer **behörigheter som krävs för**.

1. Välj **Windows Azure Active Directory**.
   
1. I den **Aktivera åtkomst** fönstret under **programbehörigheter**väljer **läsa och skriva katalogdata**, och välj sedan **spara**.
   
1. I den **nödvändiga behörigheter** väljer **bevilja behörigheter**.
   
   ![Programbehörigheter](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
Nu har du ett program med behörighet att skapa, läsa och uppdatera användare från din Azure AD B2C-klient.

### <a name="step-14-optional-environment-cleanup"></a>Steg 1.4: (Valfritt) Miljö-rensning
Läsa och skriva data katalogbehörigheter gör *inte* får du ta bort användare. Om du vill ge ditt program kan ta bort användare (för att rensa upp din miljö), måste du utföra ett extra steg, vilket involverar körning av PowerShell för att ange Användarkontoadministratören behörigheter. Annars kan gå du till nästa avsnitt.

> [!IMPORTANT]
> Du måste använda ett administratörskonto för B2C-klient som är *lokala* till B2C-klient. Konto Namnsyntaxen är *admin\@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Följande PowerShell-skript kräver [Azure Active Directory PowerShell Version 2][AD-Powershell].

I det här PowerShell-skriptet kan du göra följande:
1. Anslut till din online-tjänst. Du gör detta genom att köra den `Connect-AzureAD` cmdlet i Windows PowerShell kommandotolk och ange dina autentiseringsuppgifter.
   
1. Använd den **program-ID** att tilldela programmet till administratörsrollen-konto. Dessa roller har välkänd identifierare, så allt du behöver göra är att ange din **program-ID** i skriptet.
   
```powershell
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

## <a name="step-2-pre-migration-application-sample"></a>Steg 2: Exempel på före migreringen för program
[Ladda ned och kör exempelkoden][UserMigrationSample]. Du kan hämta den som en .zip-fil.

### <a name="step-21-edit-the-migration-data-file"></a>Steg 2.1: Redigera migreringsfilen
Exempelappen använder en JSON-fil som innehåller data som dummy-användare. När du köra exemplet kan ändra du koden för att använda data från en egen databas. Eller så kan du exportera användarens profil till en JSON-fil och sedan ange att appen använder filen.

Om du vill redigera JSON-filen, öppna den `AADB2C.UserMigration.sln` Visual Studio-lösning. I den `AADB2C.UserMigration` projektet öppnar den `UsersData.json` filen.

![Datafil för användare](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Som du ser innehåller filen en lista över användarentiteter. Varje entitet har följande egenskaper:
- e-post
- displayName
- firstName
- lastName
- lösenord (kan vara tom)

> [!NOTE]
> Vid kompilering, Visual Studio kopierar filen till den `bin` directory.

### <a name="step-22-configure-the-application-settings"></a>Steg 2.2: Konfigurera inställningar för program
Under den `AADB2C.UserMigration` projektet öppnar den *App.config* fil. Ersätt följande inställningar för appen med dina egna värden:

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
> - Användning av en anslutningssträng för Azure-tabell beskrivs i nästa avsnitt.
> - Namnet på din B2C-klientorganisation är den domän som du angav när du skapade klienten, och den visas i Azure-portalen. Vanligtvis slutar innehavarens namn med suffixet *. onmicrosoft.com* (till exempel *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Steg 2.3: Kör processen innan migrering
Högerklicka på den `AADB2C.UserMigration` lösningen och sedan återskapa exemplet. Om du är klar kan du bör nu ha en `UserMigration.exe` körbar fil som finns i `AADB2C.UserMigration\bin\Debug\net461`. För att köra migreringsprocessen, använder du något av följande parametrar:

- Att **Migrera användare med lösenord**, använda den `UserMigration.exe 1` kommando.

- Att **Migrera användare med slumpmässiga lösenord**, använda den `UserMigration.exe 2` kommando. Den här åtgärden skapar även en Azure-tabellentiteten. Senare kan konfigurera du principen för att anropa REST API-tjänst. Tjänsten använder en Azure-tabell för att spåra och hantera migreringsprocessen.

![Migreringsdemonstration för process](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Steg 2.4: Kontrollera processen innan migrering
Använd någon av följande två metoder för att verifiera migreringen:

- Om du vill söka efter en användare efter visningsnamn, använder du Azure portal:
   
   1. Öppna **Azure AD B2C**, och välj sedan **användare och grupper**.
   
   1. I sökrutan anger du användarens visningsnamn och sedan visa användarens profil.
   
- Använd det här exempelprogrammet för att hämta en användare genom att logga in e-postadress:
   
   1. Kör följande kommando:
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > Du kan också hämta en användare med visningsnamn med hjälp av följande kommando: `UserMigration.exe 4 "<Display name>"`.
      
   1. Öppna filen UserProfile.json i en JSON-redigerare för att se information om användaren.
   
      ![Filen UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>Steg 2,5: (Valfritt) Miljö-rensning
Om du vill rensa upp din Azure AD-klient och ta bort användare från Azure AD-katalog, kör den `UserMigration.exe 5` kommando.

> [!NOTE]
> * Konfigurera Användarkontoadministratören behörigheter för ditt program för att rensa din klient.
> * Exempelapp för migrering Rensar upp alla användare som visas i JSON-filen.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Steg 2.6: Logga in med migrerade användare (med lösenord)
Konton som är redo att använda när du har kört processen innan migrering med lösenord, och användarna kan logga in på ditt program med hjälp av Azure AD B2C. Om du inte har åtkomst till lösenord kan fortsätta till nästa avsnitt.

## <a name="step-3-help-users-reset-their-password"></a>Steg 3: Hjälp användarna att återställa sina lösenord
Om du migrerar användare med ett slumpmässigt lösenord, måste de återställa sina lösenord. Skicka ett välkomstmeddelande med en länk för att återställa lösenordet för att hjälpa dem återställa lösenordet.

Om du vill hämta en länk till principen för återställning av lösenord gör du följande:

1. Välj **Azure AD B2C-inställningar**, och välj sedan **Återställ lösenord** Principegenskaper.

1. Välj ditt program.

    > [!NOTE]
    > Kör nu kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar] [ B2C-GetStarted] artikel eller [programregistrering] [ B2C-AppRegister] artikeln.

1. Välj **kör nu**, och markerar sedan principen.

1. I den **kör nu slutpunkt** rutan, Kopiera URL: en och skicka den till dina användare.

    ![Set-diagnostikloggar](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Steg 4: (Valfritt) Ändra din princip för att kontrollera och ställa in användarstatus för migrering

> [!NOTE]
> Om du vill kontrollera och ändra användarstatus för migreringen, måste du använda en anpassad princip. Ställa in instruktionerna från [Kom igång med anpassade principer] [ B2C-GetStartedCustom] måste utföras.
>

När användare försöker logga in utan att återställa lösenordet först, kommer din princip ska returnera ett användarvänligt felmeddelande. Exempel:
>*Ditt lösenord har upphört att gälla. Välj länken Återställ lösenord för att återställa den.*

Det här valfria steget kräver användning av Azure AD B2C anpassade principer, enligt beskrivningen i den [komma igång med anpassade principer] [ B2C-GetStartedCustom] artikeln.

I det här avsnittet ska ändra du principen för att kontrollera användarstatus för migrering på Logga in. Om du inte ändrar lösenordet kan returnera ett HTTP 409 felmeddelande som uppmanar användaren att välja den **har du glömt lösenordet?** länk.

För att spåra lösenordsändringen måste använda du en Azure-tabell. När du kör processen innan migrering med kommandoradsparametern `2`, du skapar en entitet i en Azure-tabell. Tjänsten gör följande:

- Om inloggningen anropar Azure AD B2C-princip migreringen RESTful-tjänst, skicka ett e-postmeddelande som ett inkommande anspråk. Tjänsten söker efter e-postadress i Azure-tabellen. Om adressen finns utlöser tjänsten ett felmeddelande visas: *Du måste ändra lösenord*.

- När användaren ändrar har lösenordet, kan du ta bort entiteten från Azure-tabellen.

>[!NOTE]
>Vi använder en Azure-tabell för att förenkla exemplet. Du kan lagra Migreringsstatus för i en databas eller som en anpassad egenskap i Azure AD B2C-konto.

### <a name="41-update-your-application-setting"></a>4.1: Uppdatera din programinställning
1. Testa demon RESTful-API genom att öppna `AADB2C.UserMigration.sln` i Visual Studio.

1. I den `AADB2C.UserMigration.API` projektet öppnar den *Web.config* fil. Ersätter inställningen med den som konfigurerats i [steg 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Steg 4.2: Distribuera ditt webbprogram till Azure App Service
I Solution Explorer högerklickar du på den `AADB2C.UserMigration.API`, Välj ”publicera...”. Följ anvisningarna för att publicera till Azure App Service. Mer information finns i [distribuera din app till Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Steg 4.3: Lägga till en tekniska profilen och tekniska profilen verifiering i din princip
1. Expandera ”lösningsobjekt” i Solution Explorer och öppna den *TrustFrameworkExtensions.xml* principfil.
1. Ändra `TenantId`, `PublicPolicyUri` och `<TenantId>` fält från `yourtenant.onmicrosoft.com` till namnet på din klient.
1. Under den `<TechnicalProfile Id="login-NonInteractive">` element, Ersätt alla förekomster av `ProxyIdentityExperienceFrameworkAppId` och `IdentityExperienceFrameworkAppId` med program-ID: N som konfigurerats i [komma igång med anpassade principer][B2C-GetStartedCustom].
1. Under den `<ClaimsProviders>` nod, hitta följande XML-fragment. Ändra värdet för `ServiceUrl` så att den pekar till din Azure App Service-URL.

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

Föregående tekniska profil definierar ett inkommande anspråk: `signInName` (skicka som e-post). Om inloggningen skickas anspråket till din RESTful-slutpunkt.

När du har definierat den tekniska profilen för ditt RESTful-API, berätta för din Azure AD B2C-princip för att anropa den tekniska profilen. XML-kodstycke åsidosätter `SelfAsserted-LocalAccountSignin-Email`, som definieras i basprincipen. XML-kodfragment lägger också till `ValidationTechnicalProfile`, med ReferenceId som pekar på den tekniska profilen `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Steg 4.4: Ladda upp principen till din klient
1. I den [Azure-portalen][Portal], växla till den [kontext som din Azure AD B2C-klient][B2C-NavContext], och välj sedan **Azure AD B2C**.

1. Välj **Identitetsramverk**.

1. Välj **alla principer**.

1. Välj **överföra princip**.

1. Välj den **Skriv över principen om den finns** markerar du kryssrutan.

1. Ladda upp den *TrustFrameworkExtensions.xml* filen och se till att den godkänns vid.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Steg 4.5: Testa den anpassade principen med hjälp av kör nu
1. Välj **Azure AD B2C-inställningar**, och gå till **Identitetsramverk**.

1. Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassa princip som du överförde och väljer sedan **kör nu**.

1. Försök att logga in med något av de migrerade användarna autentiseringsuppgifter och välj sedan **logga In**. REST-API utlösa följande felmeddelande visas:

    ![Set-diagnostikloggar](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Steg 4.6: (Valfritt) Felsöka REST-API
Du kan visa och övervaka loggningsinformation i nära realtid.

1. På menyn för programmets RESTful inställningar under **övervakning**väljer **diagnostikloggar**.

1. Ange **programloggning (filsystem)** till **på**.

1. Ange den **nivå** till **utförlig**.

1. Välj **spara**

    ![Set-diagnostikloggar](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. På den **inställningar** menyn och välj **loggström**.

1. Kontrollera resultatet av RESTful-API.

> [!IMPORTANT]
> Använd diagnostikloggar endast under utveckling och testning. RESTful-API-utdata kan innehålla konfidentiell information som inte ska göras tillgänglig i produktion.
>

## <a name="optional-download-the-complete-policy-files"></a>(Valfritt) Hämta de fullständiga principfilerna
När du har slutfört den [Kom igång med anpassade principer] [ B2C-GetStartedCustom] genomgången ska vi rekommenderar att du skapar ditt scenario genom att använda din egen anpassade principfiler. För referens har vi samlat [exempel principfiler][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/yoelhor/Azure-AD-B2C-UserMigration
