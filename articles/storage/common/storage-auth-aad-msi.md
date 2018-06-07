---
title: Autentisera med Azure AD från en Azure VM hanterade tjänstidentiteten (förhandsversion) | Microsoft Docs
description: Autentisera med Azure AD från en Azure VM hanterade tjänstidentiteten (förhandsversion).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 83d3a2d973604e3b8a709b24cabcb3abba1e304c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660998"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Autentisera med Azure AD från en Azure hanterade tjänstidentiteten (förhandsgranskning)

Azure Storage har stöd för Azure Active Directory (AD Azure) autentisering med [hanterade tjänstidentiteten](../../active-directory/managed-service-identity/overview.md). Hanterad Service identitet (MSI) innehåller en automatiskt hanterade identitet i Azure Active Directory (AD Azure). Du kan använda MSI för att autentisera till Azure Storage från program som körs i virtuella Azure-datorer, funktionen appar, skalningsuppsättningar i virtuella datorer och andra. Med hjälp av MSI och utnyttja kraften i Azure AD-autentisering, kan du undvika lagring av autentiseringsuppgifter med program som körs i molnet.  

Om du vill tilldela behörigheter till en hanterad tjänstidentitet för köer eller behållare för lagring, kan du tilldela en RBAC-roll som omfattar lagring behörigheter till MSI. Mer information om RBAC-roller i lagring finns [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md). 

> [!IMPORTANT]
> Den här förhandsgranskningen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga förrän Azure AD-integrering för Azure Storage har deklarerats allmänt tillgänglig. Om Azure AD-integrering inte stöds ännu för ditt scenario, fortsätta att använda delad nyckel auktorisering eller SAS-token i dina program. Mer information om förhandsversionen finns [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)](storage-auth-aad.md).
>
> Under förhandsgranskningen inkluderas kan RBAC rolltilldelningar ta upp till fem minuter att sprida.

Den här artikeln visar hur du autentiserar till Azure Storage med MSI från en Azure VM.  

## <a name="enable-msi-on-the-vm"></a>Aktivera MSI på den virtuella datorn

Innan du kan använda MSI för att autentisera till Azure Storage från den virtuella datorn, måste du först aktivera MSI på den virtuella datorn. Om du vill veta hur du aktiverar MSI finns i något av följande artiklar:

- [Azure Portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Hämta en MSI-åtkomst-token

För att autentisera med MSI måste ditt program eller skript skaffa en MSI-åtkomsttoken. Läs om hur du skaffar en åtkomst-token i [hur du använder en Azure VM hanterade tjänsten identitet (MSI) för token](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>.NET-kodexempel: skapa en blockblobb

Exemplet förutsätter att du har en MSI-åtkomsttoken. Åtkomst-token används för att auktorisera hanterade tjänstidentiteten att skapa en blockblob.

### <a name="add-references-and-using-statements"></a>Lägg till referenser och med-uttryck  

Installera förhandsversionen av Azure Storage-klientbiblioteket i Visual Studio. Från den **verktyg** väljer du **Nuget Package Manager**, sedan **Pakethanterarkonsolen**. Skriv följande kommando i konsolen:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Lägg till följande using-instruktioner till din kod:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Skapa autentiseringsuppgifter från MSI-åtkomst-token

Använd för att skapa blockblob den **TokenCredentials** klass som tillhandahålls av preview-paketet. Skapa en ny instans av **TokenCredentials**, och skickar den MSI åtkomst-token som du hämtade tidigare:

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

- Mer information om RBAC-roller för Azure storage finns [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).
- Om du vill lära dig mer om att bevilja åtkomst till behållare och köer från i storage-program, se [använda Azure AD med lagring program](storage-auth-aad-app.md).
- Om du vill lära dig mer om att logga in på Azure CLI och PowerShell med en Azure AD-identitet, se [använder en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsgranskning)](storage-auth-aad-script.md).
- Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg inlägg, [om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
