---
title: Vägledning för Azure Key Vault-begränsning
description: Key Vault-begränsning begränsar antalet samtidiga anrop för att förhindra felaktig användning av resurser.
services: key-vault
documentationcenter: ''
author: bryanla
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: aa71eac9bdcc841337891a1a8c281f5c18efbf13
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107726"
---
# <a name="azure-key-vault-throttling-guidance"></a>Vägledning för Azure Key Vault-begränsning

Begränsning är en process som du har initierat som begränsar antalet samtidiga anrop till Azure-tjänsten för att förhindra felaktig användning av resurser. Azure Key Vault (AKV) är utformad för att hantera ett stort antal begäranden. Om det uppstår en överväldigande antalet begäranden, begränsningar din klientbegäranden till att behålla optimala prestanda och tillförlitlighet för AKV-tjänsten.

Begränsningar gränser varierar beroende på vilket scenario. Om du utför ett stort antal skrivningar är till exempel möjligheten för begränsning av högre än om du endast utför läsningar.

## <a name="how-does-key-vault-handle-its-limits"></a>Hur hanterar dess gränser i Key Vault?

Tjänstbegränsningar i Key Vault finns det att förhindra missbruk av resurser och se till att Tjänstkvalitet för alla klienter för Key Vault. När ett tröskelvärde för tjänsten överskrids begränsar Key Vault eventuella ytterligare begäranden från klienten för en viss tidsperiod. När det händer gör Key Vault returnerar HTTP-statuskod 429 (för många begäranden), och förfrågningarna som misslyckas. Dessutom misslyckade begäranden som returnerar 429 mot de spårade av Key Vault-begränsningar. 

Om du har en giltig affärsfall för högre begränsningar kan du kontakta oss.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hur du ska begränsa din app som svar på tjänstbegränsningar

Följande är **bästa praxis** för begränsning av din app:
- Minska antalet åtgärder per begäran.
- Minska frekvensen av begäranden.
- Undvik att omedelbart återförsök. 
    - Alla förfrågningar påförs mot din användningsbegränsningar.

När du implementerar felhantering för din app kan du använda HTTP-felkod 429 för att identifiera behovet av begränsning på klientsidan. Om begäran misslyckas igen med en HTTP 429-felkod kan påträffar du fortfarande en gräns för Azure-tjänsten. Fortsätta att använda rekommenderade klientsidan begränsning metod, försöka igen tills den lyckas.

Kod som implementerar en exponentiell backoff visas nedan. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Med den här koden i en klient C\# program (mikrotjänster med en annan webb-API-klient, ASP.NET MVC-program eller även om C\# Xamarin-program) är enkelt. I följande exempel visas hur du med hjälp av HttpClient-klassen.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Kom ihåg att den här koden är lämplig endast som ett konceptbevis. 

### <a name="recommended-client-side-throttling-method"></a>Rekommenderade metoden för begränsning av klientsidan

Börja begränsning klienten med hjälp av en exponentiell backoff-metod på http-felkod 429:

1. Vänta 1 sekund, försök begäran
2. Om fortfarande begränsad vänta 2 sekunder, försök igen med begäran
3. Om fortfarande begränsad vänta 4 sekunder, försök igen med begäran
4. Om fortfarande begränsad vänta 8 sekunder, försök igen med begäran
5. Om fortfarande begränsad vänta 16 sekunder, försök igen med begäran

Nu kan bör du inte hämta HTTP 429-svarskoder.

## <a name="see-also"></a>Se också

En djupare orientering av begränsning i Microsoft Cloud finns [mönster för begränsning av](https://docs.microsoft.com/azure/architecture/patterns/throttling).

