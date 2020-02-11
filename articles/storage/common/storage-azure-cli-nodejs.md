---
title: Använda den klassiska Azure-CLI med Azure Storage | Microsoft Docs
description: Lär dig hur du använder det klassiska kommando rads gränssnittet i Azure (CLI) med Azure Storage för att skapa och hantera lagrings konton och arbeta med Azure-blobbar och filer.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 6554385a879b054153dcb808c3dff4b60c136458
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120854"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Använda den klassiska Azure-CLI med Azure Storage

## <a name="overview"></a>Översikt

Den klassiska CLI-funktionen i Azure innehåller en uppsättning kommandon med öppen källkod och plattforms oberoende funktioner för att arbeta med Azure-plattformen. Det ger en stor del av samma funktioner som finns i [Azure Portal](https://portal.azure.com) och omfattande funktioner för data åtkomst.

I den här hand boken får vi lära dig hur du använder [Azure klassisk CLI](../../cli-install-nodejs.md) för att utföra en mängd olika utvecklings-och administrations uppgifter med Azure Storage. Vi rekommenderar att du hämtar och installerar eller uppgraderar till den senaste klassiska CLI: en innan du använder den här guiden.

Den här guiden förutsätter att du förstår de grundläggande begreppen i Azure Storage. Guiden innehåller ett antal skript som demonstrerar användningen av klassisk CLI med Azure Storage. Se till att uppdatera skript variablerna baserat på konfigurationen innan du kör varje skript.

> [!NOTE]
> Guiden innehåller det klassiska CLI-kommandot i Azure och skript exempel för klassiska lagrings konton. Se [använda Azure CLI för Mac, Linux och Windows med Azures resurs hantering](../../virtual-machines/azure-cli-arm-commands.md#storage-objects) för klassiska CLI-kommandon i Resource Manager för Resource Manager-lagringsenheter.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Kom igång med Azure Storage och den klassiska Azure-CLI: en i 5 minuter
I den här guiden används Ubuntu till exempel, men andra OS-plattformar bör utföras på samma sätt.

**Nytt till Azure:** Hämta en Microsoft Azure prenumeration och ett Microsoft-konto som är kopplat till den prenumerationen. Information om köp alternativ för Azure finns i [kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/), [köp alternativ](https://azure.microsoft.com/pricing/purchase-options/)och [medlems erbjudanden](https://azure.microsoft.com/pricing/member-offers/) (för medlemmar i MSDN, Microsoft Partner Network och BizSpark och andra Microsoft-program).

Mer information om Azure-prenumerationer finns [i tilldela administratörs roller i Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) .

**När du har skapat en Microsoft Azure-prenumeration och ett konto:**

1. Hämta och installera den klassiska Azure-CLI: en genom att följa instruktionerna i [installera den klassiska Azure-CLI: en](../../cli-install-nodejs.md).
2. När den klassiska CLI har installerats kan du använda Azure-kommandot från ditt kommando rads gränssnitt (bash, Terminal, kommando tolk) för att få åtkomst till de klassiska CLI-kommandona. Skriv _Azure_ -kommandot och se följande utdata.

    ![Utdata från Azure-kommando](./media/storage-azure-cli/azure_command.png)   
3. I kommando rads gränssnittet skriver du `azure storage` för att lista ut alla Azure Storage-kommandon och få en första bild av funktionerna i den klassiska CLI-funktionen. Du kan skriva kommando namn med parametern **-h** (till exempel `azure storage share create -h`) för att se information om kommandosyntaxen.
4. Nu ger vi dig ett enkelt skript som visar grundläggande klassiska CLI-kommandon för att få åtkomst till Azure Storage. Skriptet uppmanar först dig att ange två variabler för ditt lagrings konto och din nyckel. Sedan skapar skriptet en ny behållare i det nya lagrings kontot och laddar upp en befintlig avbildnings fil (BLOB) till behållaren. När skriptet visar alla blobar i behållaren hämtas avbildnings filen till mål katalogen som finns på den lokala datorn.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. På den lokala datorn öppnar du din önskade text redigerare (t. ex. vim). Skriv skriptet ovan i text redigeraren.
6. Nu måste du uppdatera skript-variablerna baserat på dina konfigurations inställningar.

   * **< storage_account_name >** Använd det aktuella namnet i skriptet eller ange ett nytt namn för ditt lagrings konto. **Viktigt:** Namnet på lagrings kontot måste vara unikt i Azure. Det måste vara gemener, för!
   * **< storage_account_key >** Åtkomst nyckeln för ditt lagrings konto.
   * **< container_name >** Använd det namn som anges i skriptet eller ange ett nytt namn för din behållare.
   * **< image_to_upload >** Ange en sökväg till en bild på din lokala dator, till exempel: "~/images/HelloWorld.png".
   * **< destination_folder >** Ange en sökväg till en lokal katalog för att lagra filer som hämtats från Azure Storage, till exempel: "~/downloadImages".
7. När du har uppdaterat de nödvändiga variablerna i vim trycker du på tangentkombinationer `ESC``:``wq!` för att spara skriptet.
8. Om du vill köra skriptet skriver du bara skript filens namn i bash-konsolen. När skriptet har körts bör du ha en lokal målmapp som innehåller den hämtade avbildnings filen. Följande skärm bild visar ett exempel på utdata:

När skriptet har körts bör du ha en lokal målmapp som innehåller den hämtade avbildnings filen.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Hantera lagrings konton med Azures klassiska CLI
### <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration
De flesta av lagrings kommandona fungerar utan en Azure-prenumeration, men vi rekommenderar att du ansluter till din prenumeration från den klassiska CLI: en.

### <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto
Om du vill använda Azure Storage behöver du ett lagrings konto. Du kan skapa ett nytt Azure Storage-konto när du har konfigurerat datorn för att ansluta till din prenumeration.

```azurecli
azure storage account create <account_name>
```

Namnet på ditt lagrings konto måste vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Ange ett standard konto för Azure Storage i miljövariabler
Du kan ha flera lagrings konton i din prenumeration. Du kan välja en av dem och ange den i miljövariablerna för alla lagrings kommandon i samma session. På så sätt kan du köra klassiska CLI-kommandon utan att ange lagrings kontot och nyckeln explicit.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Ett annat sätt att ange ett standard lagrings konto är att använda anslutnings strängen. Hämta först anslutnings strängen med kommandot:

```azurecli
azure storage account connectionstring show <account_name>
```

Kopiera sedan anslutnings strängen för utdata och ange den som miljö variabel:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Skapa och hantera blobar
Azure Blob Storage är en tjänst för att lagra stora mängder ostrukturerade data, till exempel text eller binära data, som kan nås från var som helst i världen via HTTP eller HTTPS. Det här avsnittet förutsätter att du redan är bekant med Azure Blob Storage-koncepten. Detaljerad information finns i [Kom igång med Azure Blob Storage med hjälp av .net](../blobs/storage-dotnet-how-to-use-blobs.md) och [BLOB service-koncept](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Skapa en container
Varje BLOB i Azure Storage måste finnas i en behållare. Du kan skapa en privat behållare med hjälp av kommandot `azure storage container create`:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Det finns tre nivåer av anonym Läs åtkomst: **av**, **BLOB**och **container**. Om du vill förhindra anonym åtkomst till blobbar ställer du in behörighets parametern på **av**. Som standard är den nya behållaren privat och kan endast användas av konto ägaren. Om du vill tillåta anonym offentlig Läs behörighet till BLOB-resurser, men inte till behållar metadata eller till listan över blobbar i behållaren, ställer du in behörighets parametern på **BLOB**. Om du vill tillåta fullständig offentlig Läs behörighet till BLOB-resurser, container-metadata och listan över blobbar i behållaren anger du behörighets parametern till **container**. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container
Azure Blob Storage stöder blockblobbar och sidblobbar. Mer information finns i [förstå block-blobbar, bifoga blobbar och sid-blobar](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Du kan använda `azure storage blob upload`för att överföra blobbar i till en behållare. Som standard överför det här kommandot de lokala filerna till en Block-Blob. Du kan använda parametern `--blobtype` för att ange typen för blobben.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Ladda ned blobbar från en behållare
Följande exempel visar hur du laddar ned blobbar från en behållare.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopiera blobbar
Du kan kopiera blobar i eller mellan lagringskonton och regioner asynkront.

Följande exempel visar hur du kopierar blobar från ett lagringskonto till ett annat. I det här exemplet skapar vi en behållare där blobbar är offentligt, anonymt tillgängliga.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Det här exemplet utför en asynkron kopia. Du kan övervaka status för varje kopierings åtgärd genom att köra åtgärden `azure storage blob copy show`.

Observera att käll-URL: en som angetts för kopierings åtgärden måste antingen vara offentligt tillgänglig eller innehålla en SAS-token (signatur för delad åtkomst).

### <a name="delete-a-blob"></a>Ta bort en blob
Om du vill ta bort en BLOB använder du följande kommando:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Skapa och hantera fil resurser
Azure Files erbjuder delad lagring för program som använder standard-SMB-protokollet. Microsoft Azure virtuella datorer och moln tjänster, samt lokala program, kan dela fildata via monterade resurser. Du kan hantera fil resurser och fildata via den klassiska CLI-filen. Mer information om Azure Files finns i [Introduktion till Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Skapa en filresurs
En Azure-filresurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en fil resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagrings kontots kapacitets gränser. I följande exempel skapas en fil resurs med namnet **Unshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Skapa en katalog
En katalog innehåller en valfri hierarkisk struktur för en Azure-filresurs. I följande exempel skapas en katalog med namnet **myDir** i fil resursen.

```azurecli
azure storage directory create myshare myDir
```

Observera att katalog Sök vägen kan innehålla flera nivåer, *t. ex.* **a/b**. Du måste dock se till att alla överordnade kataloger finns. För sökväg **a/b**måste du till exempel skapa **en** katalog först och sedan skapa katalog **b**.

### <a name="upload-a-local-file-to-directory"></a>Ladda upp en lokal fil till katalogen
I följande exempel överförs en fil från **~/Temp/samplefile.txt** till katalogen **myDir** . Redigera fil Sök vägen så att den pekar på en giltig fil på den lokala datorn:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Observera att en fil i resursen kan vara upp till 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Lista filerna i resurs roten eller katalogen
Du kan visa en lista över filer och under kataloger i en resurs rot eller en katalog med hjälp av följande kommando:

```azurecli
azure storage file list myshare myDir
```

Observera att katalog namnet är valfritt för List åtgärden. Om detta utelämnas visar kommandot innehållet i resursens rot Katalog.

### <a name="copy-files"></a>Kopiera filer
Från och med version 0.9.8 av klassisk CLI kan du kopiera en fil till en annan fil, en fil till en BLOB eller en blob till en fil. Nedan demonstrerar vi hur du utför dessa kopierings åtgärder med CLI-kommandon. Så här kopierar du en fil till den nya katalogen:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Så här kopierar du en blob till en fil katalog:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Nästa steg

Du hittar den klassiska kommando referensen för Azure CLI för att arbeta med lagrings resurser här:

* [Azures klassiska CLI-kommandon i Resource Manager-läge](../../virtual-machines/azure-cli-arm-commands.md#storage-objects)
* [Azures klassiska CLI-kommandon i Azure Service Management-läge](../../cli-install-nodejs.md)

Du kanske också vill testa den senaste versionen av [Azure CLI](../storage-azure-cli.md)för användning med distributions modellen Resource Manager.
