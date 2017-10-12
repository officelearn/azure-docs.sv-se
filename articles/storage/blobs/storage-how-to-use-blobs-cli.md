---
title: "Utföra åtgärder på Azure Blob Storage (objektlagring) med Azure CLI | Microsoft Docs"
description: "Lär dig mer om att ladda upp och ladda ned blobar i Azure Blob Storage, samt skapa en signatur för delad åtkomst (SAS) för att hantera åtkomst till en blob i ditt lagringskonto."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: marsma
ms.openlocfilehash: c37fc0b701b668ab6bb9213a487ec8baa33fe663
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Utföra åtgärder för Blob Storage med Azure CLI

Azure Blob Storage är en tjänst för att lagra stora mängder ostrukturerad objektdata, exempelvis text eller binär data som kan nås från var som helst i världen via HTTP eller HTTPS. Den här kursen beskriver grunderna i Azure Blob Storage, till exempel hur du laddar upp, laddar ned och tar bort blobar. Lär dig att:

> [!div class="checklist"]
> * Skapa en behållare
> * Överföra en fil (blob) till en behållare
> * Visa en lista över blobbarna i en behållare
> * Hämta en blob från en behållare
> * Kopiera en blob mellan lagringskonton
> * Ta bort en blob
> * Visa och ändra blob-egenskaper och metadata
> * Hantera säkerhet med en signatur för delad åtkomst (SAS)

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Skapa en behållare

