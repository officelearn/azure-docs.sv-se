---
title: Ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser – Azure Storage
description: Azure blob-och Queue Storage-stöd för att auktorisera åtkomst till resurser med Azure Active Directory och hanterade identiteter för Azure-resurser. Du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till blobbar och köer från program som körs på virtuella Azure-datorer, Function-appar, skal uppsättningar för virtuella datorer och andra.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 469790660e843816cc431420e7e1407c90a7de05
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249938"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser

Azure blob-och Queue Storage-stöd Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan ge åtkomst till blob-och Queue-data med hjälp av Azure AD-autentiseringsuppgifter från program som körs i virtuella Azure-datorer, Function-appar, skalnings uppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.  

Den här artikeln visar hur du auktoriserar åtkomst till BLOB-eller Queue data med en hanterad identitet från en virtuell Azure-dator.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till blobbar och köer från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Bevilja behörighet till en hanterad Azure AD-identitet

Om du vill auktorisera en begäran till blobben eller Kötjänst från en hanterad identitet i ditt Azure Storage-program måste du först konfigurera rollbaserad åtkomst kontroll (RBAC) för den hanterade identiteten. Azure Storage definierar RBAC-roller som omfattar behörigheter för blob-och Queue-data. När RBAC-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten dessa behörigheter till BLOB-eller Queue-data i lämplig omfattning.

Mer information om hur du tilldelar RBAC-roller finns i någon av följande artiklar:

- [Bevilja åtkomst till Azure blob-och Queue-data med RBAC i Azure Portal](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till Azure blob-och Queue-data med RBAC med hjälp av Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till Azure blob-och Queue-data med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Resurs-ID för Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kod exempel: Skapa en Block-Blob

I kod exemplet visas hur du hämtar en OAuth 2,0-token från Azure AD och använder den för att auktorisera en begäran om att skapa en Block-Blob. Om du vill komma igång med det här exemplet börjar du med att följa stegen som beskrivs i föregående avsnitt.

Klient biblioteket för [Microsoft Azure App-autentisering](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) för .net (för hands version) fören klar processen att förvärva och förnya en token från din kod. Klient biblioteket för app-autentisering hanterar autentiseringen automatiskt. Biblioteket använder utvecklarens autentiseringsuppgifter för att autentisera under lokal utveckling. Att använda autentiseringsuppgifter för utvecklare under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare. När lösningen senare distribueras till Azure växlar biblioteket automatiskt till att använda programautentiseringsuppgifter.

### <a name="install-packages"></a>Installera paket

Om du vill använda app Authentication-biblioteket i ett Azure Storage-program installerar du det senaste för hands versions paketet från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), samt den senaste versionen av [Azure Storage vanliga klient bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) och [Azure Blob Storage klient biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Lägg till följande **using** -uttryck i koden:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

### <a name="add-the-callback-method"></a>Lägg till callback-metoden

Callback-metoden kontrollerar förfallo tiden för token och förnyar den efter behov:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>Hämta en token och skapa en Block-Blob

App Authentication-biblioteket innehåller klassen **AzureServiceTokenProvider** . En instans av den här klassen kan skickas till ett återanrop som hämtar en token och sedan förnyar token innan den upphör att gälla.

Följande exempel hämtar en token och använder den för att skapa en ny blob och använder sedan samma token för att läsa blobben.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider,
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Mer information om app Authentication-biblioteket finns i [tjänst-till-tjänst-autentisering för Azure Key Vault med .net](../../key-vault/service-to-service-authentication.md).

Mer information om hur du skaffar en åtkomsttoken finns i [så här använder du hanterade identiteter för Azure-resurser på en virtuell Azure-dator för att få en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Om du vill auktorisera begär Anden mot BLOB-eller Queue data med Azure AD måste du använda HTTPS för dessa begär Anden.

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure Storage finns i [Hantera åtkomst behörigheter till lagrings data med RBAC](storage-auth-aad-rbac.md).
- Information om hur du auktoriserar åtkomst till behållare och köer i dina lagrings program finns i [använda Azure AD med lagrings program](storage-auth-aad-app.md).
- Information om hur du kör Azure CLI-och PowerShell-kommandon med Azure AD-autentiseringsuppgifter finns i [köra Azure CLI-eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data](storage-auth-aad-script.md).
