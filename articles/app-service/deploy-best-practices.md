---
title: Metod tips för distribution – Azure App Service | Microsoft Docs
description: Lär dig mer om viktiga komponenter för att distribuera till Azure App Service.
keywords: Azure App Service, webbapp, distribuera, distribution, pipeliner, build
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 2beb35002cb98119db90f4cd278decc185ea35d7
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536500"
---
# <a name="deployment-best-practices"></a>Metod tips för distribution

Alla utvecklings team har unika krav som kan göra det svårt att implementera en effektiv distributions pipeline på alla moln tjänster. Den här artikeln beskriver de tre huvud komponenterna i distribution till App Service: distributions källor, bygga pipelines och distributions metoder. Den här artikeln beskriver också några metod tips och tips för specifika språk stackar.

## <a name="deployment-components"></a>Distributions komponenter

### <a name="deployment-source"></a>Distributionskälla

En distributions källa är platsen för program koden. För produktions program är distributions källan vanligt vis en lagrings plats som är värd för program vara för versions kontroll, till exempel [GitHub, BitBucket eller Azure databaser](deploy-continuous-deployment.md). I utvecklings-och test scenarier kan distributions källan vara [ett projekt på din lokala dator](deploy-local-git.md). App Service stöder även [OneDrive-och Dropbox-mappar](deploy-content-sync.md) som distributions källor. Även om Cloud Folders kan göra det enkelt att komma igång med App Service, rekommenderar vi vanligt vis inte att använda den här källan för produktions program på företags nivå. 

### <a name="build-pipeline"></a>Bygg-pipeline

När du bestämmer dig för en distributions källa är nästa steg att välja en pipeline för bygge. En build-pipeline läser din käll kod från distributions källan och kör en serie steg (till exempel kompilera kod, minifying HTML och Java Script, köra tester och paket komponenter) för att hämta programmet i ett körbara-tillstånd. Specifika kommandon som körs av bygg pipelinen beror på din språks tack. Dessa åtgärder kan utföras på en build-Server, till exempel Azure-pipelines eller som körs lokalt.

### <a name="deployment-mechanism"></a>Distributions mekanism

Distributions mekanismen är den åtgärd som används för att publicera det inbyggda programmet i */Home/site/wwwroot* -katalogen för din webbapp. */Wwwroot* -katalogen är en monterad lagrings plats som delas av alla instanser av din webbapp. När mekanismen för distribution placerar ditt program i den här katalogen, får dina instanser ett meddelande om att synkronisera de nya filerna. App Service stöder följande distributions metoder:

- Kudu-slutpunkter: [Kudu](https://github.com/projectkudu/kudu/wiki) är ett produktivitets verktyg för utvecklare med öppen källkod som körs som en separat process i Windows app service och som en andra behållare i Linux-app service. Kudu hanterar kontinuerliga distributioner och tillhandahåller HTTP-slutpunkter för distribution, till exempel zipdeploy.
- FTP och WebDeploy: Med hjälp av dina [webbplats-eller användarautentiseringsuppgifter](deploy-configure-credentials.md)kan du ladda upp filer [via FTP](deploy-ftp.md) eller WebDeploy. Dessa mekanismer går inte igenom kudu.  

Distributions verktyg som Azure-pipeline, Jenkins och redigerings-plugin-program använder en av dessa distributions mekanismer.

## <a name="language-specific-considerations"></a>Språkspecifika överväganden

### <a name="java"></a>Java

Använd kudu [-zipdeploy/](deploy-zip.md) API för att distribuera jar-program och [WARDEPLOY/](deploy-zip.md#deploy-war-file) för krigs appar. Om du använder Jenkins kan du använda dessa API: er direkt i distributions fasen. Mer information finns i [den här artikeln](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Nod

Som standard kör kudu Bygg stegen för Node-programmet (`npm install`). Om du använder en build-tjänst, till exempel Azure DevOps, är kudu-versionen onödig. Om du vill inaktivera kudu skapar du en app-inställning `SCM_DO_BUILD_DURING_DEPLOYMENT`med `false`värdet.

### <a name="net"></a>.NET 

Som standard kör kudu Bygg stegen för ditt .NET-program (`dotnet build`). Om du använder en build-tjänst, till exempel Azure DevOps, är kudu-versionen onödig. Om du vill inaktivera kudu skapar du en app-inställning `SCM_DO_BUILD_DURING_DEPLOYMENT`med `false`värdet.

## <a name="other-deployment-considerations"></a>Andra distributions överväganden

### <a name="use-deployment-slots"></a>Använd distributions platser

När det är möjligt ska du använda [distributions platser](deploy-staging-slots.md) när du distribuerar en ny produktions version. När du använder en standard-App Service-plannivå eller bättre kan du distribuera din app till en mellanlagringsplats, validera dina ändringar och utföra rök test. När du är klar kan du växla mellan mellanlagrings-och produktions platser. Växlings åtgärden ökar de arbets instanser som krävs för att matcha din produktions skala, vilket eliminerar drift stopp. 

### <a name="local-cache"></a>Lokal cache

Azure App Service innehåll lagras på Azure Storage och placeras på ett hållbart sätt som en innehålls resurs. Vissa appar behöver dock bara ett skrivskyddat innehålls lager med hög prestanda som de kan köra med hög tillgänglighet. De här apparna kan dra nytta av [lokal cache](overview-local-cache.md). Local cache rekommenderas inte för innehålls hanterings webbplatser som WordPress.

Använd alltid lokalt cacheminne tillsammans med [distributions platser] (distribuera-mellanlagrings platser MD) för att förhindra stillestånds tid. I [det här avsnittet](overview-local-cache.md#best-practices-for-using-app-service-local-cache) finns information om hur du använder dessa funktioner tillsammans.

### <a name="high-cpu-or-memory"></a>Hög CPU eller minne

Om din App Services plan använder över 90% av tillgänglig processor eller minne kan den underliggande virtuella datorn ha problem med att bearbeta distributionen. När detta inträffar kan du tillfälligt skala upp antalet instanser för att utföra distributionen. När distributionen är färdig kan du returnera instans antalet till det tidigare värdet.
