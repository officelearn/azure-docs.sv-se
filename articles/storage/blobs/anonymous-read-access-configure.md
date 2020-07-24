---
title: Konfigurera anonym offentlig Läs behörighet för behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du tillåter eller nekar anonym åtkomst till BLOB-data för lagrings kontot. Ange offentlig åtkomst inställning för behållare för att göra behållare och blobbar tillgängliga för anonym åtkomst.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: a153a3735bbc46dbbce7e58374e1015ac1ec0bfb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133188"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurera anonym offentlig Läs behörighet för behållare och blobbar

Azure Storage stöder valfri anonym offentlig Läs behörighet för behållare och blobbar. Anonym åtkomst till dina data tillåts som standard aldrig. Om du inte uttryckligen aktiverar anonym åtkomst måste alla begär anden till en behållare och dess blobbar verifieras med hjälp av antingen Azure Active Directory (Azure AD) eller autentisering med delad nyckel. När du konfigurerar en behållares inställning för offentlig åtkomst nivå för att tillåta anonym åtkomst, kan klienterna läsa data i den behållaren utan att auktorisera begäran.

> [!WARNING]
> När en behållare har kon figurer ATS för offentlig åtkomst kan alla klienter läsa data i behållaren. Offentlig åtkomst innebär en potentiell säkerhets risk, så om ditt scenario inte kräver det rekommenderar Microsoft att du inte tillåter det för lagrings kontot. Mer information finns i [förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md).

