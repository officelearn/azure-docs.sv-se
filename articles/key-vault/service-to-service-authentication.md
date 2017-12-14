---
title: "Tjänst-till-tjänst autentisering till Azure Key Vault med hjälp av .NET"
description: "Använd Microsoft.Azure.Services.AppAuthentication-biblioteket för att autentisera till Azure Key Vault med hjälp av .NET."
keywords: Azure key vault lokala autentiseringsuppgifter
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: 
ms.service: microsoft-keyvault
ms.technology: 
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: f67f81aeee0775ea8d90e4459f2c46266a774786
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Tjänst-till-tjänst autentisering till Azure Key Vault med hjälp av .NET

Du behöver en autentiseringsuppgift för Azure Active Directory (AD), en delad hemlighet eller ett certifikat för att autentisera till Azure Key Vault. Det kan vara svårt att hantera dessa autentiseringsuppgifter och den är nära till hands att paketera autentiseringsuppgifter i en app genom att inkludera dem i källan eller konfigurationsfiler.

Den `Microsoft.Azure.Services.AppAuthentication` för .NET-bibliotek förenklar det här problemet. Utvecklarens autentiseringsuppgifter används för att autentisera under lokal utveckling. När lösningen distribueras senare till Azure, växlar biblioteket automatiskt till autentiseringsuppgifter.  

Med utvecklare autentiseringsuppgifter under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare.

Den `Microsoft.Azure.Services.AppAuthentication` biblioteket hanterar autentisering automatiskt, vilket i sin tur kan du fokusera på din lösning i stället för dina autentiseringsuppgifter.

Den `Microsoft.Azure.Services.AppAuthentication` bibliotek stöder lokal utveckling med Microsoft Visual Studio, Azure CLI eller Azure AD-integrerad autentisering. Vid distribution till Azure App Service eller Azure Virtual Machine (VM), använder biblioteket automatiskt [hanterade tjänstidentiteten](/azure/active-directory/msi-overview) (MSI). Ingen kod eller konfigurationsfil ändringar krävs. Biblioteket stöder även direkt användning av Azure AD [klientens autentiseringsuppgifter](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal) när MSI inte är tillgänglig eller när utvecklarens säkerhetskontext som inte kan fastställas under lokal utveckling.

<a name="asal"></a>
## <a name="using-the-library"></a>Använda biblioteket

För .NET-program är det enklaste sättet att arbeta med en hanterad tjänst identitet (MSI) via den `Microsoft.Azure.Services.AppAuthentication` paketet. Här är hur du kommer igång:

