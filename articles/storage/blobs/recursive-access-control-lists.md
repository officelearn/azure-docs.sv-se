---
title: 'Ange ACL: er rekursivt för Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Du kan lägga till, uppdatera och ta bort åtkomst kontrol listor rekursivt för befintliga underordnade objekt i en överordnad katalog.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 53ff86f7dc5dfd6b7b60f99848c48f4e44f5a8a1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948852"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Ange åtkomst kontrol listor (ACL) rekursivt för Azure Data Lake Storage Gen2

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Nu kan du lägga till, uppdatera och ta bort ACL rekursivt för befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt.

> [!NOTE]
> Möjligheten att ange åtkomst listor rekursivt finns i offentlig för hands version och är tillgänglig i alla regioner.  

[Bibliotek](#libraries)  |  [Exempel](#code-samples)  |  [Metod tips](#best-practice-guidelines)  |  [Ge feedback](#provide-feedback)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.

- Rätt behörighet för att köra den rekursiva ACL-processen. Rätt behörighet inkluderar något av följande: 

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.   

  - Ägande användare av den mål behållare eller katalog till vilken du planerar att tillämpa den rekursiva ACL-processen. Detta inkluderar alla underordnade objekt i mål behållaren eller katalogen. 

- En förståelse för hur ACL: er tillämpas på kataloger och filer. Se [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Se avsnittet **Konfigurera ditt projekt** i den här artikeln för att Visa installations vägledning för PowerShell, .NET SDK och python SDK.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera de nödvändiga biblioteken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Se till att .NET Framework är installerat. Se [Ladda ned .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Kontrol lera att PowerShell-versionen som har installerats är `5.1` eller högre genom att använda följande kommando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Information om hur du uppgraderar din version av PowerShell finns i [uppgradera befintliga Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
3. Installera den senaste versionen av PowershellGet-modulen.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Stäng och öppna sedan PowerShell-konsolen igen.

5. Installera **AZ. Storage** Preview module.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

### <a name="net"></a>[.NET](#tab/dotnet)

1. Öppna ett kommando fönster (till exempel: Windows PowerShell).

2. Från projekt katalogen ska du installera paketet Azure. Storage. files. DataLake. för hands version med hjälp av `dotnet add package` kommandot.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.3.0-dev.20200811.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Lägg till dessa med-instruktioner överst i din kod fil.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Ladda ned [Azure Data Lake Storage klient biblioteket för python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Installera biblioteket som du laddade ned med hjälp av [pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Lägg till dessa import uttryck längst upp i din kod fil.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Anslut till ditt konto

Du kan ansluta genom att använda Azure Active Directory (AD) eller genom att använda en konto nyckel. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Öppna ett Windows PowerShell-kommando fönster och logga sedan in på Azure-prenumerationen med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill skapa och hantera kataloger i. I det här exemplet ersätter du `<subscription-id>` plats hållarens värde med ID: t för din prenumeration.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Välj sedan hur du vill att dina kommandon ska få behörighet till lagrings kontot. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Alternativ 1: få behörighet genom att använda Azure Active Directory (AD)

Med den här metoden säkerställer systemet att ditt användar konto har lämpliga RBAC-tilldelningar (rollbaserad åtkomst kontroll) och ACL-behörigheter. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

Följande tabell visar var och en av de roller som stöds och deras inställnings funktion för ACL.

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage BLOB data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Alternativ 2: få behörighet genom att använda lagrings konto nyckeln

Med den här metoden kontrollerar systemet inte RBAC-eller ACL-behörigheter.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

Om du vill använda kodfragmenten i den här artikeln måste du skapa en [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans som representerar lagrings kontot.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

Du kan använda [Azure Identity client-biblioteket för .net för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) att autentisera ditt program med Azure AD.

När du har installerat paketet lägger du till denna using-instruktion överst i din kod fil.

```csharp
using Azure.Identity;
```

Hämta ett klient-ID, en klient hemlighet och ett klient-ID. Information om hur du gör detta finns i [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den här processen måste du tilldela en av följande roller för [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/overview.md) till säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage BLOB data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

I det här exemplet skapas en [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Den här metoden är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -instans med hjälp av en konto nyckel.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Fler exempel finns i dokumentationen [för Azure Identity Client library för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="python"></a>[Python](#tab/python)

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

Du kan använda [klient biblioteket för Azure Identity för python för](https://pypi.org/project/azure-identity/) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den här processen måste du tilldela en av följande roller för [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/overview.md) till säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage BLOB data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity för python](https://pypi.org/project/azure-identity/) -dokumentation.

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Ersätt platshållarvärdet `storage_account_name` med namnet på ditt lagringskonto.

- Ersätt `storage_account_key` placeholder-värdet med din åtkomst nyckel för lagrings kontot.

---

## <a name="set-an-acl-recursively"></a>Ange en ACL rekursivt

Du kan ange ACL: erna rekursivt.  

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ange en ACL rekursivt med hjälp av `Set-AzDataLakeGen2AclRecursive` cmdleten.

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Ange en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItems](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post.

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Ange en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. set_access_control_recursive** .

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet.

```python
def set_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Uppdatera en ACL rekursivt

Du kan uppdatera en befintlig ACL rekursivt.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Uppdatera en ACL rekursivt med hjälp av cmdleten  **Update-AzDataLakeGen2AclRecursive** . 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

### <a name="net"></a>[.NET](#tab/dotnet)

Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** . 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. 

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. update_access_control_recursive** . 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. 

```python
def update_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Du kan ta bort en eller flera ACL-poster rekursivt.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ta bort ACL-poster med hjälp av cmdleten **Remove-AzDataLakeGen2AclRecursive** . 

Det här exemplet tar bort en ACL-post från rot katalogen för behållaren.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

### <a name="net"></a>[.NET](#tab/dotnet)

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . 

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . 

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient. remove_access_control_recursive** . 

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . 

```python
def remove_permission_recursively():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel. Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Returnera resultat till variabeln. Pipe kunde inte skriva poster till en formaterad tabell.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Baserat på utdata från tabellen kan du åtgärda eventuella behörighets fel och sedan återuppta körningen med hjälp av en fortsättnings-token.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde ``null`` för parametern för en tilläggs-token. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, null, continuationToken: continuationToken);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde ``None`` för parametern för en tilläggs-token. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Resurser

Det här avsnittet innehåller länkar till bibliotek och kod exempel.

#### <a name="libraries"></a>Bibliotek

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Kodexempel

- PowerShell: [Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [exempel](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) på README

- NET: [README](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)-  |  [exempel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [README](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)-  |  [exempel](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Rikt linjer för bästa praxis

Det här avsnittet innehåller rikt linjer för bästa praxis för att ställa in ACL: er rekursivt. 

#### <a name="handling-runtime-errors"></a>Hantera körnings fel

Ett körnings fel kan uppstå i många olika orsaker (till exempel: ett avbrott eller problem med klient anslutning). Om du stöter på ett körnings fel startar du om den rekursiva ACL-processen. ACL: er kan återanvändas på objekt utan att orsaka negativ påverkan. 

#### <a name="handling-permission-errors-403"></a>Hantering av behörighets fel (403)

Om du stöter på ett åtkomst kontroll undantag när du kör en rekursiv ACL-process kanske ditt [säkerhets objekt](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) i AD inte har tillräcklig behörighet för att tillämpa en ACL på ett eller flera av de underordnade objekten i mapphierarkin. När ett behörighets fel inträffar stoppas processen och en fortsättnings-token anges. Åtgärda behörighets problemet och Använd sedan den fortsättnings-token för att bearbeta den återstående data mängden. De kataloger och filer som redan har bearbetats behöver inte bearbetas igen. Du kan också välja att starta om den rekursiva ACL-processen. ACL: er kan återanvändas på objekt utan att orsaka negativ påverkan. 

#### <a name="credentials"></a>Autentiseringsuppgifter 

Vi rekommenderar att du etablerar ett säkerhets objekt för Azure AD som har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för mål lagrings kontot eller behållaren. 

#### <a name="performance"></a>Prestanda 

För att minska svars tiden rekommenderar vi att du kör den rekursiva ACL-processen på en virtuell Azure-dator (VM) som finns i samma region som ditt lagrings konto. 

#### <a name="acl-limits"></a>ACL-gränser

Det maximala antalet ACL: er som du kan tillämpa på en katalog eller fil är 32 åtkomst-ACL: er och 32 standard-ACL: er. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Ge feedback eller rapportera problem

Du kan ge din feedback eller rapportera ett problem genom att använda någon av dessa sidor: [PowerShell](https://github.com/Azure/azure-powershell/issues/new?assignees=&labels=triage&template=az-module-bug-report.md&title=), [.net](https://github.com/Azure/azure-sdk-for-net/issues/new?assignees=&labels=&template=bug_report.md&title=), [python](https://github.com/Azure/azure-sdk-for-python/issues/new?assignees=&labels=&template=bug_report.md&title=)

## <a name="see-also"></a>Se även

- [Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Kända problem](data-lake-storage-known-issues.md)


