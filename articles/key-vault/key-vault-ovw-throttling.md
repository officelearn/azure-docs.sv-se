---
title: Vägledning för Azure Key Vault-begränsning
description: Key Vault begränsning begränsar antalet samtidiga anrop för att förhindra felaktig användning av resurser.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067698"
---
# <a name="azure-key-vault-throttling-guidance"></a>Vägledning för Azure Key Vault-begränsning

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