Den här artikeln beskriver hur du konfigurerar anonym offentlig Läs behörighet för en behållare och dess blobbar. Information om hur du kommer åt BLOB-data anonymt från ett klient program finns i [komma åt offentliga behållare och blobbar anonymt med .net](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Om anonym offentlig Läs behörighet

Offentlig åtkomst till dina data är alltid förbjuden som standard. Det finns två separata inställningar som påverkar offentlig åtkomst:

1. **Tillåt offentlig åtkomst för lagrings kontot.** Som standard ger ett lagrings konto en användare med rätt behörighet för att aktivera offentlig åtkomst till en behållare. BLOB-data är inte tillgängliga för offentlig åtkomst om inte användaren vidtar ytterligare steg för att konfigurera behållarens offentliga åtkomst inställning explicit.
1. **Konfigurera behållarens inställning för offentlig åtkomst.** Som standard är en behållares offentliga åtkomst inställning inaktive rad, vilket innebär att auktorisering krävs för varje begäran till behållaren eller dess data. En användare med rätt behörighet kan ändra en behållares offentliga åtkomst inställning för att aktivera anonym åtkomst endast om anonym åtkomst tillåts för lagrings kontot.

I följande tabell sammanfattas hur båda inställningarna påverkar den offentliga åtkomsten för en behållare.

| Offentlig åtkomst inställning | Offentlig åtkomst har inaktiverats för en behållare (standardinställning) | Offentlig åtkomst för en behållare har angetts till container | Offentlig åtkomst en behållare har angetts till BLOB |
|--|--|--|--|
| Offentlig åtkomst tillåts inte för lagrings kontot | Ingen offentlig åtkomst till någon behållare i lagrings kontot. | Ingen offentlig åtkomst till någon behållare i lagrings kontot. Inställningen för lagrings kontot åsidosätter inställningen för behållare. | Ingen offentlig åtkomst till någon behållare i lagrings kontot. Inställningen för lagrings kontot åsidosätter inställningen för behållare. |
| Offentlig åtkomst tillåts för lagrings kontot (standardinställning) | Ingen offentlig åtkomst till den här behållaren (standard konfiguration). | Offentlig åtkomst tillåts för den här behållaren och dess blobbar. | Offentlig åtkomst tillåts till blobbar i den här behållaren, men inte till själva behållaren. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Tillåt eller neka offentlig Läs behörighet för ett lagrings konto

Som standard tillåts offentlig åtkomst för enskilda behållare i ett lagrings konto. När offentlig åtkomst tillåts kan en användare med rätt behörighet ändra en behållares offentliga åtkomst inställning för att aktivera anonym offentlig åtkomst till data i den behållaren.

Tänk på att offentlig åtkomst till en behållare alltid stängs av som standard och måste konfigureras uttryckligen för att tillåta anonyma begär Anden. Oavsett inställningen på lagrings kontot kommer dina data aldrig att vara tillgängliga för offentlig åtkomst, såvida inte en användare med rätt behörighet tar detta ytterligare steg för att aktivera offentlig åtkomst på behållaren.

Att neka offentlig åtkomst för lagrings kontot förhindrar anonym åtkomst till alla behållare och blobbar i det kontot. När offentlig åtkomst inte tillåts för kontot går det inte att konfigurera den offentliga åtkomst inställningen för en behållare för att tillåta anonym åtkomst. För förbättrad säkerhet rekommenderar Microsoft att du inte tillåter offentlig åtkomst för dina lagrings konton, såvida inte ditt scenario kräver att användare får åtkomst till BLOB-resurser anonymt.

> [!IMPORTANT]
> Om den offentliga åtkomsten för ett lagrings konto nekas åsidosätts de offentliga åtkomst inställningarna för alla behållare i lagrings kontot. När offentlig åtkomst inte tillåts för lagrings kontot kommer alla framtida anonyma begär anden till det kontot att Miss förväntas. Innan du ändrar den här inställningen bör du förstå hur klient program som kan komma åt data i ditt lagrings konto är anonymt. Mer information finns i [förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md).

Om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto använder du Azure Portal eller Azure CLI för att konfigurera kontots egenskap **blobPublicAccess** . Den här egenskapen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Mer information finns i [Översikt över lagrings konto](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto i Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Leta upp **konfigurations** inställningen under **Inställningar**.
1. Ange **offentlig åtkomst för BLOB** till **aktive rad** eller **inaktive**rad.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Skärm bild som visar hur du tillåter eller nekar den offentliga BLOB-åtkomsten för kontot":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill tillåta eller neka offentlig åtkomst för ett lagrings konto med Azure CLI ska du först hämta resurs-ID för ditt lagrings konto genom att anropa kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) . Anropa sedan kommandot [AZ Resource Update](/cli/azure/resource#az-resource-update) för att ange **allowBlobPublicAccess** -egenskapen för lagrings kontot. Ange egenskapen **allowBlobPublicAccess** till true för att tillåta offentlig åtkomst. Om du inte vill tillåta, ställer du in den på **falskt**.

I följande exempel tillåts inte offentlig BLOB-åtkomst för lagrings kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

---

> [!NOTE]
> Att neka offentlig åtkomst för ett lagrings konto påverkar inte några statiska webbplatser som finns på lagrings kontot. **$Web** containern alltid är offentligt tillgänglig.

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>Kontrol lera om offentlig åtkomst tillåts för ett lagrings konto

Hämta värdet för egenskapen **allowBlobPublicAccess** för att kontrol lera om offentlig åtkomst är tillåten för ett lagrings konto. Om du vill kontrol lera den här egenskapen för ett stort antal lagrings konton samtidigt använder du Azure Resource Graph Explorer.

> [!IMPORTANT]
> Egenskapen **allowBlobPublicAccess** har inte angetts som standard och returnerar inte något värde förrän du uttryckligen anger det. Lagrings kontot tillåter offentlig åtkomst när egenskap svärdet är **Null** eller när det är **Sant**.

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>Kontrol lera om offentlig åtkomst tillåts för ett enda lagrings konto

Om du vill kontrol lera om offentlig åtkomst är tillåten för ett enda lagrings konto med hjälp av Azure CLI anropar du kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) och frågar efter egenskapen **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>Kontrol lera om offentlig åtkomst tillåts för en uppsättning lagrings konton

Om du vill kontrol lera om offentlig åtkomst tillåts för en uppsättning lagrings konton med optimala prestanda kan du använda Azures resurs diagram Utforskaren i Azure Portal. Mer information om hur du använder resurs diagram Utforskaren finns i [snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Om du kör följande fråga i resurs diagram Utforskaren returneras en lista över lagrings konton och värdet för egenskapen **allowBlobPublicAccess** för varje konto visas:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Ange offentlig åtkomst nivå för en behållare

Om du vill ge anonyma användare Läs åtkomst till en behållare och dess blobbar måste du först tillåta offentlig åtkomst till lagrings kontot och sedan ange behållarens offentliga åtkomst nivå. Om offentlig åtkomst nekas för lagrings kontot kommer du inte att kunna konfigurera offentlig åtkomst för en behållare.

När offentlig åtkomst tillåts för ett lagrings konto kan du konfigurera en behållare med följande behörigheter:

- **Ingen offentlig Läs behörighet:** Behållaren och dess blobbar kan endast nås med en auktoriserad begäran. Det här alternativet är standardvärdet för alla nya behållare.
- **Offentlig Läs behörighet för blobbar:** Blobbar i behållaren kan läsas av en anonym begäran, men behållar data är inte tillgängliga anonymt. Anonyma klienter kan inte räkna upp blobar i behållaren.
- **Offentlig Läs behörighet för behållare och dess blobbar:** Container-och BLOB-data kan läsas av anonym begäran, förutom behörighets inställningar för behållare och container-metadata. Klienter kan räkna upp blobar i behållaren med anonym begäran, men kan inte räkna upp behållare i lagrings kontot.

Du kan inte ändra offentlig åtkomst nivå för en enskild blob. Offentlig åtkomst nivå anges bara på container nivå.

Om du vill ange en behållares offentliga åtkomst nivå använder du Azure Portal eller Azure CLI. Du kan ställa in behållarens offentliga åtkomst nivå när du skapar behållaren eller uppdatera den här inställningen på en befintlig behållare.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill uppdatera den offentliga åtkomst nivån för en eller flera befintliga behållare i Azure Portal:

1. Gå till ditt lagrings konto – översikt i Azure Portal.
1. Under **BLOB service** på Meny bladet väljer du **behållare**.
1. Välj de behållare som du vill ange offentlig åtkomst nivå för.
1. Använd knappen **ändra åtkomst nivå** för att visa inställningarna för offentliga åtkomst.
1. Välj önskad offentlig åtkomst nivå i list rutan för **offentlig åtkomst nivå** och klicka på knappen OK för att tillämpa ändringen på de valda behållarna.

    ![Skärm bild som visar hur du ställer in offentlig åtkomst nivå i portalen](./media/anonymous-read-access-configure/configure-public-access-container.png)

När offentlig åtkomst inte tillåts för lagrings kontot kan inte behållarens offentliga åtkomst nivå anges. Om du försöker ange behållarens offentliga åtkomst nivå ser du att inställningen är inaktive rad eftersom offentlig åtkomst inte tillåts för kontot.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Skärm bild som visar att inställnings behållarens offentliga åtkomst nivå är blockerad när offentlig åtkomst tillåts":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppdatera den offentliga åtkomst nivån för en eller flera behållare med Azure CLI anropar du kommandot [AZ Storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) . Auktorisera den här åtgärden genom att skicka in din konto nyckel, en anslutnings sträng eller en signatur för delad åtkomst (SAS). Den [angivna ACL](/rest/api/storageservices/set-container-acl) -åtgärden för behållare som anger behållarens offentliga åtkomst nivå stöder inte auktorisering med Azure AD. Mer information finns i [behörigheter för att anropa blob-och Queue data-åtgärder](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

I följande exempel anges den offentliga åtkomst inställningen för en behållare för att aktivera anonym åtkomst till behållaren och dess blobbar. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

När offentlig åtkomst inte tillåts för lagrings kontot kan inte behållarens offentliga åtkomst nivå anges. Om du försöker ange behållarens offentliga åtkomst nivå uppstår ett fel som anger att offentlig åtkomst inte tillåts på lagrings kontot.

---

## <a name="check-the-container-public-access-setting"></a>Kontrol lera inställningen för offentlig åtkomst för behållare

Om du vill kontrol lera inställningen för offentlig åtkomst för en eller flera behållare kan du använda Azure Portal, PowerShell, Azure CLI, en av Azure Storage-klient biblioteken eller Azure Storage resurs leverantören. I följande avsnitt finns några exempel.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Kontrol lera inställningen för offentlig åtkomst för en enskild behållare

Om du vill hämta offentlig åtkomst nivå för en eller flera behållare med Azure CLI anropar du kommandot [AZ Storage container show permission](/cli/azure/storage/container#az-storage-container-show-permission) . Auktorisera den här åtgärden genom att skicka in din konto nyckel, en anslutnings sträng eller en signatur för delad åtkomst (SAS). Åtgärden [Hämta behållar-ACL](/rest/api/storageservices/get-container-acl) som returnerar en behållares offentliga åtkomst nivå stöder inte auktorisering med Azure AD. Mer information finns i [behörigheter för att anropa blob-och Queue data-åtgärder](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

I följande exempel läses den offentliga åtkomst inställningen för en behållare. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Kontrol lera inställningen för offentlig åtkomst för en uppsättning behållare

Det går att kontrol lera vilka behållare i ett eller flera lagrings konton som har kon figurer ATS för offentlig åtkomst genom att lista behållarna och kontrol lera inställningen för offentlig åtkomst. Den här metoden är ett användbart alternativ när ett lagrings konto inte innehåller ett stort antal behållare, eller när du kontrollerar inställningen för ett litet antal lagrings konton. Prestanda kan dock bli lidande om du försöker räkna upp ett stort antal behållare.

I följande exempel används PowerShell för att hämta den offentliga åtkomst inställningen för alla behållare i ett lagrings konto. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Nästa steg

- [Förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md)
- [Få åtkomst till offentliga behållare och blobbar anonymt med .NET](anonymous-read-access-client.md)
- [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md)
