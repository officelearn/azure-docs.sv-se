---
title: Använda Azure AD Domain Services för att auktorisera åtkomst till fildata via SMB
description: Lär dig hur du aktiverar identitetsbaserad autentisering via SMB (Server Message Block) för Azure-filer via Azure Active Directory Domain Services. Dina domänanslutna virtuella datorer i Windows (VMs) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666855"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Aktivera Azure Active Directory Domain Services-autentisering på Azure-filer

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

En översikt över Azure AD-autentisering över SMB för Azure-filresurser finns i [Översikt över Azure Active Directory-autentisering över SMB för Azure-filer](storage-files-active-directory-overview.md). Den här artikeln fokuserar på hur du aktiverar autentisering med Azure Active Directory Domain Services (Azure AD DS) på Azure-filer.

> [!NOTE]
> Azure Files stöder Kerberos-autentisering med Azure AD DS med RC4-HMAC-kryptering. AES Kerberos-kryptering stöds ännu inte.

## <a name="prerequisites"></a>Krav

Innan du aktiverar Azure AD över SMB för Azure-filresurser kontrollerar du att du har slutfört följande förutsättningar:

1.  **Välj eller skapa en Azure AD-klientorganisation.**

    Du kan använda en ny eller befintlig klient för Azure AD-autentisering via SMB. Klienten och filresursen som du vill komma åt måste associeras med samma prenumeration.

    Om du vill skapa en ny Azure AD-klient kan du [lägga till en Azure AD-klientorganisation och en Azure AD-prenumeration](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Om du har en befintlig Azure AD-klient men vill skapa en ny klient för användning med Azure-filresurser läser du [Skapa en Azure Active Directory-klientorganisation](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Aktivera Azure AD Domain Services på Azure AD-klienten.**

    Om du vill stödja autentisering med Azure AD-autentiseringsuppgifter måste du aktivera Azure AD Domain Services för din Azure AD-klientorganisation. Om du inte är administratör för Azure AD-klienten kontaktar du administratören och följer steg-för-steg-anvisningarna för att [aktivera Azure Active Directory Domain Services med Azure-portalen](../../active-directory-domain-services/tutorial-create-instance.md).

    Det tar vanligtvis cirka 15 minuter för en Azure AD DS-distribution att slutföras. Kontrollera att hälsotillståndet för Azure AD DS visar **Kör**, med lösenord hash-synkronisering aktiverad, innan du fortsätter till nästa steg.

1.  **Domänanslutning till en virtuell Azure-dator med Azure AD DS.**

    Om du vill komma åt en filresurs med hjälp av Azure AD-autentiseringsuppgifter från en virtuell dator måste den virtuella datorn vara domänansluten till Azure AD DS. Mer information om hur du ansluter till en virtuell dator i Ansluta till [en virtuell Windows Server-dator till en hanterad domän](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD DS-autentisering via SMB med Azure-filresurser stöds endast på virtuella Azure-datorer som körs på OS-versioner ovanför Windows 7 eller Windows Server 2008 R2.

1.  **Markera eller skapa en Azure-filresurs.**

    Välj en ny eller befintlig filresurs som är associerad med samma prenumeration som din Azure AD-klientorganisation. Information om hur du skapar en ny filresurs finns [i Skapa en filresurs i Azure Files](storage-how-to-create-file-share.md).
    För bästa prestanda rekommenderar vi att filresursen finns i samma region som den virtuella datorn som du planerar att komma åt resursen från.

1.  **Verifiera Azure Files-anslutning genom att montera Azure-filresurser med hjälp av din lagringskontonyckel.**

    Om du vill kontrollera att den virtuella datorn och filresursen är korrekt konfigurerade kan du prova att montera filresursen med hjälp av nyckeln för lagringskontot. Mer information finns i [Montera en Azure-filresurs och komma åt resursen i Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Översikt över arbetsflödet

Innan du aktiverar Azure AD DS-autentisering över SMB för Azure-filresurser kontrollerar du att dina Azure AD- och Azure Storage-miljöer är korrekt konfigurerade. Vi rekommenderar att du går igenom [förutsättningarna](#prerequisites) för att se till att du har slutfört alla nödvändiga steg.

Gör sedan följande för att bevilja åtkomst till Azure Files-resurser med Azure AD-autentiseringsuppgifter:

1. Aktivera Azure AD DS-autentisering via SMB för ditt lagringskonto för att registrera lagringskontot med den associerade Azure AD DS-distributionen.
2. Tilldela åtkomstbehörigheter för en resurs till en Azure AD-identitet (en användare, grupp eller tjänsthuvudnamn).
3. Konfigurera NTFS-behörigheter över SMB för kataloger och filer.
4. Montera en Azure-filresurs från en domänansluten virtuell dator.

Följande diagram illustrerar arbetsflödet från slutna till slutna för att aktivera Azure AD DS-autentisering via SMB för Azure-filer.

![Diagram som visar Azure AD över SMB för Azure Files-arbetsflöde](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. Aktivera Azure AD DS-autentisering för ditt konto

Om du vill aktivera Azure AD DS-autentisering via SMB för Azure-filer kan du ange en egenskap på lagringskonton med hjälp av Azure-portalen, Azure PowerShell eller Azure CLI. Ange den här egenskapen implicit "domän ansluter" lagringskontot med den associerade Azure AD DS-distributionen. Azure AD DS-autentisering över SMB är sedan aktiverad för alla nya och befintliga filresurser i lagringskontot.

Tänk på att du kan aktivera Azure AD DS-autentisering via SMB först när du har distribuerat Azure AD DS till din Azure AD-klientorganisation. Mer information finns i [förutsättningarna](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Så här aktiverar du Azure AD DS-autentisering via SMB med [Azure-portalen:](https://portal.azure.com)

1. Gå till ditt befintliga lagringskonto i Azure-portalen eller [skapa ett lagringskonto](../common/storage-account-create.md).
1. Välj **Konfiguration**i avsnittet **Inställningar** .
1. Under **Identitetsbaserad åtkomst för filresurser** växlar du växlingsknappen för **Azure Active Directory Domain Service (AAD DS)** till **Aktiverad**.
1. Välj **Spara**.

Följande avbildning visar hur du aktiverar Azure AD DS-autentisering via SMB för ditt lagringskonto.

![Aktivera Azure AD DS-autentisering via SMB i Azure-portalen](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Om du vill aktivera Azure AD DS-autentisering via SMB med Azure PowerShell installerar du den senaste Az-modulen (2.4 eller nyare) eller Az.Storage-modulen (1,5 eller nyare). Mer information om hur du installerar PowerShell finns i [Installera Azure PowerShell i Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Om du vill skapa ett nytt lagringskonto anropar du [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)och ställer sedan in parametern **EnableAzureActiveDirectoryDomainServicesForFile** till **true**. I följande exempel bör du komma ihåg att ersätta platshållarvärdena med dina egna värden. (Om du använde den tidigare förhandsgranskningsmodulen är parametern för funktionsaktivering **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Om du vill aktivera den här funktionen på befintliga lagringskonton använder du följande kommando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera Azure AD-autentisering via SMB med Azure CLI installerar du den senaste CLI-versionen (version 2.0.70 eller nyare). Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill skapa ett nytt lagringskonto `--enable-files-aadds` anropar du az[storage-konto skapa](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)och anger egenskapen till **true**. I följande exempel bör du komma ihåg att ersätta platshållarvärdena med dina egna värden. (Om du använde den tidigare förhandsgranskningsmodulen är parametern för funktionsaktivering **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Om du vill aktivera den här funktionen på befintliga lagringskonton använder du följande kommando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Du har nu aktiverat Azure AD DS-autentisering via SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en Azure AD-identitet. Om du vill ge ytterligare användare åtkomst till filresursen följer du instruktionerna i [behörigheterna Tilldela åtkomst](#2-assign-access-permissions-to-an-identity) för att använda en identitet och [konfigurera NTFS-behörigheter över SMB-avsnitt](#3-configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-filer och hur du använder Azure AD via SMB finns i följande resurser:

- [Översikt över Azure Files identitetsbaserad autentiseringsstöd för SMB-åtkomst](storage-files-active-directory-overview.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
