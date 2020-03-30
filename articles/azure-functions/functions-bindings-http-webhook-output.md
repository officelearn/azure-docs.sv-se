---
title: HTTP-utdatabindningar för Azure Functions
description: Lär dig hur du returnerar HTTP-svar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277627"
---
# <a name="azure-functions-http-output-bindings"></a>HTTP-utdatabindningar för Azure Functions

Använd HTTP-utdatabindningen för att svara på HTTP-begäranden. Den här bindningen kräver en HTTP-utlösare och du kan anpassa svaret som associeras med utlösarens begäran.

Standardreturvärdet för en HTTP-utlöst funktion är:

- `HTTP 204 No Content`med en tom kropp i Funktioner 2.x och högre
- `HTTP 200 OK`med en tom kropp i Funktioner 1.x

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som du anger i *filen function.json.* För C#-klassbibliotek finns det inga attributegenskaper som motsvarar dessa *function.json-egenskaper.*

|Egenskap  |Beskrivning  |
|---------|---------|
| **Typ** |Måste ställas `http`in på . |
| **riktning** | Måste ställas `out`in på . |
| **Namn** | Variabelnamnet som används i funktionskoden `$return` för svaret eller för att använda returvärdet. |

## <a name="usage"></a>Användning

Om du vill skicka ett HTTP-svar använder du svarsmönstren för språkstandard. I C# eller C#-skriptet `IActionResult` gör `Task<IActionResult>`du funktionens returtyp eller . I C#krävs inte ett returvärdeattribut.

Till exempel svar, se [utlösarexempel .](./functions-bindings-http-webhook-trigger.md#example)

## <a name="hostjson-settings"></a>värd.json-inställningar

I det här avsnittet beskrivs de globala konfigurationsinställningar som är tillgängliga för den här bindningen i version 2.x och senare. Exempelvärden.json filen nedan innehåller endast inställningarna för version 2.x+ för den här bindningen. Mer information om globala konfigurationsinställningar i version 2.x och senare finns i [host.json-referens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x finns i [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
| customHeaders|ingen|Gör att du kan ange anpassade rubriker i HTTP-svaret. I föregående exempel `X-Content-Type-Options` läggs sidhuvudet till i svaret för att undvika att innehållstypen sniffar. |
|dynamicThrottlesEnabled|Sant<sup>\*</sup>|När den här inställningen är aktiverad gör den att pipelinen `connections/threads/processes/memory/cpu/etc` för bearbetning av begäran regelbundet kontrollerar systemprestandaräknare som och om någon `429 "Too Busy"` av dessa räknare är över ett inbyggt högt tröskelvärde (80 %), kommer begäranden att avvisas med ett svar tills räknarna/räknarna återgår till normala nivåer.<br/><sup>\*</sup>Standardvärdet i en `true`förbrukningsplan är . Standardvärdet i en dedikerad plan är `false`.|
|hsts (hsts)|inte aktiverat|När `isEnabled` är `true`inställt på tillämpas [http Strict Transport Security (HSTS) för .NET Core,](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) enligt definitionen i [ `HstsOptions` klassen](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0). I exemplet ovan [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) anges också 10 dagar. Egenskaper som `hsts` stöds är: <table><tr><th>Egenskap</th><th>Beskrivning</th></tr><tr><td>excludedHosts</td><td>En strängmatris med värdnamn som HSTS-huvudet inte läggs till för.</td></tr><tr><td>inkluderaSubDomains</td><td>Booleskt värde som anger om parametern includeSubDomain i huvudet Strikt transport-säkerhet är aktiverad.</td></tr><tr><td>maxAge (maxAge)</td><td>Sträng som definierar parametern maxålder i huvudet Strikt transport-säkerhet.</td></tr><tr><td>preload</td><td>Booleskt som anger om parametern för förinläsning i huvudet Strikt transport-säkerhet är aktiverad.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Det maximala antalet HTTP-funktioner som körs parallellt. Med det här värdet kan du styra samtidighet, vilket kan hjälpa till att hantera resursutnyttjande. Du kan till exempel ha en HTTP-funktion som använder ett stort antal systemresurser (minne/cpu/sockets) så att det orsakar problem när samtidigheten är för hög. Eller så kan du ha en funktion som gör utgående förfrågningar till en tjänst från tredje part, och dessa samtal måste vara begränsad. I dessa fall kan det vara till hjälp att använda en gasrep här. <br/><sup>*</sup>Standardvärdet för en förbrukningsplan är 100. Standardvärdet för en dedikerad plan`-1`är obegränsad ( ).|
|maxOutstandingRequests|200<sup>\*</sup>|Det maximala antalet utestående begäranden som hålls vid en viss tidpunkt. Den här gränsen inkluderar begäranden som står i kö men inte har börjat köras, samt eventuella pågående körningar. Alla inkommande begäranden över den här gränsen avvisas med ett 429 "För upptagen"-svar. Det gör att de som ringer kan använda tidsbaserade strategier för återförsök och hjälper dig också att kontrollera maximal begärandenlighet. Detta styr bara kö som uppstår i skriptvärdkörningssökvägen. Andra köer som ASP.NET förfrågan-kön kommer fortfarande att gälla och påverkas inte av den här inställningen. <br/><sup>\*</sup>Standardvärdet för en förbrukningsplan är 200. Standardvärdet för en dedikerad plan`-1`är obegränsad ( ).|
|routePrefix|api|Flödesprefixet som gäller för alla vägar. Använd en tom sträng för att ta bort standardprefixet. |

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion från en HTTP-begäran](./functions-bindings-http-webhook-trigger.md)