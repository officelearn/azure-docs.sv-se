---
title: Autentisera åtkomsten till blobbar och köer med Azure Active Directory hanterade identiteter för Azure-resurser – Azure Storage | Microsoft Docs
description: Azure Blob- och Queue storage stöder Azure Active Directory-autentisering med hanterade identiteter för Azure-resurser. Du kan använda hanterade identiteter för Azure-resurser för att autentisera åtkomst till blobbar och köer från program som körs i Azure-datorer, funktionsappar, skalningsuppsättningar för virtuella datorer och andra. Med hjälp av hanterade identiteter för Azure-resurser och utnyttja kraften i Azure AD-autentisering kan du undvika lagring av autentiseringsuppgifter med dina program som körs i molnet.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4372045590938df701dd00e58a111215f6e8e56d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369665"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>Autentisera åtkomsten till blobbar och köer med hanterade identiteter för Azure-resurser

Azure Blob- och Queue storage har stöd för Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Hanterade identiteter för Azure-resurser kan autentisera åtkomst till blobbar och köer med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure-datorer (VM), funktionsappar, skalningsuppsättningar för virtuella datorer och andra. Med hjälp av hanterade identiteter för Azure-resurser och utnyttja kraften i Azure AD-autentisering kan du undvika lagring av autentiseringsuppgifter med dina program som körs i molnet.  

Om du vill tilldela behörigheter till en hanterad identitet till en blob-behållare eller en kö, tilldelar du rollen rollbaserad åtkomstkontroll (RBAC) till den hanterade identitet som omfattar behörigheter för den resursen i det aktuella området. Mer information om RBAC-roller i storage finns i [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md). 

Den här artikeln visar hur du autentiserar till Azure Blob eller Queue storage med en hanterad identitet från en Azure virtuell dator.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att autentisera åtkomst till blobbar och köer från den virtuella datorn, måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Läs hur du aktiverar hanterade identiteter för Azure-resurser i någon av följande artiklar:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDKs](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Tilldela en RBAC-roll till en Azure AD som hanterad identitet

Om du vill autentisera en hanterad identitet från Azure Storage-program måste du först konfigurera rollbaserad inställningar för åtkomstkontroll (RBAC) för den hantera identiteten. Azure Storage definierar RBAC-roller som omfattar behörigheter för behållare och köer. När RBAC-roll tilldelas till en hanterad identitet som hanterad identitet beviljas åtkomst till resursen. Mer information finns i [hantera åtkomsträttigheter till Azure-Blob och kö data med RBAC](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Hämta en hanterad identitet åtkomsttoken

Om du vill autentisera med en hanterad identitet måste hämta ditt program eller skript en åtkomsttoken för hanterad identitet. Läs om hur du hämta en åtkomsttoken i [hur du använder hanterade identiteter för Azure-resurser på en Azure virtuell dator för att hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: Skapa en blockblob

Kodexemplet förutsätter att du har en åtkomsttoken för hanterad identitet. Åtkomsttoken används för att auktorisera den hanterade identitet för att skapa en blockblob.

### <a name="add-references-and-using-statements"></a>Lägg till referenser och using-satser  

I Visual Studio, installerar du Azure Storage-klientbiblioteket. Från den **verktyg** menyn och välj **Nuget-Pakethanteraren**, sedan **Pakethanterarkonsolen**. Skriv följande kommando i konsolen:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Lägg till följande using-satser i koden:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Skapa autentiseringsuppgifter från åtkomsttoken hanterad identitet

Du kan skapa för blockbloben med den **TokenCredentials** klass. Skapa en ny instans av **TokenCredentials**, och skickar hanterad identitet åtkomsttoken som du fick tidigare:

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

- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med lagring program](storage-auth-aad-app.md).
- Om du vill lära dig mer om att logga in på Azure CLI och PowerShell med Azure AD-identitet, se [använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell](storage-auth-aad-script.md).
