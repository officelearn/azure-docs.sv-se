---
title: Snabb start – introduktion till Piggy mått exempel App – Azure våren Cloud
description: Beskriver Piggy Metrics-exempelprogrammet som används i distribution av Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d833c8f136a71d563ce10240f03e2c68e9131687
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951896"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Introduktion till Piggy Metrics exempel App

I den här snabb starten kommer vi att använda ett proof-of-Concept-exempel på personlig ekonomi som kallas Piggy-mått för att visa hur du distribuerar en app till moln tjänsten Azure våren. Piggy-mått visar arkitektur mönstret för mikrotjänster och följande avsnitt visar analys av tjänster. Du får se hur det distribueras till Azure med kraftfulla Azure våren Cloud-funktioner från tjänst identifiering, konfigurations Server för loggar, statistik och distribuerad spårning.

Om du vill följa moln distributions exemplen för Azure våren behöver du bara platsen för käll koden, som tillhandahålls vid behov.

## <a name="functional-services"></a>Funktionella tjänster
Piggy-måtten sammanställs i tre kärn mikrotjänster. Alla är oberoende distributions bara program, ordnade runt företags domäner.

* **Konto tjänst (som ska distribueras)**: innehåller allmän logik för användar indata och validering: indata/utgifter, besparingar och konto inställningar.
* **Statistik tjänst (används inte i den här snabb starten)**: utför beräkningar på viktiga statistik parametrar och fångar tids serier för varje konto. Datapoint innehåller värden, normaliserad till bas valuta och tids period. Dessa data används för att spåra kassa flödet Dynamics i kontots livs längd.
* **Meddelande tjänst (används inte i den här snabb starten)**: lagrar användarens kontakt uppgifter och meddelande inställningar, till exempel en påminnelse-och säkerhets kopierings frekvens. Schemalagda arbetare samlar in nödvändig information från andra tjänster och skickar e-postmeddelanden till kunder som prenumererar på prenumerationer.

## <a name="infrastructure-services"></a>Infrastruktur tjänster
Det finns flera vanliga mönster i distribuerade system som hjälper till att öka kärn tjänsterna. Azure våren Cloud tillhandahåller kraftfulla verktyg som förbättrar funktionerna för att starta program för att implementera dessa mönster: 

* **Konfigurations tjänst (värd av Azure våren Cloud)**: Azure våren Cloud config är en vågrätt skalbar centraliserad konfigurations tjänst för distribuerade system. Den använder ett lagrings Bart lager som för närvarande stöder lokal lagring, git och under version.
* **Tjänst identifiering (värd av Azure våren Cloud)**: den tillåter automatisk identifiering av nätverks platser för tjänst instanser, vilket kan ha dynamiskt tilldelade adresser på grund av automatisk skalning, problem och uppgraderingar.
* **Auth service (kommer att distribueras)** Auktorisations ansvar extraheras fullständigt till en separat server, vilket ger OAuth2-token för Server dels resurs tjänsterna. Auth Server auktoriserar användarautentisering och skyddar maskin-till-dator-kommunikation inuti en perimeter.
* **API-Gateway (kommer att distribueras)**: de tre kärn tjänsterna exponerar ett externt API för klienten. I verkliga system kan antalet funktioner växa mycket snabbt med system komplexitet. Hundratals tjänster kan vara inblandade i åter givning av en komplex webb sida. API-gatewayen är en enda start punkt i systemet, som används för att hantera begär Anden och dirigera dem till lämplig server dels tjänst eller för att anropa flera backend-tjänster, vilket aggregerar resultaten. 

## <a name="sample-usage-of-piggy-metrics"></a>Exempel på användning av piggy-mått
Fullständig implementerings information finns i [Piggy mått](https://github.com/Azure-Samples/piggymetrics). Exemplen refererar till käll koden vid behov.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Etablera Azure våren Cloud-instansen](spring-cloud-quickstart-provision-service-instance.md)
