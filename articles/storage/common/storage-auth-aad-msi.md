---
title: Autentisera åtkomsten till blobbar och köer med Azure Active Directory hanterade identiteter för Azure-resurser (förhandsversion), Azure Storage | Microsoft Docs
description: Azure Blob- och Queue storage stöder Azure Active Directory-autentisering med hanterade identiteter för Azure-resurser. Du kan använda hanterade identiteter för Azure-resurser för att autentisera åtkomst till blobbar och köer från program som körs i Azure-datorer, funktionsappar, skalningsuppsättningar för virtuella datorer och andra. Med hjälp av hanterade identiteter för Azure-resurser och utnyttja kraften i Azure AD-autentisering kan du undvika lagring av autentiseringsuppgifter med dina program som körs i molnet.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: c06dc83f8b460e6215448fbc9e4ac42c749b5922
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354010"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Autentisera åtkomsten till blobbar och köer med hanterade identiteter för Azure-resurser (förhandsgranskning)

Azure Blob- och Queue storage har stöd för Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Du kan använda hanterade identiteter för Azure-resurser för att autentisera åtkomst till blobbar och köer från program som körs i Azure-datorer (VM), funktionsappar, skalningsuppsättningar för virtuella datorer och andra. Med hjälp av hanterade identiteter för Azure-resurser och utnyttja kraften i Azure AD-autentisering kan du undvika lagring av autentiseringsuppgifter med dina program som körs i molnet.  

Om du vill tilldela behörigheter till en hanterad identitet till en blob-behållare eller en kö, tilldelar du rollen rollbaserad åtkomstkontroll (RBAC) till den hanterade identitet som omfattar behörigheter för den resursen i det aktuella området. Mer information om RBAC-roller i storage finns i [hantera åtkomsträttigheter till storage-data med RBAC (förhandsversion)](storage-auth-aad-rbac.md). 

Den här artikeln visar hur du autentiserar till Azure Blob eller Queue storage med en hanterad identitet från en Azure virtuell dator.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att autentisera åtkomst till blobbar och köer från den virtuella datorn, måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Läs hur du aktiverar hanterade identiteter för Azure-resurser i någon av följande artiklar:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK: er](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-a-managed-identity-access-token"></a>Hämta en hanterad identitet åtkomsttoken

Om du vill autentisera med en hanterad identitet måste hämta ditt program eller skript en åtkomsttoken för hanterad identitet. Läs om hur du hämta en åtkomsttoken i [hur du använder hanterade identiteter för Azure-resurser på en Azure virtuell dator för att hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Kodexempel för .NET: skapa en blockblob

Kodexemplet förutsätter att du har en åtkomsttoken för hanterad identitet. Åtkomsttoken används för att auktorisera den hanterade identitet för att skapa en blockblob.

### <a name="add-references-and-using-statements"></a>Lägg till referenser och using-satser  

Installera förhandsversionen av Azure Storage-klientbiblioteket i Visual Studio. Från den **verktyg** menyn och välj **Nuget-Pakethanteraren**, sedan **Pakethanterarkonsolen**. Skriv följande kommando i konsolen:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Lägg till följande using-satser i koden:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Skapa autentiseringsuppgifter från åtkomsttoken hanterad identitet

Du kan skapa för blockbloben med den **TokenCredentials** klass som tillhandahålls av paketets förhandsversion. Skapa en ny instans av **TokenCredentials**, och skickar hanterad identitet åtkomsttoken som du fick tidigare:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD-integrering med Azure Storage kräver att du använder HTTPS för Azure Storage-åtgärder.

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC (förhandsversion)](storage-auth-aad-rbac.md).
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med lagring program](storage-auth-aad-app.md).
- Läs hur du logga in på Azure CLI och PowerShell med Azure AD-identitet i [använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsversion)](storage-auth-aad-script.md).
- Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg publicerar, [meddelande om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
