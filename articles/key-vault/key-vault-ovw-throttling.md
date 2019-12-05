---
title: Vägledning för Azure Key Vault-begränsning
description: Key Vault-begränsning begränsar antalet samtidiga anrop för att förhindra felaktig användning av resurser.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 28e79dffb206e8a62410bf3b4e0e239879b51224
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806685"
---
# <a name="azure-key-vault-throttling-guidance"></a>Vägledning för Azure Key Vault-begränsning

Begränsning är en process som du har initierat som begränsar antalet samtidiga anrop till Azure-tjänsten för att förhindra felaktig användning av resurser. Azure Key Vault (AKV) är utformad för att hantera ett stort antal begäranden. Om det uppstår en överväldigande antalet begäranden, begränsningar din klientbegäranden till att behålla optimala prestanda och tillförlitlighet för AKV-tjänsten.

Begränsningar gränser varierar beroende på vilket scenario. Om du utför ett stort antal skrivningar är till exempel möjligheten för begränsning av högre än om du endast utför läsningar.

## <a name="how-does-key-vault-handle-its-limits"></a>Hur hanterar dess gränser i Key Vault?

Tjänst begränsningar i Key Vault förhindra missbruk av resurser och garantera tjänst kvalitet för alla Key Vault klienter. När ett tröskelvärde för tjänsten överskrids, Key Vault begränsar alla ytterligare begär Anden från klienten under en viss tids period, returnerar HTTP-statuskod 429 (för många begär Anden) och begäran Miss lyckas. Misslyckade förfrågningar som returnerar ett 429-antal mot de begränsnings gränser som spåras av Key Vault. 

Key Vault har ursprungligen utformats för att användas för att lagra och hämta dina hemligheter vid distributions tillfället.  Världen har utvecklats och Key Vault används vid körning för att lagra och hämta hemligheter och ofta appar och tjänster vill använda Key Vault som en databas.  Aktuella gränser stöder inte höga data flödes hastigheter.

