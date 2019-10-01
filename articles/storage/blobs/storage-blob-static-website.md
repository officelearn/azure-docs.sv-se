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
ms.openlocfilehash: 85f7ea11638278a010b2a94d9c6472857f51b687
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710175"
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

|Verktyg| Riktlinjer |
|----|----|
|**Azure Portal** | [Hitta webbplats-URL: en med hjälp av Azure Portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Hitta webbplats-URL: en med hjälp av Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell modul** | [Hitta webbplats-URL: en med hjälp av PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

URL: en för din webbplats innehåller en regional kod. Till exempel URL: en `https://contosoblobaccount.z22.web.core.windows.net/` innehåller regional kod `z22`.

Även om koden måste vara en webb adress är den bara för intern användning och du behöver inte använda koden på något annat sätt.

Det index dokument som du anger när du aktiverar statisk webbplats värd visas när användarna öppnar platsen och inte anger en fil (till exempel: `https://contosoblobaccount.z22.web.core.windows.net`).  

Om servern returnerar ett 404-fel och du inte har angett ett fel dokument när du aktiverade webbplatsen, returneras en standard-404-sida till användaren.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Effekt av inställningen för webb behållarens offentliga åtkomst nivå

Du kan ändra den offentliga åtkomst nivån för **$Web** containern, men detta har ingen påverkan på den primära statiska webbplats slut punkten eftersom dessa filer behandlas via begär Anden om anonym åtkomst. Det innebär offentlig (skrivskyddad) åtkomst till alla filer.

Följande skärm bild visar inställningen för offentlig åtkomst nivå i Azure Portal:

![Skärm bild som visar hur du ställer in offentlig åtkomst nivå i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Även om den primära statiska webbplats slut punkten inte påverkas, påverkar en ändring av den offentliga åtkomst nivån den primära BLOB-tjänstens slut punkt.

Om du till exempel ändrar den offentliga åtkomst nivån för **$Web** containern från **privat (ingen anonym åtkomst)** till **BLOB (endast anonym Läs åtkomst för blobbar)** , så är nivån för offentlig åtkomst till den primära statiska webbplats slut punkten `https://contosoblobaccount.z22.web.core.windows.net/index.html` ändras inte.

Den offentliga åtkomsten till den primära BLOB-tjänstens slut punkt `https://contosoblobaccount.blob.core.windows.net/$web/index.html` ändras dock från privat till offentlig. Nu kan användare öppna filen genom att använda någon av dessa två slut punkter.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Stöd för Content Delivery Network (CDN) och SSL (Secure Socket Layer)

Om du vill göra dina statiska webbplats-filer tillgängliga över din anpassade domän och HTTPS, se [använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över https](storage-https-custom-domain-cdn.md). Som en del av den här processen måste du peka ditt CDN till den primära *statiska webbplats* slut punkten i stället för den primära *BLOB-tjänstens* slut punkt. Du kan behöva vänta några minuter innan ditt innehåll visas eftersom CDN-konfigurationen inte körs direkt.

När du uppdaterar din statiska webbplats måste du ta bort det cachelagrade innehållet på CDN Edge-servrarna genom att rensa CDN-slutpunkten. Mer information finns i [Purge an Azure CDN endpoint](../../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt).

> [!NOTE]
> HTTPS stöds internt via kontots webb slut punkt, så att webb slut punkten kan nås via både HTTP och HTTPS. Om lagrings kontot har kon figurer ATS för att kräva säker överföring över HTTPS måste användarna dock använda HTTPS-slutpunkten. Mer information finns i [Kräv säker överföring i Azure Storage](../common/storage-require-secure-transfer.md).
>
> Användning av anpassade domäner över HTTPS kräver att Azure CDN används just nu.

## <a name="custom-domain-names"></a>Anpassade domännamn

Du kan göra din statiska webbplats tillgänglig via en anpassad domän. Mer information finns i [Konfigurera ett anpassat domän namn för ditt Azure Storage-konto](storage-custom-domain-name.md).

För en djupgående titt på att vara värd för din domän på Azure, se [värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prissättning

Du kan aktivera statisk webbplats värd utan kostnad. Du faktureras bara för den blob-lagring som webbplatsen använder och drifts kostnader. Mer information om priser för Azure-Blob Storage finns på sidan med [priser för azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mått

Du kan aktivera mått på statiska webbplats sidor. När du har aktiverat mått rapporteras trafik statistik på filer i **$Web** -behållaren på instrument panelen mått.

Om du vill aktivera mått på dina statiska webbplats sidor, se [Aktivera mått på statiska webbplats sidor](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md)
* [Använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurera ett anpassat domän namn för din BLOB eller webb slut punkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Bygg din första server lös webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudier: Värd för din domän i Azure DNS @ no__t-0
