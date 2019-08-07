---
title: Tjänst-till-tjänst-autentisering för Azure Key Vault med .NET
description: Använd Microsoft. Azure. Services. AppAuthentication-biblioteket för att autentisera till Azure Key Vault med .NET.
keywords: lokal inloggning för Azure Key-valv-autentisering
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: d34c94ccca47d29afc4f3d83bec58db737be270c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840410"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Tjänst-till-tjänst-autentisering för Azure Key Vault med .NET

För att autentisera till Azure Key Vault behöver du en Azure Active Directory-autentiseringsuppgift (AD), antingen en delad hemlighet eller ett certifikat. 

Det kan vara svårt att hantera sådana autentiseringsuppgifter och det är frestande att paketera autentiseringsuppgifter i en app genom att inkludera dem i käll-eller konfigurationsfiler.  `Microsoft.Azure.Services.AppAuthentication` För .net-biblioteket fören klar det här problemet. Den använder utvecklarens autentiseringsuppgifter för att autentisera under lokal utveckling. När lösningen senare distribueras till Azure växlar biblioteket automatiskt till programmets autentiseringsuppgifter.    Att använda autentiseringsuppgifter för utvecklare under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare.

`Microsoft.Azure.Services.AppAuthentication` Biblioteket hanterar autentisering automatiskt, vilket i sin tur gör att du kan fokusera på din lösning i stället för dina autentiseringsuppgifter.  Den stöder lokal utveckling med Microsoft Visual Studio, Azure CLI eller Azure AD Integrated Authentication. När det distribueras till en Azure-resurs som har stöd för en hanterad identitet, använder biblioteket automatiskt [hanterade identiteter för Azure-resurser](../active-directory/msi-overview.md). Inga kod-eller konfigurations ändringar krävs. Biblioteket stöder också direkt användning av autentiseringsuppgifter för Azure AD- [klient](../azure-resource-manager/resource-group-authenticate-service-principal.md) när en hanterad identitet inte är tillgänglig, eller när utvecklarens säkerhets kontext inte kan fastställas under lokal utveckling.

## <a name="using-the-library"></a>Använda biblioteket

För .NET-program är det enklaste sättet att arbeta med en hanterad identitet genom `Microsoft.Azure.Services.AppAuthentication` paketet. Så här kommer du igång:

