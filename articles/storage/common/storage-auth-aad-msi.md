---
title: Autentisera åtkomsten till blobbar och köer med hanterade identiteter för Azure-resurser – Azure Storage | Microsoft Docs
description: Azure Blob- och Queue storage har stöd för Azure Active Directory-autentisering med hanterade identiteter för Azure-resurser. Du kan använda hanterade identiteter för Azure-resurser för att autentisera åtkomst till blobbar och köer från program som körs i Azure-datorer, funktionsappar, skalningsuppsättningar för virtuella datorer och andra.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9209161f9c9e34320b1388e0e1edbd5069e73727
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148857"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autentisera åtkomsten till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser

Azure Blob- och Queue storage har stöd för Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan auktorisera åtkomst till blob och kö-data med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure-datorer (VM), funktionsappar, skalningsuppsättningar för virtuella datorer och andra tjänster. Du kan undvika lagring av autentiseringsuppgifter med dina program som körs i molnet med hjälp av hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering.  

Den här artikeln visar hur du tillåter åtkomst till blob eller kön data med en hanterad identitet från en Azure virtuell dator. 

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att bevilja åtkomst till blobbar och köer från den virtuella datorn, måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Läs hur du aktiverar hanterade identiteter för Azure-resurser i någon av följande artiklar:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDKs](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Bevilja behörigheter till en Azure AD som hanterad identitet

För att godkänna en begäran till tjänsten Blob eller kön från en hanterad identitet i Azure Storage-program, först konfigurera rollbaserad inställningar för åtkomstkontroll (RBAC) för den hantera identiteten. Azure Storage definierar RBAC-roller som omfattar behörigheter för blob-och kö. När RBAC-roll tilldelas till en hanterad identitet, beviljas hanterad identitet de behörigheterna till blob eller kön data på det aktuella området. 

Mer information om att tilldela RBAC-roller finns i någon av följande artiklar:

- [Bevilja åtkomst till Azure blob och kö data med RBAC i Azure portal](storage-auth-aad-rbac-portal.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med hjälp av PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Auktorisera med en åtkomsttoken för hanterad identitet

För att godkänna förfrågningar mot Blob- och Queue storage med en hanterad identitet måste ditt program eller skript skaffa en OAuth-token. Den [Microsoft Azure-Appautentisering](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) klientbiblioteket för .NET (förhandsversion) gör enklare att införskaffa och förnyar ett token från din kod.

Klientbiblioteket Appautentisering hanterar autentisering automatiskt. Utvecklarens autentiseringsuppgifter använder för att autentisera under lokal utveckling. Med developer autentiseringsuppgifter under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare. När lösningen distribueras senare till Azure, växlar biblioteket automatiskt till med hjälp av autentiseringsuppgifter för programmet.

För att använda App-autentiseringsbiblioteket i ett Azure Storage-program, installera det senaste förhandsversionen-paketet från [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), samt den senaste versionen av den [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Lägg till följande **med** -uttryck för att din kod:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

App-autentisering-biblioteket innehåller den **AzureServiceTokenProvider** klass. En instans av den här klassen kan skickas till ett återanrop som hämtar en token och sedan förnyar token innan den upphör.

I följande exempel hämtas en token och används för att skapa en ny blob och sedan använder samma token för att läsa blob.

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

Motringningsmetoden kontrollerar förfallotid för token och förnyar det efter behov:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
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

Läs mer om App-autentiseringsbibliotek [tjänst-till-tjänst-autentisering till Azure Key Vault med hjälp av .NET](../../key-vault/service-to-service-authentication.md). 

Läs mer om att hämta en åtkomsttoken i [hur du använder hanterade identiteter för Azure-resurser på en Azure virtuell dator för att hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Du måste använda HTTPS för dessa begäranden för att godkänna förfrågningar mot blob eller kön data med Azure AD.

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med lagring program](storage-auth-aad-app.md).
- Läs hur du kör Azure CLI och PowerShell-kommandon med autentiseringsuppgifter för Azure AD i [kör Azure CLI eller PowerShell-kommandon med autentiseringsuppgifter för Azure AD åtkomst till blob-eller kön](storage-auth-aad-script.md).