---
title: Arbeta med Node.js-moduler
description: Lär dig hur du arbetar med Node.js-moduler när du använder Azure App Service eller molntjänster.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 045250f0b0f97cbefe05b36f1c8d4480244a172d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575855"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Använda Node.js-moduler med Azure-program
Detta dokument ger vägledning om hur du använder Node.js-moduler med program som körs på Azure. Det innehåller vägledning om att säkerställa att ditt program använder en specifik version av en modul som använder inbyggda moduler med Azure.

Om du redan är bekant med Node.js-moduler, **package.json** och **npm shrinkwrap.json** filer, följande information ger en snabb sammanfattning av vad som beskrivs i den här artikeln:

* Azure App Service förstår **package.json** och **npm shrinkwrap.json** filer och kan installera moduler baserat på posterna i de här filerna.

* Azure Cloud Services förväntar sig att alla moduler som ska installeras på utvecklingsmiljön och **nod\_moduler** directory för att ingå som en del av distributionspaketet. Det är möjligt att aktivera stöd för att installera moduler med **package.json** eller **npm shrinkwrap.json** filer på molntjänster; men den här konfigurationen kräver anpassning av standard skript som används av Cloud Service-projekt. Ett exempel på hur du konfigurerar den här miljön finns i [Azure startåtgärd för att köra npm-installation för att undvika att distribuera node-moduler](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Azure-datorer behandlas inte i den här artikeln eftersom distributionsupplevelsen i en virtuell dator är beroende av operativsystemet som är värd för den virtuella datorn.
> 
> 

## <a name="nodejs-modules"></a>Node.js-moduler
Modulerna är inläsningsbar JavaScript-paket som tillhandahåller funktioner för ditt program. Moduler installeras vanligtvis under den **npm** kommandoradsverktyget verktyget, men vissa moduler (t.ex http-modul) tillhandahålls som en del av core Node.js-paketet.

När moduler installeras de lagras i den **nod\_moduler** katalogen i roten för din program-katalogstruktur. Varje modul inom den **nod\_moduler** directory hanterar en egen katalog som innehåller alla moduler som den är beroende av och problemet upprepas för varje modul ända ned beroendekedjan. Den här miljön kan varje modulen installerad för att du har en egen version krav för modulerna som den är beroende av, men det kan resultera i en stor katalogstruktur.

Distribuera den **nod\_moduler** katalogen som en del av ditt program ökar storleken på distributionen jämfört med en **package.json** eller  **npm-shrinkwrap.json** filen; men det garanterar att versionerna av moduler som används i produktion är samma som de moduler som används under utveckling.

### <a name="native-modules"></a>Ursprungliga moduler
De flesta moduler är helt enkelt klartext JavaScript-filer, är vissa moduler plattformsspecifika binära avbildningar. Dessa moduler kompileras under installationen, vanligtvis med hjälp av Python och nod-gyp. Eftersom Azure Cloud Services är beroende av den **nod\_moduler** mapp som distribueras som en del av programmet, alla inbyggda modulen som ingår i de installerade modulerna ska fungera i en molntjänst så länge den har installerats och kompilerade på ett system för utveckling av Windows.

Azure App Service stöder inte alla interna moduler och kan misslyckas vid kompilering moduler med specifika krav. Även om vissa populära moduler mongodb har valfritt inbyggda beroenden och fungerar utan att de, visade två lösningar lyckas med nästan alla ursprungliga moduler som är tillgängliga idag:

* Kör **npm-installationsprogrammet** på en Windows-dator som har alla inbyggda modulens nödvändiga komponenter installerade. Sedan distribuerar den skapade **nod\_moduler** mappen som en del av programmet till Azure App Service.

  * Innan du kompilerar, kontrollera att din lokala Node.js-installation har matchande arkitektur och versionen är så nära som möjligt till den som används i Azure (de aktuella värdena kan vara markerad på runtime från egenskaperna för **process.arch** och **process.version**).

* Azure App Service kan konfigureras för att köra anpassade bash eller kommandoskript under distributionen, vilket ger dig möjlighet att köra anpassade kommandon och konfigurera exakt hur **npm-installationsprogrammet** körs. Se en video som visar hur du konfigurerar den miljön, [distributionsskript för anpassad webbplats med Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Med hjälp av en package.json-fil

Den **package.json** filen är ett sätt att ange högsta nivån beroendena programmet kräver så att Värdplattformen kan installera beroenden, i stället för att du inkludera den **noden\_ moduler** mappen som en del av distributionen. När programmet har distribuerats, den **npm-installationsprogrammet** används för att parsa den **package.json** filen och installera alla beroenden som anges.

Under utveckling, kan du använda den **--spara**, **--spara dev**, eller **--spara valfritt** parametrar när du installerar moduler om du vill lägga till en post för modulen din **package.json** filen automatiskt. Mer information finns i [npm install](https://docs.npmjs.com/cli/install).

Ett potentiellt problem med den **package.json** filen är det bara anger versionen för översta beroenden. Varje modul som är installerad kan eller kan inte ange versionen av moduler som den är beroende av och så det är möjligt att du kan få en annan beroendekedja än det som används under utveckling.

> [!NOTE]
> När du distribuerar till Azure App Service om din <b>package.json</b> filen refererar till en inbyggd modul som kan uppstå ett fel som liknar följande exempel när du publicerar programmet med Git:
> 
> npm fel! module-name@0.6.0 installera: 'noden gyp konfigurera build'
> 
> npm fel! ”cmd” / c ”” nod gyp konfigurera build ”' misslyckades med 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Med en npm-shrinkwrap.json-fil
Den **npm shrinkwrap.json** filen är ett försök att åtgärda modulen versionshantering begränsningar i den **package.json** fil. Medan den **package.json** filen bara innehåller versioner för de översta modulerna i **npm shrinkwrap.json** filen innehåller versionskraven för beroendekedjan hela modulen.

När programmet är redo för produktion, kan du låsa versionskraven och skapa en **npm shrinkwrap.json** filen med hjälp av den **npm förpackningsplasten** kommando. Det här kommandot ska använda de versioner som är installerad på den **nod\_moduler** mappen och registrera dessa versioner till den **npm shrinkwrap.json** fil. När programmet har distribuerats till den faktiska driftsmiljön den **npm-installationsprogrammet** används för att parsa den **npm shrinkwrap.json** filen och installera alla beroenden som anges. Mer information finns i [npm-förpackningsplasten](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> När du distribuerar till Azure App Service om din <b>npm shrinkwrap.json</b> filen refererar till en inbyggd modul som kan uppstå ett fel som liknar följande exempel när du publicerar programmet med Git:
> 
> npm fel! module-name@0.6.0 installera: 'noden gyp konfigurera build'
> 
> npm fel! ”cmd” / c ”” nod gyp konfigurera build ”' misslyckades med 1
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när du förstår hur du använder Node.js-moduler med Azure, lär du dig hur du [ange Node.js-version](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [skapa och distribuera en Node.js-webbapp](app-service/app-service-web-get-started-nodejs.md), och [hur du använder kommandoradsgränssnittet för Azure Gränssnitt för Mac och Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Mer information finns i [Node.js Developer Center](/nodejs/azure/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
