---
title: Azure front dörr – URL-omdirigering | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azures front dörr stöder URL-omdirigering för deras vägar, om den är konfigurerad.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: duau
ms.openlocfilehash: 41cb2343cb86d2ec756bb0a2fb690b7df886024f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399046"
---
# <a name="url-redirect"></a>URL-omdirigering
Du kan använda Azures frontend-dörr för att dirigera om trafik. Du kan omdirigera trafik på flera nivåer (protokoll, värdnamn, sökväg, frågesträng) och alla funktioner kan konfigureras för enskilda mikrotjänster eftersom omdirigeringen är sökväg-baserad. Detta fören klar program konfigurationen, optimerar resursanvändningen och har stöd för nya omdirigerings scenarier, inklusive global och Sök vägs baserad omdirigering.
</br>

![Azure front dörr-URL omdirigering][1]

## <a name="redirection-types"></a>Typer av omdirigering
En typ av omdirigering anger svars status koden för klienterna för att förstå syftet med omdirigeringen. Följande typer av omdirigering stöds:

- **301 (flyttad permanent)**: visar att mål resursen har tilldelats en ny permanent URI och att eventuella framtida referenser till resursen bör använda en av de omslutna URI: erna. Använd 301 status kod för omdirigering av HTTP till HTTPS. 
- **302 (hittades)**: anger att mål resursen finns tillfälligt under en annan URI. Eftersom omdirigeringen kan ändras på begäran bör klienten fortsätta att använda den effektiva begär ande-URI: n för framtida begär Anden.
- **307 (tillfällig omdirigering)**: visar att mål resursen finns tillfälligt under en annan URI och att användar agenten inte får ändra metoden för begäran om den utför en automatisk omdirigering till denna URI. Eftersom omdirigeringen kan ändras med tiden bör klienten fortsätta att använda den ursprungliga effektiva begär ande-URI: n för framtida begär Anden.
- **308 (permanent omdirigering)**: visar att mål resursen har tilldelats en ny permanent URI och att eventuella framtida referenser till den här resursen ska använda en av de omslutna URI: erna. Klienter med redigerings funktioner för länkar bör automatiskt länka om referenser till den effektiva begäran-URI: n till en eller flera av de nya referenser som skickas av servern, där det är möjligt.

## <a name="redirection-protocol"></a>Protokoll för omdirigering
Du kan ange det protokoll som ska användas för omdirigering. Detta gör att en av de vanligaste användnings fallen för funktionen omdirigera är att ställa in HTTP till HTTPS-omdirigering.

- **Endast https**: Ange protokollet till endast https, om du vill dirigera om trafiken från http till https. Azures front dörr rekommenderar att du alltid ställer in omdirigeringen till endast HTTPS.
- **Endast http**: detta omdirigerar inkommande begäran till http. Använd bara det här värdet om du vill behålla ditt trafik-HTTP-värde som inte är krypterat.
- **Matchnings förfrågan**: det här alternativet behåller protokollet som används av den inkommande begäran. En HTTP-begäran är alltså fortfarande HTTP och en HTTPS-begäran är kvar som omdirigering av HTTPS-post.

## <a name="destination-host"></a>Målvärd
Som en del av konfigureringen av en Omdirigerad routning kan du också ändra värdnamn eller domän för omdirigerings förfrågan. Du kan ange det här fältet om du vill ändra värd namnet i URL: en för omdirigeringen eller på annat sätt bevara värd namnet från den inkommande begäran. Därför kan du använda det här fältet för att dirigera om alla förfrågningar `https://www.contoso.com/*` som skickas till `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Målsökväg
I de fall där du vill ersätta Sök vägs segmentet för en URL som en del av omdirigeringen kan du ange det här fältet med värdet för ny sökväg. Annars kan du välja att bevara värdet för sökvägen som en del av omdirigeringen. Därför kan du använda det här fältet för att omdirigera alla begär Anden som skickas till `https://www.contoso.com/\*` till  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Parametrar för frågesträng
Du kan även ersätta Frågesträngens parametrar i den omdirigerade URL: en. För att ersätta en befintlig frågesträng från URL: en för inkommande begäran anger du fältet till Ersätt och anger sedan lämpligt värde. Annars kan du behålla den ursprungliga uppsättningen frågesträngar genom att ange fältet till "Preserve". Som exempel kan du använda det här fältet för att omdirigera all trafik som skickas till `https://www.contoso.com/foo/bar` till `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Målcachen
Målcachen är den del av URL: en som finns efter ' # ', som vanligt vis används av webbläsare för att landa på ett speciellt avsnitt på en sida. Du kan ange det här fältet om du vill lägga till ett fragment i omdirigerings-URL: en.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png