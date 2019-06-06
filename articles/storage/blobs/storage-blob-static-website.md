---
title: Värd för statisk webbplats i Azure Storage
description: Azure Storage statisk webbplats som är värd för, ger en kostnadseffektiv och skalbar lösning som värd för moderna webbprogram.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427973"
---
# <a name="static-website-hosting-in-azure-storage"></a>Värd för statisk webbplats i Azure Storage

Du kan hantera statiskt innehåll (HTML, CSS, JavaScript och bildfiler) direkt från en storage-behållare med namnet *$web*. Som är värd för ditt innehåll i Azure Storage kan du använda arkitekturer utan server som innehåller [Azure Functions](/azure/azure-functions/functions-overview) och annan plattform som en tjänst (PaaS)-tjänster.

> [!NOTE]
> Om din webbplats är beroende av serverkod, använda [Azure App Service](/azure/app-service/overview) i stället.

## <a name="setting-up-a-static-website"></a>Konfigurera en statisk webbplats

Som är värd för statisk webbplats är en funktion som du måste aktivera för lagringskontot.

Välj namnet på din standardfil för att aktivera statisk webbplats som är värd för, och du kan också ange en sökväg till en custom 404-sida. Om en blob storage-behållare med namnet **$web** inte redan finns på kontot, en skapas åt dig. Lägga till filer i din webbplats i den här behållaren.

Stegvisa anvisningar finns i [vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md).

![Azure Storage serverstatiska webbplatser mått mått](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Filer i den **$web** behållare är skiftlägeskänsliga, hanteras via förfrågningar om anonym åtkomst och är bara tillgänglig via läsåtgärder.

## <a name="uploading-content"></a>Överför innehåll

Du kan använda någon av dessa verktyg för att överföra innehåll till den **$web** behållare:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-tillägg](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Visa innehåll

Användare kan visa webbplatsens innehåll från en webbläsare med hjälp av en offentlig URL för webbplatsen. Du hittar URL: en med hjälp av Azure-portalen, Azure CLI eller PowerShell. Använd den här tabellen som vägledning.

|Verktyget| Riktlinjer |
|----|----|
|**Azure Portal** | [Hitta URL: webbplatsen med hjälp av Azure portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Hitta URL: webbplatsen med hjälp av Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell-modul** | [Hitta URL: webbplatsen med hjälp av PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

URL: en för webbplatsen innehåller en regional kod. Till exempel URL: en `https://contosoblobaccount.z22.web.core.windows.net/` innehåller regional kod `z22`.

Medan den koden måste vara URL: en, är det endast för intern användning och du behöver inte använda den kod i något annat sätt.

Index-dokumentet som du anger när du aktiverar som är värd för statisk webbplats, visas när användaren öppnar platsen inte anger en specifik fil (till exempel: `https://contosoblobaccount.z22.web.core.windows.net`).  

Om servern returnerar ett 404-fel, och du har inte angett ett fel-dokument när du har aktiverat på webbplatsen, returneras ett 404 standardsidan för användaren.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Effekten av inställningen offentlig åtkomstnivå av webbehållaren

Du kan ändra offentlig åtkomstnivå för den **$web** behållare, men detta påverkar inte primär webbplatsslutpunkt eftersom dessa filer hanteras via anonyma förfrågningar. Det innebär att offentlig åtkomst till alla filer som (skrivskyddad).

Följande skärmbild visar inställningen för offentlig åtkomst i Azure portal:

![Skärmbild som visar hur du ställer in offentlig åtkomstnivå i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Medan primär webbplatsslutpunkt inte påverkas, påverkar en ändring av offentlig åtkomstnivå primär blob service-slutpunkt.

Exempel: Om du ändrar offentlig åtkomstnivå för den **$web** behållare från **privat (ingen anonym åtkomst)** till **Blob (anonym läsåtkomst endast för blobbar)** , kommer för offentlig åtkomst till primär webbplatsslutpunkt `https://contosoblobaccount.z22.web.core.windows.net/index.html` ändras inte.

Men du offentlig åtkomst till primärt blob-tjänsteslutpunkt `https://contosoblobaccount.blob.core.windows.net/$web/index.html` ändras från privata till allmänheten. Användare kan nu öppna filen med någon av dessa två slutpunkter.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) och Secure Socket Layer (SSL)-support

För att göra dina filer för statisk webbplats via din anpassade domän och HTTPS, se [använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md). Som en del av den här processen måste du peka CDN-nätverket till primärt *statisk webbplats* slutpunkten till skillnad från primärt *blobtjänst* slutpunkt. Du kan behöva vänta några minuter innan innehållet är synligt när CDN-konfigurationen inte utförs omedelbart.

När du uppdaterar din statiska webbplats, måste du ta bort cachelagrat innehåll i CDN edge-servrar genom att rensa CDN-slutpunkten. Mer information finns i [Purge an Azure CDN endpoint](../../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt).

> [!NOTE]
> HTTPS stöds internt via webbslutpunkt konto så att webbslutpunkt är tillgängliga via både HTTP och HTTPS. Men om lagringskontot har konfigurerats för att kräva säker överföring via HTTPS, måste sedan användare använda HTTPS-slutpunkten. Mer information finns i [Kräv säker överföring i Azure Storage](../common/storage-require-secure-transfer.md).
>
> Användningen av anpassade domäner över HTTPS kräver användning av Azure CDN just nu.

## <a name="custom-domain-names"></a>Egna domännamn

Du kan göra din statiska webbplats via en anpassad domän. Mer information finns i [konfigurera ett anpassat domännamn för Azure Storage-kontot](storage-custom-domain-name.md).

Inblick i som är värd för din domän i Azure, se [värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prissättning

Du kan aktivera som är värd för statisk webbplats kostnadsfritt. Du faktureras bara för blob-lagring som webbplatsen använder och driftskostnader. Mer information om priser för Azure Blob Storage finns i [Prissättningssidan för Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mått

Du kan aktivera mätvärden på statisk webbplatsens sidor. När du har aktiverat mått kan trafik statistik på filer i den **$web** behållare rapporteras i instrumentpanelen för hälsostatistik.

För att aktivera mått på sidorna statisk webbplats, se [aktivera mätvärden på statisk webbplatssidor](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md)
* [Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domännamn för din blob eller web-slutpunkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Skapa din första serverlösa webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudie: Värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
