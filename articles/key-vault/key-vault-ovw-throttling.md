---
title: Vägledning för Azure Key Vault-begränsning
description: Key Vault-begränsning begränsar antalet samtidiga anrop för att förhindra felaktig användning av resurser.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 261a735a94aca23fe10bdab461a9619250f67f41
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884990"
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
     public async Task OnGetAsync()
     {
         Message = "Your application description page.";
         int retries = 0;
         bool retry = false;
         do 
         {
             try
             {
                 AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                 KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                 var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                         .ConfigureAwait(false);
                 Message = secret.Value;
             }
             /// <exception cref="KeyVaultErrorException">
             /// Thrown when the operation returned an invalid status code
             /// </exception>
             catch (KeyVaultErrorException keyVaultException)
             {
                 Message = keyVaultException.Message;
                 if ((int)keyVaultException.Response.StatusCode == 429)
                 {
                    retry = true;

                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    Thread.Sleep(new TimeSpan(waitTime));
                }
             }
         } while(retry && (retries++ < 10));         
         
     }

     // This method implements exponential backoff in case of 429 errors from Azure Key Vault
     private static long getWaitTime(int retryCount)
     {
        return (long)Math.Pow(2, retryCount) * 100L;
     }
```

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

