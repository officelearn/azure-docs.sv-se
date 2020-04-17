---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/11/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: e40171b95e6faae0020f8bf61410aad8999ddecb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536545"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Tilldela åtkomstbehörigheter till en identitet

För att komma åt Azure Files-resurser med identitetsbaserad autentisering måste en identitet (en användare, grupp eller tjänsthuvudnamn) ha de behörigheter som krävs på resursnivå. Den här processen påminner om att ange Windows-resursbehörigheter, där du anger vilken typ av åtkomst som en viss användare har till en filresurs. Vägledningen i det här avsnittet visar hur du tilldelar läs-, skriv- eller borttagningsbehörigheter för en filresurs till en identitet. 

Vi har introducerat tre azure-inbyggda roller för att bevilja behörigheter på delningsnivå till användare:

- **Storage File Data SMB Share Reader** tillåter läsåtkomst i Azure Storage-filresurser över SMB.
- **Storage File Data SMB Share Contributor** tillåter läs-, skriv- och borttagningsåtkomst i Azure Storage-filresurser över SMB.
- **Storage File Data SMB Share Elevated Contributor** tillåter läsa, skriva, ta bort och ändra NTFS-behörigheter i Azure Storage-filresurser över SMB.

> [!IMPORTANT]
> Fullständig administrativ kontroll av en filresurs, inklusive möjligheten att bli ägare till en fil, kräver att du använder lagringskontonyckeln. Administrativ kontroll stöds inte med Azure AD-autentiseringsuppgifter.

Du kan använda Azure-portalen, PowerShell eller Azure CLI för att tilldela de inbyggda rollerna till Azure AD-identiteten för en användare för att bevilja behörigheter på delningsnivå.

> [!NOTE]
> Kom ihåg att [synkronisera dina AD DS-autentiseringsuppgifter till Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) om du planerar att använda din lokala AD DS för autentisering. Synkronisering av lösenord hash från AD DS till Azure AD är valfritt. Behörighet på delningsnivå beviljas till Azure AD-identiteten som synkroniseras från din lokala AD DS.

Den allmänna rekommendationen är att använda behörighet på resursnivå för åtkomsthantering på hög nivå till en AD-grupp som representerar en grupp användare och identiteter och sedan utnyttja NTFS-behörigheter för detaljerad åtkomstkontroll på katalog-/filnivå. 

