---
title: Azure Event Grid säkerhet och autentisering
description: I den här artikeln beskrivs olika sätt att autentisera åtkomst till dina Event Grid-resurser (webhook, prenumerationer, anpassade ämnen)
services: event-grid
author: femila
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: femila
ms.openlocfilehash: 8335d5a41dc2f322623c163e08f8a4a2c1be8360
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559000"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autentisera åtkomst till Azure Event Grid resurser
Den här artikeln innehåller information om följande scenarier:  

- Autentisera klienter som publicerar händelser till Azure Event Grid ämnen med hjälp av signaturen för delad åtkomst (SAS) eller nyckel. 
- Skydda webhook-slutpunkten som används för att ta emot händelser från Event Grid att använda Azure Active Directory (Azure AD) eller en delad hemlighet.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autentisera publicerings klienter med SAS eller nyckel
I anpassade avsnitt används antingen signatur för delad åtkomst (SAS) eller nyckel-autentisering. Vi rekommenderar SAS, men Key Authentication tillhandahåller enkel programmering och är kompatibel med många befintliga webhook-utgivare.

Du inkluderar Authentication-värdet i HTTP-huvudet. För SAS använder du **AEG-SAS-token** för Head-värdet. För Key Authentication använder du **AEG-SAS-Key** för huvudets värde.

### <a name="key-authentication"></a>Nyckel autentisering

Nyckel autentisering är den enklaste formen av autentisering. Använd formatet: `aeg-sas-key: <your key>` i meddelande rubriken.

Du kan till exempel skicka en nyckel med:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Du kan också ange `aeg-sas-key` som frågeparameter. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid inkluderar resursen, en förfallo tid och en signatur. Formatet på SAS-token är: `r={resource}&e={expiration}&s={signature}` .

Resursen är sökvägen till det event Grid-ämne som du skickar händelser till. Till exempel är en giltig resurs Sök väg: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Alla API-versioner som stöds finns i [resurs typer för Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Du genererar signaturen från en nyckel.

Till exempel är ett giltigt **AEG-SAS-Toke-** värde:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

I följande exempel skapas en SAS-token för användning med Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>Vilande kryptering

Alla händelser eller data som skrivs till disk av tjänsten Event Grid krypteras av en Microsoft-hanterad nyckel som garanterar att den är krypterad i vila. Dessutom är den längsta tids perioden som händelser eller data kvarhålls vara 24 timmar i enlighet med principen för [Event Grid återförsök](delivery-and-retry.md). Event Grid tar automatiskt bort alla händelser eller data efter 24 timmar, eller händelsens tids till Live, beroende på vilket som är mindre.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autentisera slut punkter för händelse leverans till webhook
I följande avsnitt beskrivs hur du autentiserar händelse leverans till webhook-slutpunkter. Du måste använda en mekanism för validerings hand skakning oavsett vilken metod du använder. Mer information finns i avsnittet om att [leverera webhook-händelser](webhook-event-delivery.md) . 

### <a name="using-azure-active-directory-azure-ad"></a>Använda Azure Active Directory (Azure AD)
Du kan skydda webhook-slutpunkten som används för att ta emot händelser från Event Grid med hjälp av Azure AD. Du måste skapa ett Azure AD-program, skapa en roll-och tjänst-huvudobjekt i ditt program som auktoriserar Event Grid och konfigurera händelse prenumerationen så att den använder Azure AD-programmet. Lär dig hur du [konfigurerar Azure Active Directory med event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Använda klient hemlighet som frågeparameter
Du kan också skydda webhook-slutpunkten genom att lägga till frågeparametrar i URL: en för webhook-målet som anges som en del av att skapa en händelse prenumeration. Ange att en av frågeparametrar ska vara en klient hemlighet, till exempel en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token) eller en delad hemlighet. Event Grid-tjänsten innehåller alla frågeparametrar i varje händelse leverans förfrågan till webhooken. Webhook-tjänsten kan hämta och verifiera hemligheten. Om klient hemligheten uppdateras måste händelse prenumerationen också uppdateras. Undvik leverans problem under den här hemliga rotationen genom att göra så att webhooken godkänner både gamla och nya hemligheter under en begränsad tid innan du uppdaterar händelse prenumerationen med den nya hemligheten. 

Eftersom frågeparametrar kan innehålla klient hemligheter hanteras de med extra noggrannhet. De lagras som krypterade och är inte tillgängliga för tjänst operatörer. De loggas inte som en del av tjänst loggarna/spårningarna. När du hämtar egenskaperna för händelse prenumerationen returneras inte mål frågans parametrar som standard. Till exempel: [--include-fullständig-Endpoint-URL-](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parameter ska användas i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Mer information om att leverera händelser till Webhooks finns i avsnittet om [leverans av webhook-händelser](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid stöder endast **https** webhook-slutpunkter. 

## <a name="next-steps"></a>Nästa steg

- En introduktion till Event Grid finns i [About Event Grid](overview.md)
