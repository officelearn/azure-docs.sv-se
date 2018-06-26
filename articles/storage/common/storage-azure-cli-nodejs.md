---
title: Med hjälp av Azure CLI 1.0 med Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azure-kommandoradsgränssnittet (Azure CLI) 1.0 med Azure Storage för att skapa och hantera storage-konton och arbeta med Azure-blobbar och filer. Azure CLI är ett verktyg för flera plattformar
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.openlocfilehash: 1e2c2dc45f2cb91ca225bf183ddde5fa2d1123c4
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936997"
---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Med hjälp av Azure CLI 1.0 med Azure Storage

## <a name="overview"></a>Översikt

Azure CLI tillhandahåller en uppsättning med öppen källkod, plattformsoberoende kommandon för att arbeta med Azure-plattformen. Det ger mycket av samma funktioner som finns i den [Azure-portalen](https://portal.azure.com) samt som omfattande funktioner för dataåtkomst.

I den här guiden kommer vi hur du använder [Azure-kommandoradsgränssnittet (Azure CLI)](../../cli-install-nodejs.md) att utföra olika uppgifter för utveckling och administration med Azure Storage. Vi rekommenderar att du hämtar och installerar eller uppgraderar till senaste Azure CLI innan du använder den här guiden.

Den här handboken förutsätts att du förstår de grundläggande principerna för Azure Storage. Handboken innehåller ett antal skript för att demonstrera användningen av Azure CLI med Azure Storage. Glöm inte att uppdatera variablerna skript baserat på din konfiguration innan du kör varje skript.

> [!NOTE]
> Handboken innehåller Azure CLI kommando- och exempel för klassiska lagringskonton. Se [med hjälp av Azure CLI för Mac, Linux och Windows med Azure Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) för Azure CLI-kommandona för Resource Manager storage-konton.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Komma igång med Azure Storage- och Azure CLI på 5 minuter
Den här guiden använder Ubuntu exempel, men andra operativsystemplattformar ska utföra på samma sätt.

**Ny till Azure:** hämta Microsoft Azure-prenumeration och ett Microsoft-konto som är kopplade till den prenumerationen. Mer information om köpalternativ för Azure finns [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/), [köpa alternativ](https://azure.microsoft.com/pricing/purchase-options/), och [Medlemserbjudanden](https://azure.microsoft.com/pricing/member-offers/) (för medlemmar i MSDN, Microsoft Partner Network och BizSpark, och andra Microsoft-program).

Se [Tilldela administratörsroller i Azure Active Directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) för mer information om Azure-prenumerationer.

**När du har skapat ett Microsoft Azure-prenumeration och konto:**

1. Hämta och installera Azure CLI följa anvisningarna i [installerar Azure CLI](../../cli-install-nodejs.md).
2. När du har installerat Azure CLI kommer du att kunna använda azure kommando från din kommandoradsgränssnittet (Bash, Terminal, Kommandotolken) att komma åt Azure CLI-kommandona. Typ av _azure_ kommandot och du bör se följande utdata.

    ![Azure kommandoutdata](./media/storage-azure-cli/azure_command.png)   
3. Skriv i kommandoradsgränssnittet, `azure storage` för att visa en lista över alla kommandon för azure-lagring och få en första intryck av funktionerna Azure CLI tillhandahåller. Du kan skriva kommandonamn med **-h** parameter (till exempel `azure storage share create -h`) visas detaljerad information om kommandosyntax.
4. Nu får du ett enkelt skript som visar grundläggande Azure CLI-kommandona för att få åtkomst till Azure Storage. Skriptet först ber dig att ange två variabler för ditt lagringskonto och en nyckel. Skriptet kommer sedan skapa en ny behållare i den här nya storage-konto och ladda upp en befintlig bildfil (blob) till behållaren. När skriptet visar alla blobbar i behållaren, hämtas bildfilen i målkatalogen som finns på den lokala datorn.

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

5. Öppna din önskade textredigerare (vim till exempel) i den lokala datorn. Ange skriptet ovan i en textredigerare.
6. Nu kan behöver du uppdatera skriptvariabler baserat på inställningarna.

   * **< Storage_account_name >** använder det angivna namnet i skriptet eller ange ett nytt namn för ditt lagringskonto. **Viktigt:** namnet på lagringskontot måste vara unikt i Azure. Det måste vara gemena för!
   * **< Storage_account_key >** åtkomstnyckeln för ditt lagringskonto.
   * **< Container_name >** använder det angivna namnet i skriptet eller ange ett nytt namn för din behållare.
   * **< Image_to_upload >** som anger en sökväg till en bild på den lokala datorn ”: ~ / images/HelloWorld.png”.
   * **< Destination_folder >** ange en sökväg till en lokal katalog att lagra filer som hämtas från Azure Storage, exempelvis: ”~/downloadImages”.
7. När du har uppdaterat de nödvändiga variablerna i vim trycker du på tangentkombinationer `ESC`, `:`, `wq!` spara skriptet.
8. Om du vill köra det här skriptet, Skriv namnet på skriptfilen i bash-konsolen. När skriptet körs, bör du ha en lokal målmapp som innehåller hämtade avbildningsfilen. Följande skärmbild visar ett exempel på utdata:

När skriptet körs, bör du ha en lokal målmapp som innehåller hämtade avbildningsfilen.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Hantera storage-konton med Azure CLI
### <a name="connect-to-your-azure-subscription"></a>Ansluta till din Azure-prenumeration
Medan de flesta av kommandona lagring fungerar utan en Azure-prenumeration rekommenderar vi att du ansluter till din prenumeration från Azure CLI. För att konfigurera Azure CLI för att arbeta med din prenumeration, följer du stegen i [Anslut till en Azure-prenumeration från Azure CLI](/cli/azure/authenticate-azure-cli).

### <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto
För att använda Azure storage, behöver du ett lagringskonto. Du kan skapa ett nytt Azure storage-konto när du har konfigurerat datorn för att ansluta till din prenumeration.

```azurecli
azure storage account create <account_name>
```

Namnet på ditt lagringskonto måste vara mellan 3 och 24 tecken långt och innehålla siffror och gemena bokstäver.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Ange ett standardkonto för Azure storage i miljövariabler
Du kan ha flera lagringskonton i din prenumeration. Du kan välja en av dem och ange den i miljövariabler för kommandona för lagring i samma session. På så sätt kan du köra Azure CLI-kommandona lagring utan att ange lagringskontot och nyckeln explicit.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Ett annat sätt att ange standardkontot för lagring med hjälp av anslutningssträngen. Hämta det första anslutningssträngen med kommandot:

```azurecli
azure storage account connectionstring show <account_name>
```

Kopiera anslutningssträngen utdata och ange miljövariabeln:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Skapa och hantera blobbar
Azure Blob storage är en tjänst för att lagra stora mängder Ostrukturerade data, till exempel text eller binära data som kan nås från var som helst i världen via HTTP eller HTTPS. Det här avsnittet förutsätter att du redan är bekant med Azure Blob storage-koncept. Detaljerad information finns i [komma igång med Azure Blob storage med hjälp av .NET](../blobs/storage-dotnet-how-to-use-blobs.md) och [Blob-Tjänstkoncept](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Skapa en behållare
Varje blobb i Azure-lagring måste vara i en behållare. Du kan skapa en privat behållaren med hjälp av den `azure storage container create` kommando:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Det finns tre nivåer av anonym läsbehörighet: **av**, **Blob**, och **behållare**. Om du vill förhindra anonym åtkomst till blobbar, kan du ange parametern behörighet **av**. Som standard den nya behållaren är privat och kan endast användas av ägare. Du vill tillåta anonym offentlig läsbehörighet till blob-resurser, men inte till metadata för behållaren eller i listan över blobbar i behållaren anger parametern behörighet **Blob**. Om du vill ge fullständig offentlig läsbehörighet till blob resurser, metadata för behållaren och listan över blobbar i behållaren, ange parametern behörighet **behållare**. Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Azure Blob Storage stöder blockblobbar och sidblobbar. Mer information finns i [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Om du vill överföra blobbar i en behållare, kan du använda den `azure storage blob upload`. Som standard överför det här kommandot lokala filer till en blockblobb. Om du vill ange en typ av blob, du kan använda den `--blobtype` parameter.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Ladda ned blobbar från en behållare
I följande exempel visar hur du ladda ned blobbar från en behållare.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Kopiera BLOB
Du kan kopiera blobar i eller mellan lagringskonton och regioner asynkront.

Följande exempel visar hur du kopierar blobar från ett lagringskonto till ett annat. I det här exemplet skapar vi en behållare där blobbar är offentligt, anonymt tillgänglig.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Det här exemplet utför en asynkron kopia. Du kan övervaka statusen för varje kopieringen genom att köra den `azure storage blob copy show` igen.

Observera att Käll-URL som angetts för kopieringen måste antingen vara allmänt tillgänglig eller inkluderar en SAS (signatur för delad åtkomst)-token.

### <a name="delete-a-blob"></a>Ta bort en blob
Om du vill ta bort en blobb, använder den under kommando:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Skapa och hantera filresurser
Azure Files erbjuder delad lagring för program som använder SMB-standardprotokollet. Microsoft Azure-datorer och molntjänster, samt lokala program kan dela fildata via monterade resurser. Du kan hantera filresurser och fildata via Azure CLI. Mer information om Azure-filer finns [introduktion till Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Skapa en filresurs
En Azure-filresursen är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en filresurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontot kapacitetsgränser. Följande exempel skapar en filresurs med namnet **minresurs**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Skapa en katalog
En katalog innehåller ett valfritt hierarkisk struktur för ett Azure-filresursen. I följande exempel skapas en katalog med namnet **MinMapp** i filresursen.

```azurecli
azure storage directory create myshare myDir
```

Observera att sökvägen kan innehålla flera nivåer *t.ex.*, **en / b**. Dock måste du kontrollera att alla överordnade kataloger finns. Till exempel för sökvägen **en / b**, måste du skapa katalog **en** först, sedan att skapa katalog **b**.

### <a name="upload-a-local-file-to-directory"></a>Ladda upp en lokal fil till katalogen
I följande exempel överför en fil från **~/temp/samplefile.txt** till den **MinMapp** directory. Ändra sökvägen till filen så att den pekar på en giltig fil på den lokala datorn:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Observera att en fil i resursen kan vara upp till 1 TB i storlek.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Visa en lista med filerna i resursen rot- eller directory
Du kan visa en lista över filer och underkataloger i en rot-resurs eller en katalog med följande kommando:

```azurecli
azure storage file list myshare myDir
```

Observera att katalognamnet är obligatoriskt för åtgärden lista. Om det utelämnas används listar kommandot innehållet i rotkatalogen för resursen.

### <a name="copy-files"></a>Kopiera filer
Från och med version 0.9.8 av Azure CLI kan kopiera du en fil till en annan fil, en fil till en blobb eller en blobb till en fil. Nedan ser du hur du utför dessa kopieringsåtgärder med hjälp av CLI-kommandona. Du kopierar en fil till den nya katalogen:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Att kopiera en blobb till en katalog:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Nästa steg

Du kan hitta Azure CLI 1.0 kommandoreferens för att arbeta med lagringsresurser här:

* [Azure CLI-kommandona i Resource Manager-läge](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure CLI-kommandona i Azure Service Management-läge](../../cli-install-nodejs.md)

Du kan också vilja försök den [Azure CLI 2.0](../storage-azure-cli.md), våra nästa generations CLI som skrivits i Python, för användning med Resource Manager-distributionsmodellen.
