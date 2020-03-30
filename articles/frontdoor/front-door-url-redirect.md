---
title: Azure Ytterdörr - URL Redirect | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Azure Front Door stöder URL-omdirigering för deras vägar, om de är konfigurerade.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295481"
---
# <a name="url-redirect"></a>URL-omdirigering
Du kan använda Azure Front Door för att omdirigera trafik. Du kan omdirigera trafik på flera nivåer (protokoll, värdnamn, sökväg, frågesträng) och alla funktioner kan konfigureras för enskilda mikrotjänster eftersom omdirigeringen är sökvägsbaserad. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserad omdirigering.
</br>

![Omdirigering av URL-omdirigering av Front Door][1]

## <a name="redirection-types"></a>Omdirigeringstyper
En omdirigeringstyp anger svarsstatuskoden för klienterna för att förstå syftet med omdirigeringen. Följande typer av omdirigering stöds:

- **301 (Flyttad permanent):** Anger att målresursen har tilldelats en ny permanent URI och eventuella framtida referenser till den här resursen bör använda en av de bifogade URI:erna. Använd 301-statuskod för HTTP till HTTPS-omdirigering. 
- **302 (Hittades):** Anger att målresursen tillfälligt finns under en annan URI. Eftersom omdirigeringen kan ändras ibland, bör klienten fortsätta att använda den effektiva begäran URI för framtida begäranden.
- **307 (Tillfällig omdirigering):** Anger att målresursen tillfälligt finns under en annan URI och att användaragenten INTE får ändra begäran om den utför en automatisk omdirigering till den URI.307 (Temporary redirect) : Indicates that the target resource resides temporarily under a different URI and the user agent MUST NOT change the request method if it performs an automatic redirection to that URI. Eftersom omdirigeringen kan ändras med tiden bör klienten fortsätta att använda den ursprungliga effektiva begäranden URI för framtida begäranden.
- **308 (Permanent omdirigering):** Anger att målresursen har tilldelats en ny permanent URI och att eventuella framtida referenser till den här resursen bör använda en av de slutna URI:erna. Klienter med länkredigeringsfunktioner, bör automatiskt länka referenser till den faktiska begäran URI till en eller flera av de nya referenser som skickas av servern, om möjligt.

## <a name="redirection-protocol"></a>Omdirigeringsprotokoll
Du kan ange det protokoll som ska användas för omdirigering. Detta möjliggör ett av de vanligaste användningsfallen för omdirigeringsfunktionen, det vill veta att HTTP ska vara https-omdirigering.

- **HTTPS endast:** Ställ in protokollet på HTTPS, om du vill omdirigera trafiken från HTTP till HTTPS. Azure Front Door rekommenderar att du alltid bör ställa in omdirigering till HTTPS bara.
- **ENDAST HTTP:** Detta omdirigerar den inkommande begäran till HTTP. Använd bara det här värdet om du vill behålla din trafik HTTP som är icke-krypterad.
- **Matchningsbegäran**: Det här alternativet behåller det protokoll som används av den inkommande begäran. Så en HTTP-begäran förblir HTTP och en HTTPS-begäran förblir HTTPS post omdirigering.

## <a name="destination-host"></a>Värd för mål
Som en del av konfigurationen av en omdirigeringsroutning kan du också ändra värdnamnet eller domänen för begäran om omdirigering. Du kan ange det här fältet om du vill ändra värdnamnet i URL:en för omdirigering eller på annat sätt bevara värdnamnet från den inkommande begäran. Så med det här fältet kan `https://www.contoso.com/*` du `https://www.fabrikam.com/*`omdirigera alla begäranden som skickas vidare till .

## <a name="destination-path"></a>Målsökväg
För de fall där du vill ersätta sökvägssegmentet för en URL som en del av omdirigering kan du ange det här fältet med det nya sökvägsvärdet. Annars kan du välja att bevara sökvägsvärdet som en del av omdirigering. Så med det här fältet kan du `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`omdirigera alla begäranden som skickas till .

## <a name="query-string-parameters"></a>Parametrar för frågesträng
Du kan också ersätta frågesträngparametrarna i den omdirigerade URL:en. Om du vill ersätta en befintlig frågesträng från URL:en för inkommande begäran anger du det här fältet till Ersätt och anger sedan lämpligt värde. Annars kan du behålla den ursprungliga uppsättningen frågesträngar genom att ange fältet till Bevara. Med det här fältet kan du till exempel `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`omdirigera all trafik som skickas till . 

## <a name="destination-fragment"></a>Målfragment
Målfragmentet är den del av webbadressen efter '#', som normalt används av webbläsare för att landa på ett visst avsnitt på en sida. Du kan ange det här fältet om du vill lägga till ett fragment i omdirigerings-URL:en.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png