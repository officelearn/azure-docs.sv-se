---
title: Statisk webbplats som är värd för i Azure Storage
description: Azure Storage statisk webbplats värd, vilket ger en kostnads effektiv och skalbar lösning för moderna webb program.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: a35239354d23f75361d5577d6b7efc8254943147
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906588"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statisk webbplats som är värd för i Azure Storage

Du kan hantera statiskt innehåll (HTML, CSS, Java Script och bildfiler) direkt från en lagrings behållare med namnet *$Web*. Om du är värd för ditt innehåll i Azure Storage kan du använda serverbaserade arkitekturer som inkluderar [Azure Functions](/azure/azure-functions/functions-overview) och andra PaaS-tjänster (Platform as a Service).

> [!NOTE]
> Om din plats är beroende av kod på Server sidan, använder du [Azure App Service](/azure/app-service/overview) i stället.

## <a name="setting-up-a-static-website"></a>Konfigurera en statisk webbplats

En statisk webbplats värd är en funktion som du måste aktivera på lagrings kontot.

Om du vill aktivera statisk webbplats värd väljer du namnet på din standard fil och anger sedan en sökväg till en anpassad 404-sida. Om en Blob Storage-behållare med namnet **$Web** inte redan finns i kontot, skapas en åt dig. Lägg till filerna på platsen i den här behållaren.

Steg-för-steg-anvisningar finns i vara [värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md).

![Mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Filer i **$Web** container är Skift läges känsliga, hanteras via begär Anden om anonym åtkomst och är bara tillgängliga via Läs åtgärder.

## <a name="uploading-content"></a>Laddar upp innehåll

Du kan använda något av dessa verktyg för att ladda upp innehåll till **$Web** container:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell modul](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-pipeline](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-tillägg](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visa innehåll

Användare kan visa webbplats innehåll från en webbläsare med hjälp av den offentliga URL: en för webbplatsen. Du kan hitta URL: en med hjälp av Azure Portal, Azure CLI eller PowerShell. Använd den här tabellen som en guide.

|Verktyg| Vägledning |
|----|----|
|**Azure-portalen** | [Hitta webbplats-URL: en med hjälp av Azure Portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Hitta webbplats-URL: en med hjälp av Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell modul** | [Hitta webbplats-URL: en med hjälp av PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

URL: en för din webbplats innehåller en regional kod. URL-`https://contosoblobaccount.z22.web.core.windows.net/` innehåller till exempel en regional kod `z22`.

Även om koden måste finnas i URL: en, är den bara för intern användning och du behöver inte använda koden på något annat sätt.

Det index dokument som du anger när du aktiverar statisk webbplats värd visas när användarna öppnar platsen och inte anger en fil (till exempel: `https://contosoblobaccount.z22.web.core.windows.net`).  

Om servern returnerar ett 404-fel och du inte har angett ett fel dokument när du aktiverade webbplatsen, returneras en standard-404-sida till användaren.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) stöds inte med den statiska webbplatsen.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Effekt av inställningen för webb behållarens offentliga åtkomst nivå

Du kan ändra den offentliga åtkomst nivån för **$Web** containern, men detta har ingen påverkan på den primära statiska webbplats slut punkten eftersom dessa filer behandlas via begär Anden om anonym åtkomst. Det innebär offentlig (skrivskyddad) åtkomst till alla filer.

Följande skärm bild visar inställningen för offentlig åtkomst nivå i Azure Portal:

![Skärm bild som visar hur du ställer in offentlig åtkomst nivå i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Även om den primära statiska webbplats slut punkten inte påverkas, påverkar en ändring av den offentliga åtkomst nivån den primära BLOB-tjänstens slut punkt.

Om du till exempel ändrar den offentliga åtkomst nivån för **$Web** containern från **privat (ingen anonym åtkomst)** till **BLOB (endast anonym Läs åtkomst för blobbar)** , ändras nivån för offentlig åtkomst till den primära statiska webbplats slut punkten `https://contosoblobaccount.z22.web.core.windows.net/index.html` inte.

Den offentliga åtkomsten till den primära BLOB-tjänstens slut punkt `https://contosoblobaccount.blob.core.windows.net/$web/index.html` ändras dock från privat till offentlig. Nu kan användare öppna filen genom att använda någon av dessa två slut punkter.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mappa en anpassad domän till en statisk webbplats-URL

Du kan göra din statiska webbplats tillgänglig via en anpassad domän. 

Det är enklare att aktivera HTTP-åtkomst för din anpassade domän eftersom Azure Storage stöder det internt. Om du vill aktivera HTTPS måste du använda Azure CDN eftersom Azure Storage inte har inbyggt stöd för HTTPS med anpassade domäner. Se [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md) för steg-för-steg-anvisningar.

Om lagrings kontot har kon figurer ATS för att [kräva säker överföring](../common/storage-require-secure-transfer.md) över HTTPS måste användarna använda https-slutpunkten. 

> [!TIP]
> Överväg att vara värd för din domän på Azure. Mer information finns i vara [värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prissättning

Du kan aktivera statisk webbplats värd utan kostnad. Du faktureras bara för den blob-lagring som webbplatsen använder och drifts kostnader. Mer information om priser för Azure-Blob Storage finns på sidan med [priser för azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mått

Du kan aktivera mått på statiska webbplats sidor. När du har aktiverat mått rapporteras trafik statistik på filer i **$Web** -behållaren på instrument panelen mått.

Om du vill aktivera mått på dina statiska webbplats sidor, se [Aktivera mått på statiska webbplats sidor](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md)
* [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Bygg din första server lös webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudie: vara värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
