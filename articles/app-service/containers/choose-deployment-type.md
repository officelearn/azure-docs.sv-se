---
title: Distributionsalternativ för Linux-behållare
description: Bestäm mellan anpassad Docker-behållardistribution, flera behållare och ett inbyggt programramverk för App Service på Linux.
keywords: azure app tjänst, webbapp, linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687518"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Anpassad bild, flera behållare eller inbyggd plattformsavbildning?

[App Service på Linux](app-service-linux-intro.md) erbjuder tre olika vägar för att få ditt program publiceras på webben:

- **Anpassad avbildningsdistribution**: "Dockerize" din app till en Docker-avbildning som innehåller alla dina filer och beroenden i ett färdigt paket.
- **Distribution med flera behållare:**"Dockerize" din app över flera behållare med hjälp av en Docker Compose-konfigurationsfil.
- **Appdistribution med en inbyggd plattformsavbildning:** Våra inbyggda plattformsavbildningar innehåller vanliga webbappkörningar och beroenden, till exempel Node och PHP. Använd någon av [distributionsmetoderna](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) för Azure App Service för att distribuera din app till webbappens lagring och använd sedan en inbyggd plattformsavbildning för att köra den.

## <a name="which-method-is-right-for-your-app"></a>Vilken metod är rätt för din app? 

De viktigaste faktorerna att tänka på är:

- **Tillgänglighet för Docker i utvecklingsarbetsflödet**: Anpassad bildutveckling kräver grundläggande kunskaper i Dockers utvecklingsarbetsflöde. Distribution av en anpassad avbildning till en webbapp kräver publicering av din anpassade avbildning till en databasvärd som Docker Hub. Om du är bekant med Docker och kan lägga till Docker-uppgifter i ditt byggarbetsflöde, eller om du redan publicerar appen som en Docker-avbildning, är en anpassad avbildning nästan säkert det bästa valet.
- **Arkitektur i flera lager**: Distribuera flera behållare, till exempel ett webbprogramlager och ett API-lager, för att separera funktioner med hjälp av flera behållare. 
- **Programprestanda**: Öka prestanda för din app med flera behållare med hjälp av ett cachelager som Redis. Välj flera behållare för att uppnå detta.
- **Unika körningskrav:** De inbyggda plattformsavbildningarna är utformade för att möta behoven hos de flesta webbappar, men är begränsade i sin anpassningsbarhet. Din app kan ha unika beroenden eller andra körningskrav som överskrider vad de inbyggda avbildningarna klarar av.
- **Byggkrav:** Med [kontinuerlig distribution](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)kan du få igång din app på Azure direkt från källkoden. Ingen extern bygg- eller publiceringsprocess krävs. Det finns dock en gräns för anpassningsbarhet och tillgänglighet [Kudu](https://github.com/projectkudu/kudu/wiki) för byggverktyg i Kudu-distributionsmotorn. Din app kan växa ur Kudus funktioner när den växer i sina beroenden eller krav för anpassad bygglogik.
- **Diskläsnings-/skrivkrav**: Alla webbappar tilldelas en lagringsvolym för webbinnehåll. Den här volymen, som stöds av `/home` Azure Storage, är monterad i appens filsystem. Till skillnad från filer i behållarfilsystemet är filer i innehållsvolymen tillgängliga för alla skalningsinstanser i en app, och ändringar sparas över appstarter. Innehållsvolymens diskfördröjning är dock högre och mer variabel än svarstiden för det lokala behållarfilsystemet, och åtkomsten kan påverkas av plattformsuppgraderingar, oplanerade driftstopp och problem med nätverksanslutning. Appar som kräver tung skrivskyddad åtkomst till innehållsfiler kan dra nytta av anpassad avbildningsdistribution, vilket placerar filer i bildfilsystemet i stället för på innehållsvolymen.
- **Skapa resursanvändning:** När en app distribueras från källan använder distributionsskripten som körs av Kudu samma beräknings- och lagringsresurser för App Service Plan som appen som körs. Stora appdistributioner kan förbruka mer resurser eller tid än önskat. I synnerhet genererar många distributionsarbetsflöden tung diskaktivitet på appens innehållsvolym, som inte är optimerad för sådan aktivitet. En anpassad avbildning levererar alla appens filer och beroenden till Azure i ett enda paket utan behov av ytterligare filöverföringar eller distributionsåtgärder.
- **Behov av snabb iteration:** Dockerisering av en app kräver ytterligare byggsteg. För att ändringarna ska börja gälla måste du skicka den nya bilden till en databas med varje uppdatering. Dessa uppdateringar hämtas sedan till Azure-miljön. Om en av de inbyggda behållarna uppfyller appens behov kan distribution från källa erbjuda ett snabbare utvecklingsarbetsflöde.

## <a name="next-steps"></a>Nästa steg

Anpassad behållare:
* [Köra en anpassad container](quickstart-docker-go.md)

Flera behållare:
* [Skapa app med flera behållare](quickstart-multi-container.md)

Följande artiklar kommer igång med App Service på Linux med en inbyggd plattformsavbildning:

* [.NET Core](quickstart-dotnetcore.md)
* [Php](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)