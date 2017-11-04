---
title: Arbeta med Node.js-moduler
description: "Lär dig hur du arbetar med Node.js-moduler när du använder Azure App Service eller molntjänster."
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 76679ea0ff2c1e88d1923488717a245351437165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Använda Node.js-moduler med Azure-program
Detta dokument ger vägledning om hur du använder Node.js-moduler med program i Azure. Det innehåller vägledning om hur du säkerställer att ditt program använder en viss version av en modul eller någon av Azures egna moduler.

Om du redan är bekant med Node.js-moduler **package.json** och **npm shrinkwrap.json** filer, följande information ger en snabb sammanfattning av vad som beskrivs i den här artikeln:

* Azure Apptjänst förstår **package.json** och **npm shrinkwrap.json** filer och kan installera moduler baserat på informationen i de här filerna.

* Azure Cloud Services räknar alla moduler som ska installeras på development environment och **nod\_moduler** katalog som ska inkluderas som en del av distributionspaketet. Det är möjligt att aktivera stöd för installation av moduler med **package.json** eller **npm shrinkwrap.json** filer på Cloud Services, men den här konfigurationen kräver anpassning av standard skript som används av Cloud Service-projekt. Ett exempel på hur du konfigurerar den här miljön finns [Azure startåtgärd för att köra npm-installationen för att undvika att distribuera nod moduler](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Virtuella Azure-datorer beskrivs inte i den här artikeln som distributionsupplevelse på en virtuell dator beror på operativsystemet hos den virtuella datorn.
> 
> 

## <a name="nodejs-modules"></a>Node.js-moduler
Moduler är inläsningsbar JavaScript-paket som tillhandahåller funktioner för ditt program. Moduler installeras vanligtvis under den **npm** kommandoradsverktyget verktyget, men vissa moduler (t.ex http-modul) tillhandahålls som en del av core Node.js-paketet.

När moduler installeras de lagras i den **nod\_moduler** katalogen i roten för ditt program katalogstruktur. Varje modul i den **nod\_moduler** directory hanterar en egen **nod\_moduler** katalog som innehåller alla moduler som den är beroende av och problemet upprepas för varje modul ända ned beroendekedjan. Den här miljön kan varje modul installerat för att få sin egen versionskraven för modulerna som den beroende, men det kan resultera i en stor katalogstruktur.

Distribuera den **nod\_moduler** katalogen som en del av ditt program ökar storleken på distributionen jämfört med en **package.json** eller  **npm shrinkwrap.json** filen; men den garanterar att versionerna av moduler som används i produktionen är samma som de moduler som används under utveckling.

### <a name="native-modules"></a>Inbyggda moduler
De flesta moduler är helt enkelt klartext JavaScript-filer, är vissa moduler plattformsspecifika binära avbildningar. Dessa moduler kompileras under installationen, vanligtvis med hjälp av Python och nod-gyp. Eftersom Azure Cloud Services är beroende av den **nod\_moduler** mapp som distribueras som en del av ett program, alla egna modulen ingår som en del av de installera modulerna bör fungera i en molntjänst så länge den har installerats och kompilerade under Windows-utveckling.

Azure Apptjänst har inte stöd för alla interna moduler och kan misslyckas vid kompilering av moduler med specifika krav. Även om vissa populära moduler som MongoDB har valfria inbyggda beroenden och fungerar utan dem., visat två lösningar lyckas med nästan alla interna moduler som är tillgängliga i dag:

* Kör **npm installera** på en Windows-dator som har den inbyggda modulens nödvändiga komponenter installeras. Sedan kan du distribuera den skapade **nod\_moduler** mappen som en del av programmet till Azure App Service.

  * Innan du kompilerar, kontrollera att den lokala Node.js-installationen har motsvarande arkitektur och versionen är så nära som möjligt till den som används i Azure (aktuella värden kan kontrolleras på runtime från egenskaperna för **process.arch** och **process.version**).

* Azure Apptjänst kan konfigureras för att köra anpassade bash eller kommandoskript under distributionen, vilket ger dig möjlighet att köra anpassade kommandon och konfigurera exakt hur **installera npm** körs. Se en video som visar hur du konfigurerar den miljön [anpassad webbplats distribution skript med Kudu].

### <a name="using-a-packagejson-file"></a>Med hjälp av en package.json-fil

Den **package.json** filen är ett sätt att ange de översta nivån beroenden som krävs för ditt program så att Värdplattformen kan installera beroenden i stället för att kräva att du inkluderar den **noden\_ moduler** mappen som en del av distributionen. När programmet har distribuerats på **installera npm** kommando som används för att parsa den **package.json** filen och installera alla beroenden i listan.

Under utveckling, kan du använda den **--spara**, **--spara dev**, eller **--spara valfria** parametrar när du installerar moduler för att lägga till en post för modulen din **package.json** filen automatiskt. Mer information finns i [npm-installera](https://docs.npmjs.com/cli/install).

Ett möjligt problem med den **package.json** filen är det endast anger versionen för översta beroenden. Varje modul som installerats kan eller kan inte ange version för moduler som den är beroende av och så är det möjligt att du kan det sluta med en annan beroendekedjan än det som används under utveckling.

> [!NOTE]
> När du distribuerar till Azure App Service din <b>package.json</b> filen refererar till en ursprunglig modul som du kan se ett fel som liknar följande exempel när du publicerar program med Git:
> 
> npm fel! module-name@0.6.0installera: ”nod gyp konfigurera build'
> 
> npm fel! ' cmd ”/ c” ”nod gyp konfigurera build” ' misslyckades med 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Med hjälp av en npm-shrinkwrap.json-fil
Den **npm shrinkwrap.json** filen är ett försök att åtgärda modulen versionshantering begränsningar i **package.json** fil. När den **package.json** filen endast innehåller versioner för översta moduler i **npm shrinkwrap.json** filen innehåller versionskraven för hela modulen beroendekedjan.

När ditt program är klar för produktion, låsa versionskraven och skapa en **npm shrinkwrap.json** filen med hjälp av den **npm förpackningsplasten** kommando. Det här kommandot ska använda de versioner som är installerade i den **nod\_moduler** mapp, och registrera dessa versioner till den **npm shrinkwrap.json** fil. När programmet har distribuerats till värdmiljön den **installera npm** kommando som används för att parsa den **npm shrinkwrap.json** filen och installera alla beroenden i listan. Mer information finns i [npm förpackningsplasten](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> När du distribuerar till Azure App Service din <b>npm shrinkwrap.json</b> filen refererar till en ursprunglig modul som du kan se ett fel som liknar följande exempel när du publicerar program med Git:
> 
> npm fel! module-name@0.6.0installera: ”nod gyp konfigurera build'
> 
> npm fel! ' cmd ”/ c” ”nod gyp konfigurera build” ' misslyckades med 1
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du använder Node.js-moduler med Azure lär du dig hur du [ange Node.js-version], [skapa och distribuera en Node.js-webbapp](app-service/app-service-web-get-started-nodejs.md), och [hur du använder Azure kommandoraden Gränssnitt för Mac- och Linux].

Mer information finns i [Node.js Developer Center](/nodejs/azure/).

[ange Node.js-version]: nodejs-specify-node-version-azure-apps.md
[hur du använder Azure kommandoraden Gränssnitt för Mac- och Linux]:cli-install-nodejs.md
[anpassad webbplats distribution skript med Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
