---
title: Azure Front luckan Service - URL-omdirigering | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure ytterdörren Service stöder URL-omdirigering för sina vägar, om konfigurerad.
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
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333310"
---
# <a name="url-redirect"></a>URL-omdirigering
Du kan använda Azure ytterdörren Service för att omdirigera trafik. Du kan omdirigera trafik på flera nivåer (protokoll, värdnamn, sökväg, frågesträng) och vissa funktioner kan konfigureras för enskilda mikrotjänster skick omdirigerings-baserad. Detta förenklar konfigurationen, optimerar resursanvändningen och har stöd för den nya omdirigering scenarier inklusive globala och sökvägsbaserad omdirigering.
</br>

![Azure ytterdörren URL-omdirigering][1]

## <a name="redirection-types"></a>Typer av omdirigering
En typ av omdirigering anger Svarets statuskod för klienterna att förstå syftet med omdirigeringen. Följande typer av omdirigering stöds:

- **301 (flyttas permanent)** : Anger att målresursen har tilldelats en ny permanent URI och alla framtida referenser till den här resursen bör använda en av de bifogade URI: er. Använd 301 statuskod för HTTP till HTTPS-omdirigering. 
- **302 (hittades)** : Anger att målresursen finns tillfälligt under en annan URI. Eftersom omdirigeringen kan ändras ibland, bör fortsätta att använda effektiva begärande-URI för framtida begäranden till klienten.
- **307 (tillfällig omdirigerings)** : Anger att målresursen finns tillfälligt under en annan URI och användaragent får inte ändra metoden för begäran om den utför en automatisk omdirigering till denna URI. Eftersom omdirigeringen kan ändras med tiden bör fortsätta att använda den ursprungliga effektiva URI för framtida begäranden till klienten.
- **308 (permanent omdirigering)** : Anger att målresursen har tilldelats en ny permanent URI och alla framtida referenser till den här resursen bör använda en av de bifogade URI: er. Klienter med länken redigeringsfunktioner, borde automatiskt länka om referenser till faktiska be URI till en eller flera av de nya referenser som skickats av servern, där det är möjligt.

## <a name="redirection-protocol"></a>Omdirigering av protokollet
Du kan ange det protokoll som ska användas för omdirigering. Det möjliggör en av de vanligaste användningsområden i omdirigerings-funktion som är att ange HTTP till HTTPS-omdirigering.

- **Endast HTTPS**: Ställ in protokollet till HTTPS, om du vill dirigera trafik från HTTP till HTTPS. Azure rekommenderar ytterdörren att du ska alltid ange omdirigeringen till HTTPS endast.
- **Endast HTTP**: Det här dirigerar inkommande begäran till HTTP. Använd det här värdet endast om du vill behålla din trafik HTTP som är, icke-krypterade.
- **Matcha begäran**: Det här alternativet behåller det protokoll som används av den inkommande begäranden. Därför en HTTP-förfrågan stannar kvar HTTP och HTTPS-begäran förblir HTTPS post omdirigering.

## <a name="destination-host"></a>Målvärd
Som en del av att konfigurera en omdirigerings-routning, kan du också ändra värdnamnet eller domänen för begäran om omdirigering. Du kan ange det här fältet om du vill ändra värdnamn i URL-Adressen för omdirigeringen eller annars bevara värdnamnet från den inkommande begäranden. Så använder det här fältet du kan omdirigera alla begäranden som skickas https://www.contoso.com/ * till https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Målsökväg
I de fall där du vill ersätta vägsegment av en URL som en del av omdirigering, kan du ange det här fältet med det nya värdet för sökvägen. Du kan också välja att bevara sökvägen som en del av omdirigering. Så använder det här fältet, du kan omdirigera alla begäranden som skickas till https://www.contoso.com/ * till https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Parametrar för frågesträngen
Du kan även ersätta frågesträngparametrar i den omdirigerade platsen. Ange det här fältet till ”Replace” för att ersätta alla befintliga frågesträngen från URL: en för inkommande begäran, och ange sedan lämpligt värde. Annars kan behålla du den ursprungliga uppsättningen frågesträngar genom att ange värdet 'Preserve'. Till exempel använda det här fältet, du kan omdirigera all trafik som skickas till https://www.contoso.com/foo/bar till https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Mål-fragment
Mål-fragment är del av URL: en efter '#', som normalt används av webbläsare hamnar på ett visst avsnitt på en sida. Du kan ange det här fältet för att lägga till ett fragment i omdirigerings-URL.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png