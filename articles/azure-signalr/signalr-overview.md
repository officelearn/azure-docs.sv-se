---
title: Vad är Azure SignalR Service?
description: En översikt över Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: dc7ba3585ec49921c0a0e66185fc5550d3d4a006
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303622"
---
# <a name="what-is-azure-signalr-service"></a>Vad är Azure SignalR Service?

Azure SignalR Service förenklar arbetet med att lägga till webbfunktioner i realtid för program via HTTP. Denna realtidsfunktion gör att tjänsten kan skicka innehållsuppdateringar till anslutna klienter, till exempel en webbplats med en sida eller ett mobilt program. Därmed uppdateras klienterna utan att man behöver avsöka servern eller skicka nya HTTP-begäranden om uppdateringar.


Den här artikeln ger en översikt över Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Vad används Azure SignalR Service till?

Alla scenarier som kräver att skicka data från servern till klienten i realtid, kan använda Azure SignalR Service.

Traditionella i realtid funktioner som kräver ofta kan avsökning från servern också använda Azure SignalR Service.

Azure SignalR Service har använts i en mängd olika branscher, för alla typer av program som kräver uppdateringar av innehållet i realtid. Vi visar några exempel som är bra att använda Azure SignalR Service:

* **Hög frekvens datauppdateringar:** spel, rösta, avsökning, auktionen.
* **Instrumentpaneler och övervakning:** instrumentpanelen, finansmarknaden data, omedelbar försäljning uppdatering, ledartavla för flera spelare spel och IoT-övervakning.
* **Chatt:** chattrum, chattrobot, online kundsupport, i realtid i assistent, messenger, i spelet chatt och så vidare.
* **I realtid plats på kartan:** logistic spårning, leverans statusspårning, transport statusuppdateringar, GPS-appar.
* **Realtid riktad reklam:** anpassade Läs tid push reklam och erbjudanden, interaktiva annonser.
* **Samarbetsfunktioner appar:** samtidig redigering, whiteboard appar och team som uppfyller programvara.
* **Push-meddelanden:** sociala nätverk, e-post, spel, resa aviseringen.
* **I realtid broadcasting:** live ljud/video broadcasting, finnas textning, översätta, händelser/nyheter broadcasting.
* **IoT och anslutna enheter:** realtidsstatistisk för IoT, fjärrstyrning, realtidsstatus och spårning av placering.
* **Automation:** i realtid utlösare från överordnad händelser.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Vilka är fördelarna med Azure SignalR Service?

**Standard baserat:**

SignalR ger en abstrakt samling tekniker som används för att skapa webbappar av realtidstyp. [WebSockets](https://wikipedia.org/wiki/WebSocket) är den optimala transportmetoden, men andra metoder som [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) och lång avsökning används när andra alternativ inte är tillgängliga. SignalR identifierar och initierar automatiskt lämplig transportmetod baserat på de funktioner som stöds på servern och klienten.

**Inbyggt stöd för ASP.NET Core:**

SignalR Service tillhandahåller inbyggd programmeringsmiljö med ASP.NET Core och ASP.NET. Utveckla nya SignalR-program med SignalR Service eller migrera från befintliga SignalR kräver beroende programmet till SignalR Service minimalt arbete.
SignalR Service har också stöd för ASP.NET Core ny funktion, serversidan Blazor.

**Bred klientstöd:**

SignalR Service fungerar med ett brett utbud av klienter, till exempel webb- och mobila webbläsare, skrivbordsappar, mobilappar, server-processen, IoT-enheter och spelkonsoler. SignalR Service erbjuder SDK: er på olika språk. Förutom inbyggda ASP.NET Core och ASP.NET C# SDK: er, SignalR Service innehåller också JavaScript-klient SDK om du vill aktivera webbklienter och många JavaScript-ramverk. Java-klientens SDK har också stöd för Java-program, inklusive interna Android-appar. SignalR Service har stöd för REST-API utan server via integrering med Azure Functions och Event Grid.

**Hantera storskaliga klientanslutningar:**

SignalR Service är utformad för storskaliga program i realtid. SignalR Service kan flera instanser att arbetar tillsammans för att skala till miljoner-klientanslutningar. Tjänsten stöder också flera globala regioner för horisontell partitionering, hög tillgänglighet och katastrofåterställning.

**Ta bort belastningen som egen värd SignalR:**

Växla till SignalR Service tar bort jämfört med egen värd SignalR-program som behöver du inte hantera tillbaka plan som hanterar den skalas och klientanslutningar. Fullständigt hanterad tjänst kan du också förenklar webbprogram och sparar som är värd för kostnaden. SignalR Service erbjuder global räckvidd och i världsklass datacenter och nätverk, kan skalas upp till miljontals anslutningar, garanterar SLA, samtidigt som du tillhandahåller den efterlevnad och säkerhet på Azure standard.

![Hanterad SignalR-tjänst](./media/signalr-overview/managed-signalr-service.png)

**Erbjud omfattande API: er för olika meddelandemönster:**

SignalR Service gör att servern kan skicka meddelanden till en viss anslutning, alla anslutningar eller en delmängd av anslutningar som tillhör en viss användare eller har placerats i en valfri grupp.

## <a name="how-to-use-azure-signalr-service"></a>Så använder du Azure SignalR Service

Det finns många olika sätt att program med Azure SignalR Service som några av de exempel som visas här:

- **[Skala en ASP.NET Core SignalR-App](signalr-concept-scale-aspnet-core.md)** – Integrera Azure SignalR Service med ett ASP.NET Core SignalR-program för att skala ut till hundratusentals anslutningar.
- **[Skapa serverlösa realtidsappar](signalr-concept-azure-functions.md)** – Använd Azure Functions-integrering med Azure SignalR Service för att skapa serverlösa realtidsprogram med språk som JavaScript, C# och Java.
- **[Skicka meddelanden från servern till klienter via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Azure SignalR Service har REST API så att program kan skicka meddelanden till klienter som är anslutna med SignalR Service i alla REST-kompatibla programmeringsspråk.