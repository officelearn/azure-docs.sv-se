---
title: Vara värd för en statisk webbplats i Azure Storage
description: Lär dig hur du visar statiskt innehåll (HTML-, CSS-, JavaScript- och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247018"
---
# <a name="host-a-static-website-in-azure-storage"></a>Vara värd för en statisk webbplats i Azure Storage

Du kan hantera statiskt innehåll (HTML-, CSS-, JavaScript- och bildfiler) direkt från en behållare i ett Azure Storage GPv2-konto. Mer information finns [i Statisk webbplatshosting i Azure Storage](storage-blob-static-website.md).

Den här artikeln visar hur du aktiverar statisk webbplatshosting med hjälp av Azure-portalen, Azure CLI eller PowerShell.

## <a name="enable-static-website-hosting"></a>Aktivera statisk webbplatshosting

Statisk webbhotell är en funktion som du måste aktivera på lagringskontot.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

2. Leta upp ditt lagringskonto och visa kontoöversikten.

3. Välj **Statisk webbplats** för att visa konfigurationssidan för statiska webbplatser.

4. Välj **Aktivera** för att aktivera hantering av statisk webbplats för lagringskontot.

5. Ange en standardindexsida i fältet **Indexdokumentnamn** (till exempel *index.html*). 

   Standardindexsidan visas när en användare navigerar till roten för den statiska webbplatsen.  

6. I fältet **Feldokumentsökväg** anger du en standardfelsida (till exempel: *404.html*). 

   Standardfelsidan visas när en användare försöker navigera till en sida som inte finns på den statiska webbplatsen.

7. Klicka på **Spara**. Azure-portalen visar nu slutpunkten för den statiska webbplatsen. 

    ![Aktivera hantering av statisk webbplats för ett lagringskonto](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

Du kan aktivera statisk webbplatshosting med hjälp av [AZURE Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Öppna först [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)eller om du har [installerat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI lokalt öppnar du ett kommandokonsolprogram som Windows PowerShell.

2. Om din identitet är kopplad till mer än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på lagringskontot som ska vara värd för din statiska webbplats.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` platshållarvärdet med id:t för din prenumeration.

3. Aktivera statisk webbplatshosting.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * Ersätt `<error-document-name>` platshållaren med namnet på det feldokument som visas för användarna när en webbläsare begär en sida på webbplatsen som inte finns.

   * Ersätt `<index-document-name>` platshållaren med namnet på indexdokumentet. Det här dokumentet är ofta "index.html".

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

Du kan aktivera statisk webbplatshosting med hjälp av Azure PowerShell-modulen.

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

4. Om din identitet är kopplad till mer än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på lagringskontot som ska vara värd för din statiska webbplats.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` platshållarvärdet med id:t för din prenumeration.

5. Hämta den lagringskontokontext som definierar det lagringskonto som du vill använda.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

6. Aktivera statisk webbplatshosting.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Ersätt `<error-document-name>` platshållaren med namnet på det feldokument som visas för användarna när en webbläsare begär en sida på webbplatsen som inte finns.

   * Ersätt `<index-document-name>` platshållaren med namnet på indexdokumentet. Det här dokumentet är ofta "index.html".

---

## <a name="upload-files"></a>Överföra filer 

### <a name="portal"></a>[Portal](#tab/azure-portal)

De här instruktionerna visar hur du laddar upp filer med hjälp av den version av Storage Explorer som visas i Azure-portalen. Du kan dock också använda den version av [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) som körs utanför Azure-portalen. Du kan använda [AzCopy,](../common/storage-use-azcopy-v10.md)PowerShell, CLI eller något anpassat program som kan ladda upp filer till **$web** behållaren för ditt konto. En steg-för-steg-självstudiekurs som laddar upp filer med Visual Studio-kod finns i [Självstudiekurs: Vara värd för en statisk webbplats på Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Välj **Storage Explorer (förhandsgranskning)**.

2. Expandera noden **BLOB CONTAINERS** och markera sedan **$web** behållaren.

3. Välj **knappen Ladda upp** för att ladda upp filer.

   ![Överföra filer](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Om du tänker visa innehållet i filen ska du se till att filens innehållstyp är inställd på `text/html`. 

   ![Kontrollera innehållstyper](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer ställer automatiskt `text/html` in den här egenskapen `.html`på för allmänt erkända tillägg, till exempel . Men i vissa fall måste du ställa in detta själv. Om du inte ställer in `text/html`den här egenskapen på uppmanas användarna att hämta filen i stället för att återge innehållet. Om du vill ange den här egenskapen högerklickar du på filen och klickar sedan på **Egenskaper**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Överför objekt till *$web* behållaren från en källkatalog.

> [!NOTE]
> Om du använder Azure Cloud Shell kontrollerar `\` du att du `$web` lägger till `\$web`ett escape-tecken när du refererar till behållaren (till exempel: ). Om du använder en lokal installation av Azure CLI behöver du inte använda escape-tecknet.

Det här exemplet förutsätter att du kör kommandon från Azure Cloud Shell-session.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt `<source-path>` platshållaren med en sökväg till platsen för de filer som du vill överföra.

> [!NOTE]
> Om du använder en platsinstallation av Azure CLI kan du använda sökvägen till valfri `C:\myFolder`plats på den lokala datorn (till exempel: .
>
> Om du använder Azure Cloud Shell måste du referera till en filresurs som är synlig för Cloud Shell. Den här platsen kan vara filresursen för själva molnresursen eller en befintlig filresurs som du monterar från Cloud Shell. Mer information om hur du gör detta finns [i Beständiga filer i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Överför objekt till *$web* behållaren från en källkatalog.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Ersätt `<path-to-file>` platshållarvärdet med den fullständigt kvalificerade sökvägen till den `C:\temp\index.html`fil som du vill överföra (till exempel: ).

* Ersätt `<blob-name>` platshållarvärdet med det namn som du vill ge `index.html`den resulterande blobben (till exempel: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Hitta webbadressen med hjälp av Azure-portalen

Du kan visa sidorna på din webbplats från en webbläsare med hjälp av den offentliga webbadressen till webbplatsen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

<a id="portal-find-url" />

Välj **Statisk webbplats**i fönstret som visas bredvid kontoöversiktssidan för ditt lagringskonto . Webbadressen till webbplatsen visas i fältet **Primär slutpunkt.**

![Mätmått för Azure Storage-statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

Hitta den offentliga webbadressen till din statiska webbplats med hjälp av följande kommando:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

* Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

Hitta den offentliga webbadressen till din statiska webbplats med hjälp av följande kommando:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen.

* Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Aktivera mått på statiska webbplatssidor

När du har aktiverat mått rapporteras trafikstatistik för filer i **$web** behållaren i måttinstrumentpanelen.

1. Klicka på Mått under avsnittet **Övervaka** på **lagringskontomenyn.**

   > [!div class="mx-imgBorder"]
   > ![Länken Mätvärden](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Måttdata genereras genom att ansluta till olika mått API:er. Portalen visar bara API-medlemmar som används inom en viss tidsram för att bara fokusera på medlemmar som returnerar data. För att säkerställa att du kan välja den nödvändiga API-medlemmen är det första steget att utöka tidsramen.

2. Klicka på tidsramen, välj en tidsram och klicka sedan på **Använd**.

   ![Azure Storage statiska webbplatser mått tidintervall](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Välj **Blob** i listrutan *Namnområde.*

   ![Azure Storage statiska webbplatser mått namnområde](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Välj sedan **egress-måttet.**

   ![Mätmått för Azure Storage-statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Välj **Summa** i *aggregeringsväljaren.*

   ![Azure Storage statiska webbplatser mått aggregering](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klicka på knappen **Lägg till filter** och välj **API-namn** i *egenskapsväljaren.*

   ![Api-namn för Azure Storage-statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Markera rutan bredvid **GetWebContent** i *värdeväljaren* för att fylla i måttrapporten.

   ![Azure Storage statiska webbplatser mått GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > **GetWebContent-kryssrutan** visas bara om API-medlemmen har använts inom en viss tidsram. Portalen visar bara API-medlemmar som används inom en viss tidsram för att bara fokusera på medlemmar som returnerar data. Om du inte hittar en specifik API-medlem i den här listan expanderar du tidsramen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar en anpassad domän med din statiska webbplats. Se [Mappa en anpassad domän till en slutpunkt för Azure Blob Storage](storage-custom-domain-name.md).

