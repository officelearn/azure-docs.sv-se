---
title: Använd Azure AD Domain Services för att ge åtkomst till fildata över SMB
description: Lär dig hur du aktiverar identitets baserad autentisering över SMB (Server Message Block) för Azure Files via Azure Active Directory Domain Services. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06ff14b23057755a643e5a57fbaf711798cca00e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210490"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Aktivera Azure Active Directory Domain Services autentisering på Azure Files

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

En översikt över den identitetsbaserade autentisering som stöds på Azure Files finns i [Översikt över Azure Active Directory autentisering över SMB för Azure Files](storage-files-active-directory-overview.md). Den här artikeln fokuserar på hur du aktiverar autentisering med Azure Active Directory Domain Services (Azure AD DS) på Azure Files. 
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Översikt över arbets flödet

Innan du aktiverar Azure AD DS-autentisering över SMB för Azure Files kontrollerar du att dina Azure AD-och Azure Storage-miljöer är korrekt konfigurerade. Vi rekommenderar att du går igenom [kraven](#prerequisites) för att se till att du har slutfört alla nödvändiga steg.

Ge sedan åtkomst till Azure Files resurser med Azure AD-autentiseringsuppgifter genom att följa dessa steg:

1. Aktivera Azure AD DS-autentisering över SMB för ditt lagrings konto för att registrera lagrings kontot med den associerade Azure AD DS-distributionen.
2. Tilldela åtkomst behörigheter för en resurs till en Azure AD-identitet (en användare, grupp eller tjänstens huvud namn).
3. Konfigurera NTFS-behörigheter över SMB för kataloger och filer.
4. Montera en Azure-filresurs från en domänansluten virtuell dator.

Följande diagram illustrerar arbets flödet från slut punkt till slut punkt för att aktivera Azure AD DS-autentisering över SMB för Azure Files.

![Diagram över Azure AD över SMB för Azure Files arbets flöde](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Förutsättningar

Innan du aktiverar Azure AD över SMB för Azure Files bör du kontrol lera att du har slutfört följande krav:

1.  **Välj eller skapa en Azure AD-klient.**

    Du kan använda en ny eller befintlig klient för Azure AD-autentisering över SMB. Klienten och den fil resurs som du vill komma åt måste vara associerad med samma prenumeration.

    Om du vill skapa en ny Azure AD-klient kan du [lägga till en Azure AD-klient och en Azure AD-prenumeration](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Om du har en befintlig Azure AD-klient men vill skapa en ny klient som ska användas med Azure Files, se [skapa en Azure Active Directory klient](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Aktivera Azure AD Domain Services på Azure AD-klienten.**

    Om du vill ha stöd för autentisering med Azure AD-autentiseringsuppgifter måste du aktivera Azure AD Domain Services för din Azure AD-klient. Om du inte är administratör för Azure AD-klienten kontaktar du administratören och följer steg-för-steg-vägledningen för att [aktivera Azure Active Directory Domain Services att använda Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Det tar vanligt vis ungefär 15 minuter för en Azure AD DS-distribution att slutföras. Kontrol lera att hälso statusen för Azure AD **DS visar att den är**aktive rad och att synkronisering av lösen ord är aktiverat, innan du fortsätter till nästa steg.

3.  **Domän – Anslut till en virtuell Azure-dator med Azure AD DS.**

    För att få åtkomst till en fil resurs med hjälp av autentiseringsuppgifter för Azure AD från en virtuell dator måste den virtuella datorn vara domänansluten till Azure AD DS. Mer information om hur du ansluter till en virtuell dator finns i [ansluta en virtuell Windows Server-dator till en hanterad domän](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure AD DS-autentisering över SMB med Azure Files stöds endast på virtuella Azure-datorer som körs på OS-versioner över Windows 7 eller Windows Server 2008 R2.

4.  **Välj eller skapa en Azure-filresurs.**

    Välj en ny eller befintlig fil resurs som är associerad med samma prenumeration som din Azure AD-klient. Information om hur du skapar en ny fil resurs finns [i skapa en fil resurs i Azure Files](storage-how-to-create-file-share.md).
    För optimala prestanda rekommenderar vi att fil resursen finns i samma region som den virtuella dator som du planerar att komma åt resursen från.

5.  **Verifiera Azure Files anslutningen genom att montera Azure-filresurser med hjälp av lagrings konto nyckeln.**

    Om du vill kontrol lera att den virtuella datorn och fil resursen är korrekt konfigurerade kan du försöka att montera fil resursen med din lagrings konto nyckel. Mer information finns i [montera en Azure-filresurs och få åtkomst till resursen i Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Aktivera Azure AD DS-autentisering för ditt konto

Om du vill aktivera Azure AD DS-autentisering över SMB för Azure Files kan du ange en egenskap för lagrings konton med hjälp av Azure Portal, Azure PowerShell eller Azure CLI. Om du anger den här egenskapen implicit "domän anslutning" är lagrings kontot med den associerade Azure AD DS-distributionen. Azure AD DS-autentisering över SMB aktive ras sedan för alla nya och befintliga fil resurser i lagrings kontot.

Tänk på att du bara kan aktivera Azure AD DS-autentisering över SMB när du har distribuerat Azure AD DS till Azure AD-klienten. Mer information finns i [krav](#prerequisites).

### <a name="azure-portal"></a>Azure-portalen

Följ dessa steg om du vill aktivera Azure AD DS-autentisering över SMB med [Azure Portal](https://portal.azure.com):

1. I Azure Portal går du till ditt befintliga lagrings konto eller så [skapar du ett lagrings konto](../common/storage-account-create.md).
2. I avsnittet **Inställningar** väljer du **konfiguration**.
3. Välj **Azure Active Directory Domain Services (Azure AD DS)** i list rutan **identitetsbaserade katalog tjänster för Azure-filautentisering** .

Följande bild visar hur du aktiverar Azure AD DS-autentisering över SMB för ditt lagrings konto.

![Aktivera Azure AD-autentisering över SMB i Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Om du vill aktivera Azure AD DS-autentisering över SMB med Azure PowerShell installerar du den senaste AZ-modulen (2,4 eller senare) eller modulen AZ. Storage (1,5 eller senare). Mer information om hur du installerar PowerShell finns i [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Om du vill skapa ett nytt lagrings konto anropar du [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)och anger sedan parametern **EnableAzureActiveDirectoryDomainServicesForFile** till **True**. I följande exempel ska du komma ihåg att ersätta plats hållarnas värden med dina egna värden. (Om du använde den tidigare förhands granskningen är parametern för funktions aktivering **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Om du vill aktivera den här funktionen på befintliga lagrings konton använder du följande kommando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Om du vill aktivera Azure AD-autentisering över SMB med Azure CLI installerar du den senaste CLI-versionen (version 2.0.70 eller senare). Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill skapa ett nytt lagrings konto anropar du[AZ lagrings konto Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)och anger egenskapen `--enable-files-aadds` till **True**. I följande exempel ska du komma ihåg att ersätta plats hållarnas värden med dina egna värden. (Om du använde den tidigare förhands granskningen är parametern för funktions aktivering **fil-AAD**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Om du vill aktivera den här funktionen på befintliga lagrings konton använder du följande kommando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Tilldela åtkomst behörigheter till en identitet

För att få åtkomst till Azure Files-resurser med Azure AD-autentiseringsuppgifter måste en identitet (användare, grupp eller tjänstens huvud namn) ha nödvändig behörighet på delnings nivå. Den här processen liknar att ange behörigheter för Windows-resurs, där du anger vilken typ av åtkomst som en viss användare har till en fil resurs. Rikt linjerna i det här avsnittet visar hur du tilldelar behörigheterna läsa, skriva eller ta bort för en fil resurs till en identitet.

Vi har lanserat två inbyggda Azure-roller för att bevilja behörigheter på resurs nivå till användare:

- **Lagrings fil data SMB Share Reader** tillåter Läs åtkomst i Azure Storage fil resurser över SMB.
- **Storage File data SMB Share Contributor** tillåter Läs-, skriv-och borttagnings åtkomst i Azure Storage fil resurser över SMB.
- **Lagrings fil data SMB dela utökad deltagare** tillåter Läs-, Skriv-, borttagnings-och ändrings-NTFS-behörigheter i Azure Storage fil resurser över SMB.

> [!IMPORTANT]
> Fullständig administrativ kontroll av en fil resurs, inklusive möjligheten att tilldela en roll till en identitet, kräver att du använder lagrings konto nyckeln. Administrativ kontroll stöds inte med autentiseringsuppgifter för Azure AD.

Du kan använda Azure Portal, PowerShell eller Azure CLI för att tilldela de inbyggda rollerna till Azure AD-identiteten för en användare för att bevilja behörigheter på resurs nivå.

#### <a name="azure-portal"></a>Azure-portalen
Följ dessa steg om du vill tilldela en RBAC-roll till en Azure AD-identitet med hjälp av [Azure Portal](https://portal.azure.com):

1. I Azure Portal går du till fil resursen eller [skapar en fil resurs i Azure Files](storage-how-to-create-file-share.md).
2. Välj **Access Control (IAM)** .
3. Välj **Lägg till en roll tilldelning**
4. På bladet **Lägg till roll tilldelning** väljer du lämplig inbyggd roll (lagrings fil data SMB Share Reader, Storage File data SMB Share Contributor) från **roll** listan. Behåll alternativet **tilldela åtkomst till** med standardinställningen: **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj målets Azure AD-identitet efter namn eller e-postadress.
5. Klicka på **Spara** för att slutföra roll tilldelnings åtgärden.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-kommando visar hur du tilldelar en RBAC-roll till en Azure AD-identitet baserat på inloggnings namn. Mer information om hur du tilldelar RBAC-roller med PowerShell finns i [Hantera åtkomst med RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Innan du kör följande exempel skript måste du komma ihåg att ersätta plats hållarnas värden, inklusive hakparenteser, med dina egna värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI

Följande CLI 2,0-kommando visar hur du tilldelar en RBAC-roll till en Azure AD-identitet baserat på inloggnings namn. Mer information om hur du tilldelar RBAC-roller med Azure CLI finns i [Hantera åtkomst med RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md).

Innan du kör följande exempel skript måste du komma ihåg att ersätta plats hållarnas värden, inklusive hakparenteser, med dina egna värden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurera NTFS-behörigheter över SMB
När du har tilldelat behörigheter på resurs nivå med RBAC måste du tilldela rätt NTFS-behörighet på rot-, katalog-eller filnivå. Tänk på behörigheter på resurs nivå som den övergripande gatekeepern som avgör om en användare har åtkomst till resursen. NTFS-behörigheter fungerar på en mer detaljerad nivå för att avgöra vilka åtgärder användaren kan göra på katalog-eller filnivå.

Azure Files stöder en fullständig uppsättning NTFS Basic-och Advanced-behörigheter. Du kan visa och konfigurera NTFS-behörigheter för kataloger och filer i en Azure-filresurs genom att montera resursen och sedan använda Utforskaren i Windows eller köra kommandot Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) eller [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) .

Om du vill konfigurera NTFS med behörigheter för superanvändare måste du montera resursen med hjälp av lagrings konto nyckeln från din domänanslutna VM. Följ anvisningarna i nästa avsnitt för att montera en Azure-filresurs från kommando tolken och konfigurera NTFS-behörigheter enligt detta.

Följande behörighets uppsättningar stöds i rot katalogen för en fil resurs:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT INSTANS\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT Instans\autentiserade-användare: (OI) (CI) (M)
- NT INSTANS\SYSTEM: (F)
- SKAPARE ÄGARE: (OI) (CI) (IO) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montera en fil resurs från kommando tolken

Använd kommandot Windows **net use** för att montera Azure-filresursen. Kom ihåg att ersätta plats hållarnas värden i följande exempel med dina egna värden. Mer information om hur du monterar fil resurser finns i [montera en Azure-filresurs och få åtkomst till resursen i Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurera NTFS-behörigheter med Windows Utforskaren
Använd Utforskaren i Windows för att ge fullständig behörighet till alla kataloger och filer under fil resursen, inklusive rot katalogen.

1. Öppna Utforskaren i Windows och högerklicka på filen/katalogen och välj **Egenskaper**
2. Klicka på fliken **säkerhet**
3. Klicka på **Redigera..** . knapp för att ändra behörigheter
4. Du kan ändra behörigheten för befintliga användare eller klicka på **Lägg till...** om du vill bevilja behörigheter för nya användare
5. I fönstret prompt för att lägga till nya användare anger du det mål användar namn som du vill ge behörighet i rutan **Ange de objekt namn som ska väljas** och klickar på **kontrol lera namn** för att hitta det fullständiga UPN-namnet för mål användaren.
7.  Klicka på **OK**
8.  På fliken säkerhet väljer du alla behörigheter som du vill bevilja till den nyligen tillagda användaren
9.  Klicka på **Verkställ**

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurera NTFS-behörigheter med icacls
Använd följande Windows-kommando för att ge fullständig behörighet till alla kataloger och filer under fil resursen, inklusive rot katalogen. Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Mer information om hur du använder icacls för att ange NTFS-behörigheter och de olika typerna av behörigheter som stöds finns i [kommando rads referens för icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montera en fil resurs från en domänansluten virtuell dator

Följande process kontrollerar att dina autentiseringsuppgifter för Azure AD har kon figurer ATS korrekt och att du kan komma åt en Azure-filresurs från en domänansluten virtuell dator:

Logga in på den virtuella datorn med hjälp av den Azure AD-identitet som du har beviljat behörigheter för, som visas i följande bild.

![Skärm bild som visar inloggnings skärmen för Azure AD för användarautentisering](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Använd följande kommando för att montera Azure-filresursen. Kom ihåg att ersätta plats hållarnas värden med dina egna värden. Eftersom du redan har autentiserats behöver du inte ange lagrings konto nyckeln eller användar namnet och lösen ordet för Azure AD. Azure AD över SMB har stöd för enkel inloggning med Azure AD-autentiseringsuppgifter.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Nu har du aktiverat Azure AD-autentisering över SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en Azure AD-identitet. Om du vill ge fler användare åtkomst till fil resursen följer du anvisningarna i avsnitten [tilldela åtkomst behörigheter till en identitet](#assign-access-permissions-to-an-identity) och [Konfigurera NTFS-behörigheter över SMB](#configure-ntfs-permissions-over-smb) .

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files och hur du använder Azure AD över SMB finns i följande resurser:

- [Introduktion till Azure Files](storage-files-introduction.md)
- [Översikt över Azure Active Directory-autentisering över SMB för Azure Files](storage-files-active-directory-overview.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
