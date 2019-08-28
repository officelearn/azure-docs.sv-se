---
title: Distribuera anpassad avbildning, flera behållare eller inbyggda avbildnings Azure App Service | Microsoft Docs
description: Hur du avgör mellan en anpassad distribution av Docker-behållare, flera behållare och ett inbyggt program ramverk för App Service i Linux
keywords: Azure App Service, webbapp, Linux, oss
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066779"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Anpassad avbildning, multi-container eller inbyggd plattforms avbildning?

[App service på Linux](app-service-linux-intro.md) erbjuder tre olika sökvägar för att få ditt program att publiceras på webben:

- **Anpassad avbildnings distribution**: "Hantera" din app i en Docker-avbildning som innehåller alla dina filer och beroenden i ett paket som är redo att köras.
- **Distribution med flera behållare**: "Hantera" din app över flera behållare med hjälp av en Docker-filkonfigurations fil.
- **Distribution av appar med en inbyggd plattforms avbildning**: Våra inbyggda plattforms avbildningar innehåller vanliga webb program körningar och beroenden, till exempel Node och PHP. Använd någon av [Azure App Service distributions metoder](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) för att distribuera din app till webbappens lagring och Använd sedan en inbyggd plattforms avbildning för att köra den.

## <a name="which-method-is-right-for-your-app"></a>Vilken metod är rätt för din app? 

De främsta faktorer som du bör tänka på är:

- **Tillgänglighet för Docker i arbets flödet för utveckling**: För anpassad bild utveckling krävs grundläggande kunskaper om Docker Development-arbetsflödet. Distribution av en anpassad avbildning till en webbapp kräver att den anpassade avbildningen publiceras till en lagrings plats värd som Docker Hub. Om du är bekant med Docker och kan lägga till Docker-uppgifter till ditt Bygg arbets flöde, eller om du redan publicerar din app som en Docker-avbildning, är en anpassad avbildning nästan verkligen det bästa valet.
- **Arkitektur med flera lager**: Distribuera flera behållare, till exempel ett webb program lager och ett API-lager, för att separera funktioner med hjälp av flera behållare. 
- **Program prestanda**: Öka prestandan för din app med flera behållare med hjälp av ett cache-lager, till exempel Redis. Välj multi-container för att uppnå detta.
- **Unika körnings krav**: De inbyggda plattforms avbildningarna är utformade för att uppfylla behoven hos de flesta webbappar, men är begränsade i deras anpassningsbarhet. Din app kan ha unika beroenden eller andra körnings krav som överskrider vad de inbyggda avbildningarna kan hantera.
- **Versions krav**: Med [kontinuerlig distribution](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)kan du få igång din app på Azure direkt från käll koden. Ingen extern version eller publicerings process krävs. Det finns dock en gräns för anpassningsbarhet och tillgänglighet för build-verktyg i [kudu](https://github.com/projectkudu/kudu/wiki) -distributions motorn. Din app kan utvidga kudu-funktionerna när de växer i dess beroenden eller krav för anpassad versions logik.
- **Läs-/skriv krav för disk**: Alla webbappar tilldelas en lagrings volym för webb innehåll. Den här volymen, som backas upp av Azure Storage, `/home` monteras på i appens fil system. Till skillnad från filer i behållarens fil system, är filer i innehålls volymen tillgängliga i alla skalnings instanser av en app, och ändringar behålls i appens omstarter. Disk fördröjningen för innehålls volymen är dock högre och mer variabel än svars tiden för det lokala behållar systemet och åtkomst kan påverkas av plattforms uppgraderingar, oplanerade avbrott och problem med nätverks anslutningen. Appar som kräver tung skrivskyddad åtkomst till innehållsfiler kan dra nytta av anpassad avbildnings distribution, som placerar filer i avbildnings fil systemet i stället för på innehålls volymen.
- **Bygg**resursanvändning: När en app distribueras från källan använder de distributions skript som körs av kudu samma App Service planera beräknings-och lagrings resurser som appen som körs. Stora distributioner av appar kan förbruka fler resurser eller tid än vad som önskas. I synnerhet genererar många distributions arbets flöden tung disk aktivitet på appens innehålls volym, som inte är optimerad för sådan aktivitet. En anpassad avbildning ger all din apps filer och beroenden till Azure i ett enda paket utan några behov av ytterligare fil överföringar eller distributions åtgärder.
- **Behov av snabb iteration**: Dockerizing en app kräver ytterligare build-steg. För att ändringarna ska börja gälla måste du skicka den nya avbildningen till en lagrings plats med varje uppdatering. De här uppdateringarna hämtas sedan till Azure-miljön. Om någon av de inbyggda behållarna uppfyller appens behov kan distributionen från källan erbjuda ett snabbare utvecklings arbets flöde.

## <a name="next-steps"></a>Nästa steg

Anpassad behållare:
* [Kör anpassad behållare](quickstart-docker-go.md)

Multi-container:
* [Skapa app med flera behållare](quickstart-multi-container.md)

Följande artiklar hjälper dig att komma igång med App Service på Linux med en inbyggd plattforms avbildning:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)