Key Vault skapades ursprungligen med de gränser som angavs i [Azure Key Vault tjänst gränser](key-vault-service-limits.md).  Här är några rekommenderade rikt linjer/bästa metoder för att Key Vault maximera ditt data flöde:
1. Se till att du har en begränsning på plats.  Klienten måste följa exponent back-off-principer för 429 och se till att du gör nya försök enligt anvisningarna nedan.
1. Dela upp Key Vault trafik mellan flera valv och olika regioner.   Använd ett separat valv för varje säkerhets-/tillgänglighets domän.   Om du har fem appar, var och en i två regioner, rekommenderar vi 10 valv som innehåller hemligheterna som är unika för appen och regionen.  En prenumerations gräns för alla transaktions typer är fem gånger den enskilda nyckel valvs gränsen. Till exempel är HSM-andra transaktioner per prenumeration begränsad till 5 000 transaktioner på 10 sekunder per prenumeration. Överväg att cachelagra hemligheten i din tjänst eller app för att också minska RPS direkt till nyckel valv och/eller hantera burst-baserad trafik.  Du kan också dela upp trafiken mellan olika regioner för att minimera svars tiden och använda en annan prenumeration/valv.  Skicka inte fler än prenumerations gränsen till Key Vault tjänsten i en enda Azure-region.
1. Cachelagra de hemligheter som du hämtar från Azure Key Vault i minnet och återanvänd från minnet när det är möjligt.  Läs bara från Azure Key Vault när den cachelagrade kopian slutar fungera (t. ex. på grund av att den har roterats vid källan). 
1. Key Vault har utformats för dina egna tjänste hemligheter.   Om du lagrar dina kunders hemligheter (särskilt för nyckel lagrings scenarier med höga data flöden) bör du överväga att placera nycklarna i en databas eller ett lagrings konto med kryptering och enbart lagra huvud nyckeln i Azure Key Vault.
1. Åtgärder för att kryptera, figursätta och kontrol lera offentliga nycklar kan utföras utan åtkomst till Key Vault, vilket inte bara minskar risken för begränsning, utan även ökar tillförlitligheten (så länge du cachelagrar det offentliga nyckel materialet).
1. Om du använder Key Vault för att lagra autentiseringsuppgifter för en tjänst kontrollerar du om tjänsten stöder AAD-autentisering för att autentisera direkt. Detta minskar belastningen på Key Vault, förbättrar tillförlitligheten och fören klar koden eftersom Key Vault nu kan använda AAD-token.  Många tjänster har flyttats till med AAD-autentisering.  Se den aktuella listan med [tjänster som har stöd för hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Överväg att sprida belastningen/distributionen över en längre tids period för att ligga under de nuvarande RPS-gränserna.
1. Om din app innehåller flera noder som behöver läsa samma hemligheter, kan du överväga att använda ett mönster för utskrivning, där en entitet läser hemligheten från Key Vault och fläktar ut till alla noder.   Cachelagra bara hämtade hemligheter i minnet.
Om du upptäcker att ovanstående fortfarande inte uppfyller dina behov kan du fylla i tabellen nedan och kontakta oss för att ta reda på vilken ytterligare kapacitet som kan läggas till (exempel som bara beskrivs i exempel).

| Valv namn | Valv region | Objekt typ (hemligt, nyckel eller certifikat) | Åtgärd (er) * | Nyckel typ | Nyckel längd eller kurva | HSM-nyckel?| RPS för stabilt tillstånd krävs | Topp-RPS krävs |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Nyckel | Signera | EC | P-256 | Nej | 200 | 1 000 |

\* en fullständig lista över möjliga värden finns i [Azure Key Vault åtgärder](/rest/api/keyvault/key-operations).

Om ytterligare kapacitet godkänns bör du tänka på följande när kapaciteten ökar:
1. Modell ändringar för data konsekvens. När ett valv har tillåtts med ytterligare data flödes kapacitet ändras den Key Vault tjänstens data konsekvens ändringar (krävs för att uppfylla högre volym-RPS eftersom den underliggande Azure Storage-tjänsten inte kan fortsätta).  I en kortfattat så Jenkins:
  1. **Utan att tillåta registrering**: Key Vault tjänsten visar resultatet av en Skriv åtgärd (t. ex. SecretSet, CreateKey) omedelbart i efterföljande anrop (t. ex. SecretGet, inloggning).
  1. **Med Tillåt-lista**: Key Vault tjänsten visar resultatet av en Skriv åtgärd (t. ex. SecretSet, CreateKey) inom 60 sekunder i efterföljande anrop (t. ex. SecretGet, inloggning).
1. Klient koden måste uppfylla en princip för säkerhets kopiering för 429-försök. Klient koden som anropar tjänsten Key Vault får inte omedelbart försöka igen Key Vault begär anden när en 429-svarskod tas emot.  Vägledningen för Azure Key Vault begränsning som publiceras här rekommenderar att du använder exponentiell backoff när du tar emot en 429 HTTP-svarskod.

Om du har en giltig affärsfall för högre begränsningar kan du kontakta oss.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hur du ska begränsa din app som svar på tjänstbegränsningar

Följande är **metod tips** som du bör implementera när din tjänst är begränsad:
- Minska antalet åtgärder per begäran.
- Minska frekvensen av begäranden.
- Undvik att omedelbart återförsök. 
    - Alla förfrågningar påförs mot din användningsbegränsningar.

När du implementerar felhantering för din app kan du använda HTTP-felkod 429 för att identifiera behovet av begränsning på klientsidan. Om begäran misslyckas igen med en HTTP 429-felkod kan påträffar du fortfarande en gräns för Azure-tjänsten. Fortsätta att använda rekommenderade klientsidan begränsning metod, försöka igen tills den lyckas.

Kod som implementerar en exponentiell backoff visas nedan. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Det är enkelt att använda den C# här koden i ett klient program. 

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

