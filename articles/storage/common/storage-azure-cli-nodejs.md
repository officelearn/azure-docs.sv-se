---
title: Använda den klassiska Azure CLI med Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azure klassiska kommandoradsgränssnittet (CLI) med Azure Storage för att skapa och hantera storage-konton och arbeta med Azure-blobar och filer.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 28c9e342aec3eb3ba61b46f4f80c7d097b0653b6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258722"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Använda den klassiska Azure CLI med Azure Storage

## <a name="overview"></a>Översikt

Den klassiska Azure CLI innehåller en uppsättning med öppen källkod plattformsoberoende kommandon för att arbeta med Azure-plattformen. Det ger stor del samma funktioner som finns i den [Azure-portalen](https://portal.azure.com) samt som omfattande funktioner för dataåtkomst.

I den här guiden kommer vi att utforska hur du använder [Azure klassiskt CLI](../../cli-install-nodejs.md) att utföra olika utvecklings- och uppgifter med Azure Storage. Vi rekommenderar att du ladda ned och installera eller uppgradera till den senaste versionen av klassiska CLI innan du använder den här guiden.

Den här guiden förutsätter att du förstår de grundläggande principerna för Azure Storage. Handboken innehåller ett antal skript som demonstrerar användningen av klassiska CLI med Azure Storage. Glöm inte att uppdatera skriptvariabler utifrån din konfiguration innan du kör varje skript.

> [!NOTE]
> Guiden visar de Azure klassiska CLI kommando- och exempel för klassiska lagringskonton. Se [med Azure CLI för Mac, Linux och Windows med Azure Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) för Azure klassiska CLI-kommandon för Resource Manager-lagringskonton.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Kom igång med Azure Storage och Azure klassiskt CLI på 5 minuter
Den här guiden använder Ubuntu exempel, men andra OS-plattformar som ska utföras på samma sätt.

**Nybörjare på Azure:** får en Microsoft Azure-prenumeration och ett Microsoft-konto som är associerade med den aktuella prenumerationen. Information om köpalternativ för Azure finns i [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/), [Inköpsalternativ](https://azure.microsoft.com/pricing/purchase-options/), och [Medlemserbjudanden](https://azure.microsoft.com/pricing/member-offers/) (för medlemmar i MSDN, Microsoft Partner Network och BizSpark och andra Microsoft-program).

Se [Tilldela administratörsroller i Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) för mer information om Azure-prenumerationer.

**När du har skapat en Microsoft Azure-prenumeration och ett konto:**

1. Ladda ned och installera Azure klassiska CLI följa anvisningarna som beskrivs i [installera Azure CLI för klassiska](../../cli-install-nodejs.md).
2. När klassiskt CLI har installerats, kommer du att kunna använda kommandot azure från ditt kommandoradsgränssnitt (Bash, Terminal, Kommandotolken) att komma åt de klassiska CLI-kommandona. Skriv den _azure_ kommandot och du bör se följande utdata.

    ![Azure kommandoutdata](./media/storage-azure-cli/azure_command.png)   
3. Skriv i kommandoradsgränssnittet, `azure storage` lista med alla azure storage-kommandon och få en första intryck av funktionerna klassiskt CLI tillhandahåller. Du kan skriva kommandonamn med **-h** parameter (till exempel `azure storage share create -h`) att visa information om kommandosyntax.
4. Nu måste vi ge ett enkelt skript som visar grundläggande klassiska CLI-kommandon för att få åtkomst till Azure Storage. Skriptet först ber dig att ställa in två variabler för ditt lagringskonto och nyckel. Sedan skriptet skapar en ny behållare i den här nya storage-konto och ladda upp en befintlig fil (blob) till behållaren. När skriptet visar en lista över alla blobar i behållaren, hämtas image-filen till målkatalogen som finns på den lokala datorn.

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

5. Öppna din önskade textredigerare (vim till exempel) i din lokala dator. Ange skriptet ovan till en textredigerare.
6. Nu kan behöva du uppdatera skriptvariabler baserat på dina inställningar.

   * **< Storage_account_name >** använder det angivna namnet i skriptet eller ange ett nytt namn för ditt lagringskonto. **Viktigt:** namnet på lagringskontot måste vara unikt i Azure. Det måste vara gemener, för!
   * **< Storage_account_key >** åtkomstnyckeln för ditt lagringskonto.
   * **< Container_name >** använder det angivna namnet i skriptet eller ange ett nytt namn för din behållare.
   * **< Image_to_upload >** ange en sökväg till en bild på din lokala dator, till exempel ”: ~ / images/HelloWorld.png”.
   * **< Destination_folder >** ange en sökväg till en lokal katalog att lagra filer som hämtas från Azure Storage, till exempel: ”~/downloadImages”.
7. När du har uppdaterat de nödvändiga variablerna i vim, trycker du tangentkombinationer `ESC`, `:`, `wq!` att spara skriptet.
8. Om du vill köra det här skriptet, skriver du helt enkelt namnet på filen i bash-konsolen. När skriptet har körts bör du ha en lokal destination-mapp som innehåller den hämtade image-filen. Följande skärmbild visar ett exempel på utdata:

När skriptet har körts, bör du ha en lokal destination-mapp som innehåller den hämtade image-filen.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Hantera lagringskonton med den klassiska Azure CLI
### <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration
Även om de flesta av kommandona storage fungerar utan en Azure-prenumeration, rekommenderar vi att du ansluter till din prenumeration från klassiskt CLI.

### <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto
Om du vill använda Azure storage, behöver du ett lagringskonto. Du kan skapa ett nytt Azure storage-konto när du har konfigurerat datorn för att ansluta till din prenumeration.

```azurecli
azure storage account create <account_name>
```

Namnet på ditt lagringskonto måste vara mellan 3 och 24 tecken och siffror och gemener.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Ange ett standardkonto för Azure storage i miljövariabler
Du kan ha flera lagringskonton i din prenumeration. Du kan välja en av dem och ange den i miljövariabler för storage-kommandon i samma session. Detta gör att du kan köra CLI-kommandon för klassiska lagring utan att ange lagringskontot och nyckeln uttryckligen.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Ett annat sätt att ange ett standard storage-konto använder anslutningssträngen. Det första hämta anslutningssträngen med kommandot:

```azurecli
azure storage account connectionstring show <account_name>
```

Kopiera anslutningssträngen utdata och ange den till miljövariabeln:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Skapa och hantera BLOB-objekt
Azure Blob storage är en tjänst för att lagra stora mängder Ostrukturerade data, exempelvis text eller binära data som kan nås från var som helst i världen via HTTP eller HTTPS. Det här avsnittet förutsätter att du redan är bekant med principerna för Azure Blob storage. Detaljerad information finns i [komma igång med Azure Blob storage med hjälp av .NET](../blobs/storage-dotnet-how-to-use-blobs.md) och [Blob Service-koncept](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Skapa en container
Varje blobb i Azure storage måste vara i en behållare. Du kan skapa en privat behållaren med hjälp av den `azure storage container create` kommando:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Det finns tre nivåer av anonym läsbehörighet: **av**, **Blob**, och **behållare**. Om du vill förhindra anonym åtkomst till blobbar, kan du ange parametern behörighet **av**. Som standard den nya behållaren är privat och kan endast användas av ägare. Tillåt anonyma offentlig läsbehörighet till blob-resurser, men inte till metadata för behållaren eller till listan över blobar i behållaren ange parametern behörighet **Blob**. Tillåt fullständig offentlig läsbehörighet för blob-resurser, metadata för behållaren och listan över blobar i behållaren genom att ange parametern behörighet **behållare**. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container
Azure Blob Storage stöder blockblobbar och sidblobbar. Mer information finns i [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Om du vill ladda upp blobar i en behållare, du kan använda den `azure storage blob upload`. Som standard överför lokala filer till en blockblob i det här kommandot. Om du vill ange typen för blobben som du kan använda den `--blobtype` parametern.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Ladda ned blobar från en behållare
I följande exempel visar hur du laddar ned blobar från en behållare.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopiera blobar
Du kan kopiera blobar i eller mellan lagringskonton och regioner asynkront.

Följande exempel visar hur du kopierar blobar från ett lagringskonto till ett annat. I det här exemplet skapar vi en behållare där BLOB-lagring är offentligt och anonymt tillgänglig.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Det här exemplet utför en asynkron kopia. Du kan övervaka statusen för varje kopieringsåtgärden genom att köra den `azure storage blob copy show` igen.

Observera att Käll-URL som angetts för kopieringen måste antingen vara offentligt åtkomlig eller inkludera en token för SAS (signatur för delad åtkomst).

### <a name="delete-a-blob"></a>Ta bort en blob
Ta bort en blob genom att använda den kommandot nedan:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Skapa och hantera filresurser
Azure Files erbjuder delad lagring för program som använder SMB-standardprotokollet. Microsoft Azure-datorer och molntjänster, samt lokala program kan dela fildata via monterade resurser. Du kan hantera filresurser och fildata via klassiska CLI. Mer information om Azure Files finns i [introduktion till Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Skapa en filresurs
En Azure-filresurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en filresurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot. I följande exempel skapas en filresurs med namnet **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Skapa en katalog
En katalog innehåller ett valfritt hierarkisk struktur för en Azure-filresurs. I följande exempel skapas en katalog med namnet **MinMapp** i filresursen.

```azurecli
azure storage directory create myshare myDir
```

Obs den katalogsökvägen kan innehålla flera nivåer *t.ex.*, **en / b**. Du måste dock se till att alla överordnade kataloger finns. Till exempel för sökvägen **en / b**, måste du skapa katalog **en** först, sedan att skapa katalogen **b**.

### <a name="upload-a-local-file-to-directory"></a>Ladda upp en lokal fil till katalogen
I följande exempel laddar upp en fil från **~/temp/samplefile.txt** till den **MinMapp** directory. Ändra sökvägen till filen så att den pekar på en giltig fil på den lokala datorn:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Observera att en fil i resursen kan vara upp till 1 TB i storlek.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Visa filerna i resursen rot- eller directory
Du kan lista filer och underkataloger i en rot-resurs eller en katalog med följande kommando:

```azurecli
azure storage file list myshare myDir
```

Observera att katalognamnet är valfritt för liståtgärden. Om det utelämnas används visar kommandot innehållet i rotkatalogen för resursen.

### <a name="copy-files"></a>Kopiera filer
Från och med version 0.9.8 av klassisk CLI kan kopiera du en fil till en annan fil, en fil till en blobb eller en blobb till en fil. Nedan ser du hur du utför dessa kopieringsåtgärder med CLI-kommandon. Kopiera en fil till den nya katalogen:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Kopiera en blobb till en katalog:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Nästa steg

Du hittar Azure klassiska CLI-kommandoreferens för att arbeta med Storage-resurser här:

* [Azure klassiska CLI-kommandon i Resource Manager-läge](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure klassiska CLI-kommandon i Azure Service Management-läge](../../cli-install-nodejs.md)

Du kanske också tycker att testa den senaste versionen av den [Azure CLI](../storage-azure-cli.md), för användning med Resource Manager-distributionsmodellen.
