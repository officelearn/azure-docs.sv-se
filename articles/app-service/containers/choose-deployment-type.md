---
title: Distribuera anpassad avbildning flera behållare eller inbyggd avbildning – Azure App Service | Microsoft Docs
description: Hur du avgör mellan anpassad Docker-behållardistribution, flera behållare och ett inbyggt ramverk för App Service i Linux
keywords: azure app service, web app, linux, oss
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c8a700bcd2780ef7b0c7ad1fbb513d4b4febffcb
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549328"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Anpassad avbildning, flera behållare eller inbyggda plattformsavbildning?

[App Service i Linux](app-service-linux-intro.md) erbjuder tre olika sökvägar för att få ditt program som publicerats på webben:

- **Distribution av anpassade avbildningar**: ”Behållarlagringsplats” din app i en dockeravbildning som innehåller alla dina filer och beroenden i ett paket är klara att köra.
- **Distribution av flera behållare**: ”Behållarlagringsplats” din app över flera behållare med Docker Compose eller en Kubernetes-konfigurationsfil.
- **Distribution med en inbyggd plattformsavbildning**: Våra inbyggda plattformsavbildningar innehåller vanliga web app körningar och beroenden, till exempel Node och PHP. Använd någon av de [metoder för distribution av Azure App Service](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) att distribuera appen till din webbapp lagring och sedan använda en inbyggd plattformsavbildning för att köra den.

## <a name="which-method-is-right-for-your-app"></a>Vilken metod som passar bäst för din app? 

De främsta faktorerna att överväga är:

- **Tillgängligheten för Docker i ditt utvecklingsarbetsflöde**: Anpassad avbildningsutveckling kräver grundläggande kunskaper om Docker-utvecklingsflöde. Distribution av en anpassad avbildning till en webbapp kräver publiceringen av den anpassade avbildningen till en värd för databasen som Docker Hub. Om du är bekant med Docker och kan lägga till Docker-aktiviteter i arbetsflödet build, eller om du redan publicerar din app som en Docker-avbildning, är en anpassad avbildning sannolikt det bästa valet.
- **Flera lager arkitektur**: Distribuera flera behållare, till exempel en web-programnivån och ett API-lager för att separera funktioner med hjälp av flera behållare. 
- **Programprestanda**: Öka prestandan för din app för flera behållare med en cache-lager, till exempel Redis. Välj flera behållare för att uppnå detta.
- **Unikt runtime krav**: De inbyggda plattformsavbildningar är utformade för att uppfylla behoven hos de flesta webbappar, men är begränsade i sina anpassningsbarhet. Din app kan ha unika beroenden eller andra krav för körning som överskrider vad de inbyggda avbildningarna har kapacitet för.
- **Skapa krav**: Med [kontinuerlig distribution](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), du kan få din app igång och körs på Azure direkt från källkoden. Det krävs ingen extern process för bygge eller publikationen. Det finns dock en gräns för anpassningsbarhet och tillgängligheten för genereringsverktyg inom den [Kudu](https://github.com/projectkudu/kudu/wiki) distributionsmotorn. Din app kan växa ifrån Kudus funktioner när den växer i dess beroenden eller krav för anpassade build logik.
- **Läs/Skriv krav**: Alla webbprogram tilldelas en lagringsvolym för webbinnehåll. Den här volymen som backas upp av Azure Storage, monteras `/home` i appens filsystem. Till skillnad från filer i behållaren filsystemet filerna i innehåll volymen är tillgängliga i alla instanser för skalning av en app och ändringar beständig i alla app startas om. Dock disk svarstiden för innehåll volymen är större och mer variabeln än svarstiden för lokal behållare filsystem och åtkomst kan påverkas av uppgraderingar och oplanerade driftsavbrott problem med nätverksanslutningen. Appar som kräver tunga skrivskyddad åtkomst till innehållet i filerna kan ha nytta av distribution av anpassade avbildningar, som placerar filer i filsystemet avbildningen i stället för på innehåll volymen.
- **Skapa Resursanvändning**: När en app har distribuerats från källa kör distributionsskripten med Kudu samma App Service-Plan beräknings- och resurser som appen körs. Stora appdistributioner kan förbruka mer resurser eller tid än önskad. I synnerhet genererar många arbetsflöden för distribution tung diskaktivitet på app innehåll volym, vilket inte är optimerad för sådana aktiviteter. En anpassad avbildning får du alla filer och beroenden för din app till Azure i ett enda paket med inget behov av ytterligare filöverföringar eller distributionsåtgärder.
- **Behovet av snabba iterationer**: Dockerizing en app kräver ytterligare byggsteg. För att ändringarna ska börja gälla måste du skicka dina nya avbildningen till en lagringsplats med varje uppdatering. De här uppdateringarna hämtas sedan till Azure-miljön. Om någon av de inbyggda behållarna uppfyller behoven för dina appar, kan distribuera från källan erbjuda en snabbare arbetsflöde för utveckling.