Behållare liknar kataloger på datorn. Du kan använda dem för att ordna grupper med blobar i en behållare precis som du ordnar filer i en katalog. Ett lagringskonto kan ett antal behållare. Du kan lagra upp till 500 TB blobdata i en behållare, vilket är den största mängden data i ett lagringskonto.

Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Behållarnamnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om namngivning av blobar och behållare finns i [Namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Aktivera offentlig läsbehörighet för en behållare

En nyligen skapad behållare är privat som standard. Det innebär att ingen kan få åtkomst till behållaren utan en [signatur för delad åtkomst](#create-a-shared-access-signature-sas) eller åtkomstnycklar till lagringskontot. Med Azure CLI kan du ändra beteendet genom att ställa in behörigheter för behållare på någon av tre nivåer:

| | |
|---|---|
| `--public-access off` | (Standard) Ingen offentlig läsbehörighet |
| `--public-access blob` | Endast offentlig läsbehörighet för blobar |
| `--public-access container` | Offentlig läsbehörighet för blobar, kan visa blobar i behållaren |

När du ställer in offentlig åtkomst till `blob` eller `container` aktiverar du skrivskyddad åtkomst för alla på Internet. Om du till exempel vill visa bilder som är lagrade som blobar på din webbplats måste du aktivera offentlig läsbehörighet. Om du vill aktivera läs-/skrivbehörighet måste du istället använda en [signatur för delad åtkomst (SAS)](#create-a-shared-access-signature-sas).

Aktivera offentlig läsbehörighet för din behållare med kommandot [az storage container set-permission](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Ladda upp en blob till en behållare

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är den vanligaste typen av blob som lagras i Azure Storage. Tilläggsblobar används när data måste läggas till i en befintlig blob utan att det befintliga innehållet ändras, som vid loggning. Sidblobar säkerhetskopierar VHD-filerna i virtuella IaaS-datorer.

I det här exemplet har vi laddat upp en blob i behållaren vi skapade i det senaste steget med kommandot [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Den här åtgärden skapar bloben om den inte redan finns, och skriver över den om den finns. Ladda upp flera filer så du kan se flera poster när du listar blobarna i nästa steg.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Lista blobarna i behållaren med kommandot [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Exempel på utdata:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Ladda ned en blob

Ladda ned bloben du laddade upp i ett tidigare steg med kommandot [az storage blob download](/cli/azure/storage/blob#download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Kopiera en blob mellan lagringskonton

Du kan kopiera blobar i eller mellan lagringskonton och regioner asynkront.

Följande exempel visar hur du kopierar blobar från ett lagringskonto till ett annat. Vi skapar först en behållare i källagringskontot där vi anger offentlig läsbehörighet för dess blobar. Efter det laddar vi upp en fil till behållaren och slutligen kopierar vi bloben från den behållaren till en behållare i mållagringskontot.

I det här exemplet måste målbehållaren redan finnas i källagringskontot för att kopieringen ska fungera. Dessutom måste källbloben som anges i `--source-uri`-argumentet antingen inkludera en signatur för delad åtkomst (SAS) eller vara offentligt åtkomlig, som i det här exemplet.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Ta bort en blob

Ta bort bloben från behållaren med kommandot [az storage blob delete](/cli/azure/storage/blob#delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Visa och ändra blob-egenskaper och metadata

Varje blob har flera tjänstdefinierade egenskaper du kan visa med kommandot [az storage blob show](/cli/azure/storage/blob#show), däribland namn, typ och längd. Du kan också konfigurera en blob med egna egenskaper och deras värden med kommandot [az storage blob metadata update](/cli/azure/storage/blob/metadata#update).

I det här exemplet visar vi först en blobs tjänstdefinierade egenskaper och därefter uppdaterar vi bloben med två av våra egna metadataegenskaper. Slutligen visar vi blobens metadataegenskaper och deras värden med kommandot [az storage blob metadata show](/cli/azure/storage/blob/metadata#show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Skapa en signatur för delad åtkomst (SAS)

Med signaturer för delad åtkomst (SAS) får du ett sätt att ge begränsad åtkomst till objekt i ditt lagringskonto utan att exponera dina åtkomstnycklar för lagringskontot. För att skapa en URI som tillåter åtkomst till en privat resurs skapar du SAS-token med önskade behörigheter och giltighetsfönster (dess effektiva varaktighet) och lägger till den som en frågesträng till en resurs URL-adress, och formar därför dess fullständiga SAS-URI. Alla med denna SAS-URI (resursen URL plus SAS-token) kan sedan nå den under SAS-tokens giltighetsfönster. Du kanske vill tillåta läsåtkomst till en privat blob i två minuter så att någon kan visa den.

I följande steg inaktiverar du offentlig åtkomst till din behållare, testar endast privat åtkomst och genererar och testar sedan en SAS-URI.

### <a name="disable-container-public-access"></a>Inaktivera offentlig åtkomst till behållare

Ställ först in behållarens åtkomstnivå på `off`. Det här betyder att det inte finns någon åtkomst till behållaren eller dess blobar utan antingen en signatur för delad åtkomst eller en åtkomstnyckel för lagringskontot.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Verifiera privat åtkomst

För att verifiera att det inte finns någon offentlig läsbehörighet till blobarna i den behållaren hämtar du URL-adressen för en av dess blobar med kommandot [az storage blob url](/cli/azure/storage/blob#url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Gå till blobens URL-adress i ett privat webbläsarfönster. Du ser felet `ResourceNotFound` eftersom bloben är privat, och att du inte har inkluderat en signatur för delad åtkomst.

### <a name="create-a-sas-uri"></a>Skapa en SAS-URI

Nu ska vi skapa en SAS-URI som tillåter åtkomst till bloben. I följande exempel fyller vi först i en variabel med URL-adressen för bloben med [az storage blob url](/cli/azure/storage/blob#url), och fyller sedan i en annan variabel med en SAS-token som genererats med kommandot [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas). Slutligen matar vi ut blobens fullständiga SAS-URI genom att sammanfoga de två, separerat av frågesträngavgränsaren `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Testa SAS-URI:n

I ett privat webbläsarfönster går du till den fullständiga SAS-URI som du visade med kommandot `echo` i föregående kodfragment. Den här gången får du inget fel, och du kan visa eller ladda ned bloben.

Vänta tills URL-adressen slutar gälla (två minuter i det här exemplet) och gå sedan till URI:n i ett annat privat webbläsarfönster. Nu kanske du ser felet `AuthenticationFailed` eftersom SAS-token har upphört att gälla.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive lagringskontot som du skapade och alla blobar som du har laddat upp i den här självstudiekursen tar du bort resursgruppen med kommandot [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig grunderna för att arbeta med blobar i Azure Storage:

> [!div class="checklist"]
> * Skapa en behållare
> * Överföra en fil (blob) till en behållare
> * Visa en lista över blobbarna i en behållare
> * Hämta en blob från en behållare
> * Kopiera en blob mellan lagringskonton
> * Ta bort en blob
> * Visa och ändra blob-egenskaper och metadata
> * Hantera säkerhet med en signatur för delad åtkomst (SAS)

Följande resurser innehåller ytterligare information om hur du arbetar med Azure CLI, och hur du arbetar med resurserna i ditt lagringskonto.

* Azure CLI
  * [Logga in med Azure CLI 2.0](/cli/azure/authenticate-azure-cli) – Läs mer om de olika metoderna för autentisering med CLI, däribland icke-interaktiv inloggning via [service principal](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) för att köra obevakade Azure CLI-skript.
  * [Azure CLI 2.0-kommandoreferens](/cli/azure/)
* Microsoft Azure Storage Explorer
  * [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
