---
title: Konfigurera anonym offentlig Läs behörighet för behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du aktiverar eller inaktiverar anonym åtkomst till BLOB-data för lagrings kontot. Ange offentlig åtkomst inställning för behållare för att göra behållare och blobbar tillgängliga för anonym åtkomst.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209939"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurera anonym offentlig Läs behörighet för behållare och blobbar

Azure Storage stöder anonym offentlig Läs behörighet för behållare och blobbar. Som standard måste alla begär anden till en behållare och dess blobbar godkännas med hjälp av antingen Azure Active Directory (Azure AD) eller autentisering med delad nyckel. När du konfigurerar en behållares inställning för offentlig åtkomst nivå för att tillåta anonym åtkomst, kan klienterna läsa data i den behållaren utan att auktorisera begäran.

> [!WARNING]
> När en behållare har kon figurer ATS för offentlig åtkomst kan alla klienter läsa data i behållaren. Offentlig åtkomst innebär en potentiell säkerhets risk, så om ditt scenario inte kräver det rekommenderar Microsoft att du inaktiverar det för lagrings kontot. Mer information finns i [förhindra anonym offentlig Läs behörighet till behållare och blobbar](anonymous-read-access-prevent.md).

Om du vill konfigurera offentlig åtkomst för en behållare måste du utföra två steg:

1. Aktivera offentlig åtkomst för lagrings kontot
1. Konfigurera behållarens inställningar för offentlig åtkomst

Den här artikeln beskriver hur du konfigurerar anonym offentlig Läs behörighet för en behållare och dess blobbar. Information om hur du kommer åt BLOB-data anonymt från ett klient program finns i [komma åt offentliga behållare och blobbar anonymt med .net](anonymous-read-access-client.md).

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>Aktivera eller inaktivera offentlig Läs behörighet för ett lagrings konto

Som standard är offentlig åtkomst aktive rad för ett lagrings konto. Inaktive ring av offentlig åtkomst förhindrar all anonym åtkomst till behållare och blobbar i det kontot. För förbättrad säkerhet rekommenderar Microsoft att du inaktiverar offentlig åtkomst för dina lagrings konton, såvida inte ditt scenario kräver att användare får åtkomst till BLOB-resurser anonymt.

> [!WARNING]
> Om du inaktiverar offentlig åtkomst för ett lagrings konto åsidosätts de offentliga åtkomst inställningarna för alla behållare i lagrings kontot. När offentlig åtkomst är inaktive rad för lagrings kontot kommer alla framtida anonyma begär anden till kontot att Miss förväntas.

Om du vill aktivera eller inaktivera offentlig åtkomst för ett lagrings konto använder du Azure Portal eller Azure CLI för att konfigurera kontots egenskap **blobPublicAccess** . Den här egenskapen är tillgänglig för alla lagrings konton som skapas med Azure Resource Manager distributions modell. Mer information finns i [Översikt över lagrings konto](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Följ dessa steg om du vill aktivera eller inaktivera offentlig åtkomst för ett lagrings konto i Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Leta upp **konfigurations** inställningen under **Inställningar**.
1. Ange **offentlig åtkomst till BLOB** för **inaktiverade** eller **aktiverade**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Skärm bild som visar hur du aktiverar eller inaktiverar offentlig BLOB-åtkomst för kontot":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill aktivera eller inaktivera offentlig åtkomst för ett lagrings konto med Azure CLI måste du först hämta resurs-ID för ditt lagrings konto genom att anropa kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) . Anropa sedan kommandot [AZ Resource Update](/cli/azure/resource#az-resource-update) för att ange **allowBlobPublicAccess** -egenskapen för lagrings kontot. Om du vill aktivera offentlig åtkomst anger du egenskapen **allowBlobPublicAccess** till true. Om du vill inaktivera ställer du in det på **falskt**.

I följande exempel inaktive ras offentlig BLOB-åtkomst för lagrings kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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

Om du vill kontrol lera om offentlig åtkomst är aktive rad med Azure CLI anropar du kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) och frågar efter egenskapen **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> Att inaktivera offentlig åtkomst för ett lagrings konto påverkar inte några statiska webbplatser som finns på lagrings kontot. **$Web** containern alltid är offentligt tillgänglig.

## <a name="check-the-public-access-setting-for-a-storage-account"></a>Kontrol lera inställningen för offentlig åtkomst för ett lagrings konto

Hämta värdet för egenskapen **allowBlobPublicAccess** för att kontrol lera den offentliga åtkomst inställningen för ett lagrings konto. Om du vill kontrol lera den här egenskapen för ett stort antal lagrings konton samtidigt använder du Azure Resource Graph Explorer.

Egenskapen **allowBlobPublicAccess** har inte angetts som standard och returnerar inte något värde förrän du uttryckligen anger det. Lagrings kontots standardinställningar för att tillåta offentlig åtkomst när egenskap svärdet är null.

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>Kontrol lera inställningen för offentlig åtkomst för ett enda lagrings konto

Om du vill kontrol lera den offentliga åtkomst inställningen för ett enda lagrings konto med hjälp av Azure CLI anropar du kommandot [AZ Resource show](/cli/azure/resource#az-resource-show) och fråga efter egenskapen **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>Kontrol lera inställningen för offentlig åtkomst för en uppsättning lagrings konton

Om du vill kontrol lera inställningen för offentlig åtkomst i en uppsättning lagrings konton med optimala prestanda kan du använda Azures resurs diagram Utforskaren i Azure Portal. Mer information om hur du använder resurs diagram Utforskaren finns i [snabb start: kör din första resurs diagram fråga med Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Om du kör följande fråga i resurs diagram Utforskaren returneras en lista över lagrings konton och värdet för egenskapen **allowBlobPublicAccess** för varje konto visas:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Ange offentlig åtkomst nivå för en behållare

Om du vill ge anonyma användare Läs åtkomst till en behållare och dess blobbar, aktiverar du först offentlig åtkomst till lagrings kontot och anger sedan behållarens offentliga åtkomst nivå. Om offentlig åtkomst är inaktive rad för lagrings kontot kommer du inte att kunna konfigurera offentlig åtkomst för en behållare.

När offentlig åtkomst har Aktiver ATS för ett lagrings konto kan du konfigurera en behållare med följande behörigheter:

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

När offentlig åtkomst har inaktiverats för lagrings kontot går det inte att ange en behållares offentliga åtkomst nivå. Om du försöker ange behållarens offentliga åtkomst nivå ser du att inställningen är inaktive rad eftersom offentlig åtkomst är förbjuden för kontot.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Skärm bild som visar att inställnings behållaren offentlig åtkomst nivå är blockerad när offentlig åtkomst är inaktive rad":::

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

När offentlig åtkomst har inaktiverats för lagrings kontot går det inte att ange en behållares offentliga åtkomst nivå. Om du försöker ange behållarens offentliga åtkomst nivå uppstår ett fel som anger att offentlig åtkomst inte tillåts på lagrings kontot.

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