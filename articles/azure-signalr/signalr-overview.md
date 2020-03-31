---
title: Vad är Azure SignalR-tjänst?
description: Bättre förstå vad typiska användningsscenarier för att använda Azure SignalR och lär dig de viktigaste fördelarna med Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157589"
---
# <a name="what-is-azure-signalr-service"></a>Vad är Azure SignalR-tjänst?

Azure SignalR Service förenklar arbetet med att lägga till webbfunktioner i realtid för program via HTTP. Denna realtidsfunktion gör att tjänsten kan skicka innehållsuppdateringar till anslutna klienter, till exempel en webbplats med en sida eller ett mobilt program. Därmed uppdateras klienterna utan att man behöver avsöka servern eller skicka nya HTTP-begäranden om uppdateringar.


Den här artikeln ger en översikt över Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Vad används Azure SignalR Service till?

Alla scenarier som kräver att data ska skjutas från server till klient i realtid kan använda Azure SignalR-tjänsten.

Traditionella realtidsfunktioner som ofta kräver avsökning från servern kan också använda Azure SignalR-tjänsten.

Azure SignalR-tjänsten har använts i en mängd olika branscher, för alla programtyper som kräver innehållsuppdateringar i realtid. Vi listar några exempel som är bra att använda Azure SignalR Service:

* **Högfrekventa datauppdateringar:** spel, röstning, röstning, auktion.
* **Instrumentpaneler och övervakning:** företagets instrumentpanel, finansmarknadsdata, omedelbar försäljningsuppdatering, spelledartavla för flera spelare och IoT-övervakning.
* **Chatt:** chattrum, chattrobot, kundsupport online, shoppingassistent i realtid, messenger, chatt i spelet och så vidare.
* **Plats i realtid på kartan:** logistikspårning, spårning av leveransstatus, uppdateringar av transportstatus, GPS-appar.
* **Riktade annonser i realtid:** anpassade realtids push-annonser och erbjudanden, interaktiva annonser.
* **Samarbetsappar:** samtidig redigering, whiteboardappar och teammötesprogram.
* **Push-meddelanden:** sociala nätverk, e-post, spel, resevarning.
* **Sändning i realtid:** direktsända ljud-/videosändningar, live-textning, översättning, evenemang/nyhetssändningar.
* **IoT och anslutna enheter:** IoT-mått i realtid, fjärrkontroll, realtidsstatus och platsspårning.
* **Automatisering:** realtidsutlösare från händelser uppströms.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Vilka är fördelarna med Azure SignalR-tjänsten?

**Standardbaserad:**

SignalR ger en abstrakt samling tekniker som används för att skapa webbappar av realtidstyp. [WebSockets](https://wikipedia.org/wiki/WebSocket) är den optimala transportmetoden, men andra metoder som [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) och lång avsökning används när andra alternativ inte är tillgängliga. SignalR identifierar och initierar automatiskt lämplig transportmetod baserat på de funktioner som stöds på servern och klienten.

**Stöd för ASP.NET Core:**

SignalR Service ger inbyggd programmeringsupplevelse med ASP.NET Core och ASP.NET. Utveckla nya SignalR-program med SignalR Service, eller migrera från befintliga SignalR-baserade program till SignalR Service kräver minimala ansträngningar.
SignalR Service stöder också ASP.NET Cores nya funktion, Server-side Blazor.

**Brett kundstöd:**

SignalR Service fungerar med ett brett spektrum av klienter, till exempel webb- och mobilwebbläsare, skrivbordsappar, mobilappar, serverprocess, IoT-enheter och spelkonsoler. SignalR Service erbjuder SDK:er på olika språk. Förutom inbyggda ASP.NET Core eller ASP.NET C# SDK: er, ger SignalR Service också JavaScript-klient SDK, för att aktivera webbklienter och många JavaScript-ramverk. Java-klient SDK stöds också för Java-program, inklusive Android-inbyggda appar. SignalR-tjänsten stöder REST API och serverlös via integreringar med Azure Functions och Event Grid.

**Hantera storskaliga klientanslutningar:**

SignalR Service är utformad för storskaliga realtidsprogram. SignalR-tjänsten gör att flera instanser kan arbeta tillsammans för att skala till miljontals klientanslutningar. Tjänsten stöder också flera globala regioner för fragmentering, hög tillgänglighet eller katastrofåterställning.

**Ta bort bördan till självvärd SignalR:**

Jämfört med självvärderade SignalR-program tar om du byter till SignalR-tjänsten bort behovet av att hantera tillbaka plan som hanterar skalor och klientanslutningar. Den fullständigt hanterade tjänsten förenklar också webbprogram och sparar värdkostnader. SignalR-tjänsten erbjuder global räckvidd och datacenter och nätverk i världsklass, skalor till miljontals anslutningar, garanterar serviceavtal och ger all efterlevnad och säkerhet på Azure-standarden.

![Hanterad signalr-tjänst](./media/signalr-overview/managed-signalr-service.png)

**Erbjud rika API:er för olika meddelandemönster:**

Med SignalR-tjänsten kan servern skicka meddelanden till en viss anslutning, alla anslutningar eller en delmängd av anslutningar som tillhör en viss användare eller har placerats i en godtycklig grupp.

## <a name="how-to-use-azure-signalr-service"></a>Så använder du Azure SignalR Service

Det finns många olika sätt att programmera med Azure SignalR Service, som några av de exempel som anges här:

- **[Skala en ASP.NET Core SignalR-App](signalr-concept-scale-aspnet-core.md)** – Integrera Azure SignalR Service med ett ASP.NET Core SignalR-program för att skala ut till hundratusentals anslutningar.
- **[Skapa serverlösa realtidsappar](signalr-concept-azure-functions.md)** – Använd Azure Functions-integrering med Azure SignalR Service för att skapa serverlösa realtidsprogram med språk som JavaScript, C# och Java.
- **[Skicka meddelanden från servern till klienter via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Azure SignalR Service har REST API så att program kan skicka meddelanden till klienter som är anslutna med SignalR Service i alla REST-kompatibla programmeringsspråk.
