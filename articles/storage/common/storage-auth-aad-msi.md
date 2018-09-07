---
title: Autentisera med Azure AD från en Azure virtuell dator hanterad tjänstidentitet (förhandsversion) | Microsoft Docs
description: Autentisera med Azure AD från en Azure virtuell dator hanterad tjänstidentitet (förhandsversion).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: e20e0c412206b2a35973b192ef911bb99ed7c210
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021871"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Autentisera med Azure AD från en Azure hanterad tjänstidentitet (förhandsversion)

Azure Storage har stöd för Azure Active Directory (Azure AD)-autentisering med [hanterad tjänstidentitet](../../active-directory/managed-identities-azure-resources/overview.md). Hanterad tjänstidentitet (MSI) innehåller en automatiskt hanterad identitet i Azure Active Directory (AD Azure). Du kan använda MSI för att autentisera till Azure Storage från program som körs i Azure-datorer, funktionsappar, skalningsuppsättningar för virtuella datorer och andra. Med hjälp av MSI och utnyttja kraften i Azure AD-autentisering, kan du undvika lagring av autentiseringsuppgifter med dina program som körs i molnet.  

Om du vill tilldela behörigheter till en hanterad tjänstidentitet för storage-behållare eller köer, kan du tilldela en RBAC-roll som omfattar storage behörigheter till MSI. Mer information om RBAC-roller i storage finns i [hantera åtkomsträttigheter till storage-data med RBAC (förhandsversion)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Den här förhandsversionen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga förrän Azure AD-integrering för Azure Storage har deklarerats allmänt tillgänglig. Om Azure AD-integrering inte stöds ännu för ditt scenario, fortsätta att använda auktorisering för delad nyckel eller SAS-token i dina program. Mer information om förhandsversionen finns [autentisera åtkomsten till Azure Storage med Azure Active Directory (förhandsversion)](storage-auth-aad.md).
>
> RBAC-rolltilldelningar kan ta upp till fem minuter att sprida i förhandsversionen.

Den här artikeln visar hur du autentiserar till Azure Storage med MSI från en Azure virtuell dator.  

## <a name="enable-msi-on-the-vm"></a>Aktivera MSI på den virtuella datorn

Innan du kan använda MSI för att autentisera till Azure Storage från din virtuella dator, måste du först aktivera MSI på den virtuella datorn. Läs hur du aktiverar MSI i någon av följande artiklar:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK: er](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Hämta en MSI åtkomsttoken

Ditt program eller skript måste skaffa en MSI-åtkomsttoken för att autentisera med MSI. Läs om hur du hämta en åtkomsttoken i [hur du använder en Azure VM hanterad tjänstidentitet (MSI) för tokenförvärv](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Kodexempel för .NET: skapa en blockblob

Kodexemplet förutsätter att du har en MSI åtkomst-token. Åtkomsttoken används för att auktorisera den hanterade tjänstidentiteten för att skapa en blockblob.

### <a name="add-references-and-using-statements"></a>Lägg till referenser och using-satser  

Installera förhandsversionen av Azure Storage-klientbiblioteket i Visual Studio. Från den **verktyg** menyn och välj **Nuget-Pakethanteraren**, sedan **Pakethanterarkonsolen**. Skriv följande kommando i konsolen:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Lägg till följande using-satser i koden:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Skapa autentiseringsuppgifter från MSI åtkomst-token

Du kan skapa för blockbloben med den **TokenCredentials** klass som tillhandahålls av paketets förhandsversion. Skapa en ny instans av **TokenCredentials**, och skickar den MSI åtkomst-token som du fick tidigare:

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
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
