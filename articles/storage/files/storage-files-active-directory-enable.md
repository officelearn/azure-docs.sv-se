---
title: Aktivera Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion), Azure Storage
description: Lär dig mer om att aktivera identity-baserad autentisering över SMB (Server Message Block) (förhandsversion) för Azure Files via Azure Active Directory (Azure AD) Domain Services. Dina domänanslutna Windows-datorer (VM) kan sedan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.openlocfilehash: ae6f7646192b7bee8cbd836f1eff3814c26a6b46
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427339"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Aktivera Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

En översikt över Azure AD-autentisering över SMB för Azure Files finns i [översikt av Azure Active Directory-autentisering över SMB för Azure Files (förhandsversion)](storage-files-active-directory-overview.md).

## <a name="overview-of-the-workflow"></a>Översikt över arbetsflödet
Innan du aktiverar Azure AD via SMB för Azure Files kan du kontrollera att din Azure AD och Azure Storage-miljöer är rätt konfigurerade. Vi rekommenderar att du går igenom den [krav](#prerequisites) att se till att du har utfört alla steg som krävs. 

Nu ska bevilja åtkomst till Azure Files-resurser med Azure AD-autentiseringsuppgifter genom att följa dessa steg: 

1. Aktivera Azure AD-autentisering över SMB för ditt lagringskonto för att registrera storage-konto med den associerade Azure AD Domain Services-distributionen.
2. Tilldela behörigheter för en resurs till en Azure AD-identitet (en användare, grupp eller tjänstens huvudnamn).
3. Konfigurera NTFS-behörigheter över SMB för kataloger och filer.
4. Montera en Azure-filresurs från en domänansluten virtuell dator.

Diagrammet nedan illustrerar arbetsflödet slutpunkt till slutpunkt för att aktivera Azure AD-autentisering över SMB för Azure Files. 

![Diagram över Azure AD via SMB för Azure Files-arbetsflöde](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Förutsättningar 

Innan du aktiverar Azure AD via SMB för Azure Files, kontrollera att du har slutfört följande krav:

1.  **Välj eller skapa en Azure AD-klient.**

    Du kan använda en ny eller befintlig klient för Azure AD-autentisering över SMB. Klienten och filresursen som du vill komma åt måste associeras med samma prenumeration.

    Att skapa en ny Azure AD-klient kan du [lägga till en Azure AD-klient och en Azure AD-prenumeration](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Om du har en befintlig Azure AD-klient, men du vill skapa en ny klient för användning med Azure Files, se [skapa en Azure Active Directory-klient](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Aktivera Azure AD Domain Services på Azure AD-klient.**

    För att stödja autentisering med autentiseringsuppgifter för Azure AD, måste du aktivera Azure AD Domain Services för din Azure AD-klient. Om du inte är administratör för Azure AD-klient, kontakta administratören och följ de stegvisa anvisningarna till [aktivera Azure Active Directory Domain Services med Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Det tar vanligtvis cirka 15 minuter för en Azure AD Domain Services-distributionen har slutförts. Kontrollera att hälsotillståndet för din Azure AD Domain Services visar **kör**, med synkronisering av lösenordshash aktiverad, innan du fortsätter till nästa steg.

3.  **Domänanslutning en Azure virtuell dator med Azure AD Domain Services.**

    För att komma åt en filresurs med hjälp av Azure AD-autentiseringsuppgifter från en virtuell dator, måste den virtuella datorn vara domänansluten till Azure AD Domain Services. Mer information om hur du domänansluter en virtuell dator finns i [ansluta en Windows Server-dator till en hanterad domän](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > Azure AD-autentisering via SMB med Azure Files stöds bara på virtuella Azure-datorer som körs på OS-versioner ovan Windows 7 eller Windows Server 2008 R2.

4.  **Välj eller skapa en Azure-filresurs.**

    Välj en ny eller befintlig filresurs som är associerat med samma prenumeration som Azure AD-klienten. Information om hur du skapar en ny filresurs finns i [skapa en filresurs i Azure Files](storage-how-to-create-file-share.md). 

    Azure AD-klient måste distribueras till en region som stöds för förhandsversionen av Azure AD via SMB. Förhandsgranskningen är tillgänglig i alla offentliga regioner förutom: västra USA, västra USA 2, södra centrala USA, östra USA, östra USA 2, centrala USA, norra centrala USA, Östra Australien, Västeuropa, Nordeuropa.

    För optimala prestanda rekommenderar Microsoft att filresursen är i samma region som den virtuella datorn från vilken du planerar att få åtkomst till resursen.

5.  **Kontrollera Azure Files-anslutningen genom att montera Azure-filresurser med din lagringskontonyckel.**

    För att verifiera att den virtuella datorn och filresursen är rätt konfigurerade, försök att montera filresursen med din lagringskontonyckel. Mer information finns i [montera en Azure-filresurs och få åtkomst till resursen i Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>Aktivera Azure AD-autentisering för ditt konto

Om du vill aktivera Azure AD-autentisering över SMB för Azure Files, kan du ange en egenskap på lagringskonton som skapats efter den 24 September 2018 med Azure-portalen, Azure PowerShell eller Azure CLI. Den här egenskapen registrerar lagringskontot med den associerade Azure AD Domain Services-distributionen. Azure AD-autentisering över SMB aktiveras sedan för alla nya och befintliga filresurser i lagringskontot. 

Tänk på att du kan aktivera Azure AD-autentisering över SMB förrän Azure AD Domain Services har distribuerats till Azure AD-klienten. Mer information finns i den [krav](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Aktivera Azure AD-autentisering via SMB med hjälp av den [Azure-portalen](https://portal.azure.com), Följ dessa steg:

1. I Azure-portalen går du till ett befintligt lagringskonto eller [skapa ett lagringskonto](../common/storage-quickstart-create-account.md).
2. I den **inställningar** väljer **Configuration**.
3. Aktivera **Azure Active Directory-autentisering för Azure Files (förhandsversion)**.

Följande bild visar hur du aktiverar Azure AD-autentisering över SMB för ditt lagringskonto.

![Aktivera Azure AD-autentisering över SMB i Azure portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Om du vill aktivera Azure AD-autentisering över SMB från Azure PowerShell, först installera den `AzureRM.Storage 6.0.0-preview` modulen. Information om hur du installerar PowerShell finns i [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Skapa sedan en ny lagring konto och sedan anropa [Set-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount) och ange den **EnableAzureFilesAadIntegrationForSMB** parameter **SANT**. Kom ihåg att ersätta platshållarvärdena med dina egna värden i exemplet nedan.

```powershell
# Create a new storage account
New-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzureRmStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
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

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Tilldela behörigheter till en identitet 

En identitet (en användare, grupp eller tjänstens huvudnamn) måste ha behörighet på nivån resurs för att komma åt Azure Files-resurser med hjälp av Azure AD-autentiseringsuppgifter. Riktlinjerna i det här avsnittet visar hur du tilldelar läsa, skriva eller ta bort behörigheter för en filresurs på en identitet.

> [!IMPORTANT]
> Fullständig administrativ kontroll över en filresurs, inklusive möjligheten att tilldela en roll till en identitet, kräver lagringskontonyckeln. Administrativa kontrollen stöds inte med Azure AD-autentiseringsuppgifter. 

### <a name="define-a-custom-role"></a>Definiera en anpassad roll

Om du vill bevilja resursnivåer, definiera en anpassad RBAC-roll och tilldela den till en identitet, omfång till en specifik filresurs. Den här processen liknar att ange Windows dela behörigheter, anger du vilken typ av åtkomst som en viss användare har till en filresurs.  

Mallar som visas i följande avsnitt tillhandahåller Läs- eller ändra behörigheter för en filresurs. För att definiera en anpassad roll, skapa en JSON-fil och kopiera den aktuella mallen till filen. Mer information om hur du definierar anpassade RBAC-roller finns i [anpassade roller i Azure](../../role-based-access-control/custom-roles.md).

**Rolldefinitionen för resursnivå ändra behörigheter**  
Följande anpassade rollmallen innehåller resursnivå ändra behörigheter, som ger en identitet som Läs-, Skriv- och ta borttagningssåtkomst till resursen.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share",
  "Actions": [
    "*"
  ],
  "NotActions": [
      "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Rolldefinitionen för resursnivå läsa behörigheter**  
Följande mall för den anpassade rollen ger läsbehörighet för resursnivå bevilja en identitet läsåtkomst till resursen.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Skapa anpassad roll

Använd PowerShell eller Azure CLI för att skapa den anpassade rollen. 

#### <a name="powershell"></a>PowerShell

Följande PowerShell-kommando skapar en anpassad roll som bygger på någon av exempelmallarna.

```powershell
#Create a custom role based on the sample template above
New-AzureRmRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>CLI 

Följande Azure CLI-kommando skapar en anpassad roll som bygger på någon av exempelmallarna.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Tilldela den anpassade rollen till mål-identitet

Använd sedan PowerShell eller Azure CLI för att tilldela den anpassade rollen till en Azure AD-identitet. 

#### <a name="powershell"></a>PowerShell

Följande PowerShell-kommando visar hur du Listar tillgängliga anpassade roller och tilldela en anpassad roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om att tilldela RBAC-roller med PowerShell finns i [hantera åtkomst med RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

När du kör följande exempelskript, Kom ihåg att ersätta platshållarvärdena, inklusive parenteser med dina egna värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzureRmRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzureRmRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Följande CLI 2.0-kommando visar hur du Listar tillgängliga anpassade roller och tilldela en anpassad roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om att tilldela RBAC-roller med Azure CLI finns i [hantera åtkomst med RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

När du kör följande exempelskript, Kom ihåg att ersätta platshållarvärdena, inklusive parenteser med dina egna värden.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custome-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
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
Använd följande kommando i Windows för att ge fullständig behörighet till alla kataloger och filer under filresurs, inklusive rotkatalogen. Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden.

```
icacls <mounted-drive-letter> /grant <user-email>:(f)
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