1. Lägg till en referens till den [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet-paket i ditt program.

2. Lägg till följande kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

Den `AzureServiceTokenProvider` klassen cachelagrar token i minnet och hämtar från Azure AD innan upphör att gälla. Därför behöver du inte längre kontrollera upphör att gälla innan du anropar den `GetAccessTokenAsync` metoden. Anropa bara metoden när du vill använda token. 

Den `GetAccessTokenAsync` metoden kräver att en resursidentifierare. Läs mer i [som Azure-tjänster stöder hanterade tjänstidentiteten](https://docs.microsoft.com/en-us/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Exempel

Följande exempel visas den `Microsoft.Azure.Services.AppAuthentication` bibliotek i praktiken:

1. [Använda en hanterad tjänst identitet (MSI) för att hämta en hemlighet från Azure Key Vault vid körning](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Distribuera en Azure Resource Manager-mall från en virtuell Azure-dator med en MSI programmässigt](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Använda .NET Core exempel och MSI för att anropa Azure-tjänster från en Azure Linux VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Lokal utveckling autentisering

Det finns två scenarier för primär autentisering för lokal utveckling:

- [Autentisera till Azure-tjänster](#authenticating-to-azure-services)
- [Autentisering till anpassade tjänster](#authenticating-to-custom-services)

Här kan du lära dig kraven för varje scenario och verktyg.


### <a name="authenticating-to-azure-services"></a>Autentisera till Azure-tjänster

Lokala datorer stöder inte hanteras Service identitet (MSI).  Därför kan den `Microsoft.Azure.Services.AppAuthentication` använder dina autentiseringsuppgifter för utvecklare för att köras i utvecklingsmiljön lokala. När lösningen har distribuerats till Azure, använder MSI för att växla till en OAuth 2.0 autentiseringsuppgifter bevilja flödet.  Det innebär att du kan testa samma kod lokalt och på distans utan att oroa dig.

För lokal utveckling `AzureServiceTokenProvider` hämtar token med **Visual Studio**, **Azure-kommandoradsgränssnittet** (CLI) eller **Azure AD-integrerad autentisering**. Varje alternativ testas i turordning och använder det första alternativet som lyckas. Om inget alternativ fungerar, en `AzureServiceTokenProviderException` undantag med detaljerad information.

### <a name="authenticating-with-visual-studio"></a>Autentisera med Visual Studio

Kontrollera om du vill använda Visual Studio:

1. Du har installerat [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) eller senare.

2. Den [App autentiseringstillägget för Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) är installerad.
 
3. Du har loggat in till Visual Studio och har valt ett konto som används för lokal utveckling. Använd **verktyg**&nbsp;>&nbsp;**alternativ**&nbsp;>&nbsp;**Azure Service autentisering**att välja ett konto för lokal utveckling. 

Om du stöter på problem med hjälp av Visual Studio, till exempel fel om filen tokenleverantör noggrant gå igenom de här stegen. 

Det kan också vara nödvändigt att autentiseras developer-token.  Det gör du genom att gå till **verktyg**&nbsp;>&nbsp;**alternativ**>**Azure&nbsp;Service&nbsp;autentisering**  och leta efter en **återautentisera** länken under det valda kontot.  Välj det att autentisera. 

### <a name="authenticating-with-azure-cli"></a>Autentisera med Azure CLI

Använda Azure CLI för lokal utveckling:

1. Installera [Azure CLI v2.0.12](/cli/azure/install-azure-cli) eller senare. Uppgradera tidigare versioner. 

2. Använd **az inloggningen** att logga in på Azure.

Använd `az account get-access-token` att verifiera åtkomst.  Om du får ett fel kan du kontrollera att steg 1 har slutförts. 

Om Azure CLI inte är installerad i standardkatalogen kan du få ett felmeddelande som rapporteringen som `AzureServiceTokenProvider` går inte att hitta sökvägen för Azure CLI.  Använd den **AzureCLIPath**miljövariabeln för att definiera Azure CLI-installationsmappen. `AzureServiceTokenProvider`lägger till katalogen som anges i den **AzureCLIPath** miljövariabeln den **sökväg** miljövariabeln vid behov.

Om du är inloggad på Azure CLI med flera konton eller ditt konto har åtkomst till flera prenumerationer, måste du ange specifika prenumerationen som ska användas.  Om du vill göra det använder du:

```
az account set --subscription <subscription-id>
```

Det här kommandot genererar utdata endast vid fel.  Om du vill verifiera inställningarna för aktuellt konto, använder du:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Autentisera med Azure AD-integrera autentisering

Om du vill använda Azure AD-autentisering, kontrollerar du att:

- Din lokala active directory- [synkroniseras till Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Din kod körs på en domänansluten dator.


### <a name="authenticating-to-custom-services"></a>Autentisering till anpassade tjänster

När tjänstanrop Azure-tjänster fungerar de här stegen eftersom Azure-tjänster tillåta åtkomst till både användare och program.  

När du skapar en tjänst som anropar en anpassad tjänst använder du autentiseringsuppgifter för Azure AD-klient för lokal utveckling autentisering.  Det finns två alternativ: 

1.  Använd ett huvudnamn för tjänsten för att logga in på Azure:

    1.  [Skapa ett huvudnamn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Använda Azure CLI för att logga in:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Eftersom tjänstens huvudnamn inte kan ha åtkomst till en prenumeration, använder du den `--allow-no-subscriptions` argumentet.

2.  Använd miljövariabler för att ange information om UPN-tjänsten.  Mer information finns i [kör programmet med hjälp av ett huvudnamn för tjänsten](#running-the-application-using-a-service-principal).

När du har loggat in till Azure, `AzureServiceTokenProvider` använder tjänstens huvudnamn för att hämta en token för lokal utveckling.

Detta gäller endast för lokal utveckling. När lösningen har distribuerats till Azure, växlar biblioteket till MSI-autentisering.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Kör programmet med hjälp av en hanterad tjänstidentitet 

När du kör din kod på en Azure App Service eller en virtuell Azure-dator med aktiverat MSI använder hanterade tjänstidentiteten automatiskt i biblioteket. Det krävs inga kodändringar. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Kör programmet med hjälp av ett huvudnamn för tjänsten 

Det vara nödvändigt att skapa en Azure AD-klienten autentiseringsuppgifter vid autentisering. Vanliga exempel:

1. Koden körs på en lokal utvecklingsmiljö, men inte under utvecklarens identitet.  Service Fabric, till exempel använder den [kontot](/azure/service-fabric/service-fabric-application-secret-management) för lokal utveckling.
 
2. Koden körs på en lokal utvecklingsmiljö och du autentisera till en anpassad tjänst så att du kan använda din identitet för utvecklare. 
 
3. Din kod körs på en resurs i Azure-beräkning som ännu inte stöder hanterade tjänstidentiteten som Azure Batch.

Använda ett certifikat för att logga in på Azure AD:

1. Skapa en [huvudnamn tjänstcertifikat](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Distribuera certifikatet till antingen den _LocalMachine_ eller _CurrentUser_ lagras. 

3. Ange miljövariabeln **AzureServicesAuthConnectionString** till:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Ersätt _{AppId}_, _{TenantId}_, och _{Thumbprint}_ med värden som skapades i steg 1.

    **CertificateStoreLocation** måste vara antingen _CurrentUser_ eller _LocalMachine_, baserat på din distributionsplan.

4. Kör appen. 

Om du vill logga in med en Azure delade AD hemliga autentiseringsuppgifter:

1. Skapa en [tjänstens huvudnamn med ett lösenord](/azure/azure-resource-manager/resource-group-authenticate-service-principal) och bevilja åtkomst till Nyckelvalvet. 

2. Ange miljövariabeln **AzureServicesAuthConnectionString** till:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Ersätt _{AppId}_, _{TenantId}_, och _{ClientSecret}_ med värden som skapades i steg 1.

3. Kör appen. 

När allt har ställts in korrekt inga ytterligare kodändringar krävs.  `AzureServiceTokenProvider`använder miljövariabeln och certifikatet för att autentisera till Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Stöd för anslutning sträng

Som standard `AzureServiceTokenProvider` använder flera metoder för att hämta en token. 

Om du vill kontrollera processen att använda en anslutningssträng som skickades till den `AzureServiceTokenProvider` konstruktor eller har angetts i den *AzureServicesAuthConnectionString* miljövariabeln. 

Stöd för följande alternativ:

| Anslutningen&nbsp;sträng&nbsp;alternativet | Scenario | Kommentarer|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokal utveckling | AzureServiceTokenProvider använder AzureCli för att hämta token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokal utveckling | AzureServiceTokenProvider använder Visual Studio för att hämta token. |
| `RunAs=CurrentUser;` | Lokal utveckling | AzureServiceTokenProvider använder Azure AD-integrerad autentisering för att hämta token. |
| `RunAs=App;` | Hanterad tjänstidentitet | AzureServiceTokenProvider använder hanterade tjänstidentiteten för att hämta token. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Tjänstens huvudnamn | `AzureServiceTokenProvider`använder certifikat för att hämta token från Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Tjänstens huvudnamn | `AzureServiceTokenProvider`använder certifikat för att hämta token från Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Tjänstens huvudnamn |`AzureServiceTokenProvider`använder hemligheten för att hämta token från Azure AD. |


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterade tjänstidentiteten](/azure/app-service/app-service-managed-service-identity).

- Läs olika sätt att [autentisera och auktorisera appar](/azure/app-service/app-service-authentication-overview).

- Lär dig mer om Azure AD [autentiseringsscenarier](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).