---
title: Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS
description: Lär dig hur du integrerar Azure CDN med Blob storage för att få åtkomst till blobar med anpassade domäner över HTTPS
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: da3a6dcb0d125ac4666bc375e843c57cf12fb2fc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148396"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS

Azure Content Delivery Network (Azure CDN) har nu stöd för HTTPS för anpassade domännamn. Du kan använda Azure CDN för att komma åt blobar med hjälp av ditt anpassade domännamn via HTTPS. Om du vill göra det aktiverar Azure CDN på din slutpunkt för blob eller webb- och mappa Azure CDN till ett anpassat domännamn. När du är klar kan Azure gör det enklare att aktivera HTTPS för din anpassade domän via en klickning och fullständig certifikathantering. Det finns inga ökning av den normala Azure CDN-priser.

Azure CDN hjälper dig att skydda sekretessen och dataintegritet med dina data för webbprogrammet i överföringen. Med hjälp av SSL-protokollet för att hantera trafik via HTTPS, Azure CDN för att hålla dina data krypteras när de skickas över internet. Med hjälp av HTTPS med Azure CDN hjälper till att skydda dina webbprogram mot angrepp.

> [!NOTE]  
> Förutom att tillhandahålla SSL-stöd för anpassade domännamn, Azure CDN kan hjälpa dig att skala ditt program för att leverera innehåll med hög bandbredd över hela världen. Mer information finns i [översikt över Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Snabbstart

Om du vill aktivera HTTPS för din anpassade slutpunkt för Blob storage, gör du följande:

1.  [Integrera ett Azure storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Den här artikeln visar hur du skapar ett lagringskonto i Azure-portalen om du inte redan gjort det.

    > [!NOTE]  
    > Om du vill lägga till dina web-slutpunkt för lagring i förhandsversionen av serverstatiska webbplatser stöd i Azure Storage, Välj **anpassat ursprung** i den **ursprungstyp** listrutan. Du behöver göra detta från Azure CDN-profilen i stället för direkt i ditt storage-konto i Azure-portalen.

2.  [Mappa Azure CDN-innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Aktivera HTTPS på en anpassad Azure CDN-domän](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Tillåt inte anonym läsbehörighet som standard Blob storage-slutpunkter. Om din Blob storage-slutpunkt är konfigurerad för att tillåta anonym läsbehörighet, ange en [signatur för delad åtkomst](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token i varje begäran till din anpassade domän. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](storage-manage-access-to-resources.md).

Azure CDN respekterar inte begränsningar som läggs till signaturtoken för delad åtkomst. Till exempel alla signatortokens för delad åtkomst går ut. Du kan fortfarande komma åt innehåll med en signatur för delad åtkomst som har upphört att gälla förrän innehållet tas bort från Azure CDN-gränsnoder. Du kan styra hur länge data cachelagras i Azure CDN genom att ställa in cache-svarshuvudet. Läs hur genom att läsa [hanterar du förfallodatum för Azure Storage-blobbar i Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Om du skapar två eller flera delad åtkomst signatur URL: er för samma blobbslutpunkt, rekommenderar vi att aktivera cachelagring av frågan frågesträngar i Azure CDN-nätverket. Den här åtgärden säkerställer att Azure behandlar varje URL som en unik entitet. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP till HTTPS-omdirigering

Du kan omdirigera HTTP-trafik till HTTPS. Detta kräver användning av premiumprodukt för Azure CDN från Verizon. [Åsidosätt beteende för HTTP med Azure CDN-regelmotor](../../cdn/cdn-rules-engine.md) genom att använda följande regel:

![HTTP till HTTPS-regel för omdirigering](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*CDN-slutpunktsnamn*, som du väljer i listrutan, refererar till det namn som du har konfigurerat för Azure CDN-slutpunkten. *Sökväg till ursprung* refererar till sökvägen i ditt ursprung storage-konto där dina statiskt innehåll lagras. Om du är värd för alla statiskt innehåll i en enskild behållare, ersätter *sökväg till ursprung* med namnet på behållaren.

En djupdykning i regler finns den [Azure CDN regelmotor – funktioner](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Priser och fakturering

När du har åtkomst till blobbar via Azure CDN betalar du [Blob storage-priser](https://azure.microsoft.com/pricing/details/storage/blobs/) för trafik mellan edge-noder och det ursprungliga (Blob storage). Du betalar [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/) för data som hämtas från edge-noder.

Anta exempelvis att du har ett lagringskonto i västra USA, som du kommer åt via Azure CDN. När någon i Storbritannien försöker ansluta till en blob i det lagringskontot via Azure CDN kontrollerar Azure först blob i edge-nod som är närmast Storbritannien. Om Azure hittar blob, har åtkomst till en kopia och använder Azure CDN-priser, eftersom Azure CDN har åtkomst till den. Om Azure inte hittar blob, kopieras bloben till kantnoden. Den här åtgärden leder till avgifter för utgående trafik och transaktion, som anges i priser för Blob storage. Azure kan sedan komma åt filen på kantnoden, vilket resulterar i fakturering för Azure CDN.

På den [Azure CDN sidan med priser](https://azure.microsoft.com/pricing/details/cdn/), HTTPS-stöd för anpassade domännamn är tillgängliga för Azure CDN från Verizon Standard och Premium-produkter.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett anpassat domännamn för din Blob storage-slutpunkt](storage-custom-domain-name.md)
* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)
