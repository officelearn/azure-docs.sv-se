---
title: "Azure Apptjänst i Linux vanliga frågor och svar | Microsoft Docs"
description: "Azure Apptjänst i Linux vanliga frågor och svar."
keywords: "Azure apptjänst, webbprogram, vanliga frågor och svar, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: b22d5f3497c388192764aa6b4ee8c95fec568bd8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="azure-app-service-on-linux-faq"></a>Azure Apptjänst i Linux vanliga frågor och svar

Vi arbetar med lanseringen av Apptjänst i Linux, lägger till funktioner och gör ständigt förbättringar att vår plattform. Den här artikeln innehåller svar på frågor som våra kunder har efterfrågat oss nyligen.

Om du har en fråga, kommentera artikeln och vi kommer att svara så snart som möjligt.

## <a name="built-in-images"></a>Inbyggda bilder

**Jag vill duplicera de inbyggda Docker-behållare som plattformen erbjuder. Var hittar jag dessa filer?**

Du hittar alla Docker-filer på [GitHub](https://github.com/azure-app-service). Du hittar alla behållare i Docker på [Docker-hubb](https://hub.docker.com/u/appsvc/).

**Vilka är de förväntade värdena för avsnittet startfil när jag konfigurera runtime-stacken?**

För Node.js anger du PM2 konfigurationsfilen eller skriptfilen. Ange namnet på din kompilerade DLL-filen för .NET Core. För Ruby, kan du ange Ruby skriptet som du vill initiera en app med.

## <a name="management"></a>Hantering

**Vad händer när jag trycka på omstartningsknappen i Azure portal?**

Den här åtgärden är samma som en Docker-omstart.

**Kan jag använda SSH (Secure Shell) för att ansluta till appen behållaren virtuell dator (VM)?**

Ja, kan du göra det via källplatsen kontrollen management (SCM).

**Hur skapar jag en Linux App Service-plan via en SDK eller en Azure Resource Manager-mall**

Du måste ange den **reserverade** i apptjänst ska *SANT*.

## <a name="continuous-integration-and-deployment"></a>Kontinuerlig integration och distribution

**Webbappen använder fortfarande en gamla Docker behållare avbildningen när jag har uppdaterat avbildningen på Docker-hubben. Har du stöd för kontinuerlig integrering och distribution av anpassade behållare?**

Att ställa in kontinuerlig integration/distribution för Azure-behållare registret eller DockerHub bilder genom att kontrollera följande artikel [kontinuerlig distribution med webbprogrammet för behållare](./app-service-linux-ci-cd.md). Du kan uppdatera behållaren genom att stoppa och starta ditt webbprogram för privata register. Eller kan du ändra eller lägga till en dummy programinställning att framtvinga en uppdatering av din behållare.

**Stöder fristående datorn?**

Ja.

**Kan jag använda *webbdistribution* att distribuera webbappen?**

Ja, du måste ange en app inställningen kallas `WEBSITE_WEBDEPLOY_USE_SCM` till *FALSKT*.

**Git-distribution för Mina program misslyckas när du använder Linux-webbprogram. Hur kan jag lösning problemet?**

Om det inte går att ditt webbprogram för Linux Git-distribution, kan du välja följande alternativ alternativ för att distribuera din programkod:

- Använd funktionen kontinuerlig leverans (förhandsversion): du kan lagra appens källkod i ett Team Services Git repo eller GitHub-repo-att använda Azure kontinuerlig leverans. Mer information finns i [hur du konfigurerar kontinuerlig leverans för Linux-webbprogrammet](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Använd den [ZIP distribuera API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): du använder detta API [SSH till ditt webbprogram](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support#making-a-client-connection) och gå till mappen där du vill distribuera din kod. Kör följande:

   ```
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Om du får ett felmeddelande som den `curl` kommando inte hittas, måste du installera curl med hjälp av `apt-get install curl` innan du kör den tidigare `curl` kommando.

## <a name="language-support"></a>Språkstöd

**Jag vill använda websockets i min Node.js-program, några särskilda inställningar eller konfigurationer för att ställa in?**

Ja, inaktivera `perMessageDeflate` i din koden på serversidan Node.js. Till exempel om du använder socket.io, gör du följande:
```
var io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Stöder Okompilerade .NET Core appar?**

Ja.

**Stöder du Composer som en beroende-hanterare för PHP-appar?**

Ja. Under en Git-distribution bör Kudu känner av att du distribuerar ett PHP-program (tack vare förekomsten av en composer.lock-fil) och Kudu sedan utlösa en composer installation för dig.

## <a name="custom-containers"></a>Anpassade behållare

**Jag använder egna anpassade container. Jag vill plattform att montera en SMB-resurs till den `/home/` directory.**

Du kan göra det genom att ange den `WEBSITES_ENABLE_APP_SERVICE_STORAGE` appinställningen *SANT* eller genom att ta bort appen ställer du in helt. Tänk på att detta kommer att orsaka behållaren omstarter när lagringsplatsen plattform genomgår en ändring. 

>[!NOTE]
>Om den `WEBSITES_ENABLE_APP_SERVICE_STORAGE` inställningen är *FALSKT*, `/home/` directory inte delas mellan skala instanser och filer som skrivs det sparas inte mellan olika omstarter.

**Min anpassade container tar lång tid att starta och plattformen startar om behållaren innan den är klar startas.**

Du kan konfigurera tidsperiod som plattformen väntar startar din behållare. Gör du genom att ange den `WEBSITES_CONTAINER_START_TIME_LIMIT` appinställningen till önskat värde. Standardvärdet är 230 sekunder och det högsta värdet är 600 sekunder.

**Vad är formatet för privata registret serverns URL?**

Ange en fullständig registret URL inklusive `http://` eller `https://`.

**Vad är namnformat för bilden i alternativet privata registret?**

Lägg till fullständig Avbildningsnamnet, inklusive privata registret URL (till exempel myacr.azurecr.io/dotnet:latest). Avbildningar som använder en anpassad port [kan inte anges via portalen](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Ange `docker-custom-image-name`, använda den [ `az` kommandoradsverktyget](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Exponerar jag mer än en port på min anpassade container avbildning?**

Vi stöder för närvarande inte exponera mer än en port.

**Kan jag använda min egen storage?**

Vi stöder för närvarande inte ta fram egna lagring.

**Varför kan jag Bläddra mitt anpassade container filen system eller som kör processer från SCM-platsen?**

SCM-webbplatsen körs i en separat behållare. Du kan inte kontrollera filen system eller som kör processer på app-behållaren.

**Min anpassade container lyssnar på en annan port än port 80. Hur kan jag konfigurera min app att skicka begäranden till porten?**

Vi har port automatiskt. Du kan också ange en app inställningen kallas *WEBSITES_PORT* och ge den värdet för det förväntade portnummer. Tidigare den plattform som används av *PORT* appinställningen. Vi planerar att inaktualisera appinställningen och använda *WEBSITES_PORT* exklusivt.

**Behöver jag implementera HTTPS i min anpassade container?**

Nej, plattformen hanterar HTTPS-avslutning på delade frontwebbservrarna.

## <a name="pricing-and-sla"></a>Priser och SLA

**Vad är prissättning, nu när tjänsten är allmänt tillgängligt?**

Du debiteras normala Azure App Service prissättning för antalet timmar som appen körs.

## <a name="other-questions"></a>Andra frågor

**Vad är stöds tecknen i programnamn inställningar?**

Du kan använda endast bokstäver (A-Z, a-z), siffror (0-9) och understreck (_) för programinställningar.

**Var kan jag begära nya funktioner?**

Du kan skicka din idé på den [Web Apps Feedbackforum](https://aka.ms/webapps-uservoice). Lägg till ”[Linux]” i rubriken för din idé.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure App Service på Linux?](app-service-linux-intro.md)
* [Skapa mellanlagringsmiljöer i Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution med webbprogrammet för behållare](./app-service-linux-ci-cd.md)
