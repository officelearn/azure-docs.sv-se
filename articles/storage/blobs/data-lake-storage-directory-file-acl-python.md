---
title: 'Azure Data Lake Storage Gen2 python SDK för filer & ACL: er (för hands version)'
description: Använd python hantera kataloger och åtkomst kontrol listor för filer och kataloger (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: cb2e1c16c1419d9925bd837bb4e12119f08d56c4
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119541"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Använd python för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2 (för hands version)

Den här artikeln visar hur du använder python för att skapa och hantera kataloger, filer och behörigheter i lagrings konton som har hierarkiskt namn område (HNS) aktiverat. 

> [!IMPORTANT]
> Azure Data Lake Storage klient biblioteket för python är för närvarande en offentlig för hands version.

[Paket (python-paket index)](https://pypi.org/project/azure-storage-file-datalake/) | [exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [ge feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Azure Data Lake Storage klient bibliotek för python med hjälp av [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Lägg till dessa import uttryck längst upp i din kod fil.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. Det enklaste sättet att hämta ett är att använda en konto nyckel. 

I det här exemplet används en konto nyckel för att skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Ersätt platshållarvärdet `storage_account_name` med namnet på ditt lagringskonto.

- Ersätt `storage_account_key` placeholder-värdet med lagrings kontots åtkomst nyckel.

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett fil system fungerar som en behållare för dina filer. Du kan skapa en genom att anropa metoden **FileSystemDataLakeServiceClient. create_file_system** .

I det här exemplet skapas ett fil system med namnet `my-file-system`.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens genom att anropa metoden **FileSystemClient. create_directory** .

I det här exemplet läggs en katalog med namnet `my-directory` till i ett fil system. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog genom att anropa metoden **DataLakeDirectoryClient. rename_directory** . Skicka sökvägen till önskad katalog en parameter. 

I det här exemplet byter namn på en under katalog till namnet `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog genom att anropa metoden **DataLakeDirectoryClient. delete_directory** .

I det här exemplet tas en katalog med namnet `my-directory`bort.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Hantera katalog behörigheter

Hämta ACL (Access Control List) för en katalog genom att anropa metoden **DataLakeDirectoryClient. get_access_control** och ange ACL genom att anropa metoden **DataLakeDirectoryClient. set_access_control** .

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Det här exemplet hämtar och anger ACL: en för en katalog med namnet `my-directory`. Strängen `rwxr-xrw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog 

Börja med att skapa en fil referens i mål katalogen genom att skapa en instans av klassen **DataLakeFileClient** . Ladda upp en fil genom att anropa metoden **DataLakeFileClient. append_data** . Se till att slutföra överföringen genom att anropa metoden **DataLakeFileClient. flush_data** .

I det här exemplet överförs en textfil till en katalog med namnet `my-directory`.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Hantera fil behörigheter

Hämta ACL (Access Control List) för en fil genom att anropa metoden **DataLakeFileClient. get_access_control** och ange ACL genom att anropa metoden **DataLakeFileClient. set_access_control** .

> [!NOTE]
> Om ditt program tillåter åtkomst genom att använda Azure Active Directory (Azure AD) måste du kontrol lera att det säkerhets objekt som programmet använder för att auktorisera åtkomst har tilldelats rollen som [lagrings-BLOB-dataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Det här exemplet hämtar och anger ACL: en för en fil med namnet `my-file.txt`. Strängen `rwxr-xrw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog 

Öppna en lokal fil för skrivning. Skapa sedan en **DataLakeFileClient** -instans som representerar den fil som du vill ladda ned. Anropa filen **DataLakeFileClient. read_file** för att läsa byte från filen och skriv sedan dessa byte till den lokala filen. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista katalog innehåll genom att anropa metoden **FileSystemClient. get_paths** och sedan räkna upp genom resultaten.

I det här exemplet skrivs sökvägen till varje under katalog och fil som finns i en katalog med namnet `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Se också

* [API-referens dokumentation](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Paket (python-paket index)](https://pypi.org/project/azure-storage-file-datalake/)
* [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-python/issues)
