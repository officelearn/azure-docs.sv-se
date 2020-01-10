---
title: 'Azure Data Lake Storage Gen2 PowerShell för filer & ACL: er (för hands version)'
description: Använd PowerShell-cmdletar för att hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 983ae646db5f51f7efaa2ff2569133e20e2d1dbd
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834955"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Använd PowerShell för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2 (för hands version)

Den här artikeln visar hur du använder PowerShell för att skapa och hantera kataloger, filer och behörigheter i lagrings konton med hierarkiskt namn område (HNS) aktiverat. 

> [!IMPORTANT]
> PowerShell-modulen som finns i den här artikeln är för närvarande en offentlig för hands version.

[Gen1 till Gen2-mappning](#gen1-gen2-map) | [ge feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.
> * .NET Framework är 4.7.2 eller senare installerad. Se [Ladda ned .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell-version `5.1` eller högre.

## <a name="install-powershell-modules"></a>Installera PowerShell-moduler

1. Kontrol lera att PowerShell-versionen som är installerad `5.1` eller högre med hjälp av följande kommando. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Information om hur du uppgraderar din version av PowerShell finns i [uppgradera befintliga Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Installera den senaste **PowershellGet** -modulen. Stäng sedan och öppna PowerShell-konsolen igen.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Installera **AZ. Storage** Preview module.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Anslut till kontot

Öppna ett Windows PowerShell-kommando fönster och logga sedan in på Azure-prenumerationen med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill skapa och hantera kataloger i. I det här exemplet ersätter du `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Välj sedan hur du vill att dina kommandon ska få behörighet till lagrings kontot. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Alternativ 1: få behörighet genom att använda Azure Active Directory (AD)

Med den här metoden säkerställer systemet att ditt användar konto har lämpliga RBAC-tilldelningar (rollbaserad åtkomst kontroll) och ACL-behörigheter. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Alternativ 2: få behörighet genom att använda lagrings konto nyckeln

Med den här metoden kontrollerar systemet inte RBAC-eller ACL-behörigheter för en resurs.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett fil system fungerar som en behållare för dina filer. Du kan skapa en med hjälp av `New-AzDatalakeGen2FileSystem`-cmdleten. 

I det här exemplet skapas ett fil system med namnet `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens med hjälp av `New-AzDataLakeGen2Item`-cmdleten. 

I det här exemplet läggs en katalog med namnet `my-directory` till i ett fil system.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Det här exemplet lägger till samma katalog, men anger även behörigheter, umask, egenskaps värden och metadata. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Visa katalog egenskaper

Det här exemplet hämtar en katalog med hjälp av `Get-AzDataLakeGen2Item`-cmdlet och skriver sedan ut egenskaps värden till-konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog med hjälp av `Move-AzDataLakeGen2Item`-cmdleten.

I det här exemplet byter namn på en katalog från namnet `my-directory` till namnet `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

I det här exemplet flyttas en katalog med namnet `my-directory` till en under katalog till `my-directory-2` med namnet `my-subdirectory`. I det här exemplet tillämpas också en umask på under katalogen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog med hjälp av `Remove-AzDataLakeGen2Item`-cmdleten.

I det här exemplet tas en katalog med namnet `my-directory`bort. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Du kan använda parametern `-Force` för att ta bort filen utan någon prompt.

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Hämta en fil från en katalog med hjälp av `Get-AzDataLakeGen2ItemContent`-cmdleten.

I det här exemplet hämtas en fil med namnet `upload.txt` från en katalog med namnet `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog med hjälp av `Get-AzDataLakeGen2ChildItem`-cmdleten.

I det här exemplet visas innehållet i en katalog med namnet `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Det här exemplet returnerar inte värden för egenskaperna `ACL`, `Permissions`, `Group`och `Owner`. Hämta dessa värden med hjälp av parametern `-FetchPermission`. 

I följande exempel visas innehållet i samma katalog, men det använder också parametern `-FetchPermission` för att returnera värden för egenskaperna `ACL`, `Permissions`, `Group`och `Owner`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Om du vill visa innehållet i ett fil system utelämnar du parametern `-Path` från kommandot.

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till en katalog med hjälp av `New-AzDataLakeGen2Item`-cmdleten.

I det här exemplet överförs en fil med namnet `upload.txt` till en katalog med namnet `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

I det här exemplet överförs samma fil, men sedan anges behörigheter, umask, egenskaps värden och metadata för målfilen. I det här exemplet skrivs även dessa värden ut till-konsolen.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Visa fil egenskaper

Det här exemplet hämtar en fil med hjälp av `Get-AzDataLakeGen2Item`-cmdlet och skriver sedan ut egenskaps värden till-konsolen.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Ta bort en fil

Ta bort en fil med hjälp av `Remove-AzDataLakeGen2Item`-cmdleten.

I det här exemplet tas en fil med namnet `upload.txt`bort. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Du kan använda parametern `-Force` för att ta bort filen utan någon prompt.

## <a name="manage-access-permissions"></a>Hantera åtkomst behörigheter

Du kan hämta, ange och uppdatera åtkomst behörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera kommandon kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Hämta katalog-och fil behörigheter

Hämta ACL för en katalog eller fil med hjälp av `Get-AzDataLakeGen2Item`cmdlet.

Det här exemplet hämtar ACL: en för en **katalog**och skriver sedan ut ACL: en till-konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Det här exemplet hämtar ACL: en för en **fil** och skriver sedan ut ACL: en till-konsolen.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Följande bild visar utdata när du har hämtat ACL för en katalog.

![Hämta ACL-utdata](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

I det här exemplet har ägande användaren Läs-, skriv-och körnings behörighet. Den ägande gruppen har bara Läs-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ange katalog-och fil behörigheter

Använd `New-AzDataLakeGen2ItemAclObject`-cmdlet för att skapa en ACL för ägande användare, ägande grupp eller andra användare. Använd sedan `Update-AzDataLakeGen2Item`-cmdlet: en för att genomföra ACL: en.

I det här exemplet anges ACL: en för en **katalog** för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
I det här exemplet anges ACL: en för en **fil** för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Följande bild visar utdata när du har angett ACL för en fil.

![Hämta ACL-utdata](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

I det här exemplet har ägande användare och ägande grupp bara Läs-och Skriv behörighet. Alla andra användare har Skriv-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Uppdatera katalog-och fil behörigheter

Använd cmdleten `Get-AzDataLakeGen2Item` för att hämta ACL för en katalog eller fil. Använd sedan `New-AzDataLakeGen2ItemAclObject`-cmdlet: en för att skapa en ny ACL-post. Använd `Update-AzDataLakeGen2Item`-cmdlet för att tillämpa den nya ACL: en.

Det här exemplet ger användaren Skriv-och körnings behörighet för en katalog.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the directory ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew  

```

Det här exemplet ger användaren Skriv-och körnings behörighet för en fil.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the file ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $aclnew 

```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Ange behörigheter för alla objekt i ett fil system

Du kan använda `Get-AzDataLakeGen2Item` och parametern `-Recurse` tillsammans med `Update-AzDataLakeGen2Item`-cmdlet för att rekursivt ange ACL för alla kataloger och filer i ett fil system. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 till Gen2-mappning

I följande tabell visas hur cmdletarna som används för Data Lake Storage Gen1 mappas till-cmdletarna för Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Anteckningar |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Som standard listar get-AzDataLakeGen2ChildItem-cmdlet endast den första nivån underordnade objekt. Parametern-rekursivt listar underordnade objekt rekursivt. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Utgående objekt i get-AzDataLakeGen2Item-cmdleten har följande egenskaper: ACL, ägare, grupp, behörighet.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Cmdlet: en get-AzDataLakeGen2FileContent hämtar fil innehållet till en lokal fil.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Denna cmdlet överför det nya fil innehållet från en lokal fil.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Uppdatera – AzDataLakeGen2Item|Cmdlet: en Update-AzDataLakeGen2Item uppdaterar endast ett enskilt objekt och inte rekursivt. Om du vill uppdatera rekursivt, list objekt med hjälp av cmdleten Get-AzDataLakeStoreChildItem, går du sedan till cmdleten Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item-cmdlet: en rapporterar ett fel om objektet inte finns.|



## <a name="see-also"></a>Se också

* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Använda Azure PowerShell med Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Storage PowerShell-cmdletar](/powershell/module/az.storage).

