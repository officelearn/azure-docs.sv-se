---
title: Använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över HTTPS
description: Lär dig hur du integrerar Azure CDN med Blob Storage för att få åtkomst till blobbar med anpassade domäner över HTTPS
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 3ad599182191e41ea43d38260692a7ab46e1af6f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844994"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över HTTPS

Azure Content Delivery Network (Azure CDN) stöder nu HTTPS för anpassade domän namn. Med Azure CDN kan du komma åt blobar med hjälp av ditt anpassade domän namn över HTTPS. Det gör du genom att aktivera Azure CDN på BLOB-eller webb slut punkten och sedan mappa Azure CDN till ett eget domän namn. När du är klar fören klar Azure aktiveringen av HTTPS för din anpassade domän via ett klick och fullständig certifikat hantering. Det finns ingen ökning av det normala Azure CDN priset.

Azure CDN hjälper till att skydda sekretessen och data integriteten för dina webb programs data medan de överförs. Genom att använda SSL-protokollet för att betjäna trafik via HTTPS, Azure CDN hålla dina data krypterade när de skickas via Internet. Att använda HTTPS med Azure CDN hjälper till att skydda dina webb program mot angrepp.

> [!NOTE]  
> Förutom att tillhandahålla SSL-stöd för anpassade domän namn kan Azure CDN hjälpa dig att skala ditt program för att leverera innehåll med hög bandbredd runt om i världen. Mer information finns i [Översikt över Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Snabbstart

Gör så här om du vill aktivera HTTPS för den anpassade Blob Storage-slutpunkten:

1.  [Integrera ett Azure Storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Den här artikeln vägleder dig genom att skapa ett lagrings konto i Azure Portal, om du inte redan har gjort det.

    > [!NOTE]  
    > Om du vill lägga till din lagrings webb slut punkt under för hands versionen av stöd för statisk webbplats i Azure Storage väljer du **anpassat ursprung** i list rutan **typ av ursprung** . I Azure Portal måste du göra detta från din Azure CDN profil i stället för direkt i ditt lagrings konto.

2.  [Mappa Azure CDN innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Aktivera HTTPS på en Azure CDN anpassad domän](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Som standard tillåter Blob Storage-slutpunkter anonym Läs åtkomst. Om din Blob Storage-slutpunkt är konfigurerad för att inte tillåta anonym Läs åtkomst, anger du en [signatur för delad åtkomst](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) i varje begäran till din anpassade domän. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](storage-manage-access-to-resources.md).

Azure CDN respekterar inte några begränsningar som läggs till i signaturen för signaturen för delad åtkomst. Till exempel går alla token för signaturer för delad åtkomst ut. Du kan fortfarande komma åt innehåll med en utgången signatur för delad åtkomst tills innehållet rensas från Azure CDN Edge-noderna. Du kan styra hur länge data cachelagras i Azure CDN genom att ställa in cache-svarshuvudet. Mer information finns [i hantera förfallo datum för Azure Storage blobbar i Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Om du skapar två eller fler URL: er för signaturer för delad åtkomst för samma BLOB-slutpunkt rekommenderar vi att du aktiverar cachelagring av frågesträngar för din Azure CDN. Den här åtgärden säkerställer att Azure behandlar varje URL som en unik entitet. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Omdirigering av HTTP till HTTPS

Du kan omdirigera HTTP-trafik till HTTPS. Detta kräver att du använder Azure CDN Premium-erbjudandet från Verizon. [Åsidosätt HTTP-beteende med Azure CDN regel motorn](../../cdn/cdn-rules-engine.md) genom att använda följande regel:

![Regel för omdirigering av HTTP till HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*CDN – slut punkt – namn*, som du väljer i list rutan, refererar till det namn som du konfigurerade för Azure CDN slut punkten. *Ursprung – sökväg* refererar till sökvägen i ditt ursprungs lagrings konto där ditt statiska innehåll lagras. Om du är värd för allt statiskt innehåll i en enda behållare ersätter du *Origin-sökvägen* med namnet på behållaren.

En djupare inblick i reglerna finns i [funktionerna för Azure CDN-regler](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Priser och fakturering

När du har åtkomst till blobbar via Azure CDN betalar du [Blob Storage-priser](https://azure.microsoft.com/pricing/details/storage/blobs/) för trafik mellan Edge-noderna och ursprunget (Blob Storage). Du betalar [Azure CDN priser](https://azure.microsoft.com/pricing/details/cdn/) för data som nås från Edge-noderna.

Anta till exempel att du har ett lagrings konto i västra USA som du kommer åt via Azure CDN. När någon i Storbritannien försöker komma åt en BLOB i det lagrings kontot via Azure CDN kontrollerar Azure först om blobben i Edge-noden som är närmast Storbritannien. Om Azure hittar blobben får den en kopia och använder Azure CDN prissättning, eftersom Azure CDN har åtkomst till den. Om Azure inte hittar blobben kopieras blobben till Edge-noden. Den här åtgärden resulterar i utgångs-och transaktions avgifter, i enlighet med priserna för Blob Storage. Azure kommer sedan åt filen på Edge-noden, vilket leder till Azure CDN fakturering.

På [sidan Azure CDN prissättning](https://azure.microsoft.com/pricing/details/cdn/)är https-stöd för anpassade domän namn endast tillgängligt för Azure CDN från Verizon standard-och Premium-produkter.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett anpassat domän namn för din Blob Storage-slutpunkt](storage-custom-domain-name.md)
* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website.md)
