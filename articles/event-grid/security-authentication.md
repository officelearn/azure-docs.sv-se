---
title: Azure Event Grid säkerhet och autentisering
description: I den här artikeln beskrivs olika sätt att autentisera åtkomst till dina Event Grid-resurser (webhook, prenumerationer, anpassade ämnen)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 71d47c83586f7e5e31b148714e2804686422326a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588266"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autentisera åtkomst till Azure Event Grid resurser
Den här artikeln innehåller information om följande scenarier:  

- Autentisera klienter som publicerar händelser till Azure Event Grid ämnen med hjälp av signaturen för delad åtkomst (SAS) eller nyckel. 
- Skydda webhook-slutpunkten med Azure Active Directory (Azure AD) för att autentisera Event Grid för att **leverera** händelser till slut punkten.

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
Du kan skydda webhook-slutpunkten genom att använda Azure Active Directory (Azure AD) för att autentisera och auktorisera Event Grid för att leverera händelser till dina slut punkter. Du måste skapa ett Azure AD-program, skapa en roll-och tjänst princip i ditt program för att auktorisera Event Grid och konfigurera händelse prenumerationen så att den använder Azure AD-programmet. [Lär dig hur du konfigurerar Azure Active Directory med event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Använda klient hemlighet som frågeparameter
Du kan skydda webhook-slutpunkten genom att lägga till frågeparametrar till webhook-URL: en när du skapar en händelse prenumeration. Ange en av följande frågeparametrar som en klient hemlighet, till exempel en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token) eller en delad hemlighet. Webhooken kan använda hemligheten för att identifiera att händelsen kommer från Event Grid med giltiga behörigheter. Event Grid tar med dessa frågeparametrar i varje händelse leverans till webhooken. Om klient hemligheten uppdateras måste händelse prenumerationen också uppdateras. Undvik leverans problem under den här hemliga rotationen genom att göra så att webhooken accepterar både gamla och nya hemligheter under en begränsad tid. 

Eftersom frågeparametrar kan innehålla klient hemligheter hanteras de med extra noggrannhet. De lagras som krypterade och inte tillgängliga för tjänst operatörer. De loggas inte som en del av tjänst loggarna/spårningarna. När du redigerar händelse prenumerationen visas eller returneras inte frågeparametrar om parametern [--include-full-URL-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Mer information om att leverera händelser till Webhooks finns i avsnittet om [leverans av webhook-händelser](webhook-event-delivery.md)

> [!IMPORTANT]
Azure Event Grid stöder endast **https** webhook-slutpunkter. 

## <a name="next-steps"></a>Nästa steg

- En introduktion till Event Grid finns i [About Event Grid](overview.md)
