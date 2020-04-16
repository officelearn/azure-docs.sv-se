---
title: Tjänst-till-tjänst-autentisering till Azure Key Vault med .NET
description: Använd biblioteket Microsoft.Azure.Services.AppAuthentication för att autentisera till Azure Key Vault med .NET.
keywords: lokala autentiseringsuppgifter för azure key-vault
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: d6ac5961cbecf4e81c0b6bcc25c39aad42b18416
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429842"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Tjänst-till-tjänst-autentisering till Azure Key Vault med .NET

För att autentisera till Azure Key Vault behöver du en Azure Active Directory -autentiseringsuppgifter (Azure AD), antingen en delad hemlighet eller ett certifikat.

Det kan vara svårt att hantera sådana autentiseringsuppgifter. Det är frestande att paketera autentiseringsuppgifter i en app genom att inkludera dem i käll- eller konfigurationsfiler. För `Microsoft.Azure.Services.AppAuthentication` .NET-biblioteket förenklar det här problemet. Den använder utvecklarens autentiseringsuppgifter för att autentisera under lokal utveckling. När lösningen senare distribueras till Azure växlar biblioteket automatiskt till programautentiseringsuppgifter. Att använda utvecklarautentiseringsuppgifter under lokal utveckling är säkrare eftersom du inte behöver skapa Azure AD-autentiseringsuppgifter eller dela autentiseringsuppgifter mellan utvecklare.