1. Lägg till referenser till [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -och [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-paketen i programmet. 

2. Lägg till följande kod:

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

`AzureServiceTokenProvider` Klassen cachelagrar token i minnet och hämtar den från Azure AD precis före förfallo datum. Därför behöver du inte längre kontrol lera förfallo tiden innan du anropar `GetAccessTokenAsync` -metoden. Anropa bara metoden när du vill använda token. 

`GetAccessTokenAsync` Metoden kräver en resurs identifierare. Mer information finns i [vilka Azure-tjänster som har stöd för hanterade identiteter för Azure-resurser](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Lokal utvecklings autentisering

Det finns två huvudsakliga autentiserings scenarier för lokal utveckling: [autentisera till Azure-tjänster](#authenticating-to-azure-services)och [autentisera till anpassade tjänster](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autentisera till Azure-tjänster

Lokala datorer har inte stöd för hanterade identiteter för Azure-resurser.  Därför `Microsoft.Azure.Services.AppAuthentication` använder biblioteket dina autentiseringsuppgifter för utvecklare för att köra i din lokala utvecklings miljö. När lösningen har distribuerats till Azure, använder-biblioteket en hanterad identitet för att växla till ett OAuth 2,0-klientens Credential Grant-flöde.  Det innebär att du kan testa samma kod lokalt och via fjärr anslutning utan att oroa dig.

För `AzureServiceTokenProvider` lokal utveckling hämtar token med **Visual Studio**, **Azure Command-Line Interface** (CLI) eller **Azure AD Integrated Authentication**. Varje alternativ provas sekventiellt och biblioteket använder det första alternativet som lyckas. Om inget alternativ fungerar, genereras `AzureServiceTokenProviderException` ett undantag med detaljerad information.

### <a name="authenticating-with-visual-studio"></a>Autentisering med Visual Studio

Att autentisera med Visual Studio uppfyller följande krav:

1. [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) eller senare.

2. [Tillägget för app-autentisering för Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), tillgängligt som ett separat tillägg för visual Studio 2017 uppdatering 5 och tillsammans med produkten i uppdatering 6 och senare. Med uppdatering 6 eller senare kan du kontrol lera installationen av tillägget för app-autentisering genom att välja Azure utvecklingsverktyg i Visual Studio Installer.
 
Logga in i Visual Studio och Använd **verktyg**&nbsp;&nbsp;&nbsp;alternativ>Azure Service Authentication för att välja ett konto för lokal utveckling. >&nbsp; 

Om du stöter på problem med hjälp av Visual Studio, till exempel fel som rör token Provider-filen, läser du noggrant igenom de här stegen. 

Det kan också vara nödvändigt att omautentisera din Developer-token. Det gör du genom att gå **till verktyg**&nbsp;>>alternativ Azure**service-&nbsp;autentisering och leta efter en återautentiserings länk under den valda&nbsp;** &nbsp; redovisning.  Välj den för att autentisera. 

### <a name="authenticating-with-azure-cli"></a>Autentisera med Azure CLI

Så här använder du Azure CLI för lokal utveckling:

1. Installera [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) eller senare. Uppgradera tidigare versioner. 

2. Använd **AZ login** för att logga in på Azure.

Används `az account get-access-token` för att kontrol lera åtkomst.  Om du får ett fel meddelande kontrollerar du att steg 1 har slutförts. 

Om Azure CLI inte är installerat i standard katalogen kan du få en fel rapportering som `AzureServiceTokenProvider` inte kan hitta sökvägen för Azure CLI.  Använd miljövariabeln **AzureCLIPath** för att definiera installations mappen för Azure CLI. `AzureServiceTokenProvider`lägger till den katalog som anges i miljövariabeln **AzureCLIPath** till **Path** -miljövariabeln när det behövs.

Om du är inloggad på Azure CLI med flera konton eller om ditt konto har åtkomst till flera prenumerationer måste du ange den angivna prenumerationen som ska användas.  Det gör du genom att använda:

```
az account set --subscription <subscription-id>
```

Det här kommandot genererar endast utdata vid ett haveri.  Verifiera de aktuella konto inställningarna med:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Autentisera med Azure AD-autentisering

Om du vill använda Azure AD-autentisering kontrollerar du att:

- Dina lokala Active Directory- [synkroniseringar till Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- Koden körs på en domänansluten dator.


### <a name="authenticating-to-custom-services"></a>Autentisera till anpassade tjänster

När en tjänst anropar Azure-tjänster fungerar föregående steg eftersom Azure-tjänster tillåter åtkomst till både användare och program.  

När du skapar en tjänst som anropar en anpassad tjänst ska du använda Azure AD-klientautentiseringsuppgifter för lokal utvecklings autentisering.  Det finns två alternativ: 

1.  Använd ett huvud namn för tjänsten för att logga in på Azure:

    1.  [Skapa ett huvud namn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Använd Azure CLI för att logga in:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Eftersom tjänstens huvud namn kanske inte har åtkomst till en prenumeration använder du `--allow-no-subscriptions` argumentet.

2.  Använd miljövariabler för att ange information om tjänstens huvud namn.  Mer information finns i [köra programmet med ett huvud namn för tjänsten](#running-the-application-using-a-service-principal).

När du har loggat in på Azure `AzureServiceTokenProvider` använder tjänstens huvud namn för att hämta en token för lokal utveckling.

Detta gäller endast för lokal utveckling. När din lösning distribueras till Azure växlar biblioteket till en hanterad identitet för autentisering.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Köra programmet med hanterad identitet eller användare tilldelad identitet 

När du kör din kod på en Azure App Service eller en virtuell Azure-dator med en hanterad identitet aktive rad använder biblioteket automatiskt den hanterade identiteten. 

Alternativt kan du autentisera med en användardefinierad identitet. Mer information om användarspecifika identiteter finns i [om hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Om du vill autentisera med en tilldelad identitet måste du ange klient-ID: t för den användare som tilldelats identiteten i anslutnings strängen. Anslutnings strängen anges i avsnittet stöd för [anslutnings strängen](#connection-string-support) nedan.

## <a name="running-the-application-using-a-service-principal"></a>Köra programmet med ett huvud namn för tjänsten 

Det kan vara nödvändigt att skapa en Azure AD-klientautentiseringsuppgifter för att autentisera. Vanliga exempel är:

- Din kod körs i en lokal utvecklings miljö, men inte under utvecklarens identitet.  Service Fabric till exempel använder [NetworkService-kontot](../service-fabric/service-fabric-application-secret-management.md) för lokal utveckling.
 
- Din kod körs i en lokal utvecklings miljö och du autentiserar till en anpassad tjänst, så du kan inte använda din utvecklares identitet. 
 
- Din kod körs på en Azure Compute-resurs som ännu inte har stöd för hanterade identiteter för Azure-resurser, t. ex. Azure Batch.

Det finns tre primära metoder för att använda ett huvud namn för tjänsten för att köra programmet. Om du vill använda någon av dem måste du först [skapa ett huvud namn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Använd ett certifikat i lokal nyckel lagring för att logga in på Azure AD

1. Skapa ett tjänst huvud certifikat med hjälp av Azure CLI [-AZ AD SP Create-for-RBAC-](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommandot. 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Detta skapar en. PEM-fil (privat nyckel) som kommer att lagras i din arbets katalog. Distribuera det här certifikatet till antingen *LocalMachine* -eller *CurrentUser* -butiken. 

    > [!Important]
    > CLI-kommandot genererar en. PEM-fil, men Windows tillhandahåller endast inbyggt stöd för PFX-certifikat. Om du vill generera ett PFX-certifikat i stället använder du PowerShell-kommandona som visas här: [Skapa tjänstens huvud namn med självsignerat certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Dessa kommandon distribuerar även certifikatet automatiskt.

1. Ange en miljö variabel med namnet **AzureServicesAuthConnectionString** som:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Ersätt *{AppId}* , *{TenantId}* och *{tumavtryck}* med värden som genereras i steg 1. Ersätt *{CertificateStore}* med antingen `LocalMachine` eller `CurrentUser`, baserat på din distributions plan.

1. Kör appen. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Använd delade hemliga autentiseringsuppgifter för att logga in på Azure AD

1. Skapa ett certifikat för tjänstens huvud namn med ett lösen ord med hjälp av [AZ AD SP Create-for-RBAC--Password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Ange en miljö variabel med namnet **AzureServicesAuthConnectionString** som:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Ersätt _{AppId}_ , _{TenantId}_ och _{ClientSecret}_ med värden som genereras i steg 1.

3. Kör appen. 

När allt har kon figurer ATS korrekt behövs inga ytterligare kod ändringar.  `AzureServiceTokenProvider`använder miljövariabeln och certifikatet för att autentisera till Azure AD. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Använd ett certifikat i Key Vault för att logga in på Azure AD

Med det här alternativet kan du lagra ett tjänst objekts klient certifikat i Key Vault och använda det för autentisering av tjänstens huvud namn. Du kan använda detta i följande scenarier:

* Lokal autentisering, där du vill autentisera med hjälp av ett explicit huvud namn för tjänsten och vill behålla autentiseringsuppgifterna för tjänstens huvud namn på ett säkert sätt i ett nyckel valv. Developer-kontot måste ha åtkomst till nyckel valvet. 
* Autentisering från Azure där du vill använda explicita autentiseringsuppgifter (t. ex. för scenarier med flera innehavare) och vill behålla autentiseringsuppgifterna för tjänstens huvud namn på ett säkert sätt i ett nyckel valv. Hanterad identitet måste ha åtkomst till nyckel valvet. 

Den hanterade identiteten eller din utvecklares identitet måste ha behörighet att hämta klient certifikatet från Key Vault. AppAuthentication-biblioteket använder det hämtade certifikatet som tjänst huvud kontots klientautentiseringsuppgifter.

Använda ett klient certifikat för tjänstens huvud namns autentisering

1. Skapa ett huvud namn för tjänsten och lagra det automatiskt i ditt nyckel valv med hjälp av Azure CLI- [AZ AD SP Create-for-RBAC- <keyvaultname> --cert <certificatename> --cert--Create-cert-– Skip-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommando:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    Certifikat-ID: n är en URL i formatet`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Ersätt `{KeyVaultCertificateSecretIdentifier}` i den här anslutnings strängen med certifikat-ID:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Om till exempel ditt nyckel valv kallades "Mittkeyvault" och du skapade ett certifikat med namnet "cert", skulle certifikat-ID: t vara:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>Stöd för anslutnings sträng

Som standard `AzureServiceTokenProvider` använder flera metoder för att hämta en token. 

Om du vill kontrol lera processen använder du en anslutnings sträng som `AzureServiceTokenProvider` skickas till konstruktorn eller anges i miljövariabeln *AzureServicesAuthConnectionString* . 

Följande alternativ stöds:

| Alternativ för anslutnings sträng | Scenario | Kommentar|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokal utveckling | AzureServiceTokenProvider använder AzureCli för att hämta token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokal utveckling | AzureServiceTokenProvider använder Visual Studio för att hämta token. |
| `RunAs=CurrentUser` | Lokal utveckling | AzureServiceTokenProvider använder Azure AD-integrerad autentisering för att hämta token. |
| `RunAs=App` | [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider använder en hanterad identitet för att hämta token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Användar tilldelad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider använder en användardefinierad identitet för att hämta token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autentisering av anpassade tjänster | KeyVaultCertificateSecretIdentifier = certifikatets hemliga ID. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Tjänstens huvudnamn | `AzureServiceTokenProvider`använder certifikat för att hämta token från Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Tjänstens huvudnamn | `AzureServiceTokenProvider`använder certifikat för att hämta token från Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Tjänstens huvudnamn |`AzureServiceTokenProvider`använder hemlighet för att hämta token från Azure AD. |

## <a name="samples"></a>Exempel

Se följande kod `Microsoft.Azure.Services.AppAuthentication` exempel för att se hur biblioteket fungerar.

1. [Använd en hanterad identitet för att hämta en hemlighet från Azure Key Vault vid körning](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Distribuera en Azure Resource Manager-mall program mässigt från en virtuell Azure-dator med en hanterad identitet](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Använd .net Core-exempel och en hanterad identitet för att anropa Azure-tjänster från en virtuell Azure Linux-dator](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>AppAuthentication-felsökning

### <a name="common-issues-during-local-development"></a>Vanliga problem under lokal utveckling

#### <a name="azure-cli-is-not-installed-you-are-not-logged-in-or-you-do-not-have-the-latest-version"></a>Azure CLI är inte installerat, du är inte inloggad eller har inte den senaste versionen

Kör **AZ-konto get-Access-token** för att se om Azure CLI visar en token åt dig. Om inget sådant program hittas installerar du den [senaste versionen av Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Om du har installerat det kan du uppmanas att logga in. 
 
#### <a name="azureservicetokenprovider-cannot-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider kan inte hitta sökvägen till Azure CLI

AzureServiceTokenProvider letar efter Azure CLI på standard installations platserna. Om du inte hittar Azure CLI ställer du in miljövariabeln **AzureCLIPath** till installationsmappen för Azure CLI. AzureServiceTokenProvider kommer att lägga till miljövariabeln i miljövariabeln PATH.
 
#### <a name="you-are-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Du är inloggad på Azure CLI med flera konton, samma konto har åtkomst till prenumerationer i flera klienter, eller så får du ett meddelande om nekad åtkomst när du försöker ringa samtal under lokal utveckling

Använd Azure CLI och Ställ in standard prenumerationen på en som har det konto som du vill använda och som finns i samma klient organisation som den resurs som du vill få åtkomst till: **AZ-konto uppsättning--prenumeration [prenumerations-ID]** . Om inga utdata visas, lyckades det. Kontrol lera att rätt konto är nu standard **konto listan med AZ**.

### <a name="common-issues-across-environments"></a>Vanliga problem i miljöer

#### <a name="unauthorized-access-access-denied-forbidden-etc-error"></a>Obehörig åtkomst, åtkomst nekad, förbjuden, etc.-fel
 
Huvud kontot som används har inte åtkomst till den resurs som det försöker komma åt. Bevilja antingen ditt användar konto eller App Services MSI "Contributor" åtkomst till önskad resurs, beroende på om du kör exemplet på din lokala utvecklings dator eller distribuerat i Azure till din App Service. Vissa resurser, som nyckel valv, har också sina egna [åtkomst principer](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) som du använder för att bevilja åtkomst till huvud konton (användare, appar, grupper osv.).

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Vanliga problem vid distribution till Azure App Service

#### <a name="managed-identity-is-not-setup-on-the-app-service"></a>Hanterad identitet konfigureras inte på App Service
 
Kontrol lera miljövariablerna MSI_ENDPOINT och MSI_SECRET finns med hjälp av kudu-felsöknings [konsolen](https://azure.microsoft.com/en-us/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Om dessa miljövariabler inte finns är hanterad identitet inte aktive rad på App Service. 
 
### <a name="common-issues-when-deployed-locally-with-iis"></a>Vanliga problem vid distribution lokalt med IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Det går inte att hämta tokens vid fel sökning av appen i IIS

Som standard körs AppAuth i en annan användar kontext i IIS och har därför inte åtkomst att använda din utvecklares identitet för att hämta åtkomsttoken. Du kan konfigurera IIS att köras med användar kontexten med följande två steg:
- Konfigurera programpoolen för webbappen så att den körs som ditt aktuella användar konto. Mer information finns [här](https://docs.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Konfigurera "setProfileEnvironment" till "true". Mer information finns [här](https://docs.microsoft.com/en-us/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Gå till%windir%\System32\inetsrv\config\applicationHost.config
    - Sök efter "setProfileEnvironment". Om den är inställd på "falskt" ändrar du den till "true". Om den inte finns lägger du till den som ett attribut till processModel-elementet (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) och anger det till "true".

- Lär dig mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/index.yml).
- Läs mer om [Azure AD-autentisering](../active-directory/develop/active-directory-authentication-scenarios.md).
