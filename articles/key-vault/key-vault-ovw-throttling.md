---
ms.assetid: 
title: "Azure Key Vault begränsning vägledning | Microsoft Docs"
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 06/21/2017
ms.openlocfilehash: cc39a5ee466d2e1661281a32010c3d3201c4a0af
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault begränsning vägledning

Begränsning är en process som du har initierat som begränsar antalet samtidiga anrop till Azure-tjänsten för att förhindra felaktig användning av resurser. Azure Key Vault (AKV) är utformad för att hantera ett stort antal begäranden. Om det inträffar ett överväldigande antal begäranden, upprätthåller begränsning din klientbegäranden optimala prestanda och tillförlitlighet för AKV-tjänsten.

Bandbreddsbegränsning gränser variera beroende på scenario. Om du utför ett stort antal skrivningar är till exempel möjligheten för begränsning av högre än om du bara utför läsningar.

## <a name="how-does-key-vault-handle-its-limits"></a>Hur hanterar Key Vault gränsen?

Tjänstbegränsningarna i Key Vault finns det att förhindra missbruk av resurser och säkerställa Tjänstkvalitet för alla Key Vault-klienter. När ett tröskelvärde för tjänsten har överskridits begränsar Key Vault ytterligare förfrågningar från klienten för en viss tidsperiod. När det händer gör Key Vault returnerar HTTP-statuskod 429 (för många begäranden), och begäranden att misslyckas. Dessutom misslyckade begäranden som 429 antal mot begränsningen gränserna spåras av Key Vault. 

Om du har ett giltigt företag ärende för högre begränsning gränser, kontaktar du oss.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hur du begränsar din app som svar på tjänstbegränsningarna

Följande är **metodtips** för begränsning av din app:
- Minska antalet åtgärder per begäran.
- Minska frekvensen av begäranden.
- Undvik omedelbara försök. 
    - Alla begäranden som görs mot gränserna för Resursanvändning.

När du implementerar din app felhantering, Använd HTTP-felkod 429 för att identifiera behovet av att klientens begränsning. Om begäran misslyckas igen med en HTTP-429 felkod, det fortfarande uppstår en gräns för Azure-tjänsten. Fortsätta att använda den rekommenderade klientsidan begränsning metod, försök tills den lyckas.

### <a name="recommended-client-side-throttling-method"></a>Rekommenderad metod för klientsidan bandbreddsbegränsning

HTTP-felkod: 429 börja begränsning klienten med en exponentiell backoff-metoden:

1. Vänta 1 sekund, försök igen
2. Om du fortfarande begränsas vänta 2 sekunder, försök igen med begäran
3. Om du fortfarande begränsas vänta 4 sekunder, försök igen med begäran
4. Om du fortfarande begränsas vänta 8 sekunder, försök igen med begäran
5. Om du fortfarande begränsas vänta 16 sekunder, försök igen med begäran

Nu bör du inte att hämta HTTP 429 svarskoder.

## <a name="see-also"></a>Se också

En djupare orientering med begränsningen på Microsoft Cloud finns [begränsning mönster](https://docs.microsoft.com/azure/architecture/patterns/throttling).

