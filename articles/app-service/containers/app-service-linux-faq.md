---
title: App Service i Linux vanliga frågor och svar – Azure | Microsoft Docs
description: Azure App Service i Linux vanliga frågor och svar.
keywords: Azure apptjänst, webbapp, vanliga frågor och svar, linux, oss, webbapp för behållare, flera behållare, multicontainer
services: app-service
documentationCenter: ''
author: yili
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: a12d3708cdb547cc036b249bebf901d2ec5121c3
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729327"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service i Linux vanliga frågor och svar

Med lanseringen av App Service i Linux arbetar vi på att lägga till funktioner och att göra förbättringar på vår plattform. Den här artikeln innehåller svar på frågor att våra kunder har efterfrågat oss nyligen.

Om du har en fråga kan du kommentera den här artikeln.

## <a name="built-in-images"></a>Inbyggda avbildningar

**Jag vill Förgrena inbyggda Docker-behållare som i plattformen. Var hittar jag dessa filer?**

Du hittar alla Docker-filer på [GitHub](https://github.com/azure-app-service). Du hittar alla Docker-behållare på [Docker Hub](https://hub.docker.com/u/appsvc/).

**Vilka är de förväntade värdena för avsnittet startfil när jag konfigurerar körningsstack?**

För Node.js anger du PM2-konfigurationsfilen eller skriptfilen. För .NET Core, anger du ditt kompilerade DLL-namn som `dotnet <myapp>.dll`. För Ruby, kan du ange det Ruby-skript som du vill initiera din app med.

## <a name="management"></a>Hantering

**Vad händer när jag trycker på knappen Starta om i Azure-portalen?**

Den här åtgärden är samma som en Docker-omstart.

**Kan jag använda SSH (Secure Shell) för att ansluta till appen behållare virtuell dator (VM)?**

Ja, kan du göra det via källplatsen control management (SCM).

> [!NOTE]
> Du kan också ansluta till appcontainern direkt från din lokala utvecklingsdator med SSH, SFTP eller Visual Studio Code (för livefelsökning av Node.js-appar). Mer information finns i [Remote debugging and SSH in App Service on Linux](https://aka.ms/linux-debug) (Fjärrfelsökning och SSH i App Service på Linux).
>

**Hur kan jag skapa en Linux App Service-plan via ett SDK eller en mall för Azure Resource Manager?**

Du bör ange den **reserverade** i app service för att *SANT*.

## <a name="continuous-integration-and-deployment"></a>Kontinuerlig integrering och distribution

**Min webbapp använder fortfarande ett gamla Docker-behållaravbildning när jag har uppdaterat avbildning på Docker Hub. Har du stöd för kontinuerlig integrering och distribution av anpassade behållare?**

Ja, om du vill konfigurera kontinuerlig integrering/distribution för Azure Container Registry eller DockerHub, genom att följa [kontinuerlig distribution med Web App for Containers](./app-service-linux-ci-cd.md). Du kan uppdatera behållaren genom att stoppa och sedan starta webbappen privat register. Eller du kan ändra eller lägga till en dummy programinställning du tvingar fram en uppdatering av din behållare.

**Stöder mellanlagringsmiljön?**

Ja.

**Kan jag använda *WebDeploy/MSDeploy* distribuera min webbapp?**

Ja, du måste ange en app som heter `WEBSITE_WEBDEPLOY_USE_SCM` till *FALSKT*.

**Git-distribution av mitt program misslyckas när du använder Linux-webbapp. Hur kan jag undvika problemet?**

Om det inte går att webbappen Linux Git-distribution, väljer du något av följande alternativ för att distribuera din programkod:

- Använda funktionen för kontinuerlig leverans (förhandsversion): Du kan lagra källkoden för din app i en Azure DevOps Git-lagringsplats eller GitHub-lagringsplatsen att använda Azure kontinuerlig leverans. Mer information finns i [konfigurera kontinuerlig leverans för Linux-webbapp](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Använd den [ZIP distribuera API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Att använda detta API [SSH till din webbapp](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) och gå till mappen där du vill distribuera din kod. Kör följande kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Om du får ett felmeddelande som den `curl` kommando inte hittas, måste du installera curl med hjälp av `apt-get install curl` innan du kör den tidigare `curl` kommando.

## <a name="language-support"></a>Stöd för språk

**Jag vill använda webbsockets i mitt Node.js-program, eventuella specialinställningar eller konfigurationer för att ställa in?**

Ja, inaktivera `perMessageDeflate` i din Node.js-kod på serversidan. Till exempel om du använder socket.io, Använd följande kod:

```nodejs
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Stöder Okompilerade .NET Core-appar?**

Ja.

**Det stöd för kompositör som en beroendehanterare för PHP-appar?**

Ja, under en Git-distribution Kudu bör identifierar att du distribuerar en PHP-program (tack vare förekomsten av en composer.lock-fil) och Kudu utlöser sedan en composer-installation.

## <a name="custom-containers"></a>Anpassad behållare

**Jag använder en egen anpassad behållare. Jag vill ha den plattform du monterar en SMB-filresurs till den `/home/` directory.**

Du kan göra det genom att ange den `WEBSITES_ENABLE_APP_SERVICE_STORAGE` appinställningen *SANT*. Tänk på att detta innebär att behållaren startas om när plattformen storage går igenom en ändring.

>[!NOTE]
>Om den `WEBSITES_ENABLE_APP_SERVICE_STORAGE` inställningen är Ospecificerad eller inställd på *FALSKT*, `/home/` directory delas inte mellan instanser för skalning och filer som skrivs det sparas inte mellan omstarter.

**Min anpassade behållaren tar lång tid att starta och plattformen startar om behållaren innan den är klar startar.**

Du kan konfigurera tidsperiod som plattformen väntar innan den startar om din behållare. Du gör detta genom att ange den `WEBSITES_CONTAINER_START_TIME_LIMIT` appinställningen till det värde du vill. Standardvärdet är 230 sekunder och det högsta värdet är 1 800 sekunder.

**Vad är formatet för privat register-Serveradress?**

Ange URL som fullständig registret, inklusive `http://` eller `https://`.

**Vad är formatet för avbildningens namn på privat register-alternativet?**

Lägg till den fullständiga avbildningsnamn, inklusive privat register-URL (till exempel myacr.azurecr.io/dotnet:latest). Avbildningar som använder en anpassad port [kan inte anges via portalen](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Ange `docker-custom-image-name`, använda den [ `az` kommandoradsverktyget](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Kan jag lägga upp mer än en port på min anpassade behållaravbildning?**

Vi stöder för närvarande inte exponera mer än en port.

**Kan jag använda min egen lagring?**

Ja, [tillhandahålla egen lagring](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) är en förhandsversion.

**Varför kan jag Bläddra min anpassade behållare file system eller som kör processer från SCM-webbplatsen?**

SCM-webbplatsen körs i en separat behållare. Du kan inte kontrollera file system eller som kör processer på app-behållaren.

**Min anpassade behållare lyssnar på en annan port än port 80. Hur kan jag konfigurera min app att dirigera begäranden till den porten?**

Vi har port automatiskt. Du kan också ange en app som heter *WEBSITES_PORT* och ge den värdet för det förväntade portnummer. Tidigare den plattform som används i *PORT* appinställningen. Vi planerar att avverka den här appinställningen och använda *WEBSITES_PORT* exklusivt.

**Behöver jag implementera HTTPS i min anpassade behållare?**

Plattformen hanterar Nej, HTTPS-avslutning på de delade klientdelar.

## <a name="multi-container-with-docker-compose-and-kubernetes"></a>Flera behållare med Docker Compose och Kubernetes

**Hur konfigurerar jag Azure Container Registry (ACR) ska användas med flera behållare?**

För att kunna använda ACR med flera behållare **alla behållaravbildningar** måste finnas på samma server för ACR-registret. När de är på samma server i registret, måste du skapa inställningar för program och sedan uppdatera konfigurationsfilen för att inkludera namnet på ACR-avbildning för Docker Compose- eller Kubernetes.

Skapa följande programinställningar för:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (fullständig URL, t.ex.: https://<server-name>.azurecr.io)
- DOCKER_REGISTRY_SERVER_PASSWORD (aktivera administratörsåtkomst i inställningarna för ACR)

I konfigurationsfilen, referera till din ACR-avbildning som i följande exempel:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hur vet jag vilka behållare är åtkomlig via internet?**

- Endast en behållare kan vara öppna för åtkomst
- Endast port 80 och 8080 är tillgänglig (portar)

Här följer reglerna för att fastställa vilka behållare som är tillgänglig – i den ordning som prioritet:

- Tillämpningsinställning `WEBSITES_WEB_CONTAINER_NAME` inställd på behållarens namn
- Den första behållaren att definiera port 80 eller 8080
- Om inget av ovanstående stämmer den första behållaren som definieras i filen kommer att komma åt (exponeras)

## <a name="pricing-and-sla"></a>Priser och SLA

**Vad är priser, nu när tjänsten är allmänt tillgänglig?**

Du debiteras normal Azure App Service priset för antalet timmar som appen körs.

## <a name="other-questions"></a>Andra frågor

**Vilka är tecken som stöds i inställningarna för programnamn?**

Du kan använda endast bokstäver (A-Z, a – z), siffror (0-9) och understreck (_) för programinställningar.

**Var hittar jag nya funktioner?**

Du kan skicka in din idé på den [Web Apps-Feedbackforum](https://aka.ms/webapps-uservoice). Lägg till ”[Linux]” i rubriken för din idé.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure App Service i Linux?](app-service-linux-intro.md)
- [Konfigurera mellanlagringsmiljöer i Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Kontinuerlig distribution med Webbapp for Containers](./app-service-linux-ci-cd.md)
