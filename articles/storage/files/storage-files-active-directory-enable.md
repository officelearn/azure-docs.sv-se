---
title: Aktivera Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion), Azure Storage
description: Lär dig mer om att aktivera identity-baserad autentisering över SMB (Server Message Block) (förhandsversion) för Azure Files via Azure Active Directory (Azure AD) Domain Services. Dina domänanslutna Windows-datorer (VM) kan sedan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696132"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Aktivera Azure Active Directory Domain Service-autentisering över SMB för Azure Files (förhandsversion)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

En översikt över Azure AD-autentisering över SMB för Azure Files finns i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Översikt över arbetsflödet
Innan du aktiverar Azure AD DS-autentisering över SMB för Azure Files kan du kontrollera att din Azure AD och Azure Storage-miljöer är rätt konfigurerade. Vi rekommenderar att du går igenom den [krav](#prerequisites) att se till att du har utfört alla steg som krävs. 

Nu ska bevilja åtkomst till Azure Files-resurser med Azure AD-autentiseringsuppgifter genom att följa dessa steg: 

1. Aktivera Azure AD DS-autentisering över SMB för ditt lagringskonto för att registrera storage-konto med den associerade Azure AD Domain Services-distributionen.
2. Tilldela behörigheter för en resurs till en Azure AD-identitet (en användare, grupp eller tjänstens huvudnamn).
3. Konfigurera NTFS-behörigheter över SMB för kataloger och filer.
4. Montera en Azure-filresurs från en domänansluten virtuell dator.

Diagrammet nedan illustrerar arbetsflödet slutpunkt till slutpunkt för att aktivera Azure AD DS-autentisering över SMB för Azure Files. 

![Diagram över Azure AD via SMB för Azure Files-arbetsflöde](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Förutsättningar 

Innan du aktiverar Azure AD via SMB för Azure Files, kontrollera att du har slutfört följande krav:

1.  **Välj eller skapa en Azure AD-klient.**

    Du kan använda en ny eller befintlig klient för Azure AD-autentisering över SMB. Klienten och filresursen som du vill komma åt måste associeras med samma prenumeration.

    Att skapa en ny Azure AD-klient kan du [lägga till en Azure AD-klient och en Azure AD-prenumeration](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Om du har en befintlig Azure AD-klient, men du vill skapa en ny klient för användning med Azure Files, se [skapa en Azure Active Directory-klient](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Aktivera Azure AD Domain Services på Azure AD-klient.**

    För att stödja autentisering med autentiseringsuppgifter för Azure AD, måste du aktivera Azure AD Domain Services för din Azure AD-klient. Om du inte är administratör för Azure AD-klient, kontakta administratören och följ de stegvisa anvisningarna till [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/create-instance.md).

    Det tar vanligtvis cirka 15 minuter för en Azure AD Domain Services-distributionen har slutförts. Kontrollera att hälsotillståndet för din Azure AD Domain Services visar **kör**, med synkronisering av lösenordshash aktiverad, innan du fortsätter till nästa steg.

3.  **Domänanslutning en Azure virtuell dator med Azure AD Domain Services.**

    För att komma åt en filresurs med hjälp av Azure AD-autentiseringsuppgifter från en virtuell dator, måste den virtuella datorn vara domänansluten till Azure AD Domain Services. Mer information om hur du domänansluter en virtuell dator finns i [ansluta en Windows Server-dator till en hanterad domän](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD DS-autentisering via SMB med Azure Files stöds bara på virtuella Azure-datorer som körs på OS-versioner ovan Windows 7 eller Windows Server 2008 R2.

4.  **Välj eller skapa en Azure-filresurs.**

    Välj en ny eller befintlig filresurs som är associerat med samma prenumeration som Azure AD-klienten. Information om hur du skapar en ny filresurs finns i [skapa en filresurs i Azure Files](storage-how-to-create-file-share.md). 
    För optimala prestanda rekommenderar Microsoft att filresursen är i samma region som den virtuella datorn från vilken du planerar att få åtkomst till resursen.

5.  **Kontrollera Azure Files-anslutningen genom att montera Azure-filresurser med din lagringskontonyckel.**

    För att verifiera att den virtuella datorn och filresursen är rätt konfigurerade, försök att montera filresursen med din lagringskontonyckel. Mer information finns i [montera en Azure-filresurs och få åtkomst till resursen i Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Aktivera Azure AD DS-autentisering för ditt konto

Om du vill aktivera Azure AD DS-autentisering över SMB för Azure Files, kan du ange en egenskap på lagringskonton som skapats efter den 24 September 2018 med Azure-portalen, Azure PowerShell eller Azure CLI. Den här egenskapen registrerar lagringskontot med den associerade Azure AD Domain Services-distributionen. Azure AD DS-autentisering över SMB är sedan aktiverat för alla nya och befintliga filresurser i lagringskontot. 

Tänk på att du kan aktivera Azure AD DS-autentisering över SMB förrän Azure AD Domain Services har distribuerats till Azure AD-klienten. Mer information finns i den [krav](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Att aktivera Azure AD DS-autentisering via SMB med hjälp av den [Azure-portalen](https://portal.azure.com), Följ dessa steg:

1. I Azure-portalen går du till ett befintligt lagringskonto eller [skapa ett lagringskonto](../common/storage-quickstart-create-account.md).
2. I den **inställningar** väljer **Configuration**.
3. Aktivera **Azure Active Directory-autentisering för Azure Files (förhandsversion)** .

Följande bild visar hur du aktiverar Azure AD-autentisering över SMB för ditt lagringskonto.

![Aktivera Azure AD-autentisering över SMB i Azure portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Aktivera Azure AD DS-autentisering via SMB med Azure PowerShell: Först installera den senaste Az-modulen (2,4 eller senare) eller modulen Az.Storage (1,5 eller senare). Mer information om hur du installerar PowerShell finns i [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Skapa sedan en ny lagring konto och sedan anropa [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) och ange den **EnableAzureActiveDirectoryDomainServicesForFile** parameter **SANT**. Kom ihåg att ersätta platshållarvärdena med dina egna värden i exemplet nedan. (Om du använde tidigare förhandsversionsmodulen parametern för funktionen aktivering är **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Om du vill aktivera den här funktionen på befintliga lagringskonton, använder du följande kommando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera Azure AD-autentisering över SMB från Azure CLI 2.0, först installera den `storage-preview` tillägg:

```cli-interactive
az extension add --name storage-preview
```
  
Skapa sedan en ny lagring konto och sedan anropa [az storage-konto update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) och ange den `--file-aad` egenskap **SANT**. Kom ihåg att ersätta platshållarvärdena med dina egna värden i exemplet nedan.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Tilldela behörigheter till en identitet 

En identitet (en användare, grupp eller tjänstens huvudnamn) måste ha behörighet på nivån resurs för att komma åt Azure Files-resurser med hjälp av Azure AD-autentiseringsuppgifter. Den här processen liknar att ange Windows dela behörigheter, anger du vilken typ av åtkomst som en viss användare har till en filresurs. Riktlinjerna i det här avsnittet visar hur du tilldelar läsa, skriva eller ta bort behörigheter för en filresurs på en identitet.

Vi har introducerat två Azure inbyggda roller för att bevilja behörigheter för resursen på kolumnnivå för användare: Storage filen Data SMB dela läsare och lagring filen Data SMB-resurs deltagare. 

- **Storage filen Data SMB filresurs läsare** ger läsbehörighet i Azure Storage-filresurser över SMB
- **Storage filen Data SMB-resurs-deltagare** kan läsa, skriva och ta borttagningssåtkomst i Azure Storage-filresurser över SMB

> [!IMPORTANT]
> Fullständig administrativ kontroll över en filresurs, inklusive möjligheten att tilldela en roll till en identitet, kräver lagringskontonyckeln. Administrativ kontroll stöds inte med Azure AD-autentiseringsuppgifter. 

Du kan använda den Azure-portalen, PowerShell eller Azure CLI för att tilldela de inbyggda rollerna till Azure AD-identitet för en användare för att bevilja behörigheter för resursen på kolumnnivå. 

#### <a name="azure-portal"></a>Azure Portal
Tilldela en RBAC-roll till en Azure AD-identitet med hjälp av den [Azure-portalen](https://portal.azure.com), Följ dessa steg:

1. I Azure-portalen går du till filresursen, eller [skapa en filresurs i Azure Files](storage-how-to-create-file-share.md).
2. Välj **åtkomstkontroll (IAM)** .
3. Välj **Lägg till en rolltilldelning**
4. I den **Lägg till rolltilldelning** bladet väljer rätt inbyggd roll (Storage filen Data SMB filresurs läsare, Storage filen Data SMB-resurs-deltagare) från den **rollen** listrutan. Behåll den **tilldela åtkomst till** som standard: ”Azure AD-användare, grupp eller tjänstens huvudnamn” och välj målobjektet för Azure AD-identitet efter namn eller e-postadress. 
5. Till sist väljer **spara** att slutföra åtgärden för roll-tilldelning.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-kommando visar hur du tilldelar en RBAC-roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om att tilldela RBAC-roller med PowerShell finns i [hantera åtkomst med RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

När du kör följande exempelskript, Kom ihåg att ersätta platshållarvärdena, inklusive parenteser med dina egna värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Följande CLI 2.0-kommando visar hur du tilldelar en RBAC-roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om att tilldela RBAC-roller med Azure CLI finns i [hantera åtkomst med RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

När du kör följande exempelskript, Kom ihåg att ersätta platshållarvärdena, inklusive parenteser med dina egna värden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurera NTFS-behörigheter över SMB 
När du har tilldelat resursnivåer med RBAC, måste du tilldela rätt NTFS-behörigheter i roten, katalogen eller filnivå. Se resursnivåer som den övergripande gatekeeper som avgör om en användare har åtkomst till resursen, medan NTFS-behörigheter som fungerar på en mer detaljerad nivå för att avgöra vilka åtgärder du kan utföra på katalogen eller filen. 

Azure Files stöder den fullständiga uppsättningen NTFS-behörigheter för grundläggande och avancerade. Du kan visa och konfigurera NTFS-behörigheter för kataloger och filer i en Azure-filresurs genom att montera resursen och sedan köra Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) eller [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) kommando. 

> [!NOTE]
> Förhandsversionen stöder visningsbehörigheter med Windows Utforskaren endast. Redigera behörigheter stöds inte ännu.

Om du vill konfigurera NTFS-behörigheter med superanvändare, måste du montera filresursen med din lagringskontonyckel från Virtuellt datorn är domänansluten. Följ anvisningarna i nästa avsnitt för att montera en Azure-filresurs från Kommandotolken och därefter konfigurera NTFS-behörigheter.

Följande uppsättningar behörigheter stöds i rotkatalogen för en filresurs:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,ge)
- NT Instans\Autentiserade Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montera en filresurs från Kommandotolken

Använd Windows **nätverksanv** kommando för att montera Azure-filresursen. Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden. Mer information om montering av filresurser finns i [montera en Azure-filresurs och få åtkomst till resursen i Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurera NTFS-behörigheter med icacls
Använd följande kommando i Windows för att ge fullständig behörighet till alla kataloger och filer under filresurs, inklusive rotkatalogen. Kom ihåg att ersätta platshållarvärdena visas inom parentes i exemplet med dina egna värden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Mer information om hur du använder icacls att ange NTFS-behörigheter och på olika typer av behörigheter som stöds, se [kommandoreferens för icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montera en filresurs från en domänansluten virtuell dator 

Nu är du redo att verifiera att du har slutfört stegen ovan har med hjälp av dela dina autentiseringsuppgifter för Azure AD åtkomst till en Azure-fil från en domänansluten VM. Först måste logga in på den virtuella datorn med hjälp av Azure AD-identitet som du har beviljat behörigheter, som visas i följande bild.

![Skärmbild som visar Azure AD-inloggningsskärmen för autentisering av användare](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Använd sedan följande kommando för att montera Azure-filresursen. Kom ihåg att ersätta platshållarvärdena med dina egna värden. Eftersom du har redan verifierats, behöver du inte ange lagringskontonyckeln eller Azure AD-användarnamn och lösenord. Azure AD via SMB stöder en enkel inloggning med hjälp av Azure AD-autentiseringsuppgifter.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Nu har du har aktiverat Azure AD-autentisering över SMB och tilldelat en anpassad roll som ger åtkomst till en filresurs på en Azure AD-identitet. Om du vill bevilja åtkomst till filresursen för fler användare följer du anvisningarna i steg 2 och 3.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files och använda Azure AD över SMB finns i följande källor:

- [Introduktion till Azure Files](storage-files-introduction.md)
- [Översikt över Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](storage-files-active-directory-overview.md)
- [Vanliga frågor och svar](storage-files-faq.md)
