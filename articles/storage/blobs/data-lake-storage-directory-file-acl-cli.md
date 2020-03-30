---
title: Använda Azure CLI för filer & ACL:er i Azure Data Lake Storage Gen2 (förhandsversion)
description: Använd Azure CLI för att hantera kataloger och ACL (File and Directory Access Control List) i lagringskonton som har ett hierarkiskt namnområde.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486142"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Använda Azure CLI för att hantera kataloger, filer och ACL:er i Azure Data Lake Storage Gen2 (förhandsversion)

Den här artikeln visar hur du använder [AZURE Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) för att skapa och hantera kataloger, filer och behörigheter i lagringskonton som har ett hierarkiskt namnområde. 

> [!IMPORTANT]
> Tillägget `storage-preview` som visas i den här artikeln är för närvarande i offentlig förhandsversion.

[Exempel gen1](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [till gen2 mappning](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | Ge[feedback](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagringskonto med hierarkiskt namnområde (HNS) aktiverat. Följ [dessa](data-lake-storage-quickstart-create-account.md) instruktioner för att skapa en.
> * Azure CLI-version `2.0.67` eller senare.

## <a name="install-the-storage-cli-extension"></a>Installera CLI-tillägget för lagring

1. Öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)eller om du har [installerat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI lokalt öppnar du ett kommandokonsolprogram som Windows PowerShell.

2. Kontrollera att den version av Azure `2.0.67` CLI som har installerat är eller högre med hjälp av följande kommando.

   ```azurecli
    az --version
   ```
   Om din version av Azure `2.0.67`CLI är lägre än installerar du en senare version. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installera tillägget `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Anslut till kontot

1. Om du använder Azure CLI lokalt kör du inloggningskommandot.

   ```azurecli
   az login
   ```

   Om CLI kan öppna din standardwebbläsare kommer den att göra det och läsa in en Azure-inloggningssida.

   Annars öppnar du en [https://aka.ms/devicelogin](https://aka.ms/devicelogin) webbläsarsida och anger auktoriseringskoden som visas i terminalen. Logga sedan in med dina kontouppgifter i webbläsaren.

   Mer information om olika autentiseringsmetoder finns i Logga in med Azure CLI.

2. Om din identitet är kopplad till mer än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på lagringskontot som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` platshållarvärdet med id:t för din prenumeration.

## <a name="create-a-file-system"></a>Skapa ett filsystem

Ett filsystem fungerar som en behållare för dina filer. Du kan skapa en `az storage container create` med kommandot. 

I det här exemplet `my-file-system`skapas ett filsystem med namnet .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalogreferens `az storage blob directory create` med kommandot. 

I det här `my-directory` exemplet läggs en `my-file-system` katalog som heter `mystorageaccount`till i ett filsystem med namnet som finns i ett konto med namnet .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Visa katalogegenskaper

Du kan skriva ut egenskaperna för en `az storage blob show` katalog till konsolen med kommandot.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn eller flytta `az storage blob directory move` en katalog med kommandot.

I det här exemplet byter `my-directory` du `my-new-directory`namn på en katalog från namnet till namnet .

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en `az storage blob directory delete` katalog med kommandot.

I det här exemplet `my-directory`tas en katalog med namnet . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Kontrollera om det finns en katalog

Ta reda på om det finns en `az storage blob directory exist` viss katalog i filsystemet med kommandot.

I det här exemplet `my-directory` visas `my-file-system` om det finns en katalog med namnet i filsystemet. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Hämta från en katalog

Hämta en fil från en `az storage blob directory download` katalog med kommandot.

I det här exemplet `upload.txt` hämtas `my-directory`en fil som heter från en katalog med namnet . 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

I det här exemplet hämtas en hel katalog.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog `az storage blob directory list` med kommandot.

I det här exemplet visas `my-directory` innehållet i `my-file-system` en katalog med namnet `mystorageaccount`som finns i filsystemet för ett lagringskonto med namnet . 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till `az storage blob directory upload` en katalog med kommandot.

I det här exemplet `upload.txt` överförs en `my-directory`fil som heter till en katalog med namnet . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Det här exemplet överför en hel katalog.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Visa filegenskaper

Du kan skriva ut egenskaperna för en `az storage blob show` fil till konsolen med kommandot.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Byta namn på eller flytta en fil

Byt namn eller flytta `az storage blob move` en fil med kommandot.

I det här exemplet byter `my-file.txt` du `my-file-renamed.txt`namn på en fil från namnet till namnet .

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Ta bort en fil

Ta bort en `az storage blob delete` fil med kommandot.

I det här exemplet tas en fil med namnet`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Hantera behörigheter

Du kan hämta, ange och uppdatera åtkomstbehörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera kommandon kontrollerar du att säkerhetsobjektet har tilldelats [rollen Lagringsblobbdataägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Hämta katalog- och filbehörigheter

Hämta åtkomstkontrollistan **directory** för en `az storage blob directory access show` katalog med kommandot.

Det här exemplet hämtar åtkomstkontrollistan för en katalog och skriver sedan ut åtkomstkontrollistan till konsolen.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Hämta åtkomstbehörigheterna **file** för en `az storage blob access show` fil med kommandot. 

Det här exemplet hämtar åtkomstkontrollistan för en fil och skriver sedan ut åtkomstkontrollistan till konsolen.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Följande bild visar utdata efter att ha hämtat åtkomstkontrollistan för en katalog.

![Hämta ACL-utgång](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

I det här exemplet har den ägande användaren läst, skrivit och kör behörigheter. Den ägande gruppen har bara läs- och körningsbehörigheter. Mer information om åtkomstkontrollistor finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ange katalog- och filbehörigheter

Använd `az storage blob directory access set` kommandot för att ange åtkomstkontrollistan för en **katalog**. 

I det här exemplet anges åtkomstkontrollistan i en katalog för den ägande användaren, ägargruppen eller andra användare och sedan ACL:en till konsolen.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

I det *default* här exemplet anges standard-åtkomstkontrollistan på en katalog för den ägande användaren, ägargruppen eller andra användare och sedan ACL:en till konsolen.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Använd `az storage blob access set` kommandot för att ställa in acl för en **fil**. 

I det här exemplet anges åtkomstkontrollistan för en fil för den ägande användaren, ägargruppen eller andra användare och sedan ACL:en till konsolen.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Följande bild visar utdata när du har ställt in åtkomstkontrollistan för en fil.

![Hämta ACL-utgång](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

I det här exemplet har den ägande användaren och den ägande gruppen bara läs- och skrivbehörighet. Alla andra användare har skriv- och körningsbehörighet. Mer information om åtkomstkontrollistor finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Uppdatera katalog- och filbehörigheter

Ett annat sätt att ange `az storage blob directory access update` den `az storage blob access update` här behörigheten är att använda kommandot eller. 

Uppdatera åtkomstkontrollistan för en katalog `-permissions` eller fil genom att ange parametern till den korta formen av en åtkomstkontrollista.

I det här exemplet uppdateras åtkomstkontrollistan för en **katalog**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

I det här exemplet uppdateras åtkomstkontrollistan för en **fil**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Du kan också uppdatera den ägande användaren och gruppen `--owner` `group` för en katalog eller fil genom att ange parametrarna eller parametrarna på entitets-ID eller UPN (User Principal Name) för en användare. 

I det här exemplet ändras ägaren till en katalog. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

I det här exemplet ändras ägaren till en fil. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Hantera användardefinierade metadata

Du kan lägga till användardefinierade metadata i `az storage blob directory metadata update` en fil eller katalog med hjälp av kommandot med ett eller flera namnvärdespar.

I det här exemplet läggs användardefinierade metadata till för en katalog med namnet `my-directory` katalog.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

I det här exemplet visas alla `my-directory`användardefinierade metadata för katalogen .

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Se även

* [Prov](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Gen1 till Gen2 mappning](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Ge feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Källkod](https://github.com/Azure/azure-cli-extensions/tree/master/src)