Biblioteket `Microsoft.Azure.Services.AppAuthentication` hanterar autentisering automatiskt, vilket i sin tur gör att du kan fokusera på din lösning, snarare än dina autentiseringsuppgifter. Den stöder lokal utveckling med Microsoft Visual Studio, Azure CLI eller Azure AD Integrated Authentication. När du distribueras till en Azure-resurs som stöder en hanterad identitet använder biblioteket automatiskt [hanterade identiteter för Azure-resurser](../../active-directory/msi-overview.md). Ingen kod eller konfigurationsändringar krävs. Biblioteket stöder också direkt användning av Azure [AD-klientautentiseringsuppgifter](../../azure-resource-manager/resource-group-authenticate-service-principal.md) när en hanterad identitet inte är tillgänglig eller när utvecklarens säkerhetskontext inte kan fastställas under lokal utveckling.

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) eller [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Tillägget Appautentisering för Visual Studio, tillgängligt som ett separat tillägg för Visual Studio 2017 Update 5 och levereras med produkten i uppdatering 6 och senare. Med uppdatering 6 eller senare kan du verifiera installationen av tillägget App Authentication genom att välja Azure Development-verktyg inifrån Visual Studio-installationsprogrammet.

## <a name="using-the-library"></a>Använda biblioteket

För .NET-program är det enklaste sättet att arbeta `Microsoft.Azure.Services.AppAuthentication` med en hanterad identitet genom paketet. Så här kommer du igång:

1. Välj **Verktyg** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning om** du vill lägga till referenser till [microsoft.Azure.Services.AppAuthentication-](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-paket till ditt projekt.

1. Lägg till följande kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Klassen `AzureServiceTokenProvider` cachelagrar token i minnet och hämtar den från Azure AD strax före förfallodatum. Så du behöver inte längre kontrollera utgångsdatumet innan du `GetAccessTokenAsync` anropar metoden. Anropa bara metoden när du vill använda token.

Metoden `GetAccessTokenAsync` kräver en resursidentifierare. Mer information om Microsoft Azure-tjänster finns i [Vad är hanterade identiteter för Azure-resurser](../../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Autentisering av lokal utveckling

För lokal utveckling finns det två primära autentiseringsscenarier: [autentisera till Azure-tjänster](#authenticating-to-azure-services)och [autentisera till anpassade tjänster](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autentisera till Azure Services

Lokala datorer stöder inte hanterade identiteter för Azure-resurser. Därför använder `Microsoft.Azure.Services.AppAuthentication` biblioteket dina utvecklarautentiseringsuppgifter för att köras i din lokala utvecklingsmiljö. När lösningen distribueras till Azure använder biblioteket en hanterad identitet för att växla till ett OAuth 2.0-klientautentiseringsbidragsflöde. Den här metoden innebär att du kan testa samma kod lokalt och på distans utan att oroa dig.

För lokal `AzureServiceTokenProvider` utveckling hämtar du token med **Visual Studio,** **Azure command-line interface** (CLI) eller Azure AD Integrated **Authentication**. Varje alternativ provas sekventiellt och biblioteket använder det första alternativet som lyckas. Om inget alternativ `AzureServiceTokenProviderException` fungerar genereras ett undantag med detaljerad information.

#### <a name="authenticating-with-visual-studio"></a>Autentisera med Visual Studio

Så här autentiserar du med Visual Studio:

1. Logga in i Visual Studio och använd&nbsp;>&nbsp;**verktygsalternativ** för att öppna **Alternativ**. **Tools**

1. Välj **Azure Service Authentication**, välj ett konto för lokal utveckling och välj **OK**.

Om du stöter på problem med att använda Visual Studio, till exempel fel som involverar tokenproviderfilen, bör du noggrant granska föregående steg.

Du kan behöva omauktorisera utvecklartoken. Det gör du genom att välja&nbsp;>&nbsp;**Verktygsalternativ**och sedan **välja Azure&nbsp;Service&nbsp;Authentication**. **Tools** Leta efter en **åter autentisera** länk under det valda kontot. Markera den för att autentisera.

#### <a name="authenticating-with-azure-cli"></a>Autentisera med Azure CLI

Om du vill använda Azure CLI för lokal utveckling måste du se till att du har version [Azure CLI v2.0.12](/cli/azure/install-azure-cli) eller senare.

Så här använder du Azure CLI:

1. Sök efter Azure CLI i Aktivitetsfältet i Windows för att öppna **Microsoft Azure-kommandotolken**.

1. Logga in på Azure-portalen: *az logga in* för att logga in på Azure.

1. Verifiera åtkomst genom att ange *az-konto https://vault.azure.netget-access-token --resource *. Om du får ett felmeddelande kontrollerar du att rätt version av Azure CLI är korrekt installerad.

   Om Azure CLI inte är installerat i standardkatalogen kan `AzureServiceTokenProvider` du få en felrapportering som inte kan hitta sökvägen till Azure CLI. Använd variabeln **AzureCLIPath-miljö** för att definiera azure CLI-installationsmappen. `AzureServiceTokenProvider`lägger till den katalog som anges i variabeln **AzureCLIPath-miljö** i **sökvägsmiljövariabeln** när det behövs.

1. Om du är inloggad på Azure CLI med flera konton eller om ditt konto har åtkomst till flera prenumerationer måste du ange vilken prenumeration som ska användas. Ange kommandot *az-kontouppsättningen --subscription <prenumerations-ID>*.

Det här kommandot genererar endast utdata vid fel. Om du vill verifiera de `az account list`aktuella kontoinställningarna anger du kommandot .

#### <a name="authenticating-with-azure-ad-authentication"></a>Autentisera med Azure AD-autentisering

Om du vill använda Azure AD-autentisering kontrollerar du att:

- Din lokala Active Directory synkroniseras med Azure AD. Mer information finns i [Vad är hybrididentitet med Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Koden körs på en domänansluten dator.

### <a name="authenticating-to-custom-services"></a>Autentisera till anpassade tjänster

När en tjänst anropar Azure-tjänster fungerar de föregående stegen eftersom Azure-tjänster ger åtkomst till både användare och program.

När du skapar en tjänst som anropar en anpassad tjänst använder du Azure AD-klientautentiseringsuppgifter för autentisering av lokal utveckling. Det finns två alternativ:

- Använd ett tjänsthuvudnamn för att logga in på Azure:

    1. Skapa ett huvudnamn för tjänsten. Mer information finns i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Använd Azure CLI för att logga in med följande kommando:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Eftersom tjänstens huvudnamn kanske inte har `--allow-no-subscriptions` åtkomst till en prenumeration använder du argumentet.

- Använd miljövariabler för att ange information om tjänstens huvudnamn. Mer information finns i [Köra programmet med hjälp av ett huvudnamn för tjänsten](#running-the-application-using-a-service-principal).

När du har loggat `AzureServiceTokenProvider` in på Azure använder du tjänstens huvudnamn för att hämta en token för lokal utveckling.

Detta tillvägagångssätt gäller endast för lokal utveckling. När din lösning distribueras till Azure växlar biblioteket till en hanterad identitet för autentisering.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Köra programmet med hanterad identitet eller användartilldelad identitet

När du kör din kod på en Azure App Service eller en Azure VM med en hanterad identitet aktiverad, använder biblioteket automatiskt den hanterade identiteten. Inga kodändringar krävs, men den hanterade identiteten måste *ha* behörighet för nyckelvalvet. Du kan ge den hanterade *identiteten* behörigheter via nyckelvalvets *åtkomstprinciper*.

Du kan också autentisera med en användartilldelad identitet. Mer information om användartilldelade identiteter finns i [Om hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Om du vill autentisera med en användartilldelad identitet måste du ange klient-ID för den användartilldelade identiteten i anslutningssträngen. Anslutningssträngen anges i [stöd för anslutningssträng](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Köra programmet med hjälp av ett tjänsthuvudnamn

Det kan vara nödvändigt att skapa en Azure AD-klientautentiseringsuppgifter för att autentisera. Den här situationen kan inträffa i följande exempel:

- Koden körs i en lokal utvecklingsmiljö, men inte under utvecklarens identitet. Service Fabric, till exempel, använder [NetworkService-kontot](../../service-fabric/service-fabric-application-secret-management.md) för lokal utveckling.

- Koden körs i en lokal utvecklingsmiljö och du autentiserar till en anpassad tjänst, så att du inte kan använda utvecklaridentiteten.

- Koden körs på en Azure-beräkningsresurs som ännu inte stöder hanterade identiteter för Azure-resurser, till exempel Azure Batch.

Det finns tre primära metoder för att använda ett tjänsthuvudnamn för att köra ditt program. Om du vill använda någon av dem måste du först skapa ett huvudnamn för tjänsten. Mer information finns i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Använda ett certifikat i lokal keystore för att logga in på Azure AD

1. Skapa ett huvudcertifikat för tjänsten med kommandot Azure CLI [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Med det här kommandot skapas en PEM-fil (privat nyckel) som lagras i arbetskatalogen. Distribuera det här certifikatet till antingen *LocalMachine* eller *CurrentUser-arkivet.*

    > [!Important]
    > CLI-kommandot genererar en PEM-fil, men Windows ger bara inbyggt stöd för PFX-certifikat. Om du vill generera ett PFX-certifikat i stället använder du PowerShell-kommandona som visas här: [Skapa tjänstens huvudnamn med självsignerat certifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Dessa kommandon distribuerar automatiskt certifikatet också.

1. Ange en miljövariabel med namnet **AzureServicesAuthConnectionString** till följande värde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Ersätt *{AppId}*, *{TenantId}* och *{Thumbprint}* med värden som genereras i steg 1. Ersätt *{CertificateStore}* med antingen *LocalMachine*' eller *CurrentUser*, baserat på distributionsplanen.

1. Kör appen.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Använda en delad hemlig autentiseringsinformation för att logga in på Azure AD

1. Skapa ett huvudcertifikat för tjänsten med ett lösenord med kommandot Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) med parametern --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Ange en miljövariabel med namnet **AzureServicesAuthConnectionString** till följande värde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Ersätt _{AppId}_, _{TenantId}_ och _{ClientSecret}_ med värden som genereras i steg 1.

1. Kör appen.

När allt är korrekt inställt behövs inga ytterligare kodändringar. `AzureServiceTokenProvider`använder miljövariabeln och certifikatet för att autentisera till Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Använda ett certifikat i Key Vault för att logga in på Azure AD

Med det här alternativet kan du lagra ett tjänsthuvudnamns klientcertifikat i Key Vault och använda det för autentisering av tjänstens huvudnamn. Du kan använda det här alternativet för följande scenarier:

- Lokal autentisering, där du vill autentisera med ett uttryckligt tjänsthuvudnamn, och vill behålla tjänstens huvudautentisering på ett säkert sätt i ett nyckelvalv. Utvecklarkontot måste ha åtkomst till nyckelvalvet.

- Autentisering från Azure där du vill använda explicita autentiseringsuppgifter och vill behålla tjänstens huvudautentiseringsuppgifter säkert i ett nyckelvalv. Du kan använda det här alternativet för ett scenario mellan innehavare. Hanterad identitet måste ha åtkomst till nyckelvalvet.

Den hanterade identiteten eller utvecklaridentiteten måste ha behörighet att hämta klientcertifikatet från Key Vault. AppAuthentication-biblioteket använder det hämtade certifikatet som tjänsthuvudhuvudets klientautentiseringsuppgifter.

Så här använder du ett klientcertifikat för autentisering av tjänstens huvudnamn:

1. Skapa ett huvudcertifikat för tjänsten och lagra det automatiskt i key vault.Create a service principal certificate and automatically store it in your Key Vault. Använd azure CLI [az ad sp create-for-rbac \<--keyvault keyvaultname \<> --cert-certifikatnamn> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) command:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Certifikatidentifieraren kommer att vara en URL i formatet`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Ersätt `{KeyVaultCertificateSecretIdentifier}` i den här anslutningssträngen med certifikatidentifieraren:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Om nyckelvalvet till exempel hette *myKeyVault* och du skapade ett certifikat som heter *myCert*skulle certifikatidentifieraren vara:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Stöd för anslutningssträng

Som standard `AzureServiceTokenProvider` använder flera metoder för att hämta en token.

Om du vill styra processen använder `AzureServiceTokenProvider` du en anslutningssträng som skickas till konstruktorn eller som anges i variabeln *AzureServicesAuthConnectionString.*

Följande alternativ stöds:

| Alternativet Anslutningssträng | Scenario | Kommentarer|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokal utveckling | `AzureServiceTokenProvider`använder AzureCli för att hämta token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokal utveckling | `AzureServiceTokenProvider`använder Visual Studio för att hämta token. |
| `RunAs=CurrentUser` | Lokal utveckling | `AzureServiceTokenProvider`använder Azure AD-integrerad autentisering för att hämta token. |
| `RunAs=App` | [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`använder en hanterad identitet för att hämta token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Användartilldelade identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`använder en användartilldelad identitet för att hämta token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autentisering av anpassade tjänster | `KeyVaultCertificateSecretIdentifier`är certifikatets hemliga identifierare. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Tjänstens huvudnamn | `AzureServiceTokenProvider`använder certifikat för att hämta token från Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Tjänstens huvudnamn | `AzureServiceTokenProvider`använder certifikat för att hämta token från Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Tjänstens huvudnamn |`AzureServiceTokenProvider`använder hemlighet för att hämta token från Azure AD. |

## <a name="samples"></a>Exempel

Information om `Microsoft.Azure.Services.AppAuthentication` hur biblioteket fungerar finns i följande kodexempel.

- [Använda en hanterad identitet för att hämta en hemlighet från Azure Key Vault vid körning](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Distribuera en Azure Resource Manager-mall programmatiskt från en virtuell Azure-dator med en hanterad identitet](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Använd .NET Core-exempel och en hanterad identitet för att anropa Azure-tjänster från en Virtuell Azure Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Felsökning av appautentisering

### <a name="common-issues-during-local-development"></a>Vanliga frågor under lokal utveckling

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI är inte installerat, du är inte inloggad eller så har du inte den senaste versionen

Kör *az-konto get-access-token* för att se om Azure CLI visar en token för dig. Om det står **inget sådant program hittades,** installera den [senaste versionen av Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Du kan bli uppmanad att logga in.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider kan inte hitta sökvägen till Azure CLI

AzureServiceTokenProvider söker efter Azure CLI på standardinstallationsplatserna. Om den inte kan hitta Azure CLI anger du miljövariabeln **AzureCLIPath** till Azure CLI-installationsmappen. AzureServiceTokenProvider lägger till miljövariabeln i variabeln Sökvägsmiljö.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Du är inloggad på Azure CLI med flera konton, samma konto har åtkomst till prenumerationer i flera klienter eller så får du ett åtkomstsnad-fel när du försöker ringa samtal under lokal utveckling

Med Hjälp av Azure CLI anger du standardprenumerationen till en som har det konto du vill använda. Prenumerationen måste finnas i samma klient som den resurs som du vill komma åt: **az-kontouppsättningen --subscription [subscription-id]**. Om ingen utgång ses, lyckades det. Verifiera rätt konto är nu standard med **az-kontolista**.

### <a name="common-issues-across-environments"></a>Vanliga problem i olika miljöer

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Obehörig åtkomst, åtkomst nekad, förbjuden eller liknande fel

Huvudkontot som används har inte åtkomst till resursen som det försöker få åtkomst till. Ge antingen ditt användarkonto eller App-tjänstens MSI-deltagare åtkomst till en resurs. Vilken beror på om du kör exemplet på din lokala dator eller distribueras i Azure till din App Service. Vissa resurser, till exempel nyckelvalv, har också egna [åtkomstprinciper](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) som du använder bevilja åtkomst till huvudmän, till exempel användare, appar och grupper.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Vanliga problem när de distribueras till Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Hanterad identitet har inte konfigurerats på Apptjänsten

Kontrollera miljövariablerna MSI_ENDPOINT och MSI_SECRET finns med [Kudu debug-konsolen](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Om dessa miljövariabler inte finns är hanterad identitet inte aktiverad på App-tjänsten.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Vanliga problem när de distribueras lokalt med IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Det går inte att hämta token när du felsöker app i IIS

Som standard körs AppAuth i en annan användarkontext i IIS. Det är därför den inte har åtkomst att använda utvecklaridentiteten för att hämta åtkomsttoken. Du kan konfigurera IIS så att det körs med användarkontexten med följande två steg:
- Konfigurera programpoolen för webbappen så att den körs som ditt aktuella användarkonto. Se mer information [här](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Konfigurera "setProfileEnvironment" till "True". Se mer information [här](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Gå till %windir%\System32\inetsrv\config\applicationHost.config
    - Sök efter "setProfileEnvironment". Om den är inställd på "False" ändrar du den till "True". Om det inte finns lägger du till det som/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmentett attribut till elementet processModel ( ) och ställer in det på "True".

- Läs mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/index.yml).
- Läs mer om [Azure AD-autentiseringsscenarier](../../active-directory/develop/active-directory-authentication-scenarios.md).
