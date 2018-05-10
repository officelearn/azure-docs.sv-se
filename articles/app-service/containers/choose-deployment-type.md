---
title: Azure Apptjänst för distribution av Linux - anpassad bild, flera behållare eller inbyggda plattformsavbildning?  | Microsoft Docs
description: Hur du avgör mellan distribution av anpassade Docker-behållare, flera behållare och inbyggda programramverk för Apptjänst på Linux
keywords: Azure apptjänst, webbprogram, linux, oss
services: app-service
documentationCenter: ''
authors: msangapu
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.openlocfilehash: 012f78fc07f237e8ed532246c81a3c86bb6ab4ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Anpassad avbildning, flera behållare eller inbyggda plattformsavbildning?

[Apptjänst i Linux](app-service-linux-intro.md) erbjuder tre olika sökvägar för att få ditt program som publiceras på webben:

- **Distribution av anpassade avbildningar**: ”Dockerize” din app i en Docker-avbildning som innehåller alla filer och beroenden i en klar och kör paketet.
- **Distribution av flera behållare**: ”Dockerize” din app över flera behållare med Docker Compose eller en Kubernetes konfigurationsfil. Mer information finns i [flera behållare app](#multi-container-apps-supportability).
- **App-distribution med en inbyggd plattformsavbildning**: vår inbyggda plattform avbildningar innehåller vanliga web app körningar och beroenden, till exempel nod och PHP. Använd någon av de [distributionsmetoder för Azure App Service](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) att distribuera appen till ditt webbprogram lagring och sedan använda en inbyggd plattformsavbildning för att köra den.

##<a name="which-method-is-right-for-your-app"></a>Vilken metod som passar din app? 

Det är de primära faktorerna att tänka på:

- **Tillgängligheten för Docker i arbetsflödet development**: anpassad utveckling kräver grundläggande kunskaper om Docker utveckling arbetsflödet. Distribution av en anpassad avbildning till en webbapp kräver publicering av den anpassade avbildningen till en värd för databasen som Docker-hubb. Om du är bekant med Docker och kan lägga till Docker aktiviteter till build-arbetsflöde, eller om du redan publicerar appen som en Docker-avbildning är en anpassad avbildning sannolikt det bästa valet.
- **Arkitektur för flera lager**: distribuera flera behållare, till exempel ett web application lager och en API-lagret för att separera funktioner med hjälp av flera behållare. 
- **Programprestanda**: öka prestanda för flera behållare appen med en cache-lager, till exempel Redis. Välj flera behållare för att åstadkomma detta.
- **Unik runtime krav**: inbyggda plattform bilder är utformade för att möta behoven hos de flesta webbappar, men är begränsade i sina anpassningsbarheten. Din app kan ha unika beroenden eller andra krav för körning som överskrider vad inbyggda avbildningar kan.
- **Skapa krav**: med [kontinuerlig distribution](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), kan du appen körs på Azure direkt från koden. Det krävs ingen extern process build eller publikation. Men det finns en gräns anpassningsbarheten och tillgänglighet av build tools inom den [Kudu](https://github.com/projectkudu/kudu/wiki) distributionsmotorn. Din app kan växa ifrån Kudus funktioner som den växer i dess beroenden eller krav för anpassade build-logiken.
- **Krav för läsning och skrivning**: alla webbprogram är tilldelat en lagringsvolym för webbinnehåll. Den här volymen som backas upp av Azure Storage monteras `/home` i appens filsystem. Till skillnad från filer i filsystemet behållaren filerna i innehåll volymen är tillgänglig i alla skala instanser av en app och ändringar behålls mellan olika omstarter av app. Dock disk svarstiden för innehåll volymen är större och mer variabeln än svarstiden för lokala behållaren filsystemet och åtkomst kan påverkas av uppgraderingar och oplanerade driftavbrott problem med nätverksanslutningen. Appar som kräver mycket skrivskyddad åtkomst till innehållet i filerna kan dra nytta av distribution av anpassade avbildningar, som placerar filer i filsystemet bilden i stället för på innehåll volymen.
- **Skapa Resursanvändning**: när en app distribueras från källan distributionsskriptet kör av Kudu Använd samma App Service-Plan beräknings- och resurser som appen körs. Stora appdistributioner kan förbruka mer resurser eller tid än önskad. I synnerhet generera arbetsflöden för distribution av många tunga diskaktivitet på app innehåll volym, vilket inte är optimerad för denna aktivitet. En anpassad avbildning levererar alla filer och beroenden för din app till Azure i ett paket med inget behov av ytterligare filöverföringar eller distributionsåtgärder.
- **Behöver för snabb iteration**: Dockerizing en app kräver ytterligare build steg. För att ändringarna ska börja gälla måste du skicka nya avbildningen till en databas som innehåller varje uppdatering. Uppdateringarna hämtas sedan till Azure-miljön. Om en av de inbyggda behållarna uppfyller behoven för din app kan distribution från källan erbjuda ett snabbare utveckling arbetsflöde.

## <a name="multi-container-apps-supportability"></a>Flera behållare appar support

### <a name="supported-docker-compose-configuration-options"></a>Konfigurationsalternativ för stöds Docker Compose
- kommandot
- EntryPoint
- miljö
- Bild
- Portar
- Starta om
- Tjänster
- Volymer

### <a name="unsupported-docker-compose-configuration-options"></a>Stöds inte Docker Compose konfigurationsalternativ
- Skapa (tillåts inte)
- depends_on (ignoreras)
- nätverk (ignoreras)
- hemligheter (ignoreras)
- andra portar än 80 och 8080 (ignoreras)

> [!NOTE]
> Andra alternativ som inte uttryckligen påpekas ignoreras också i Public Preview.

### <a name="supported-kubernetes-configuration-options"></a>Stöds Kubernetes konfigurationsalternativ
- argument
- kommandot
- behållare
- Bild
- namn
- Portar
- specifikationen

> [!NOTE]
>Andra alternativ som Kubernetes inte uttryckligen påpekas stöds inte i Public Preview.
>