---
title: Azure Data Lake Storage Gen2 Python SDK för filer & ACL:er
description: Använd Python hantera kataloger och register över fil- och katalogåtkomstkontroll (ACL) i lagringskonton som har aktiverat hierarkiskt namnområde (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b43bfe5979b8232f1fee2f5c1c6873c9c62695a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061535"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använda Python för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Python för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har aktiverat hierarkiskt namnområde (HNS). 

[Paket (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Prover](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | Ge[feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Azure Data Lake Storage-klientbiblioteket för Python med hjälp av [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Lägg till dessa importsatser överst i kodfilen.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodavsnitten i den här artikeln måste du skapa en **DataLakeServiceClient-instans** som representerar lagringskontot. 

### <a name="connect-by-using-an-account-key"></a>Ansluta med hjälp av en kontonyckel

Det här är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient-instans** med hjälp av en kontonyckel.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Ersätt platshållarvärdet `storage_account_name` med namnet på ditt lagringskonto.

- Ersätt `storage_account_key` platshållarvärdet med åtkomstnyckeln för lagringskontot.

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med Hjälp av Azure Active Directory (AD)

Du kan använda [Azure identity-klientbiblioteket för Python för](https://pypi.org/project/azure-identity/) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient-instans** med hjälp av ett klient-ID, en klienthemlighet och ett klient-ID.  Information om hur du hämtar dessa värden finns [i Hämta en token från Azure AD för att godkänna begäranden från ett klientprogram](../common/storage-auth-aad-app.md).

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
> Fler exempel finns i [Azure identity-klientbiblioteket för Python-dokumentation.](https://pypi.org/project/azure-identity/)

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett filsystem fungerar som en behållare för dina filer. Du kan skapa en genom att anropa metoden **FileSystemDataLakeServiceClient.create_file_system.**

I det här exemplet `my-file-system`skapas ett filsystem med namnet .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalogreferens genom att anropa metoden **FileSystemClient.create_directory.**

I det här `my-directory` exemplet läggs en katalog som heter till i ett filsystem. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn eller flytta en katalog genom att anropa **metoden DataLakeDirectoryClient.rename_directory.** Skicka sökvägen till den önskade katalogen en parameter. 

I det här exemplet byter du `my-subdirectory-renamed`namn på en underkatalog till namnet .

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

Ta bort en katalog genom att anropa metoden **DataLakeDirectoryClient.delete_directory.**

I det här exemplet `my-directory`tas en katalog med namnet .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Hantera katalogbehörigheter

Hämta åtkomstkontrollistan (ACL) för en katalog genom att anropa **metoden DataLakeDirectoryClient.get_access_control** och ange ACL genom att anropa metoden **DataLakeDirectoryClient.set_access_control.**

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Det här exemplet hämtar och anger `my-directory`åtkomstkontrollistan för en katalog med namnet . Strängen `rwxr-xrw-` ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läs- och skrivbehörighet.

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

Skapa först en filreferens i målkatalogen genom att skapa en instans av klassen **DataLakeFileClient.** Ladda upp en fil genom att anropa metoden **DataLakeFileClient.append_data.** Se till att slutföra överföringen genom att anropa **Metoden DataLakeFileClient.flush_data.**

I det här exemplet överförs en `my-directory`textfil till en katalog med namnet .   

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

> [!TIP]
> Om filstorleken är stor måste koden ringa flera samtal till metoden **DataLakeFileClient.append_data.** Överväg att använda metoden **DataLakeFileClient.upload_data** i stället. På så sätt kan du ladda upp hela filen i ett enda samtal. 

## <a name="upload-a-large-file-to-a-directory"></a>Ladda upp en stor fil till en katalog

Använd **metoden DataLakeFileClient.upload_data** för att ladda upp stora filer utan att behöva ringa flera anrop till metoden **DataLakeFileClient.append_data.**

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Hantera filbehörigheter

Hämta åtkomstkontrollistan (ACL) för en fil genom att anropa metoden **DataLakeFileClient.get_access_control** och ange ACL genom att anropa metoden **DataLakeFileClient.set_access_control.**

> [!NOTE]
> Om ditt program godkänner åtkomst med hjälp av Azure Active Directory (Azure AD) kontrollerar du att säkerhetsobjektet som programmet använder för att auktorisera åtkomst har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Det här exemplet hämtar och anger `my-file.txt`åtkomstkontrollistan för en fil med namnet . Strängen `rwxr-xrw-` ger den givande användaren läs-, skriv- och körningsbehörigheter, ger den ägande gruppen endast läs- och körningsbehörighet och ger alla andra läs- och skrivbehörighet.

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

## <a name="download-from-a-directory"></a>Hämta från en katalog 

Öppna en lokal fil för skrivning. Skapa sedan en **DataLakeFileClient-instans** som representerar den fil som du vill hämta. Anropa **DataLakeFileClient.read_file** för att läsa byte från filen och skriv sedan dessa byte till den lokala filen. 

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

Ange kataloginnehåll genom att anropa metoden **FileSystemClient.get_paths** och räkna sedan upp resultatet.

I det här exemplet skrivs sökvägen till varje underkatalog `my-directory`och fil som finns i en katalog med namnet .

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

## <a name="see-also"></a>Se även

* [Referensdokumentation för API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Paket (Python-paketindex)](https://pypi.org/project/azure-storage-file-datalake/)
* [Prover](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 till Gen2 mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Ge feedback](https://github.com/Azure/azure-sdk-for-python/issues)
