---
title: "Azure Apptjänst för distribution av Linux - anpassade bilden eller inbyggda plattform?  | Microsoft Docs"
description: "Hur du avgör mellan distribution av anpassade Docker-behållaren och inbyggda programramverk för Apptjänst på Linux"
keywords: "Azure apptjänst, webbprogram, linux, oss"
services: app-service
documentationCenter: 
authors: nickwalk
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: nickwalk
ms.openlocfilehash: 4a04bba2375b5a107bc3cb8cdc1a75d037c50af6
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="custom-image-or-built-in-platform-image"></a>Anpassad avbildning eller inbyggda plattformsavbildning?

[Apptjänst i Linux](app-service-linux-intro.md) erbjuder två olika sökvägar för att få ditt program som publiceras på webben:

- **Distribution av anpassade avbildningar**: ”Dockerize” din app i en Docker-avbildning som innehåller alla filer och beroenden i en klar och kör paketet.
- **App-distribution med en inbyggd plattformsavbildning**: vår inbyggda plattform avbildningar innehåller vanliga web app körningar och beroenden, till exempel nod och PHP. Använd någon av de [distributionsmetoder för Azure App Service](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) att distribuera appen till ditt webbprogram lagring och sedan använda en inbyggd plattformsavbildning för att köra den.

Vilken metod som passar din app? Det är de primära faktorerna att tänka på:

- **Tillgängligheten för Docker i arbetsflödet development**: anpassad utveckling kräver grundläggande kunskaper om Docker utveckling arbetsflödet. Distribution av en anpassad avbildning till en webbapp kräver publicering av den anpassade avbildningen till en värd för databasen som Docker-hubb. Om du är bekant med Docker och kan lägga till Docker aktiviteter till build-arbetsflöde, eller om du redan publicerar appen som en Docker-avbildning är en anpassad avbildning sannolikt det bästa valet.
- **Unik runtime krav**: inbyggda plattform bilder är utformade för att möta behoven hos de flesta webbappar, men är begränsade i sina anpassningsbarheten. Din app kan ha unika beroenden eller andra krav för körning som överskrider vad inbyggda avbildningar kan.
- **Skapa krav**: med [kontinuerlig distribution](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), kan du appen körs på Azure direkt från koden. Det krävs ingen extern process build eller publikation. Men det finns en gräns anpassningsbarheten och tillgänglighet av build tools inom den [Kudu](https://github.com/projectkudu/kudu/wiki) distributionsmotorn. Din app kan växa ifrån Kudus funktioner som den växer i dess beroenden eller krav för anpassade build-logiken.
- **Krav för läsning och skrivning**: alla webbprogram är tilldelat en lagringsvolym för webbinnehåll. Den här volymen som backas upp av Azure Storage monteras `/home` i appens filsystem. Till skillnad från filer i filsystemet behållaren filerna i innehåll volymen är tillgänglig i alla skala instanser av en app och ändringar behålls mellan olika omstarter av app. Dock disk svarstiden för innehåll volymen är större och mer variabeln än svarstiden för lokala behållaren filsystemet och åtkomst kan påverkas av uppgraderingar och oplanerade driftavbrott problem med nätverksanslutningen. Appar som kräver mycket skrivskyddad åtkomst till innehållet i filerna kan dra nytta av distribution av anpassade avbildningar, som placerar filer i filsystemet bilden i stället för på innehåll volymen.
- **Skapa Resursanvändning**: när en app distribueras från källan distributionsskriptet kör av Kudu Använd samma App Service-Plan beräknings- och resurser som appen körs. Stora appdistributioner kan förbruka mer resurser eller tid än önskad. I synnerhet generera arbetsflöden för distribution av många tunga diskaktivitet på app innehåll volym, vilket inte är optimerad för denna aktivitet. En anpassad avbildning levererar alla filer och beroenden för din app till Azure i ett paket med inget behov av ytterligare filöverföringar eller distributionsåtgärder.
- **Behöver för snabb iteration**: Dockerizing en app kräver ytterligare build steg. För att ändringarna ska börja gälla måste du skicka nya avbildningen till en databas som innehåller varje uppdatering. Uppdateringarna hämtas sedan till Azure-miljön. Om en av de inbyggda behållarna uppfyller behoven för din app kan distribution från källan erbjuda ett snabbare utveckling arbetsflöde.