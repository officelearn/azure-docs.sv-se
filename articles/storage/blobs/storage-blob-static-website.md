---
title: Värd för statisk webbplats i Azure Storage
description: Azure Storage statisk webbplats som är värd för, ger en kostnadseffektiv och skalbar lösning som värd för moderna webbprogram.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/19/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f42237c1bf5c4782f47254c253d1d40289b65099
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670559"
---
# <a name="static-website-hosting-in-azure-storage"></a>Värd för statisk webbplats i Azure Storage
Azure Storage GPv2-konton kan du hantera statiskt innehåll (HTML, CSS, JavaScript och bildfiler) direkt från en storage-behållare med namnet *$web*. Dra nytta av värd i Azure Storage kan du använda arkitekturer utan server, inklusive [Azure Functions](/azure/azure-functions/functions-overview) och andra PaaS-tjänster.

Till skillnad från som är värd för statisk webbplats, dynamiska webbplatser som är beroende av serverkod är bäst hanteras med hjälp av [Azure App Service](/azure/app-service/overview).

## <a name="how-does-it-work"></a>Hur fungerar det?
När du aktiverar statisk webbplats som är värd för ditt storage-konto du väljer namnet på din standardfil och du kan också ange en sökväg till en custom 404-sida. När funktionen är aktiverad, en behållare med namnet *$web* skapas om den inte redan finns.

Filer i den *$web* behållare är:

- hanteras via anonyma förfrågningar
- endast tillgängligt via objektet läsåtgärder
- skiftlägeskänsligt
- tillgängliga till offentlig webbplats efter det här mönstret:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- tillgängliga via en slutpunkt för Blob-lagring som följer det här mönstret:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Du kan använda Blob storage-slutpunkt för att ladda upp filer. Exempelvis kan överföra filen till den här platsen:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

är tillgängligt i webbläsaren på en plats så här:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

Valda standardfilnamnet används vid roten och eventuella underkataloger när ett filnamn inte har angetts. Om servern returnerar ett 404 och du inte anger en feldokumentets sökväg, returneras ett 404 standardsidan för användaren.

## <a name="cdn-and-ssl-support"></a>CDN- och SSL-stöd

För att göra din statiska webbplats filer tillgängliga via HTTPS, se avsnittet [använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md). Som en del av den här processen måste du *peka CDN-nätverket till webbslutpunkt* till skillnad från blob-slutpunkten. Du kan behöva vänta några minuter innan innehållet är synligt när CDN-konfigurationen inte utförs omedelbart.


## <a name="custom-domain-names"></a>Egna domännamn

Du kan [konfigurera ett anpassat domännamn för Azure Storage-kontot](storage-custom-domain-name.md) att tillgängliggöra din statiska webbplats via en anpassad domän. För en grundlig genomgång som är värd för din domän på [Azure, se värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prissättning
Som är värd för statisk webbplats tillhandahålls utan extra kostnad. Mer information om priser för Azure Blob Storage finns i [Prissättningssidan för Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Snabbstart

### <a name="azure-portal"></a>Azure Portal
Börja genom att öppna Azure-portalen på https://portal.azure.com och gå igenom följande steg på din GPv2-konto:

1. Klicka på **inställningar**
2. Klicka på **statisk webbplats**
3. Ange en *indexdokumentnamn*. (Gemensamt värde är *index.html)*
4. Du kan också ange en *feldokument* till en custom 404-sida. (Gemensamt värde är *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Därefter ladda upp dina tillgångar till den *$web* behållare via Azure portal eller med den [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) att ladda upp hela kataloger. Se till att inkludera en fil som matchar den *indexdokumentnamn* du valde när du aktiverar funktionen.

Slutligen navigera till din webbslutpunkt för att testa din webbplats.

### <a name="azure-cli"></a>Azure CLI
Installera tillägget storage förhandsversion:

```azurecli-interactive
az extension add --name storage-preview
```
När det gäller flera prenumerationer, ange din CLI till prenumerationen för GPv2-lagringskonto som du vill aktivera:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Aktivera funktionen. Se till att ersätta alla platshållarvärdena, inklusive parenteser med dina egna värden:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Fråga för web slutpunkts-URL:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Överföra objekt till den *$web* behållare från en källkatalog. Se till att korrekt escape-referensen till den *$web* behållare i kommandot. Till exempel om du använder Azure CLI från CloudShell i Azure-portalen kan undvika den *$web* behållare som visas:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Distribution

Följande: metoder för att distribuera innehåll till en lagringsbehållare

- [AzCopy](../common/storage-use-azcopy.md)
- [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
- [Visual Studio Code-tillägg](https://code.visualstudio.com/tutorials/static-website/getting-started)

I samtliga fall måste du kopiera filer till den *$web* behållare.

## <a name="metrics"></a>Mått

Om du vill aktivera mått på sidorna statisk webbplats klickar du på **inställningar** > **övervakning** > **mått**.

Måttdata genereras av anslutning till olika mått API: er. Portalen visar endast API-medlemmar som används inom en angiven tidsperiod för att endast fokusera på medlemmar som returnerar data. För att kontrollera att du kan välja den nödvändiga API-medlemmen, är det första steget att expandera tidsperioden.

Klicka på knappen tidsram och välj **senaste 24 timmarna** och klicka sedan på **tillämpa**

![Azure Storage-mått serverstatiska webbplatser tidsintervall](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Välj sedan **Blob** från den *Namespace* nedrullningsbar listruta.

![Azure Storage serverstatiska webbplatser mått namnområde](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Välj sedan den **utgående** mått.

![Azure Storage serverstatiska webbplatser mått mått](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Välj **summan** från den *aggregering* väljare.

![Azure Storage serverstatiska webbplatser mått aggregering](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Klicka sedan på den **Lägg till filter** knappen och väljer **API-namn** från den *egenskapen* väljare.

![Azure Storage serverstatiska webbplatser mått-API-namn](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Slutligen, markera kryssrutan bredvid **GetWebContent** i den *värden* att fylla i mått-rapporten.

![Azure Storage-serverstatiska webbplatser mått GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

När du har aktiverat, trafik statistik på filer i den *$web* behållare rapporteras i instrumentpanelen för hälsostatistik.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Är funktionen serverstatiska webbplatser tillgänglig för alla typer av konton?**  
Nej, som är värd för statisk webbplats är endast tillgänglig i GPv2-konton för standardlagring.

**Är lagring VNET och brandväggsregler som stöds på den nya web-slutpunkten?**  
Ja, den nya webbslutpunkt följs virtuellt nätverk och brandvägg reglerna som konfigurerats för lagringskontot.

**Är webbslutpunkt skiftlägeskänsligt?**  
Ja, web-slutpunkt är skiftlägeskänsliga precis som blob-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
* [Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över https](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domännamn för din blob eller web-slutpunkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Skapa din första serverlösa webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudier: Värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
