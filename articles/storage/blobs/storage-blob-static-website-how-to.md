---
title: Vara värd för en statisk webbplats i Azure Storage
description: Lär dig att hantera statiskt innehåll (HTML, CSS, Java Script och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 5664bf1eaee85d2492601ef00968d9b17d857abb
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900486"
---
# <a name="host-a-static-website-in-azure-storage"></a>Vara värd för en statisk webbplats i Azure Storage

Du kan hantera statiskt innehåll (HTML, CSS, Java Script och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto. Mer information finns i den [statiska webbplatsen som är värd för i Azure Storage](storage-blob-static-website.md).

Den här artikeln visar hur du aktiverar statisk webbplats värd med hjälp av Azure Portal, Azure CLI eller PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

En steg-för-steg-guide finns i [Självstudier: Hantera en statisk webbplats på Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

När du har aktiverat statisk webbplats värd kan du visa sidorna på din webbplats från en webbläsare med hjälp av den offentliga URL: en för webbplatsen.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Hitta webbplats-URL: en med hjälp av Azure Portal

I rutan som visas bredvid sidan konto översikt för ditt lagrings konto väljer du **statisk webbplats**. URL: en för din webbplats visas i fältet **primär slut punkt** .

![Mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Du kan aktivera statisk webbplats som värd med hjälp av [kommando rads gränssnittet för Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Öppna först [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), eller om du har [installerat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` Ersätt placeholder-värdet med ID: t för din prenumeration.

3. Aktivera statisk webbplats värd.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * `<error-document-name>` Ersätt plats hållaren med namnet på fel dokumentet som visas för användarna när en webbläsare begär en sida på din webbplats som inte finns.

   * `<index-document-name>` Ersätt plats hållaren med namnet på index dokumentet. Det här dokumentet är vanligt vis "index. html".

4. Ladda upp objekt till *$Web* containern från en käll katalog.

   > [!NOTE]
   > Om du använder Azure Cloud Shell, se till att lägga till ett `\` escape-tecken när du refererar `$web` till behållaren (till exempel `\$web`:). Om du använder en lokal installation av Azure CLI behöver du inte använda escape-tecken.

   I det här exemplet förutsätter vi att du kör kommandon från Azure Cloud Shell-sessionen.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * `<source-path>` Ersätt plats hållaren med en sökväg till platsen för de filer som du vill ladda upp.

   > [!NOTE]
   > Om du använder en plats installation av Azure CLI kan du använda sökvägen till valfri plats på den lokala datorn (till exempel: `C:\myFolder`.
   >
   > Om du använder Azure Cloud Shell måste du referera till en fil resurs som är synlig för Cloud Shell. Den här platsen kan vara fil resursen för själva moln resursen eller en befintlig fil resurs som du monterar från Cloud Shell. Information om hur du gör detta finns i [Spara filer i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Hitta webbplats-URL: en med hjälp av Azure CLI

Du kan visa innehåll från en webbläsare genom att använda den offentliga URL: en för webbplatsen.

Hitta URL: en med hjälp av följande kommando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

* `<resource-group-name>` Ersätt placeholder-värdet med namnet på din resurs grupp.

<a id="powershell" />

## <a name="use-powershell"></a>Använd PowerShell

Du kan aktivera statisk webbplats värd med hjälp av Azure PowerShell-modulen.

1. Öppna ett Windows PowerShell-kommando fönster.

2. Kontrol lera att du har Azure PowerShell modul AZ version 0,7 eller senare.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

3. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

4. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` Ersätt placeholder-värdet med ID: t för din prenumeration.

5. Hämta lagrings konto kontexten som definierar det lagrings konto som du vill använda.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` Ersätt placeholder-värdet med namnet på din resurs grupp.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

6. Aktivera statisk webbplats värd.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * `<error-document-name>` Ersätt plats hållaren med namnet på fel dokumentet som visas för användarna när en webbläsare begär en sida på din webbplats som inte finns.

   * `<index-document-name>` Ersätt plats hållaren med namnet på index dokumentet. Det här dokumentet är vanligt vis "index. html".

7. Ladda upp objekt till *$Web* containern från en käll katalog.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Ersätt placeholder-värdet med den fullständigt kvalificerade sökvägen till den fil som du vill överföra (till exempel: `C:\temp\index.html`). `<path-to-file>`

   * Ersätt placeholder-värdet med det namn som du vill ge den resulterande blobben (till exempel: `index.html`). `<blob-name>`

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Hitta webbplats-URL: en med hjälp av PowerShell

Du kan visa innehåll från en webbläsare genom att använda den offentliga URL: en för webbplatsen.

Hitta URL: en med hjälp av följande kommando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>` Ersätt placeholder-värdet med namnet på din resurs grupp.

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Aktivera mått på statiska webbplats sidor

När du har aktiverat mått rapporteras trafik statistik på filer i **$Web** -behållaren på instrument panelen mått.

1. Klicka på **Inställningar** > **övervaknings** > **mått**.

   Mått data genereras genom att ansluta till olika mått-API: er. Portalen visar bara de API-medlemmar som används inom en bestämd tidsram för att endast fokusera på medlemmar som returnerar data. Det första steget är att expandera tids ramen för att säkerställa att du kan välja den nödvändiga API-medlemmen.

2. Klicka på knappen tidsram och välj de **senaste 24 timmarna** och klicka sedan på **Använd**.

   ![Tidsintervallet för mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Välj **BLOB** från List rutan *namn område* .

   ![Namn område för Azure Storage statiska webbplats mått](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Välj sedan **utgående** mått.

   ![Mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Välj **Sum** från *agg regerings* väljaren.

   ![Agg regerings mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klicka på knappen **Lägg till filter** och välj **API-namn** från *egenskaps* väljaren.

   ![Azure Storage statiska webbplatsers mått-API-namn](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Markera kryss rutan bredvid **GetWebContent** i *värde* väljaren för att fylla i mått rapporten.

   ![Azure Storage statiska webbplats mått GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)
* [Använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domän namn för din BLOB eller webb slut punkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Bygg din första server lös webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudier: Vara värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
