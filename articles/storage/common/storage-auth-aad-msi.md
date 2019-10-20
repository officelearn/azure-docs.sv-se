---
title: Ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser – Azure Storage
description: Azure blob-och Queue Storage-stöd för att auktorisera åtkomst till resurser med Azure Active Directory och hanterade identiteter för Azure-resurser. Du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till blobbar och köer från program som körs på virtuella Azure-datorer, Function-appar, skal uppsättningar för virtuella datorer och andra.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595185"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Ge åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser

Azure blob-och Queue Storage-stöd Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan ge åtkomst till blob-och Queue-data med hjälp av Azure AD-autentiseringsuppgifter från program som körs i virtuella Azure-datorer, Function-appar, skalnings uppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.  

Den här artikeln visar hur du auktoriserar åtkomst till BLOB-eller Queue-data från en virtuell Azure-dator med hanterade identiteter för Azure-resurser. Det beskriver också hur du testar din kod i utvecklings miljön.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till blobbar och köer från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure-portalen](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Autentisera med Azure Identity Library (för hands version)

Klient biblioteket för Azure Identity för .NET (för hands version) autentiserar ett säkerhets objekt. När din kod körs i Azure är säkerhets objekt en hanterad identitet för Azure-resurser.

När din kod körs i utvecklings miljön kan autentiseringen hanteras automatiskt, eller så kan det krävas en webb läsar inloggning, beroende på vilka verktyg du använder. Microsoft Visual Studio stöder enkel inloggning (SSO), så att det aktiva Azure AD-användarkontot används automatiskt för autentisering. Mer information om SSO finns i [enkel inloggning till program](../../active-directory/manage-apps/what-is-single-sign-on.md).

Andra utvecklingsverktyg kan bli ombedd att logga in via en webbläsare. Du kan också använda ett huvud namn för tjänsten för att autentisera från utvecklings miljön. Mer information finns i [skapa identitet för Azure-appen i portalen](../../active-directory/develop/howto-create-service-principal-portal.md).

Efter autentiseringen får klient biblioteket för Azure Identity ett token-autentiseringsuppgifter. Den här token-autentiseringsuppgiften kapslas sedan i det tjänst klient objekt som du skapar för att utföra åtgärder mot Azure Storage. Biblioteket hanterar detta för sömlöst genom att hämta rätt autentiseringsuppgifter för token.

Mer information om klient biblioteket för Azure Identity finns i [klient biblioteket för Azure Identity för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Tilldela RBAC-roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker komma åt BLOB-eller Queue data, måste säkerhets objektets behörigheter ha behörighet till resursen. Om säkerhetsobjektet är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklings miljön, måste säkerhets objekt tilldelas en RBAC-roll som ger åtkomst till BLOB-eller Queue-data i Azure Storage. Information om hur du tilldelar behörigheter via RBAC finns i avsnittet **tilldela RBAC-roller för åtkomst rättigheter** i [auktorisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Installera för hands versions paketen

I exemplen i den här artikeln används den senaste för hands versionen av Azure Storage klient biblioteket för Blob Storage. För att installera för hands versions paketet kör du följande kommando från NuGet Package Manager-konsolen:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

I exemplen i den här artikeln används även den senaste för hands versionen av [klient biblioteket för Azure Identity för .net](https://www.nuget.org/packages/Azure.Identity/) för att autentisera med Azure AD-autentiseringsuppgifter. För att installera för hands versions paketet kör du följande kommando från NuGet Package Manager-konsolen:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>.NET-kod exempel: skapa en Block-Blob

Lägg till följande `using` direktiv i din kod för att använda för hands versionerna av Azure Identity och Azure Storage klient bibliotek.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) om du vill hämta en token-autentiseringsuppgifter som din kod kan använda för att auktorisera begär anden till Azure Storage. I följande kod exempel visas hur du hämtar autentiseringsuppgifter för autentiserade token och använder den för att skapa ett tjänst klient objekt. Använd sedan tjänst klienten för att ladda upp en ny BLOB:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Om du vill auktorisera begär Anden mot BLOB-eller Queue data med Azure AD måste du använda HTTPS för dessa begär Anden.

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure Storage finns i [Hantera åtkomst behörigheter till lagrings data med RBAC](storage-auth-aad-rbac.md).
- Information om hur du auktoriserar åtkomst till behållare och köer i dina lagrings program finns i [använda Azure AD med lagrings program](storage-auth-aad-app.md).
- Information om hur du kör Azure CLI-och PowerShell-kommandon med Azure AD-autentiseringsuppgifter finns i [köra Azure CLI-eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data](storage-auth-aad-script.md).
