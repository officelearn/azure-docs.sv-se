---
title: Vad är Azure SignalR Service?
description: En översikt över Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 198eb0ff6c9f8de311cc2d39ba8fb7c8b6ed3a11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808750"
---
# <a name="what-is-azure-signalr-service"></a>Vad är Azure SignalR Service?

Azure SignalR Service förenklar arbetet med att lägga till webbfunktioner i realtid för program via HTTP. Denna realtidsfunktion gör att tjänsten kan skicka innehållsuppdateringar till anslutna klienter, till exempel en webbplats med en sida eller ett mobilt program. Därmed uppdateras klienterna utan att man behöver avsöka servern eller skicka nya HTTP-begäranden om uppdateringar.

Den här artikeln ger en översikt över Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Vad används Azure SignalR Service till?

Det finns många programtyper som kräver uppdatering av innehållet i realtid. Följande exempel är goda kandidater för användning av Azure SignalR Service:

* Appar som kräver högfrekvent uppdatering från servern. Exempel är spel, röstning, auktioner, kartor och GPS-appar.
* Instrumentpaneler och övervakningsappar. Exempel är företagsinstrumentpaneler och omedelbara försäljningsuppdateringar.
* Samarbetsappar. Whiteboard-appar och programvaror för teammöten är exempel på samarbetsappar.
* Appar som kräver aviseringar. Sociala nätverk, e-post, chattar, spel, reseaviseringar och många andra appar använder sig av aviseringar.

SignalR ger en abstrakt samling tekniker som används för att skapa webbappar av realtidstyp. [WebSockets](https://wikipedia.org/wiki/WebSocket) är den optimala transportmetoden, men andra metoder som [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) och lång avsökning används när andra alternativ inte är tillgängliga. SignalR identifierar och initierar automatiskt lämplig transportmetod baserat på de funktioner som stöds på servern och klienten.

SignalR tillhandahåller dessutom en programmeringsmodell för realtidsprogram som gör att servern kan skicka meddelanden till alla anslutningar eller till en delmängd av anslutningar som tillhör en viss användare eller som har placerats i en valfri grupp.

## <a name="how-to-use-azure-signalr-service"></a>Så använder du Azure SignalR Service

Det finns för närvarande tre sätt att använda Azure SignalR Service:

- **[Skala en ASP.NET Core SignalR-App](signalr-concept-scale-aspnet-core.md)** – Integrera Azure SignalR Service med ett ASP.NET Core SignalR-program för att skala ut till hundratusentals anslutningar.
- **[Skapa serverlösa realtidsappar](signalr-concept-azure-functions.md)** – Använd Azure Functions-integrering med Azure SignalR Service för att skapa serverlösa realtidsprogram med språk som JavaScript, C# och Java.
- **[Skicka meddelanden från servern till klienter via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Azure SignalR Service har REST API så att program kan skicka meddelanden till klienter som är anslutna med SignalR Service i alla REST-kompatibla programmeringsspråk.