#### <a name="azure-portal"></a>Azure Portal
Så här tilldelar du en RBAC-roll till en Azure AD-identitet: [Azure portal](https://portal.azure.com)

1. Gå till filresursen i Azure-portalen eller [Skapa en filresurs](../articles/storage/files/storage-how-to-create-file-share.md).
2. Välj **Åtkomstkontroll (IAM)**.
3. Välj **Lägg till en rolltilldelning**
4. I bladet **Lägg till rolltilldelning** väljer du lämplig inbyggd roll (Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor) i **rolllistan.** Lämna **tilldela åtkomst till** vid standardinställningen: Azure **AD-användare, grupp eller tjänsthuvudnamn**. Välj målet Azure AD-identitet efter namn eller e-postadress.
5. Välj **Spara** om du vill slutföra rolltilldelningsåtgärden.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-exempel visar hur du tilldelar en RBAC-roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om hur du tilldelar RBAC-roller med PowerShell finns i [Hantera åtkomst med RBAC och Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Innan du kör följande exempelskript bör du komma ihåg att ersätta platshållarvärden, inklusive parenteser, med dina egna värden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Följande CLI 2.0-kommando visar hur du tilldelar en RBAC-roll till en Azure AD-identitet, baserat på inloggningsnamn. Mer information om hur du tilldelar RBAC-roller med Azure CLI finns i [Hantera åtkomst med hjälp av RBAC och Azure CLI](../articles/role-based-access-control/role-assignments-cli.md). 

Innan du kör följande exempelskript bör du komma ihåg att ersätta platshållarvärden, inklusive parenteser, med dina egna värden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. Konfigurera NTFS-behörigheter över SMB 
När du har tilldelat behörigheter på resursnivå med RBAC måste du tilldela rätt NTFS-behörigheter på rot-, katalog- eller filnivå. Tänk på behörigheter på resursnivå som gatekeeper på hög nivå som avgör om en användare kan komma åt resursen. Medan NTFS-behörigheter fungerar på en mer detaljerad nivå för att avgöra vilka åtgärder användaren kan göra på katalog- eller filnivå.

Azure Files stöder den fullständiga uppsättningen ntfs-grundläggande och avancerade behörigheter. Du kan visa och konfigurera NTFS-behörigheter för kataloger och filer i en Azure-filresurs genom att montera resursen och sedan använda Utforskaren i Windows eller köra kommandot Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) eller [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) 

Om du vill konfigurera NTFS med superanvändarbehörigheter måste du montera resursen med hjälp av lagringskontonyckeln från domänens anslutna virtuella dator. Följ instruktionerna i nästa avsnitt för att montera en Azure-filresurs från kommandotolken och konfigurera NTFS-behörigheter därefter.

Följande behörighetsgrupper stöds i rotkatalogen för en filresurs:

- BUILTIN\Administratörer:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Användare:(RX)
- BUILTIN\Användare:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Autentiserade användare:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- SKAPARE ÄGARE:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurera NTFS-behörigheter med icacls
Använd följande Windows-kommando för att ge alla kataloger och filer fullständiga behörigheter under filresursen, inklusive rotkatalogen. Kom ihåg att ersätta platshållarvärdena i exemplet med dina egna värden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Mer information om hur du använder icacls för att ange NTFS-behörigheter och om de olika typerna av behörigheter som stöds finns [i kommandoradsreferensen för icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montera en filresurs från kommandotolken

Använd kommandot Windows **net use** för att montera Azure-filresursen. Kom ihåg att ersätta platshållarvärdena i följande exempel med dina egna värden. Mer information om hur du monterar filresurser finns i [Använda en Azure-filresurs med Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurera NTFS-behörigheter med Utforskaren
Använd Utforskaren i Utforskaren för att ge fullständig behörighet till alla kataloger och filer under filresursen, inklusive rotkatalogen.

1. Öppna Utforskaren i Utforskaren och högerklicka på filen/katalogen och välj **Egenskaper**.
2. Välj fliken **Säkerhet.**
3. Välj **Redigera..** för att ändra behörigheter.
4. Du kan ändra behörigheterna för befintliga användare eller välja **Lägg till...** om du vill bevilja behörigheter till nya användare.
5. I snabbfönstret för att lägga till nya användare anger du det målanvändarnamn som du vill bevilja behörighet till i rutan **Ange objektnamnen som ska markeras** och väljer **Kontrollera namn** för att hitta målanvändarens fullständiga UPN-namn.
7.    Välj **OK**.
8.    På fliken **Säkerhet** väljer du alla behörigheter som du vill bevilja den nya användaren.
9.    Välj **Använd**.

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Montera en filresurs från en domänansluten virtuell dator

Följande process verifierar att filresurs- och åtkomstbehörigheterna har konfigurerats korrekt och att du kan komma åt en Azure File-resurs från en domänansluten virtuell dator. Tänk på att rbac-rolltilldelningen på resursnivå kan ta lite tid att vara i kraft. 

Logga in på den virtuella datorn med hjälp av Azure AD-identitet som du har beviljat behörigheter till, vilket visas i följande avbildning. Om du har aktiverat lokal AD DS-autentisering för Azure-filer använder du dina AD DS-autentiseringsuppgifter. För Azure AD DS-autentisering loggar du in med Azure AD-autentiseringsuppgifter.

![Skärmbild som visar inloggningsskärmen för Azure AD för användarautentisering](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Använd följande kommando för att montera Azure-filresursen. Kom ihåg att ersätta platshållarvärdena med dina egna värden. Eftersom du har autentiserats behöver du inte ange lagringskontonyckeln, de lokala AD DS-autentiseringsuppgifterna eller Azure AD DS-autentiseringsuppgifterna. Enkel inloggningsupplevelse stöds för autentisering med antingen lokalt AD DS eller Azure AD DS. Om du stöter på problem med montering med AD DS-autentiseringsuppgifter läser du [Felsöka Azure Files-problem i Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) för vägledning.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
