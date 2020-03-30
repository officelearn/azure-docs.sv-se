---
title: Bästa metoder för distribution
description: Lär dig mer om de viktigaste mekanismerna för distribution till Azure App Service. Hitta språkspecifika rekommendationer och andra förbehåll.
keywords: azure app tjänst, webbapp, distribuera, distribution, pipelines, bygga
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750463"
---
# <a name="deployment-best-practices"></a>Metodtips för distribution

Varje utvecklingsteam har unika krav som kan göra implementeringen av en effektiv distributionspipeline svår för alla molntjänster. Den här artikeln introducerar de tre huvudkomponenterna för distribution till App Service: distributionskällor, byggpipelor och distributionsmekanismer. Den här artikeln innehåller också några metodtips och tips för specifika språkstaplar.

## <a name="deployment-components"></a>Distributionskomponenter

### <a name="deployment-source"></a>Distributionskälla

En distributionskälla är platsen för programkoden. För produktionsappar är distributionskällan vanligtvis en databas som finns värd för versionskontrollprogram som [GitHub, BitBucket eller Azure Repos](deploy-continuous-deployment.md). För utvecklings- och testscenarier kan distributionskällan vara [ett projekt på den lokala datorn](deploy-local-git.md). App Service stöder också [OneDrive- och Dropbox-mappar](deploy-content-sync.md) som distributionskällor. Molnmappar kan göra det enkelt att komma igång med App Service, men det rekommenderas vanligtvis inte att använda den här källan för produktionsprogram på företagsnivå. 

### <a name="build-pipeline"></a>Bygg-pipeline

När du bestämmer dig för en distributionskälla är nästa steg att välja en build pipeline. En build pipeline läser din källkod från distributionskällan och kör en serie steg (till exempel kompilering av kod, minifiering av HTML och JavaScript, tester som körs och paketerar komponenter) för att få programmet i körbart tillstånd. Vilka kommandon som körs av byggpipelinen beror på språkstacken. Dessa åtgärder kan köras på en byggserver som Azure Pipelines eller köras lokalt.

### <a name="deployment-mechanism"></a>Mekanism för distribution

Distributionsmekanismen är den åtgärd som används för att placera det inbyggda programmet i katalogen */home/site/wwwroot* i webbappen. *Katalogen /wwwroot* är en monterad lagringsplats som delas av alla instanser av webbappen. När distributionsmekanismen placerar ditt program i den här katalogen får dina instanser ett meddelande om att synkronisera de nya filerna. App-tjänsten stöder följande distributionsmekanismer:

- Kudu-slutpunkter: [Kudu](https://github.com/projectkudu/kudu/wiki) är produktivitetsverktyget för utvecklare med öppen källkod som körs som en separat process i Windows App Service och som en andra behållare i Linux App Service. Kudu hanterar kontinuerliga distributioner och tillhandahåller HTTP-slutpunkter för distribution, till exempel zipdeploy.
- FTP och WebDeploy: Med hjälp av [webbplatsens eller användarens autentiseringsuppgifter](deploy-configure-credentials.md)kan du ladda upp filer [via FTP](deploy-ftp.md) eller WebDeploy. Dessa mekanismer går inte genom Kudu.  

Distributionsverktyg som Azure Pipelines, Jenkins och editor plugins använder en av dessa distributionsmekanismer.

## <a name="language-specific-considerations"></a>Språkspecifika överväganden

### <a name="java"></a>Java

Använd Kudu [zipdeploy/](deploy-zip.md) API för distribution av JAR-program och [wardeploy/](deploy-zip.md#deploy-war-file) for WAR-appar. Om du använder Jenkins kan du använda dessa API:er direkt i distributionsfasen. Mer information finns i [den här artikeln](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Node

Som standard kör Kudu byggstegen för nodprogrammet (`npm install`). Om du använder en byggtjänst som Azure DevOps är Kudu-versionen onödig. Om du vill inaktivera Kudu-versionen `SCM_DO_BUILD_DURING_DEPLOYMENT`skapar du `false`en appinställning med värdet .

### <a name="net"></a>.NET 

Som standard kör Kudu byggstegen för ditt`dotnet build`.Net-program ( ). Om du använder en byggtjänst som Azure DevOps är Kudu-versionen onödig. Om du vill inaktivera Kudu-versionen `SCM_DO_BUILD_DURING_DEPLOYMENT`skapar du `false`en appinställning med värdet .

## <a name="other-deployment-considerations"></a>Andra spridningsöverväganden

### <a name="use-deployment-slots"></a>Använda distributionsplatser

Använd [distributionsplatser](deploy-staging-slots.md) när du distribuerar en ny produktionsversion när det är möjligt. När du använder en standardapptjänstplansnivå eller bättre kan du distribuera appen till en mellanlagringsmiljö, validera dina ändringar och göra röktester. När du är redo kan du byta mellanlagring och produktionsplatser. Växlingsåtgärden värms upp de nödvändiga arbetsinstanserna för att matcha din produktionsskala, vilket eliminerar driftstopp. 

### <a name="local-cache"></a>Lokal cache

Azure App Service-innehåll lagras på Azure Storage och visas upp på ett hållbart sätt som en innehållsresurs. Vissa appar behöver dock bara en högpresterande, skrivskyddad innehållsbutik som de kan köra med hög tillgänglighet. Dessa appar kan dra nytta av att använda [lokal cache](overview-local-cache.md). Lokal cache rekommenderas inte för innehållshanteringswebbplatser som WordPress.

Använd alltid lokal cache tillsammans med [distributionsplatser](deploy-staging-slots.md) för att förhindra driftstopp. Se [det här avsnittet](overview-local-cache.md#best-practices-for-using-app-service-local-cache) för information om hur du använder dessa funktioner tillsammans.

### <a name="high-cpu-or-memory"></a>Hög CPU eller minne

Om appserviceplanen använder över 90 % av den tillgängliga processorn eller det underliggande minnet kan den underliggande virtuella datorn ha problem med att bearbeta distributionen. När detta inträffar kan du tillfälligt skala upp antalet instanser för att utföra distributionen. När distributionen är klar kan du returnera instansantalet till dess tidigare värde.

Mer information om metodtips finns i [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) för att ta reda på användbara metodtips som är specifika för din resurs.

- Navigera till din webbapp i [Azure-portalen](https://portal.azure.com).
- Klicka på **Diagnostisera och lösa problem** i vänster navigering, som öppnar App Service Diagnostics.
- Välj startsida **för bästa praxis.**
- Klicka på **Metodtips för tillgänglighet & prestanda** eller **metodtips för optimal konfiguration om** du vill visa appens aktuella tillstånd när det gäller de här metodtipsen.

Du kan också använda den här länken för att `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`direkt öppna App Service Diagnostics för din resurs: .
