---
title: Vara värd för en statisk webbplats i Azure Storage
description: Azure Storage statisk webbplats hosting, vilket ger en kostnadseffektiv, skalbar lösning för hosting moderna webbprogram.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370499"
---
# <a name="static-website-hosting-in-azure-storage"></a>Vara värd för en statisk webbplats i Azure Storage

Du kan visa statiskt innehåll (HTML-, CSS-, JavaScript- och bildfiler) direkt från en lagringsbehållare med namnet *$web*. Med ditt innehåll i Azure Storage kan du använda serverlösa arkitekturer som innehåller [Azure Functions](/azure/azure-functions/functions-overview) och andra PaaS-tjänster (Platform as a Service).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Om din webbplats är beroende av kod på serversidan använder du [Azure App Service](/azure/app-service/overview) i stället.

## <a name="setting-up-a-static-website"></a>Konfigurera en statisk webbplats

Statisk webbhotell är en funktion som du måste aktivera på lagringskontot.

Om du vill aktivera statisk webbplatshosting väljer du namnet på standardfilen och anger sedan en sökväg till en anpassad 404-sida. Om en blob-lagringsbehållare med namnet **$web** inte redan finns i kontot skapas en åt dig. Lägg till filerna på webbplatsen i den här behållaren.

Stegvis vägledning finns i [Vara värd](storage-blob-static-website-how-to.md)för en statisk webbplats i Azure Storage .

![Mätmått för Azure Storage-statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Filer i **$web** behållaren är skiftlägeskänsliga, betjänas via anonyma åtkomstbegäranden och är endast tillgängliga genom läsåtgärder.

## <a name="uploading-content"></a>Ladda upp innehåll

Du kan använda något av dessa verktyg för att ladda upp innehåll till **$web** behållaren:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md#powershell)
> * [AzKopia](../common/storage-use-azcopy-v10.md)
> * [Utforskaren för Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-tillägg](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visa innehåll

Användare kan visa webbplatsinnehåll från en webbläsare med hjälp av den offentliga webbadressen till webbplatsen. Du hittar URL:en med hjälp av Azure-portalen, Azure CLI eller PowerShell. Använd den här tabellen som vägledning.

|Verktyg| Riktlinjer |
|----|----|
|**Azure-portal** | [Hitta webbadressen med hjälp av Azure-portalen](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Hitta webbadressen med hjälp av Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell-modul** | [Hitta webbadressen med PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

Webbadressen till din webbplats innehåller en regional kod. Url:en `https://contosoblobaccount.z22.web.core.windows.net/` innehåller till `z22`exempel regional kod .

Koden måste finnas kvar i webbadressen, men den är endast för internt bruk och du behöver inte använda koden på något annat sätt.

Det indexdokument som du anger när du aktiverar statisk webbplatshosting visas när användare öppnar `https://contosoblobaccount.z22.web.core.windows.net`webbplatsen och inte anger en specifik fil (till exempel: ).  

Om servern returnerar ett 404-fel och du inte har angett något feldokument när du aktiverade webbplatsen returneras en standardsida på 404 till användaren.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) stöds inte med statisk webbplats.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Inverkan av inställningen av webbbehållarens offentliga åtkomstnivå

Du kan ändra den offentliga åtkomstnivån **för $web** behållaren, men det påverkar inte den primära statiska webbplatsslutpunkten eftersom dessa filer visas via anonyma åtkomstbegäranden. Det innebär offentlig (skrivskyddad) åtkomst till alla filer.

Följande skärmbild visar inställningen för offentlig åtkomstnivå i Azure-portalen:

![Skärmbild som visar hur du anger nivån för offentlig åtkomst i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Även om den primära statiska webbplatsslutpunkten inte påverkas, påverkar en ändring av den offentliga åtkomstnivån den primära blob-tjänstslutpunkten.

Om du till exempel ändrar den offentliga åtkomstnivån **för $web** behållaren från **Privat (ingen anonym åtkomst)** till **Blob (endast anonym läsåtkomst för blobbar) ändras**inte nivån för offentlig åtkomst till den primära statiska webbplatsslutpunkten. `https://contosoblobaccount.z22.web.core.windows.net/index.html`

Den offentliga åtkomsten till den primära `https://contosoblobaccount.blob.core.windows.net/$web/index.html` blob-tjänstslutpunkten ändras dock från privat till offentlig. Nu kan användare öppna filen med hjälp av någon av dessa två slutpunkter.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mappa en anpassad domän till en statisk webbadress

Du kan göra din statiska webbplats tillgänglig via en anpassad domän. 

Det är enklare att aktivera HTTP-åtkomst för din anpassade domän, eftersom Azure Storage stöder den internt. Om du vill aktivera HTTPS måste du använda Azure CDN eftersom Azure Storage ännu inte stöder HTTPS med anpassade domäner. Se [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md) för steg-för-steg-vägledning.

Om lagringskontot är konfigurerat för att [kräva säker överföring](../common/storage-require-secure-transfer.md) via HTTPS måste användarna använda HTTPS-slutpunkten. 

> [!TIP]
> Överväg att vara värd för din domän på Azure. Mer information finns [i Värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Lägga till HTTP-huvuden

Det går inte att konfigurera rubriker som en del av funktionen för statisk webbplats. Du kan dock använda Azure CDN för att lägga till rubriker och lägga till (eller skriva över) rubrikvärden. Se [Motorreferens för standardregler för Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Om du vill använda rubriker för att styra cachelagring läser du [Kontrollera Azure CDN-cachelagring med cachelagringsregler](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Prissättning

Du kan aktivera statisk webbhotell gratis. Du faktureras endast för blob-lagring som din webbplats använder och driftkostnader. Mer information om priser för Azure Blob Storage finns på [prissidan för Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mått

Du kan aktivera mått på statiska webbplatssidor. När du har aktiverat mått rapporteras trafikstatistik för filer i **$web** behållaren i måttinstrumentpanelen.

Mer om du vill aktivera mått på dina statiska webbplatssidor finns i [Aktivera mått på statiska webbplatssidor](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md)
* [Mappa en anpassad domän till en slutpunkt för Azure Blob Storage](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App-tjänst](/azure/app-service/overview)
* [Skapa din första serverlösa webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudie: Använda Azure DNS som värd för din domän](../../dns/dns-delegate-domain-azure-dns.md)
