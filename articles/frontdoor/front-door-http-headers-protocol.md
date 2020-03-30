---
title: Protokollstöd för HTTP-huvuden i Azure Front Door | Microsoft-dokument
description: I den här artikeln beskrivs HTTP-huvudprotokoll som Front Door stöder.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471684"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protokollstöd för HTTP-huvuden i Azure Front Door
I den här artikeln beskrivs det protokoll som Ytterdörren stöder med delar av anropssökvägen (se bild). Följande avsnitt innehåller mer information om HTTP-huvuden som stöds av Ytterdörren.

![AZURE Front Door HTTP-huvuden protokoll][1]

>[!IMPORTANT]
>Ytterdörren certifierar inga HTTP-huvuden som inte dokumenteras här.

## <a name="client-to-front-door"></a>Klient till ytterdörren
Ytterdörren accepterar de flesta rubriker från den inkommande begäran utan att ändra dem. Vissa reserverade rubriker tas bort från den inkommande begäran om den skickas, inklusive rubriker med X-FD-* prefixet.

## <a name="front-door-to-backend"></a>Ytterdörr till backend

Ytterdörren innehåller rubriker från en inkommande begäran om den inte tas bort på grund av begränsningar. Ytterdörren lägger också till följande rubriker:

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Ytterdörren lägger till klientens HTTP-version följt av *Azure* som värde för Via-huvudet. Det här huvudet anger klientens HTTP-version och att Ytterdörren var en mellanliggande mottagare för begäran mellan klienten och klienten.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representerar klient-IP-adressen som är associerad med begäran som bearbetas. En begäran som kommer från en proxy kan till exempel lägga till X-Forwarded-For-huvudet för att ange IP-adressen för den ursprungliga anroparen. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representerar socket-IP-adressen som är associerad med TCP-anslutningen som den aktuella begäran kommer från. En begärans klient-IP-adress kanske inte är lika med dess socket-IP-adress eftersom den godtyckligt kan skrivas över av en användare.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZMNzA3NjQ3Nzgz </br> En unik referenssträng som identifierar en begäran som betjänas av ytterdörren. Den används för att söka åtkomstloggar och avgörande för felsökning.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Ett huvud som Ytterdörren använder för att identifiera begärandeloopar och användare bör inte vara beroende av det. |
| X-vidarebefordrad-för | X-Forwards-För: 127.0.0.1 </br> HTTP-huvudet för X-Forwarded (XFF) identifierar ofta den ursprungliga IP-adressen för en klient som ansluter till en webbserver via en HTTP-proxy eller belastningsutjämnare. Om det finns en befintlig XFF-huvud lägger Front Door till klientuttagets IP-adress eller lägger till XFF-huvudet med klientuttagets IP. |
| X-vidarebefordrad värd | X-Vidarebefordrad värd: contoso.azurefd.net </br> HTTP-huvudfältet X-Forwarded-Host är en vanlig metod som används för att identifiera den ursprungliga värden som begärs av klienten i värd-HTTP-begäranden. Detta beror på att värdnamnet från ytterdörren kan skilja sig åt för serverdelsservern som hanterar begäran. |
| X-vidarebefordrad-Proto | X-Vidarebefordras-Proto: http </br> Fältet X-Forwarded-Proto HTTP-huvud används ofta för att identifiera det ursprungliga protokollet för en HTTP-begäran eftersom ytterdörren, baserat på konfiguration, kan kommunicera med serverdelen med hjälp av HTTPS. Detta gäller även om begäran till den omvända proxyn är HTTP. |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP-huvudfält används för att identifiera hälsoavsökningen från ytterdörren. Om det här huvudet är 1 är begäran hälsoavsökning. Du kan använda när du vill strikt åtkomst från viss ytterdörr med X-Forwarded-Host header fält. |

## <a name="front-door-to-client"></a>Ytterdörr till kund

Alla rubriker som skickas till ytterdörren från serverdelen skickas också vidare till klienten. Följande är rubriker som skickas från ytterdörren till klienter.

| Huvud  | Exempel |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZMNzA3NjQ3Nzgz* </br> Detta är en unik referenssträng som identifierar en begäran som betjänas av ytterdörren. Detta är viktigt för felsökning eftersom det används för att söka åtkomstloggar.|

## <a name="next-steps"></a>Nästa steg

- [Skapa en ytterdörr](quickstart-create-front-door.md)
- [Hur ytterdörren fungerar](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
