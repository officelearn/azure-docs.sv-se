---
title: Vara värd för en statisk webbplats i Azure Storage
description: Lär dig mer om att hantera statiskt innehåll (HTML, CSS, JavaScript och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 61477767c59dd521e3f46db4445238a5a1ea759e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071441"
---
# <a name="host-a-static-website-in-azure-storage"></a>Vara värd för en statisk webbplats i Azure Storage

Du kan hantera statiskt innehåll (HTML, CSS, JavaScript och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto. Mer information finns i [värd för statisk webbplats i Azure Storage](storage-blob-static-website.md).

Den här artikeln visar hur du aktiverar statisk webbplats som är värd för med hjälp av Azure-portalen, Azure CLI eller PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

En självstudiekurs som steg för steg finns i [självstudien: Hantera en statisk webbplats på Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

När du har aktiverat statisk webbplats som är värd för kan du visa sidor på webbplatsen från en webbläsare med hjälp av en offentlig URL för webbplatsen.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Hitta URL: webbplatsen med hjälp av Azure portal

I fönstret som visas bredvid konto översiktssidan för ditt lagringskonto väljer **statisk webbplats**. Webbadressen till webbplatsen som visas i den **primära slutpunkten** fält.

![Azure Storage serverstatiska webbplatser mått mått](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Du kan aktivera statisk webbplats som är värd för med hjälp av den [Azure kommandoradsgränssnitt (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Först, öppnar den [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), eller om du har [installerat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI lokalt, öppna ett kommando-konsolprogram som Windows PowerShell.

2. Installera tillägget storage förhandsversion kommandofönstret som du har öppnat.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. Om din identitet är associerad med mer än en prenumeration, anger du dina aktiv prenumeration prenumeration på det lagringskonto som är värd för din statiska webbplats.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt den `<subscription-id>` platshållarvärdet med ID: T för din prenumeration.

4. Aktivera som är värd för statisk webbplats.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * Ersätt den `<error-document-name>` platshållare med namnet på fel-dokumentet som ska visas för användare när en webbläsare begär en sida på webbplatsen som inte finns.

   * Ersätt den `<index-document-name>` platshållare med namnet på indexet dokumentet. Det här dokumentet är ofta ”index.html”.

5. Överföra objekt till den *$web* behållare från en källkatalog.

   > [!NOTE]
   > Om du använder Azure Cloud Shell, bör du lägga till en `\` escape-tecknet när det gäller den `$web` behållare (till exempel: `\$web`). Om du använder en lokal installation av Azure CLI, behöver du inte använda escape-tecken.

   Det här exemplet förutsätts att du kör kommandon från Azure Cloud Shell-sessionen.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * Ersätt den `<source-path>` med en sökväg till platsen där filerna som du vill överföra.

   > [!NOTE]
   > Om du använder en plats-installation av Azure CLI så du kan använda sökvägen till valfri plats på den lokala datorn (till exempel: `C:\myFolder`.
   >
   > Om du använder Azure Cloud Shell får du att referera till en filresurs som är synlig för Cloud Shell. Den här platsen kan vara filresursen i molnet dela själva eller en befintlig resurs som du har monterat från Cloud Shell. Information om hur du gör detta finns i [spara filer i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Hitta URL: webbplatsen med hjälp av Azure CLI

Du kan visa innehåll från en webbläsare med hjälp av en offentlig URL för webbplatsen.

Hitta URL: en med hjälp av följande kommando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt den `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.

<a id="powershell" />

## <a name="use-powershell"></a>Använd PowerShell

Du kan aktivera statisk webbplats som är värd för med hjälp av Azure PowerShell-modulen.

1. Öppna ett kommandofönster i Windows PowerShell.

2. Kontrollera att du har Azure PowerShell-modulen Az version 0.7 eller senare.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

3. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

4. Om din identitet är associerad med mer än en prenumeration, anger du dina aktiv prenumeration prenumeration på det lagringskonto som är värd för din statiska webbplats.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt den `<subscription-id>` platshållarvärdet med ID: T för din prenumeration.

5. Hämta lagringskontokontexten som definierar det lagringskonto som du vill använda.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersätt den `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

6. Aktivera som är värd för statisk webbplats.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Ersätt den `<error-document-name>` platshållare med namnet på fel-dokumentet som ska visas för användare när en webbläsare begär en sida på webbplatsen som inte finns.

   * Ersätt den `<index-document-name>` platshållare med namnet på indexet dokumentet. Det här dokumentet är ofta ”index.html”.

7. Överföra objekt till den *$web* behållare från en källkatalog.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Ersätt den `<path-to-file>` platshållarvärdet med den fullständiga sökvägen till filen som du vill ladda upp (till exempel: `C:\temp\index.html`).

   * Ersätt den `<blob-name>` platshållarvärdet med namnet som du vill ge den resulterande blobben (till exempel: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Hitta URL: webbplatsen med hjälp av PowerShell

Du kan visa innehåll från en webbläsare med hjälp av en offentlig URL för webbplatsen.

Hitta URL: en med hjälp av följande kommando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Ersätt den `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Aktivera mätvärden på statisk webbplatssidor

När du har aktiverat mått kan trafik statistik på filer i den **$web** behållare rapporteras i instrumentpanelen för hälsostatistik.

1. Klicka på **inställningar** > **övervakning** > **mått**.

   Måttdata genereras av anslutning till olika mått API: er. Portalen visar endast API-medlemmar som används inom en angiven tidsperiod för att endast fokusera på medlemmar som returnerar data. För att kontrollera att du befinner dig kan du välja den nödvändiga API-medlemmen, är det första steget att expandera tidsramen.

2. Klicka på knappen tidsram och välj **senaste 24 timmarna** och klicka sedan på **tillämpa**.

   ![Azure Storage-mått serverstatiska webbplatser tidsintervall](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Välj **Blob** från den *Namespace* nedrullningsbar listruta.

   ![Azure Storage serverstatiska webbplatser mått namnområde](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Välj sedan den **utgående** mått.

   ![Azure Storage serverstatiska webbplatser mått mått](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Välj **summan** från den *aggregering* väljare.

   ![Azure Storage serverstatiska webbplatser mått aggregering](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klicka på den **Lägg till filter** knappen och väljer **API-namn** från den *egenskapen* väljare.

   ![Azure Storage serverstatiska webbplatser mått-API-namn](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Markera rutan bredvid **GetWebContent** i den *värden* att fylla i mått-rapporten.

   ![Azure Storage-serverstatiska webbplatser mått GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)
* [Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domännamn för din blob eller web-slutpunkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Skapa din första serverlösa webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudie: Värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
