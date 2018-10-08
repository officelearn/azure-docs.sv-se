---
title: Självstudie – Använda lagringsblobar via en anpassad Azure CDN-domän och HTTPS | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: a1a86f28c9650c86df7c333ca52d772a09597efd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091077"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Självstudie – Använda lagringsblobar via en anpassad Azure CDN-domän och HTTPS

När du har integrerat ditt Azure-lagringskonto med Azure Content Delivery Network (CDN) kan du lägga till en anpassad domän och aktivera HTTPS på domänen för din anpassade lagringsslutpunkt. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan utföra stegen i den här självstudien måste du först integrera ditt Azure-lagringskonto med Azure CDN. Mer information finns i [Snabbstart: Integrera ett Azure-lagringskonto med Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Lägga till en anpassad domän
När du skapar en CDN-slutpunkt i din profil ingår som standard slutpunktens namn, som är en underdomän till azureedge.net, i webbadressen för leverans av CDN-innehåll. Du kan också välja att associera en anpassad domän med en CDN-slutpunkt. Då levererar du innehållet till en anpassad domän i din URL i stället för till ett slutpunktsnamn. Om du vill lägga till en anpassad domän för din slutpunkt följer du instruktionerna i den här självstudien: [Lägga till en anpassad domän för din Azure CDN-slutpunkt](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Konfigurera HTTPS
Genom att använda HTTPS-protokollet för din anpassade domän kan du se till att data levereras säkert på internet via TLS/SSL-kryptering. När webbläsaren är ansluten till en webbplats via HTTPS valideras webbplatsens säkerhetscertifikat och verifierar att det är utfärdat av en giltig certifikatutfärdare. Om du vill konfigurera HTTPS för din domän följer du instruktionerna i den här självstudien: [Konfigurera HTTPS för en anpassad Azure CDN-domän](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst
Om din slutpunkt för bloblagring är konfigurerad att inte tillåta anonym läsåtkomst bör du tillhandahålla en [SAS-token (signatur för delad åtkomst)](cdn-sas-storage-support.md) i varje förfrågan du gör till den anpassade domänen. Som standard tillåter inte slutpunkter för bloblagring anonym läsåtkomst. Mer information om SAS finns i [Hantera anonym läsåtkomst till containrar och blobar](../storage/blobs/storage-manage-access-to-resources.md).

Azure CDN ignorerar eventuella begränsningar som läggs till i SAS-token. Alla SAS-token har till exempel en giltighetstid, vilket innebär att innehåll fortfarande nås med en utgången SAS-token tills innehållet tas bort från POP-servrarna (point of presence) i CDN. Du kan styra hur länge data cachelagras i Azure CDN genom att ställa in cache-svarshuvudet. Mer information finns i [Hantera giltighetstid för Azure-lagringsblobar i Azure CDN](cdn-manage-expiration-of-blob-content.md).

Överväg att aktivera cachelagring av frågesträngar om du skapar flera SAS-webbadresser för samma blobslutpunkt. Då ser du till att varje webbadress behandlas som en unik entitet. Mer information finns i [Kontrollera cachelagringsbeteendet i Azure CDN med frågesträngar](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Omdirigering från HTTP till HTTPS
Du kan välja att dirigera om HTTP-trafik till HTTPS genom att skapa en [regel för omdirigering av webbadresser](cdn-rules-engine-reference-features.md#url-redirect) i [Azure CDN-regelmotorn](cdn-rules-engine.md). För det här alternativet behöver du en profil av typen **Azure CDN Premium från Verizon**. 

![Omdirigeringsregel för webbadresser](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

I den här regeln avser *Cdn-endpoint-name* namnet du konfigurerat för din CDN-slutpunkt. Du kan välja namnet från listrutan. Värdet för *origin-path* avser sökvägen i ditt ursprungliga lagringskonto där det statiska innehållet finns. Om du har allt statiskt innehåll i en enda container ska du byta ut *origin-path* mot namnet på containern.

## <a name="pricing-and-billing"></a>Priser och fakturering
När du använder blobar via Azure CDN betalar du [bloblagringspriser](https://azure.microsoft.com/pricing/details/storage/blobs/) för trafiken mellan POP-servrarna och ursprunget (bloblagringen), och [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/) för data som nås via POP-servrarna.

Om du till exempel ha ett lagringskonto i USA som används via Azure CDN och någon i Europa försöker komma åt en av blobarna i lagringskontot via Azure CDN så kontrollerar Azure CDN först den POP-server som ligger närmast Europa för bloben. Om en sådan hittas så använder Azure CDN den kopian av bloben till CDN-priser, eftersom den används via Azure CDN. Om det inte finns någon sådan server kopierar Azure CDN bloben till POP-servern, vilket leder till utgående trafik och transaktionsavgifter enligt bloblagringspriserna. Sedan används filen på POP-servern enligt Azure CDN-avgifter.

## <a name="next-steps"></a>Nästa steg
[Självstudie: Konfigurera Azures CDN-cachelagringsregler](cdn-caching-rules-tutorial.md)




