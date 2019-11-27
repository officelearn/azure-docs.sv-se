---
title: 'Använda Azure CLI för filer & ACL: er i Azure Data Lake Storage Gen2 (förhands granskning)'
description: Använd Azure CLI för att hantera kataloger och åtkomst kontrol listor för kataloger och kataloger (ACL) i lagrings konton som har ett hierarkiskt namn område.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: dcd75cfefd53b3c9104052146607869515e1c86e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534285"
---
# <a name="use-azure-cli-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Använda Azure CLI för filer & ACL: er i Azure Data Lake Storage Gen2 (förhands granskning)

Den här artikeln visar hur du använder [Azures kommando rads gränssnitt (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) för att skapa och hantera kataloger, filer och behörigheter i lagrings konton som har ett hierarkiskt namn område. 

> [!IMPORTANT]
> `storage-preview` tillägget som finns i den här artikeln är för närvarande en offentlig för hands version.

[Exempel](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) [på | gen1 till Gen2-mappning](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [ge feedback](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.
> * Azure CLI-version `2.0.67` eller högre.

## <a name="install-the-storage-cli-extension"></a>Installera Storage CLI-tillägget

1. Öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), eller om du har [installerat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Kontrol lera att den version av Azure CLI som är installerad `2.0.67` eller högre med hjälp av följande kommando.

   ```azurecli
    az --version
   ```
   Om din version av Azure CLI är lägre än `2.0.67`kan du installera en senare version. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installera `storage-preview`-tillägget.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Anslut till kontot

1. Om du använder Azure CLI lokalt kan du köra inloggnings kommandot.

   ```azurecli
   az login
   ```

   Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

   Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal. Logga sedan in med dina konto uppgifter i webbläsaren.

   Mer information om olika autentiseringsmetoder finns i Logga in med Azure CLI.

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett fil system fungerar som en behållare för dina filer. Du kan skapa ett med hjälp av kommandot `az storage container create`. 

I det här exemplet skapas ett fil system med namnet `my-file-system`.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens med hjälp av kommandot `az storage blob directory create`. 

Det här exemplet lägger till en katalog med namnet `my-directory` till ett fil system med namnet `my-file-system` som finns i ett konto med namnet `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Visa katalog egenskaper

Du kan skriva ut egenskaperna för en katalog till-konsolen med hjälp av kommandot `az storage blob show`.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog med hjälp av kommandot `az storage blob directory move`.

I det här exemplet byter namn på en katalog från namnet `my-directory` till namnet `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog med hjälp av kommandot `az storage blob directory delete`.

I det här exemplet tas en katalog med namnet `my-directory`bort. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Kontrol lera om en katalog finns

Ta reda på om en katalog finns i fil systemet med hjälp av kommandot `az storage blob directory exist`.

Det här exemplet visar om en katalog med namnet `my-directory` finns i `my-file-system` fil systemet. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Hämta en fil från en katalog med hjälp av kommandot `az storage blob directory download`.

I det här exemplet hämtas en fil med namnet `upload.txt` från en katalog med namnet `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

I det här exemplet hämtas en hel katalog.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog med hjälp av kommandot `az storage blob directory list`.

I det här exemplet visas innehållet i en katalog med namnet `my-directory` som finns i fil systemet `my-file-system` för ett lagrings konto med namnet `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till en katalog med hjälp av kommandot `az storage blob directory upload`.

I det här exemplet överförs en fil med namnet `upload.txt` till en katalog med namnet `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

I det här exemplet överförs en hel katalog.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Visa fil egenskaper

Du kan skriva ut egenskaperna för en fil till-konsolen med hjälp av kommandot `az storage blob show`.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Byta namn på eller flytta en fil

Byt namn på eller flytta en fil med hjälp av kommandot `az storage blob move`.

I det här exemplet byter namn på en fil från namnet `my-file.txt` till namnet `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Ta bort en fil

Ta bort en fil med hjälp av kommandot `az storage blob delete`.

Det här exemplet tar bort en fil med namnet `my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Hantera behörigheter

Du kan hämta, ange och uppdatera åtkomst behörigheter för kataloger och filer.

### <a name="get-directory-and-file-permissions"></a>Hämta katalog-och fil behörigheter

Hämta ACL för en **katalog** med hjälp av kommandot `az storage blob directory access show`.

Det här exemplet hämtar ACL: en för en katalog och skriver sedan ut ACL: en till-konsolen.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Hämta åtkomst behörigheterna för en **fil** med hjälp av kommandot `az storage blob access show`. 

Det här exemplet hämtar ACL: en för en fil och skriver sedan ut ACL: en till-konsolen.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Följande bild visar utdata när du har hämtat ACL för en katalog.

![Hämta ACL-utdata](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

I det här exemplet har ägande användaren Läs-, skriv-och körnings behörighet. Den ägande gruppen har bara Läs-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ange katalog-och fil behörigheter

Använd `az storage blob directory access set`-kommandot för att ange ACL för en **katalog**. 

I det här exemplet anges ACL: en för en katalog för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Använd `az storage blob access set`-kommandot för att ange ACL för en **fil**. 

I det här exemplet anges ACL: en för en fil för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Följande bild visar utdata när du har angett ACL för en fil.

![Hämta ACL-utdata](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

I det här exemplet har ägande användare och ägande grupp bara Läs-och Skriv behörighet. Alla andra användare har Skriv-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Uppdatera katalog-och fil behörigheter

Ett annat sätt att ange den här behörigheten är att använda kommandot `az storage blob directory access update` eller `az storage blob access update`. 

Uppdatera ACL: en för en katalog eller fil genom att ange parametern `-permissions` till kort form för en ACL.

I det här exemplet uppdateras ACL: en för en **katalog**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

I det här exemplet uppdateras ACL: en för en **fil**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Du kan också uppdatera den ägande användaren och gruppen av en katalog eller fil genom att ange `--owner`-eller `group` parametrar till enhets-ID eller användarens huvud namn (UPN) för en användare. 

I det här exemplet ändras ägaren till en katalog. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Det här exemplet ändrar ägaren till en fil. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Hantera användardefinierade metadata

Du kan lägga till användardefinierade metadata till en fil eller katalog med hjälp av kommandot `az storage blob directory metadata update` med ett eller flera namn-värde-par.

Det här exemplet lägger till användardefinierade metadata för en katalog med namnet `my-directory` Directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

I det här exemplet visas alla användardefinierade metadata för katalogen med namnet `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Se också

* [Exempel](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 till Gen2-mappning](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Ge feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Källkod](https://github.com/Azure/azure-cli-extensions/tree/master/src)

