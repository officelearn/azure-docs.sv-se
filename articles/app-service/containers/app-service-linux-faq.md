---
title: Vanliga frågor och svar om inbyggda behållare
description: Hitta svar på vanliga frågor om de inbyggda Linux-behållarna i Azure App Service.
keywords: azure app service, webbapp, faq, linux, oss, webbapp för behållare, flera behållare, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245828"
---
# <a name="azure-app-service-on-linux-faq"></a>Vanliga frågor och svar om Azure App Service on Linux

Med lanseringen av App Service på Linux arbetar vi med att lägga till funktioner och göra förbättringar i vår plattform. Den här artikeln innehåller svar på frågor som våra kunder har frågat oss nyligen.

Om du har en fråga, kommentera den här artikeln.

## <a name="built-in-images"></a>Inbyggda bilder

**Jag vill punga de inbyggda Docker-behållarna som plattformen tillhandahåller. Var hittar jag filerna?**

Du hittar alla Docker-filer på [GitHub](https://github.com/azure-app-service). Du hittar alla Docker-behållare på [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Vilka är de förväntade värdena för avsnittet Startfil när jag konfigurerar körningsstacken?**

| Stack           | Förväntat värde                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | kommandot för att starta jar-appen `java -jar /home/site/wwwroot/app.jar --server.port=80`(till exempel) |
| Tomcat          | platsen för ett skript för att utföra nödvändiga `/home/site/deployments/tools/startup_script.sh`konfigurationer (till exempel)          |
| Node.js         | konfigurationsfilen för PM2 eller skriptfilen                                |
| .NET Core       | det kompilerade DLL-namnet som`dotnet <myapp>.dll`                                 |
| Ruby            | Ruby-skriptet som du vill initiera appen med                     |

Dessa kommandon eller skript körs när den inbyggda Docker-behållaren har startats, men innan programkoden startas.

## <a name="management"></a>Hantering

**Vad händer när jag trycker på omstartsknappen i Azure-portalen?**

Den här åtgärden är samma som en Omstart av Docker.

**Kan jag använda Secure Shell (SSH) för att ansluta till den virtuella datorn för appbehållaren (VM)?**

Ja, du kan göra det via webbplatsen för källkontrollhantering (SCM).

> [!NOTE]
> Du kan också ansluta till appcontainern direkt från din lokala utvecklingsdator med SSH, SFTP eller Visual Studio Code (för livefelsökning av Node.js-appar). Mer information finns i [Remote debugging and SSH in App Service on Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) (Fjärrfelsökning och SSH i App Service på Linux).
>

**Hur skapar jag en Linux App Service-plan via en SDK- eller Azure Resource Manager-mall?**

Ange det **reserverade** fältet för apptjänsten till *true*.

## <a name="continuous-integration-and-deployment"></a>Kontinuerlig integrering och distribution

**Min webbapp använder fortfarande en gammal Docker-behållaravbildning när jag har uppdaterat avbildningen på Docker Hub. Stöder du kontinuerlig integrering och distribution av anpassade behållare?**

Ja, för att konfigurera kontinuerlig integrering/distribution för Azure Container Registry eller DockerHub, genom att följa [kontinuerlig distribution med Web App for Containers](./app-service-linux-ci-cd.md). För privata register kan du uppdatera behållaren genom att stoppa och sedan starta webbappen. Du kan också ändra eller lägga till en dummy-programinställning för att tvinga fram en uppdatering av behållaren.

**Stöder du mellanlagringsmiljöer?**

Ja.

**Kan jag använda *WebDeploy/MSDeploy* för att distribuera min webbapp?**

Ja, du måste ange en `WEBSITE_WEBDEPLOY_USE_SCM` appinställning som anropas till *false*.

**Git-distributionen av mitt program misslyckas när du använder Linux-webbapp. Hur kan jag komma runt problemet?**

Om Git-distributionen misslyckas med din Linux-webbapp väljer du något av följande alternativ för att distribuera programkoden:

- Använd funktionen Kontinuerlig leverans (förhandsversion) : Du kan lagra appens källkod i en Azure DevOps Git-repo- eller GitHub-repo för att använda Azure Continuous Delivery. Mer information finns i [Så här konfigurerar du kontinuerlig leverans för Linux-webbapp](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Använd [ZIP-distributions-API:](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)Om du vill använda det här API:et går [SSH till webbappen](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) och går till mappen där du vill distribuera koden. Kör följande kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Om du får ett `curl` felmeddelande om att kommandot inte hittas kontrollerar du att du installerar curl med hjälp `apt-get install curl` av det tidigare `curl` kommandot.

## <a name="language-support"></a>Stöd för språk

**Jag vill använda webbuttag i node.js-programmet, några speciella inställningar eller konfigurationer för att ställa in?**

Ja, `perMessageDeflate` inaktivera i nod.js-koden på serversidan. Om du till exempel använder socket.io använder du följande kod:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Stöder du okompilerade .NET Core-appar?**

Ja.

**Stöder du Composer som beroendehanterare för PHP-appar?**

Ja, under en Git-distribution bör Kudu upptäcka att du distribuerar ett PHP-program (tack vare närvaron av en composer.lock-fil), och Kudu kommer då att utlösa en kompositörsinstallation.

## <a name="custom-containers"></a>Anpassade containrar

**Jag använder min egen specialbehållare. Jag vill att plattformen för att `/home/` montera en SMB-resurs till katalogen.**

Om `WEBSITES_ENABLE_APP_SERVICE_STORAGE` inställningen är **ospecificerad** `/home/` eller inställd på *true* **delas** katalogen över skalningsinstanser och filer som skrivs **sparas** över omstarter. Om `WEBSITES_ENABLE_APP_SERVICE_STORAGE` du uttryckligen ställer in *false* inaktiveras fästet.

**Min anpassade behållare tar lång tid att starta och plattformen startar om behållaren innan den startas.**

Du kan konfigurera hur lång tid plattformen väntar innan den startar om behållaren. Det gör du `WEBSITES_CONTAINER_START_TIME_LIMIT` genom att ange appinställningen till önskat värde. Standardvärdet är 230 sekunder och det maximala värdet är 1800 sekunder.

**Vad är formatet för url:en för den privata registerservern?**

Ange den fullständiga register-URL:en, inklusive `http://` eller `https://`.

**Vad är formatet för bildnamnet i alternativet privat register?**

Lägg till det fullständiga bildnamnet, inklusive den privata register-URL:en (till exempel myacr.azurecr.io/dotnet:latest). Bildnamn som använder en anpassad port [kan inte anges via portalen](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Om `docker-custom-image-name`du vill ställa in använder du [ `az` kommandoradsverktyget](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

**Kan jag exponera mer än en port på min anpassade behållaravbildning?**

Vi stöder inte att utsätta mer än en port.

**Kan jag ta med eget förråd?**

Ja, [ta med eget lagringsutrymme](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) är i förhandsversion.

**Varför kan jag inte bläddra i min anpassade behållarens filsystem eller köra processer från SCM-webbplatsen?**

SCM-platsen körs i en separat behållare. Du kan inte kontrollera filsystemet eller köra processer i appbehållaren.

**Min anpassade behållare lyssnar på en annan port än port 80. Hur konfigurerar jag min app för att dirigera begäranden till den porten?**

Vi har automatisk portdetektering. Du kan också ange en appinställning som kallas *WEBSITES_PORT* och ge den värdet av det förväntade portnumret. Tidigare använde plattformen *portappinställningen.* Vi planerar att göra den här appinställningen invänd och använda *WEBSITES_PORT* uteslutande.

**Måste jag implementera HTTPS i min anpassade behållare?**

Nej, plattformen hanterar HTTPS-avslutning i de delade klientdelarna.

## <a name="multi-container-with-docker-compose"></a>Multicontainer med Docker Compose

**Hur konfigurerar jag Azure Container Registry (ACR) att använda med flera behållare?**

För att kunna använda ACR med flera behållare måste **alla behållaravbildningar** finnas på samma ACR-registerserver. När de finns på samma registerserver måste du skapa programinställningar och sedan uppdatera konfigurationsfilen Docker Compose så att den innehåller ACR-avbildningsnamnet.

Skapa följande programinställningar:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (fullständig webbadress, ex: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (aktivera administratörsåtkomst i ACR-inställningar)

I konfigurationsfilen refererar du till ACR-avbildningen som följande exempel:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hur vet jag vilken behållare som är tillgänglig för internet?**

- Endast en behållare kan vara öppen för åtkomst
- Endast port 80 och 8080 är tillgänglig (exponerade portar)

Här är reglerna för att avgöra vilken behållare som är tillgänglig - i prioritetsordning:

- Programinställning `WEBSITES_WEB_CONTAINER_NAME` inställd på behållarnamnet
- Den första behållaren som definierar port 80 eller 8080
- Om inget av ovanstående är sant kommer den första behållaren som definieras i filen att vara tillgänglig (exponerad)


## <a name="web-sockets"></a>Webbuttag

Webbuttag stöds på Linux-appar.

> [!IMPORTANT]
> Webbuttag stöds för närvarande inte för Linux-appar på kostnadsfria apptjänstplaner. Vi arbetar med att ta bort denna begränsning och planerar att stödja upp till 5 webbuttagsanslutningar på kostnadsfria apptjänstplaner.

## <a name="pricing-and-sla"></a>Prissättning och SLA

**Vad är prissättningen, nu när tjänsten är allmänt tillgänglig?**

Priserna varierar beroende på SKU och region, men du kan se mer information på vår prissida: [App Service Prissättning](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Övriga frågor

**Vad betyder "Begärd funktion är inte tillgänglig i resursgruppen"?**

Det här meddelandet kan visas när du skapar webbapp med Azure Resource Manager (ARM). Baserat på en aktuell begränsning kan du för samma resursgrupp inte blanda Windows- och Linux-appar i samma region.

**Vilka tecken stöds i programinställningarsnamn?**

Du kan bara använda bokstäver (A-Ö, a-z), siffror (0-9) och understrecket (_) för programinställningar.

**Var kan jag begära nya funktioner?**

Du kan skicka in din idé på [Feedbackforumet för Webbappar](https://aka.ms/webapps-uservoice). Lägg till "[Linux]" till titeln på din idé.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure App Service på Linux?](app-service-linux-intro.md)
- [Konfigurera mellanlagringsmiljöer i Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Kontinuerlig distribution med Web App för behållare](./app-service-linux-ci-cd.md)
