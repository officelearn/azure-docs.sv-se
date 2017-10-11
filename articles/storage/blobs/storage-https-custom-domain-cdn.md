---
title: "Använder Azure CDN åtkomst till blobbar med anpassade domäner via HTTPS"
description: "Lär dig att integrera Azure CDN med blob storage för att få åtkomst till blobbar med anpassade domäner via HTTPS"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.openlocfilehash: 6bad04df324a374f6e8473890345cf516322abd6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Använder Azure CDN åtkomst till blobbar med anpassade domäner via HTTPS

Azure Content Delivery Network (CDN) har nu stöd för HTTPS för anpassade domännamn.
Du kan använda den här funktionen för att komma åt storage-blobbar med hjälp av den anpassade domänen via HTTPS. Om du vill göra det måste måste du först du aktivera Azure CDN för din slutpunkt för blob och mappa CDN till ett anpassat domännamn. När du har gjort är aktivera HTTPS för den anpassade domänen förenklad via en enda klickning aktivering, fullständig certifikathantering och alla med utan extra kostnad för normal CDN-prisnivå.

Den här möjligheten är viktigt eftersom det gör det möjligt att skydda sekretessen och dataintegritet känsliga web application data under överföringen. Använda SSL-protokollet för att hantera trafik via HTTPS garanterar att data krypteras när de skickas över internet. HTTPS ger förtroende och autentisering och skyddar ditt webbprogram från attacker.

> [!NOTE]
> Förutom att tillhandahålla SSL-stöd för anpassade domännamn kan Azure CDN hjälp du skala ditt program för att leverera innehåll med hög bandbredd runtom i världen.
> Lär dig mer genom att checka ut [översikt över Azure CDN](../../cdn/cdn-overview.md).
>
>

## <a name="quick-start"></a>Snabbstart

Dessa är de steg som krävs för att aktivera HTTPS för din anpassade blob storage-slutpunkt:

1.  [Integrera Azure storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Den här artikeln vägleder dig genom att skapa ett lagringskonto i Azure Portal om du inte har gjort det redan.
2.  [Mappa Azure CDN innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Aktivera HTTPS på en anpassad domän för Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Om din slutpunkt för blob storage är konfigurerad för att neka anonym läsbehörighet, behöver du ange en [delade signatur åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token för varje begäran som du gör i domänen. Som standard inte blob storage slutpunkter tillåta anonym läsbehörighet. Se [Hantera anonym läsbehörighet till behållare och blobbar](storage-manage-access-to-resources.md) mer information om signaturer för delad åtkomst.

Azure CDN gäller inte några begränsningar som lagts till SAS-token. Alla SAS-token har till exempel en förfallotid. Detta innebär att innehåll kan fortfarande nås med ett utgånget SAS tills innehållet tas bort från CDN edge-noder. Du kan styra hur länge data cachelagras på CDN genom att ange rubriken cache. Se [hantera förfallodatum för Azure Storage-blobbar i Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md) anvisningar.

Om du skapar flera SAS-URL: er för samma blob-slutpunkten, rekommenderar vi aktivera cachelagring för Azure CDN. Detta är att säkerställa att varje URL behandlas som en unik entitet. Se [styra Azure CDN cachelagring av frågesträngar med frågesträngar](../../cdn/cdn-query-string.md) för mer information.

## <a name="http-to-https-redirection"></a>HTTP till HTTPS-omdirigering

Du kan välja att dirigera HTTP-trafik till HTTPS. Detta kräver användning av Azure CDN premium erbjudande från Verizon. Du behöver [åsidosätta HTTP beteende med hjälp av Azure CDN regelmotor](../../cdn/cdn-rules-engine.md) med följande regel:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

”Cdn-slutpunkt-name” refererar till det namn som du har konfigurerat för CDN-slutpunkten. Du kan välja det här värdet i listrutan. ”Ursprungssökväg” syftar på sökvägen inom ditt ursprung storage-konto där din statiskt innehåll finns.
Om du är värd för alla statiska innehållet i en enskild behållare, ersätter du ”ursprungssökväg” med namnet på behållaren.

En mer grundlig genomgång i regler finns i [Azure CDN regler motorn funktioner](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Priser och fakturering

När du har åtkomst till blobbar via en Azure CDN du betalar [Blob storage-priser](https://azure.microsoft.com/pricing/details/storage/blobs/) för trafik mellan noder kant och ursprung (Blob storage) och [CDN priser](https://azure.microsoft.com/pricing/details/cdn/) för data som nås från kant-noder.

Anta exempelvis att du har ett lagringskonto i USA, västra som används med en Azure CDN. Om någon i Storbritannien försöker att komma åt en av blobbar i det lagringskontot via CDN kontrollerar Azure först kantnod som är närmast Storbritannien för blobben. Om att hitta den ansluter till kopian av blob och använder CDN prissättning, eftersom den används på CDN. Om det inte att hitta Azure kommer att kopiera blob till kantnoden, vilket resulterar i utgång och transaktionen avgifter som anges i priser för Blob storage och komma åt filen på kantnod, vilket leder till CDN fakturering.

När du tittar på det [CDN sida med priser](https://azure.microsoft.com/pricing/details/cdn/), Observera att HTTPS-stöd för anpassade domännamn är endast tillgängligt för Azure CDN från Verizon produkter (Standard och Premium).

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett anpassat domännamn för din slutpunkt för Blob-lagring](storage-custom-domain-name.md)
