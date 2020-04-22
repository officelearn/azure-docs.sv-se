---
title: Bästa metoder för distribution
description: Lär dig mer om de viktigaste mekanismerna för distribution till Azure App Service. Hitta språkspecifika rekommendationer och andra förbehåll.
keywords: azure app tjänst, webbapp, distribuera, distribution, pipelines, bygga
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770650"
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

## <a name="use-deployment-slots"></a>Använda distributionsplatser

Använd [distributionsplatser](deploy-staging-slots.md) när du distribuerar en ny produktionsversion när det är möjligt. När du använder en standardapptjänstplansnivå eller bättre kan du distribuera appen till en mellanlagringsmiljö, validera dina ändringar och göra röktester. När du är redo kan du byta mellanlagring och produktionsplatser. Växlingsåtgärden värms upp de nödvändiga arbetsinstanserna för att matcha din produktionsskala, vilket eliminerar driftstopp.

### <a name="continuously-deploy-code"></a>Distribuera kod kontinuerligt

Om projektet har utsett grenar för testning, QA och mellanlagring bör var och en av dessa grenar distribueras kontinuerligt till en mellanlagringsplats. (Detta kallas [Gitflow-designen](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) På så sätt kan dina intressenter enkelt bedöma och testa den distribuerade grenen. 

Kontinuerlig distribution bör aldrig aktiveras för din produktionsplats. I stället bör din produktionsgren (ofta huvudbehärskning) distribueras på en plats som inte är produktionsplats. När du är redo att släppa basgrenen byter du ut den i produktionsplatsen. Att byta till produktion – i stället för att distribuera till produktion – förhindrar driftstopp och gör att du kan återställa ändringarna genom att byta igen. 

![Visuellt objekt för kortplatsanvändning](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Distribuera behållare kontinuerligt

För anpassade behållare från Docker eller andra behållarregister distribuerar du avbildningen till en mellanlagringsplats och byter till produktion för att förhindra driftstopp. Automatiseringen är mer komplex än koddistributionen eftersom du måste skicka avbildningen till ett behållarregister och uppdatera avbildningstaggen på webbappen.

För varje gren som du vill distribuera till en plats ställer du in automatisering för att göra följande på varje åtagande till grenen.

1. **Skapa och tagga bilden**. Som en del av build-pipelinen taggar du avbildningen med git commit ID, tidsstämpel eller annan identifierbar information. Det är bäst att inte använda standardtaggen "senaste". Annars är det svårt att spåra tillbaka vilken kod som för närvarande distribueras, vilket gör felsökning mycket svårare.
1. **Tryck på den taggade bilden**. När avbildningen har byggts och taggats, skickar pipelinen avbildningen till vårt behållarregister. I nästa steg hämtar distributionsplatsen den taggade avbildningen från behållarregistret.
1. **Uppdatera distributionsplatsen med den nya avbildningstaggen**. När den här egenskapen uppdateras startas platsen automatiskt om och hämtar den nya behållaravbildningen.

![Visuellt objekt för kortplatsanvändning](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Det finns exempel nedan för gemensamma automatiseringsramverk.

### <a name="use-azure-devops"></a>Använda Azure DevOps

App Service har [inbyggd kontinuerlig leverans](deploy-continuous-deployment.md) för behållare via Distributionscenter. Navigera till din app i [Azure-portalen](https://portal.azure.com/) och välj **Distributionscenter** under **Distribution**. Följ instruktionerna för att välja din databas och gren. Detta konfigurerar en DevOps-bygg- och frigöringspipeline för att automatiskt skapa, tagga och distribuera din behållare när nya åtaganden skjuts till den valda grenen.

### <a name="use-github-actions"></a>Använda GitHub-åtgärder

Du kan också automatisera behållardistributionen [med GitHub-åtgärder](containers/deploy-container-github-action.md).  Arbetsflödesfilen nedan skapar och taggar behållaren med commit-ID: t, skickar den till ett behållarregister och uppdaterar den angivna platsplatsen med den nya avbildningstaggen.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Använda andra automationsleverantörer

Stegen som tidigare angetts gäller för andra automatiseringsverktyg som CircleCI eller Travis CI. Du måste dock använda Azure CLI för att uppdatera distributionsplatserna med nya avbildningstaggar i det sista steget. Om du vill använda Azure CLI i ditt automatiseringsskript genererar du ett tjänsthuvudnamn med följande kommando.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

Logga in med `az login --service-principal`skriptet och ange huvudmannens information i skriptet. Du kan `az webapp config container set` sedan använda för att ange behållarnamn, tagg, register-URL och registerlösenord. Nedan finns några användbara länkar för dig att konstruera din container CI process.

- [Så här loggar du in på Azure CLI på Circle CI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Språkspecifika överväganden

### <a name="java"></a>Java

Använd Kudu [zipdeploy/](deploy-zip.md) API för distribution av JAR-program och [wardeploy/](deploy-zip.md#deploy-war-file) for WAR-appar. Om du använder Jenkins kan du använda dessa API:er direkt i distributionsfasen. Mer information finns i [den här artikeln](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Node

Som standard kör Kudu byggstegen för nodprogrammet (`npm install`). Om du använder en byggtjänst som Azure DevOps är Kudu-versionen onödig. Om du vill inaktivera Kudu-versionen `SCM_DO_BUILD_DURING_DEPLOYMENT`skapar du `false`en appinställning med värdet .

### <a name="net"></a>.NET 

Som standard kör Kudu byggstegen för ditt`dotnet build`.NET-program ( ). Om du använder en byggtjänst som Azure DevOps är Kudu-versionen onödig. Om du vill inaktivera Kudu-versionen `SCM_DO_BUILD_DURING_DEPLOYMENT`skapar du `false`en appinställning med värdet .

## <a name="other-deployment-considerations"></a>Andra spridningsöverväganden

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
