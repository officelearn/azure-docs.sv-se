---
title: REST-API för sessionshantering
description: Beskriver hur du hanterar sessioner
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681251"
---
# <a name="use-the-session-management-rest-api"></a>Använda REST-API:et för sessionshantering

Om du vill använda Azure Remote Rendering-funktioner måste du skapa en *session*. Varje session motsvarar en virtuell dator (VM) som allokeras i Azure och väntar på att en klientenhet ska ansluta. När en enhet ansluter återger den virtuella datorn de begärda data och betjänar resultatet som en videoström. När sessionen skapas valde du vilken typ av server du vill köra på, vilket avgör prissättningen. När sessionen inte behövs längre, bör den stoppas. Om den inte stoppas manuellt stängs den av automatiskt när sessionens *lånetid* går ut.

Vi tillhandahåller ett PowerShell-skript i [ARR-exempelarkivet](https://github.com/Azure/azure-remote-rendering) i *mappen Skript,* kallad *RenderingSession.ps1,* som visar användningen av vår tjänst. Skriptet och dess konfiguration beskrivs här: [Exempel på PowerShell-skript](../samples/powershell-example-scripts.md)

> [!TIP]
> PowerShell-kommandona som visas på den här sidan är avsedda att komplettera varandra. Om du kör alla skript i följd i samma PowerShell-kommandotolk bygger de ovanpå varandra.

## <a name="regions"></a>Regioner

Se [listan över tillgängliga regioner](../reference/regions.md) som bas-URL:erna ska skicka begärandena till.

För exempelskripten nedan valde vi regionen *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Exempelskript: Välj en slutpunkt

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Konton

Om du inte har ett fjärrrenderingskonto [skapar du ett](create-an-account.md). Varje resurs identifieras av en *accountId*, som används under hela sessionen API: er.

### <a name="example-script-set-accountid-and-accountkey"></a>Exempelskript: Ange accountId och accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Vanliga begäranden

* *Auktoriseringshuvudet* måste`Bearer TOKEN`ha värdet`TOKEN`" ", där " " är autentiseringstoken [som returneras av secure token service](tokens.md).

### <a name="example-script-request-a-token"></a>Exempelskript: Begär en token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Vanliga svarsrubriker

* *MS-CV-huvudet* kan användas av produktteamet för att spåra anropet inom tjänsten.

## <a name="create-a-session"></a>Skapa en session

Det här kommandot skapar en session. Det returnerar ID för den nya sessionen. Du behöver sessions-ID för alla andra kommandon.

| URI | Metod |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**Begäran organ:**

* maxLeaseTime (tidsspann): ett timeout-värde när den virtuella datorn inaktiveras automatiskt
* modeller (matris): URL:er för tillgångsbehållare för förinläsning
* storlek (sträng): VM-storlek (**"standard"** eller **"premium"**). Se specifika [vm-storleksbegränsningar](../reference/limits.md#overall-number-of-polygons).

**Svaren:**

| Statuskod | JSON nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Lyckades |

### <a name="example-script-create-a-session"></a>Exempelskript: Skapa en session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Exempel skript: Store sessionId

Svaret från begäran ovan innehåller en **sessionId**, som du behöver för alla uppföljningsbegäranden.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Uppdatera en session

Det här kommandot uppdaterar parametrarna för en session. För närvarande kan du bara förlänga lånetiden för en session.

> [!IMPORTANT]
> Lånetiden anges alltid som en total tid sedan sessionens början. Det innebär att om du har skapat en session med en lånetid på en timme och du vill förlänga dess lånetid ytterligare en timme måste du uppdatera maxLeaseTime till två timmar.

| URI | Metod |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/sessionId*sessionId* | Patch |

**Begäran organ:**

* maxLeaseTime (tidsspann): ett timeout-värde när den virtuella datorn inaktiveras automatiskt

**Svaren:**

| Statuskod | JSON nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | | Lyckades |

### <a name="example-script-update-a-session"></a>Exempel skript: Uppdatera en session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Få aktiva sessioner

Det här kommandot returnerar en lista över aktiva sessioner.

| URI | Metod |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | HÄMTA |

**Svaren:**

| Statuskod | JSON nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | - sessioner: matris med sessionsegenskaper | Se avsnittet "Hämta sessionsegenskaper" för en beskrivning av sessionsegenskaper |

### <a name="example-script-query-active-sessions"></a>Exempelskript: Fråga aktiva sessioner

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Hämta sessionsegenskaper

Det här kommandot returnerar information om en session, till exempel dess VM-värdnamn.

| URI | Metod |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/egenskaper | HÄMTA |

**Svaren:**

| Statuskod | JSON nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | - meddelande: sträng<br/>- sessionElapsedTime: tidsspann<br/>- sessionHostname: sträng<br/>- sessionId: sträng<br/>- sessionMaxLeaseTime: tidsspann<br/>- sessionStorlek: räkna upp<br/>- sessionStatus: uppräkning | uppräkningssessionStatus { start, klar, stoppad, stoppad, utgångna, fel}<br/>Om statusen är "fel" eller "utgånget" kommer meddelandet att innehålla mer information |

### <a name="example-script-get-session-properties"></a>Exempelskript: Hämta sessionsegenskaper

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Stoppa en session

Det här kommandot stoppar en session. Den tilldelade virtuella datorn kommer att återåtas strax efter.

| URI | Metod |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/sessionId*sessionId* | DELETE |

**Svaren:**

| Statuskod | JSON nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 204 | | Lyckades |

### <a name="example-script-stop-a-session"></a>Exempel skript: Stoppa en session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Nästa steg

* [Exempel på PowerShell-skript](../samples/powershell-example-scripts.md)
