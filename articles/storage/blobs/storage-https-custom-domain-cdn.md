---
title: Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS
description: Lär dig hur du integrerar Azure CDN med blob storage för att få åtkomst till blobar med anpassade domäner över HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 7c4acc7d0832442b94735619ea3a01cb319da993
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398263"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS
Azure Content Delivery Network (CDN) har nu stöd för HTTPS för anpassade domännamn. Du kan använda den här funktionen för att komma åt storage-blobar med hjälp av din anpassade domän via HTTPS. Om du vill göra det måste du måste först aktivera Azure CDN på din slutpunkt för blob eller webb- och mappa CDN till ett anpassat domännamn. När du har gjort har att aktivera HTTPS för din anpassade domän förenklats med enklicksaktivering, fullständig certifikathantering och alla utan extra kostnad till normala priser för CDN.

Den här möjligheten är viktigt eftersom det gör det möjligt för dig att skydda sekretessen och dataintegritet med dina känsliga web application data under överföringen. Om du använder SSL-protokollet för att ge trafik via HTTPS säkerställer att data krypteras när de skickas över internet. HTTPS ger förtroende och autentisering och skyddar dina webbprogram mot attacker.

> [!NOTE]  
> Förutom att tillhandahålla SSL-stöd för anpassade domännamn, Azure CDN kan hjälpa dig att skala ditt program för att leverera innehåll med hög bandbredd över hela världen. Om du vill veta mer, Kolla in [översikt över Azure CDN](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Snabbstart
Detta är de steg som krävs för att aktivera HTTPS för din anpassade blob storage-slutpunkt:

1.  [Integrera ett Azure storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Den här artikeln visar hur du skapar ett lagringskonto i Azure Portal om du inte har gjort det redan.

    > [!NOTE]  
    > Välj ”anpassat ursprung” från nedrullningsbara menyn för att lägga till din slutpunkt för lagring av web ”typ av ursprung” i förhandsversionen av serverstatiska webbplatser stöd i Azure Storage. I Azure-portalen kommer du behöva göra detta från din CDN-profil i stället för direkt i ditt storage-konto.

2.  [Mappa Azure CDN-innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Aktivera HTTPS på en anpassad Azure CDN-domän](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst
Om din blob storage-slutpunkt är konfigurerad för att tillåta anonym läsbehörighet, behöver du ange en [signatur för delad åtkomst (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token i varje begäran som du gör i din anpassade domän. Tillåt inte anonym läsbehörighet som standard blob storage-slutpunkter. Se [Hantera anonym läsbehörighet till behållare och blobbar](storage-manage-access-to-resources.md) för mer information om signaturer för delad åtkomst.

Azure CDN gäller inte eventuella begränsningar som lagts till SAS-token. Alla SAS-token har till exempel en förfallotid. Det innebär att innehåll kan fortfarande användas med en har upphört att gälla SAS tills innehållet tas bort från CDN-gränsnoder. Du kan styra hur länge data cachelagras i CDN genom att ange cache-svarshuvudet. Se [hantera förfallodatum för Azure Storage-blobbar i Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md) anvisningar.

Om du skapar flera SAS-URL: er för samma blobbslutpunkt, rekommenderar vi att aktivera cachelagring för Azure CDN. Detta är att säkerställa att varje URL behandlas som en unik entitet. Se [Kontrollera Cachelagringsbeteendet med frågesträngar](../../cdn/cdn-query-string.md) för mer information.

## <a name="http-to-https-redirection"></a>HTTP till HTTPS-omdirigering
Du kan välja att omdirigera HTTP-trafik till HTTPS. Detta kräver användning av premiumprodukt för Azure CDN från Verizon. Du behöver [åsidosätta HTTP beteende med hjälp av Azure CDN-regelmotor](../../cdn/cdn-rules-engine.md) med följande regel:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

”Cdn-slutpunktsnamn” refererar till det namn som du har konfigurerat för CDN-slutpunkten. Du kan välja det här värdet i listrutan. ”Originalsökväg” refererar till sökvägen i ditt ursprung storage-konto där dina statiskt innehåll finns. Om du är värd för alla statiskt innehåll i en enskild behållare, ersätter du ”-sökväg till ursprung” med namnet på behållaren.

En djupdykning i regler finns i den [Azure CDN regelmotor – funktioner](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Priser och fakturering
När du har åtkomst till blobbar via en Azure CDN betalar du [Blob storage-priser](https://azure.microsoft.com/pricing/details/storage/blobs/) för trafik mellan edge-noder och det ursprungliga (Blob-lagring), och [CDN-priser](https://azure.microsoft.com/pricing/details/cdn/) för data som nås från edge-noder.

Anta exempelvis att du har ett lagringskonto i västra USA, som används med hjälp av en Azure CDN. Om någon i Storbritannien försöker gå till någon av blobbar i det lagringskontot via CDN kontrollerar Azure först gränsnoden närmast Storbritannien för blobben. Om hittas den ansluter till kopian av blobben och kommer att använda CDN-priser, eftersom den används på CDN. Om inget hittas, Azure kommer att kopiera blobben till gränsnoden, som ska leda till avgifter för utgående trafik och transaktion som anges i priser för Blob storage och sedan komma åt filen på kantnoden, vilket resulterar i CDN fakturering.

När du tittar på den [CDN sidan med priser](https://azure.microsoft.com/pricing/details/cdn/), Observera att HTTPS-stöd för anpassade domännamn är endast tillgänglig för Azure CDN från Verizon produkter (Standard och Premium).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera ett anpassat domännamn för din Blob storage-slutpunkt](storage-custom-domain-name.md)
* [Värd för statisk webbplats i Azure Storage (förhandsversion)](storage-blob-static-website.md)
