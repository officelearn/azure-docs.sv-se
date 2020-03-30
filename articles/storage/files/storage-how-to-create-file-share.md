---
title: Skapa en Azure-filresurs
titleSuffix: Azure Files
description: Så här skapar du en Azure-filresurs med hjälp av Azure-portalen, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596940"
---
# <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Om du vill skapa en Azure-filresurs måste du svara på tre frågor om hur du ska använda den:

- **Vilka är prestandakraven för din Azure-filresurs?**  
    Azure Files erbjuder standardfilresurser, som finns på hårddiskbaserad (HDD-baserad) maskinvara och premiumfilresurser, som finns på SSD-baserad maskinvara (Solid State-baserad).

- **Vilken storlek filresurs behöver du?**  
    Standardfilresurser kan sträcka sig över upp till 100 TiB, men den här funktionen är inte aktiverad som standard. Om du behöver en filresurs som är större än 5 TiB måste du aktivera den stora filresursfunktionen för ditt lagringskonto. Premium fil aktier kan sträcka sig över upp till 100 TiB utan någon särskild inställning, men premium fil aktier etableras, snarare än att betala som du går som standard filresurser. Det innebär att etablering av en filresurs mycket större än vad du behöver ökar den totala lagringskostnaden.

- **Vilka är dina redundanskrav för din Azure-filresurs?**  
    Standardfilresurser erbjuder lokalt redundant (LRS), zon redundant (ZRS), geo-redundant (GRS) eller geo-zon-redundant (GZRS) lagring, men den stora filresursfunktionen stöds bara på lokalt redundanta och zon redundanta filresurser. Premium-filresurser stöder inte någon form av geo-redundans.

    Premium-filresurser är tillgängliga med lokalt redundans i de flesta regioner som erbjuder lagringskonton och med zonredundans i en mindre delmängd av regioner. Information om hur du tar reda på om premiumfilresurser för närvarande är tillgängliga i din region finns på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. Information om regioner som stöder ZRS finns i [Azure Storage redundans](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Mer information om dessa tre alternativ finns i [Planera för en Azure Files-distribution](storage-files-planning.md).

## <a name="prerequisites"></a>Krav
- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
Azure-filresurser distribueras till *lagringskonton*, som är objekt på den högsta nivån som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera filresurser. 

Azure stöder flera typer av lagringskonton för olika lagringsscenarier som kunder kan ha, men det finns två huvudtyper av lagringskonton för Azure-filer. Vilken lagringskontotyp du behöver skapa beror på om du vill skapa en standardfilresurs eller en premiumfilresurs: 

- **GPv2-lagringskonton (General Purpose Version 2):** GPv2-lagringskonton gör att du kan distribuera Azure-filresurser på standard-/hårddiskbaserad (HDD-baserad) maskinvara. Förutom att lagra Azure-filresurser kan GPv2-lagringskonton lagra andra lagringsresurser, till exempel blob-behållare, köer eller tabeller. 

- **FileStorage lagringskonton**: FileStorage lagringskonton kan du distribuera Azure-filresurser på premium/solid state disk-baserade (SSD-baserade) hårdvara. FileStorage-konton kan endast användas för att lagra Azure-filresurser. Inga andra lagringsresurser (blob-behållare, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett lagringskonto via Azure-portalen väljer du **+ Skapa en resurs** från instrumentpanelen. I det resulterande sökfönstret på Azure Marketplace söker du efter **lagringskonto** och väljer det resulterande sökresultatet. Detta leder till en översiktssida för lagringskonton. välj **Skapa** om du vill fortsätta med guiden skapa lagringskonto.

![En skärmbild av alternativet för snabbskap för lagringskonto i en webbläsare](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Avsnittet Grunderna
Det första avsnittet som ska slutföras för att skapa ett **lagringskonto**är märkt Basics . Detta innehåller alla nödvändiga fält för att skapa ett lagringskonto. Om du vill skapa ett GPv2-lagringskonto kontrollerar du att alternativknappen **Prestanda** är inställd på *Standard* och listrutan **Kontoslag** har valts till *StorageV2 (allmänt v2)*.

![En skärmbild av alternativknappen Prestanda med standardvalt och Kontoslag med StorageV2 markerat](media/storage-how-to-create-file-share/create-storage-account-1.png)

Om du vill skapa ett FileStorage-lagringskonto kontrollerar du att alternativknappen **Prestanda** är inställd på *Premium* och listrutan **Kontotyp** har valts till *FileStorage*.

![En skärmbild av alternativknappen Prestanda med Premium markerat och Kontosort med FileStorage valt](media/storage-how-to-create-file-share/create-storage-account-2.png)

De andra grunderna fälten är oberoende av valet av lagringskonto:
- **Prenumeration**: Prenumerationen för lagringskontot som ska distribueras till. 
- **Resursgrupp**: Resursgruppen för lagringskontot som ska distribueras till. Du kan antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp.
- **Namn på lagringskonto**: Namnet på den lagringskontoresurs som ska skapas. Detta namn måste vara globalt unikt, men annars kan alla namn du önskar. Lagringskontonamnet används som servernamn när du monterar en Azure-filresurs via SMB.
- **Plats**: Regionen för lagringskontot som ska distribueras till. Detta kan vara den region som är associerad med resursgruppen eller någon annan tillgänglig region.
- **Replikering**: Även om detta är märkt replikering, innebär det här fältet faktiskt **redundans**; Detta är den önskade redundansnivån: lokalt redundans (LRS), zonredundans (ZRS), georedundans (GRS) och geo-zonredundans. Den här listrutan innehåller också georedundans (RA-GRS) och ra-GZRS (read-access geo-zone redundans) och som inte gäller för Azure-filresurser. Alla filresurser som skapas i ett lagringskonto med dessa markerade kommer faktiskt att vara antingen geo-redundant eller geo-zon-redundant, respektive. Beroende på din region eller vald lagringskontotyp kanske vissa redundansalternativ inte tillåts.
- **Åtkomstnivå**: Det här fältet gäller inte för Azure-filer, så du kan välja någon av alternativknapparna.

#### <a name="the-networking-blade"></a>Nätverksbladet
Med nätverksavsnittet kan du konfigurera nätverksalternativ. Dessa inställningar är valfria för att skapa lagringskontot och kan konfigureras senare om så önskas. Mer information om dessa alternativ finns i [Överväganden om Azure Files-nätverk](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Det avancerade bladet
Det avancerade avsnittet innehåller flera viktiga inställningar för Azure-filresurser:

- **Säker överföring krävs**: Det här fältet anger om lagringskontot kräver kryptering under överföring för kommunikation till lagringskontot. Vi rekommenderar att detta är aktiverat, men om du behöver SMB 2.1-stöd måste du inaktivera detta. Vi rekommenderar att du inaktiverar kryptering som du begränsar åtkomsten till ett virtuellt nätverk med tjänstslutpunkter och/eller privata slutpunkter.
- **Stora filresurser**: Det här fältet aktiverar lagringskontot för filresurser som sträcker sig över upp till 100 TiB. Om du aktiverar den här funktionen begränsas ditt lagringskonto till endast lokalt redundanta och zonundant lagringsalternativ. När ett GPv2-lagringskonto har aktiverats för stora filresurser kan du inte inaktivera den stora filresursfunktionen. FileStorage lagringskonton (lagringskonton för premiumfilresurser) har inte det här alternativet, eftersom alla premiumfilresurser kan skala upp till 100 TiB. 

![En skärmbild av de viktiga avancerade inställningarna som gäller för Azure-filer](media/storage-how-to-create-file-share/create-storage-account-3.png)

De andra inställningarna som är tillgängliga på den avancerade fliken (blob soft-delete, hierarkiskt namnområde för Azure Data Lake storage gen 2 och NFSv3 för blob-lagring) gäller inte för Azure-filer.

#### <a name="tags"></a>Taggar
Taggar är namn-/värdepar som gör att du kan kategorisera resurser och visa samlad fakturering genom att använda samma tagg på flera resurser och resursgrupper. Dessa är valfria och kan tillämpas när lagringskontot har skapats.

#### <a name="review--create"></a>Granska + skapa
Det sista steget för att skapa lagringskontot är att välja knappen **Skapa** på fliken **Granska + skapa.** Den här knappen är inte tillgänglig om alla obligatoriska fält för ett lagringskonto inte är ifyllda.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
För att skapa ett lagringskonto med `New-AzStorageAccount` PowerShell använder vi cmdleten. Denna cmdlet har många alternativ; endast de alternativ som krävs visas. Mer information om avancerade alternativ finns i [ `New-AzStorageAccount` cmdlet-dokumentationen](/powershell/module/az.storage/new-azstorageaccount).

För att förenkla skapandet av lagringskontot och efterföljande filresurs kommer vi att lagra flera parametrar i variabler. Du kan ersätta det variabla innehållet med de värden du vill, men observera att lagringskontonamnet måste vara globalt unikt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Om du vill skapa ett lagringskonto som kan lagra standardresurser för Azure-filer använder vi följande kommando. Parametern `-SkuName` relaterar till vilken typ av redundans som önskas. Om du vill ha ett geo-redundant eller geo-zon-redundant `-EnableLargeFileShare` lagringskonto, måste du också ta bort parametern.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Om du vill skapa ett lagringskonto som kan lagra premium-Azure-filresurser använder vi följande kommando. Observera att `-SkuName` parametern har `Premium` ändrats till att omfatta både`LRS`och önskad redundansnivå för lokalt redundant ( ). Parametern `-Kind` `FileStorage` beror `StorageV2` i stället för på att premiumfilresurser måste skapas i ett FileStorage-lagringskonto i stället för ett GPv2-lagringskonto.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa ett lagringskonto med Azure CLI använder vi kommandot az storage account create. Det här kommandot har många alternativ. endast de alternativ som krävs visas. Mer information om de avancerade alternativen finns i [ `az storage account create` kommandodokumentationen](/cli/azure/storage/account).

För att förenkla skapandet av lagringskontot och efterföljande filresurs kommer vi att lagra flera parametrar i variabler. Du kan ersätta det variabla innehållet med de värden du vill, men observera att lagringskontonamnet måste vara globalt unikt.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Om du vill skapa ett lagringskonto som kan lagra standardresurser för Azure-filer använder vi följande kommando. Parametern `--sku` relaterar till vilken typ av redundans som önskas. Om du vill ha ett geo-redundant eller geo-zon-redundant `--enable-large-file-share` lagringskonto, måste du också ta bort parametern.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Om du vill skapa ett lagringskonto som kan lagra premium-Azure-filresurser använder vi följande kommando. Observera att `--sku` parametern har `Premium` ändrats till att omfatta både`LRS`och önskad redundansnivå för lokalt redundant ( ). Parametern `--kind` `FileStorage` beror `StorageV2` i stället för på att premiumfilresurser måste skapas i ett FileStorage-lagringskonto i stället för ett GPv2-lagringskonto.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Skapa en filresurs
När du har skapat ditt lagringskonto är allt som finns kvar att skapa filresursen. Den här processen är oftast densamma oavsett om du använder en premiumfilresurs eller en standardfilresurs. Den primära skillnaden är **kvoten** och vad den representerar.

För standardfilresurser är det en övre gräns för Azure-filresursen, bortom vilken slutanvändarna inte kan gå. Det primära syftet med kvoten för en standardfilresurs är budget: "Jag vill inte att filresursen ska växa efter denna punkt". Om en kvot inte anges kan standardfilresursen sträcka sig över upp till 100 TiB (eller 5 TiB om egenskapen stora filresurser inte har angetts för ett lagringskonto).

För premiumfilresurser överbelastas kvoten till en **etablerad storlek**. Den etablerade storleken är det belopp som du kommer att faktureras för, oavsett faktisk användning. När du etablerar en premiumfilresurs vill du ta hänsyn till två faktorer: 1) den framtida tillväxten av resursen från ett utrymmesutnyttjandeperspektiv och 2) IOPS som krävs för din arbetsbelastning. Varje etablerad GiB ger dig rätt till ytterligare reserverade och burst IOPS. Mer information om hur du planerar för en premiumfilresurs finns i [etablering av premiumfilaktier](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du just har skapat ditt lagringskonto kan du navigera till det från distributionsskärmen genom att välja **Gå till resurs**. Om du tidigare har skapat lagringskontot kan du navigera till det via resursgruppen som innehåller det. En gång i lagringskontot väljer du panelen **filresurser** (du kan också navigera till **Filresurser** via innehållsförteckningen för lagringskontot).

![En skärmbild av panelen Filresurser](media/storage-how-to-create-file-share/create-file-share-1.png)

I filresurslistan bör du se alla filresurser som du tidigare har skapat i det här lagringskontot. en tom tabell om inga filresurser har skapats ännu. Välj **+ Filresurs** om du vill skapa en ny filresurs.

Det nya fildelningsbladet ska visas på skärmen. Fyll i fälten i det nya fildelningsbladet för att skapa en filresurs:

- **Namn**: namnet på den filresurs som ska skapas.
- **Kvot**: Kvoten för filresursen för standardfilresurser. filandelens etableringsstorlek för premiumfilandelar.

Välj **Skapa** för att slutföra skapandet av den nya resursen. Observera att om ditt lagringskonto finns i ett virtuellt nätverk kan du inte skapa en Azure-filresurs om inte klienten också finns i det virtuella nätverket. Du kan också komma runt den här point-in-time-begränsningen med hjälp av Azure PowerShell-cmdlet. `New-AzRmStorageShare`

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Du kan skapa Azure-filresursen med cmdleten. [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) Följande PowerShell-kommandon förutsätter att du `$resourceGroupName` `$storageAccountName` har angett variablerna och som definieras ovan i skapandet av ett lagringskonto med Azure PowerShell-avsnittet. 

> [!Important]  
> För premiumfilresurser `-QuotaGiB` refererar parametern till filresursens etablerade storlek. Filresursens etablerade storlek är det belopp som du kommer att faktureras för, oavsett användning. Standardfilresurser faktureras baserat på användning i stället för etablerad storlek.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Innan vi kan skapa en Azure-filresurs med Azure CLI måste du hämta en lagringskontonyckel för att godkänna filresursens create-åtgärd med. Detta kan göras [`az storage account keys list`](/cli/azure/storage/account/keys) med kommandot:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

När du har lagringskontonyckeln kan du skapa [`az storage share create`](/cli/azure/storage/share) Azure-filresursen med kommandot. 

> [!Important]  
> För premiumfilresurser `--quota` refererar parametern till filresursens etablerade storlek. Filresursens etablerade storlek är det belopp som du kommer att faktureras för, oavsett användning. Standardfilresurser faktureras baserat på användning i stället för etablerad storlek.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Det här kommandot misslyckas om lagringskontot finns i ett virtuellt nätverk och datorn du anropar det här kommandot från inte är en del av det virtuella nätverket. Du kan kringgå den här point-in-time-begränsningen genom att använda Azure PowerShell-cmdleten `New-AzRmStorageShare` enligt beskrivningen ovan eller genom att köra Azure CLI från en dator som är en del av det virtuella nätverket, inklusive via en VPN-anslutning.

---

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="next-steps"></a>Nästa steg
- [Planera för en distribution av Azure-filer](storage-files-planning.md) eller [planera för en distribution av Azure File Sync](storage-sync-files-planning.md). 
- [Översikt över nätverk](storage-files-networking-overview.md).
- Anslut och montera en filresurs på [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md).