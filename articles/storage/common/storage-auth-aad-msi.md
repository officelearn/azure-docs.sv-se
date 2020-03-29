---
title: Auktorisera åtkomst till data med en hanterad identitet
titleSuffix: Azure Storage
description: Lär dig hur du använder hanterade identiteter för Azure-resurser för att auktorisera åtkomst till blob- och ködata från program som körs i virtuella Azure-datorer, funktionsappar, skalningsuppsättningar för virtuella datorer och andra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255345"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Auktorisera åtkomst till blob- och ködata med hanterade identiteter för Azure-resurser

Azure Blob och Kölagring stöder Azure Active Directory-autentisering (Azure AD) med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan auktorisera åtkomst till blob- och ködata med Hjälp av Azure AD-autentiseringsuppgifter från program som körs i virtuella Azure-datorer , funktionsappar, skalningsuppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.  

Den här artikeln visar hur du auktoriserar åtkomst till blob- eller ködata från en Azure-virtuell dator med hjälp av hanterade identiteter för Azure-resurser. Den beskriver också hur du testar din kod i utvecklingsmiljön.

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera åtkomst till blobbar och köer från den virtuella datorn måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure-portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-klientbibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Mer information om hanterade identiteter finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Autentisera med Azure Identity-biblioteket

Azure Identity-klientbiblioteket ger Azure Azure AD-tokenautentiseringsstöd för [Azure SDK](https://github.com/Azure/azure-sdk). De senaste versionerna av Azure Storage-klientbiblioteken för .NET, Java, Python och JavaScript integreras med Azure Identity-biblioteket för att ge ett enkelt och säkert sätt att skaffa en OAuth 2.0-token för auktorisering av Azure Storage-begäranden.

En fördel med Azure Identity-klientbiblioteket är att det gör att du kan använda samma kod för att autentisera om ditt program körs i utvecklingsmiljön eller i Azure. Azure Identity-klientbiblioteket för .NET autentiserar ett säkerhetsobjekt. När koden körs i Azure är säkerhetsobjektet en hanterad identitet för Azure-resurser. I utvecklingsmiljön finns inte den hanterade identiteten, så klientbiblioteket autentiserar antingen användaren eller ett tjänsthuvudnamn för testning.

När du har autentiserat får Azure Identity-klientbiblioteket en tokenautentiseringsuppgifter. Den här tokenautentiseringsinformationen kapslas sedan in i tjänstklientobjektet som du skapar för att utföra åtgärder mot Azure Storage. Biblioteket hanterar detta sömlöst genom att hämta rätt tokenautentiseringsuppgifter.

Mer information om Azure Identity-klientbiblioteket för .NET finns i [Azure Identity-klientbiblioteket för .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Referensdokumentation för Azure Identity-klientbiblioteket finns i [Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Tilldela rollbaserade åtkomstkontroll (RBAC) roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker komma åt blob- eller ködata måste säkerhetsobjektet ha behörighet till resursen. Oavsett om säkerhetsobjektet är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklingsmiljön, måste säkerhetsobjektet tilldelas en RBAC-roll som ger åtkomst till blob- eller ködata i Azure Storage. Information om hur du tilldelar behörigheter via RBAC finns i avsnittet **Tilldela RBAC-roller för åtkomsträttigheter** i [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Autentisera användaren i utvecklingsmiljön

När koden körs i utvecklingsmiljön kan autentisering hanteras automatiskt eller kräva en inloggning i webbläsaren, beroende på vilka verktyg du använder. Microsoft Visual Studio stöder till exempel enkel inloggning (SSO), så att det aktiva Azure AD-användarkontot används automatiskt för autentisering. Mer information om SSO finns i [Enkel inloggning till program](../../active-directory/manage-apps/what-is-single-sign-on.md).

Andra utvecklingsverktyg kan uppmana dig att logga in via en webbläsare.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autentisera ett servicehuvudnamn i utvecklingsmiljön

Om din utvecklingsmiljö inte stöder enkel inloggning eller inloggning via en webbläsare kan du använda ett tjänsthuvudnamn för att autentisera från utvecklingsmiljön.

#### <a name="create-the-service-principal"></a>Skapa huvudnamn för tjänsten

Om du vill skapa ett tjänsthuvudnamn med Azure CLI och tilldela en RBAC-roll anropar du kommandot [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Ange en Azure Storage-dataåtkomstroll för tilldelning till det nya tjänstens huvudnamn. Dessutom anger du omfånget för rolltilldelningen. Mer information om de inbyggda rollerna för Azure Storage finns [i Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

Om du inte har tillräcklig behörighet för att tilldela tjänstens huvudnamn en roll kan du behöva be kontoägaren eller administratören att utföra rolltilldelningen.

I följande exempel används Azure CLI för att skapa ett nytt tjänsthuvudnamn och tilldela rollen **Storage Blob Data Reader** till den med kontoomfång

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Kommandot `az ad sp create-for-rbac` returnerar en lista över tjänstens huvudegenskaper i JSON-format. Kopiera dessa värden så att du kan använda dem för att skapa nödvändiga miljövariabler i nästa steg.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC-rolltilldelningar kan ta några minuter att sprida.

#### <a name="set-environment-variables"></a>Ange miljövariabler

Azure Identity-klientbiblioteket läser värden från tre miljövariabler vid körning för att autentisera tjänstens huvudnamn. I följande tabell beskrivs värdet som ska anges för varje miljövariabel.

|Miljövariabel|Värde
|-|-
|`AZURE_CLIENT_ID`|App-ID:et för tjänstens huvudnamn
|`AZURE_TENANT_ID`|Tjänsthuvudhuvudhuvudets Azure AD-klient-ID
|`AZURE_CLIENT_SECRET`|Lösenordet som genereras för tjänstens huvudnamn

> [!IMPORTANT]
> När du har ställt in miljövariablerna stänger du och öppnar konsolfönstret igen. Om du använder Visual Studio eller en annan utvecklingsmiljö kan du behöva starta om utvecklingsmiljön för att den ska kunna registrera de nya miljövariablerna.

Mer information finns i [Skapa identitet för Azure-appen i portalen](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: Skapa en blockblob

Lägg till `using` följande direktiv i koden för att använda Azure Identity- och Azure Storage-klientbiblioteken.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) för att få en tokenautentiseringsinformation som koden kan använda för att auktorisera begäranden till Azure Storage. I följande kodexempel visas hur du hämtar den autentiserade tokenautentiseringsuppgifterna och använder det för att skapa ett tjänstklientobjekt och sedan använda tjänstklienten för att ladda upp en ny blob:

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
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Om du vill auktorisera begäranden mot blob- eller ködata med Azure AD måste du använda HTTPS för dessa begäranden.

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomsträttigheter till lagringsdata med RBAC](storage-auth-aad-rbac.md).
- [Använd Azure AD med lagringsprogram](storage-auth-aad-app.md).
- [Kör Azure CLI- eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att komma åt blob- eller ködata](authorize-active-directory-powershell.md).
