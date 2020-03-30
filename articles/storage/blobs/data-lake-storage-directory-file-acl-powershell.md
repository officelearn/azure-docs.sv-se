---
title: Azure Data Lake Storage Gen2 PowerShell för filer & ACL:er (förhandsversion)
description: Använd PowerShell-cmdletar för att hantera kataloger och ACL (File and Directory Access Control List) i lagringskonton med hierarkiskt namnområde (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: a2f3dbf58363331cf6b1b05e759d246e68e7e7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471218"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Använda PowerShell för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2 (förhandsversion)

Den här artikeln visar hur du använder PowerShell för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har aktiverat hierarkiskt namnområde (HNS). 

> [!IMPORTANT]
> PowerShell-modulen som finns med i den här artikeln är för närvarande i offentlig förhandsversion.

[Gen1 till Gen2-mappning](#gen1-gen2-map) | [Ge feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.
> * .NET Framework är 4.7.2 eller mer installerat. Se [Hämta .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell-version `5.1` eller högre.

## <a name="install-powershell-modules"></a>Installera PowerShell-moduler

1. Kontrollera att den version av PowerShell som har installerats är `5.1` eller högre med hjälp av följande kommando. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Information om hur du uppgraderar din version av PowerShell finns i [Uppgradera befintliga Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Installera den senaste **PowershellGet-modulen.** Stäng sedan och öppna Powershell-konsolen igen.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Installera förhandsversionen av **Az.Storage.**

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Mer information om hur du installerar PowerShell-moduler finns i [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Anslut till kontot

Öppna ett Windows PowerShell-kommandofönster och logga sedan `Connect-AzAccount` in på din Azure-prenumeration med kommandot och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om din identitet är associerad med mer än en prenumeration ställer du in din aktiva prenumeration på prenumeration på det lagringskonto som du vill skapa och hantera kataloger i. I det här `<subscription-id>` exemplet ersätter du platshållarvärdet med id:t för din prenumeration.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Välj sedan hur du vill att dina kommandon ska få auktorisering till lagringskontot. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Alternativ 1: Skaffa auktorisering med hjälp av Azure Active Directory (AD)

Med den här metoden säkerställer systemet att ditt användarkonto har lämpliga rollbaserade åtkomstkontroll (RBAC) tilldelningar och ACL-behörigheter. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Alternativ 2: Skaffa auktorisering med hjälp av lagringskontonyckeln

Med den här metoden kontrollerar systemet inte RBAC- eller ACL-behörigheter.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett filsystem fungerar som en behållare för dina filer. Du kan skapa en `New-AzDatalakeGen2FileSystem` med hjälp av cmdlet. 

I det här exemplet `my-file-system`skapas ett filsystem med namnet .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalogreferens `New-AzDataLakeGen2Item` med hjälp av cmdleten. 

I det här `my-directory` exemplet läggs en katalog som heter till i ett filsystem.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

I det här exemplet läggs samma katalog till, men behörigheterna, umask-, egenskapsvärdena och metadatavärdena anges. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Visa katalogegenskaper

Det här exemplet hämtar `Get-AzDataLakeGen2Item` en katalog med hjälp av cmdlet och skriver sedan ut egenskapsvärden till konsolen.

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

Byt namn eller flytta `Move-AzDataLakeGen2Item` en katalog med hjälp av cmdlet.

I det här exemplet byter `my-directory` du `my-new-directory`namn på en katalog från namnet till namnet .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

I det här `my-directory` exemplet flyttas en `my-directory-2` katalog `my-subdirectory`med namnet till en underkatalog med namnet . I det här exemplet används även en umask på underkatalogen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en `Remove-AzDataLakeGen2Item` katalog med hjälp av cmdleten.

I det här exemplet `my-directory`tas en katalog med namnet . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Du kan `-Force` använda parametern för att ta bort filen utan en fråga.

## <a name="download-from-a-directory"></a>Hämta från en katalog

Hämta en fil från en `Get-AzDataLakeGen2ItemContent` katalog med hjälp av cmdlet.

I det här exemplet `upload.txt` hämtas `my-directory`en fil som heter från en katalog med namnet . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog `Get-AzDataLakeGen2ChildItem` med hjälp av cmdlet.

I det här exemplet visas `my-directory`innehållet i en katalog med namnet .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Det här exemplet returnerar `ACL`inte `Permissions` `Group`värden `Owner` för egenskaperna , , och . Använd parametern `-FetchPermission` för att hämta dessa värden. 

I följande exempel visas innehållet i samma katalog, `-FetchPermission` men parametern `ACL`används `Permissions` `Group`också `Owner` för att returnera värden för egenskaperna , , och . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Om du vill visa innehållet i `-Path` ett filsystem utelämnar du parametern från kommandot.

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till `New-AzDataLakeGen2Item` en katalog med hjälp av cmdlet.

I det här exemplet `upload.txt` överförs en `my-directory`fil som heter till en katalog med namnet . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

I det här exemplet överförs samma fil, men sedan anges behörigheter, umask, egenskapsvärden och metadatavärden för målfilen. I det här exemplet skrivs även dessa värden ut på konsolen.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Visa filegenskaper

Det här exemplet hämtar `Get-AzDataLakeGen2Item` en fil med hjälp av cmdlet och skriver sedan ut egenskapsvärden till konsolen.

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

Ta bort en `Remove-AzDataLakeGen2Item` fil med hjälp av cmdleten.

I det här exemplet `upload.txt`tas en fil med namnet . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Du kan `-Force` använda parametern för att ta bort filen utan en fråga.

## <a name="manage-access-permissions"></a>Hantera åtkomstbehörigheter

Du kan hämta, ange och uppdatera åtkomstbehörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera kommandon kontrollerar du att säkerhetsobjektet har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Hämta katalog- och filbehörigheter

Hämta åtkomstkontrollistan för en katalog `Get-AzDataLakeGen2Item`eller fil med hjälp av cmdleten.

Det här exemplet hämtar **åtkomstkontrollistan**för en katalog och skriver sedan ut åtkomstkontrollistan till konsolen.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Det här exemplet hämtar åtkomstkontrollistan för en **fil** och skriver sedan ut åtkomstkontrollistan till konsolen.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Följande bild visar utdata efter att ha hämtat åtkomstkontrollistan för en katalog.

![Hämta ACL-utgång](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

I det här exemplet har den ägande användaren läst, skrivit och kör behörigheter. Den ägande gruppen har bara läs- och körningsbehörigheter. Mer information om åtkomstkontrollistor finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ange katalog- och filbehörigheter

Använd `New-AzDataLakeGen2ItemAclObject` cmdleten för att skapa en åtkomstkontrollista för den egna användaren, ägargruppen eller andra användare. Använd sedan `Update-AzDataLakeGen2Item` cmdleten för att begå åtkomstkontrollistan.

I det här exemplet anges åtkomstkontrollistan i en **katalog** för den ägande användaren, ägargruppen eller andra användare och sedan ACL:en till konsolen.

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
I det här exemplet anges åtkomstkontrollistan för en **fil** för den ägande användaren, ägargruppen eller andra användare och sedan ACL:en till konsolen.

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

Följande bild visar utdata när du har ställt in åtkomstkontrollistan för en fil.

![Hämta ACL-utgång](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

I det här exemplet har den ägande användaren och den ägande gruppen bara läs- och skrivbehörighet. Alla andra användare har skriv- och körningsbehörighet. Mer information om åtkomstkontrollistor finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Uppdatera katalog- och filbehörigheter

Använd `Get-AzDataLakeGen2Item` cmdlet för att hämta åtkomstkontrollistan för en katalog eller fil. Använd sedan `New-AzDataLakeGen2ItemAclObject` cmdlet för att skapa en ny ACL-post. Använd `Update-AzDataLakeGen2Item` cmdleten för att tillämpa den nya åtkomstkontrollistan.

Det här exemplet ger en grupp skriv- och körningsbehörighet för en katalog.

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

Det här exemplet ger en grupp skriv- och körningsbehörighet för en fil.

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

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Ange behörigheter för alla objekt i ett filsystem

Du kan `Get-AzDataLakeGen2Item` använda `-Recurse` parametern och `Update-AzDataLakeGen2Item` parametern tillsammans med cmdleten för att rekursivt ställa in åtkomstkontrollistan för alla kataloger och filer i ett filsystem. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchPermission | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 till Gen2-mappning

Följande tabell visar hur de cmdlets som används för Data Lake Storage Gen1 mappas till cmdlets för Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Anteckningar |
|--------|---------|-----|
|Hämta-AzDataLakeStoreChildItem|Hämta AzDataLakeGen2ChildItem|Som standard listar cmdleten Get-AzDataLakeGen2ChildItem endast de underordnade objekten på den första nivån. Parametern -Recurse visar underordnade objekt rekursivt. |
|Hämta-AzDataLakeStoreItem<br>Hämta-AzDataLakeStoreItemAclEntry<br>Hämta-AzDataLakeStoreItemOwner<br>Hämta-AzDataLakeStoreItemPermission|Få-AzDataLakeGen2Item|Utdataobjekten i cmdleten Get-AzDataLakeGen2Item har följande egenskaper: Acl, Ägare, Grupp, Behörighet.|
|Get-AzDataLakeStoreItemInnehåll|Få-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent cmdlet ladda ner filinnehåll till lokal fil.|
|Flytta-AzDataLakeStoreItem|Flytta-AzDataLakeGen2Item||
|Nya-AzDataLakeStoreItem|Nya-AzDataLakeGen2Item|Den här cmdleten överför det nya filinnehållet från en lokal fil.|
|Ta bort-AzDataLakeStoreItem|Ta bort-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Uppdatering-AzDataLakeGen2Item|Cmdlet update-AzDataLakeGen2Item uppdaterar endast ett enda objekt och inte rekursivt. Om du vill uppdatera rekursivt, lista objekt med hjälp av Get-AzDataLakeStoreChildItem cmdlet, sedan pipeline till Update-AzDataLakeGen2Item cmdlet.|
|Test-AzDataLakeStoreItem|Få-AzDataLakeGen2Item|Cmdlet get-azDataLakeGen2Item rapporterar ett fel om objektet inte finns.|



## <a name="see-also"></a>Se även

* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Använda Azure PowerShell med Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [PowerShell-cmdlets](/powershell/module/az.storage)för lagring .

