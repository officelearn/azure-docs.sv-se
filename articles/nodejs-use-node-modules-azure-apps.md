---
title: Arbeta med Node.js-moduler
description: Lär dig hur du arbetar med Node.js-moduler när du använder Azure App Service eller Cloud Services.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70309282"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Använda Node.js-moduler med Azure-program
Det här dokumentet innehåller vägledning om hur du använder Node.js-moduler med program som finns på Azure. Den ger vägledning om hur du säkerställer att ditt program använder en specifik version av en modul samt använder inbyggda moduler med Azure.

Om du redan är bekant med att använda Node.js-moduler, **package.json-** och **npm-shrinkwrap.json-filer,** ger följande information en snabb sammanfattning av vad som diskuteras i den här artikeln:

* Azure App Service förstår **package.json-** och **npm-shrinkwrap.json-filer** och kan installera moduler baserat på poster i dessa filer.

* Azure Cloud Services förväntar sig att alla moduler ska installeras i utvecklingsmiljön och **nodmodulkatalogen\_** ska inkluderas som en del av distributionspaketet. Det är möjligt att aktivera stöd för installation av moduler med **package.json-** eller **npm-shrinkwrap.json-filer** på Molntjänster. Den här konfigurationen kräver dock anpassning av standardskript som används av Cloud Service-projekt. Ett exempel på hur du konfigurerar den här miljön finns i [Azure Startup-uppgift för att köra npm-installation för att undvika att distribuera nodmoduler](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Virtuella Azure-datorer beskrivs inte i den här artikeln, eftersom distributionsupplevelsen i en virtuell dator är beroende av det operativsystem som finns på den virtuella datorn.
> 
> 

## <a name="nodejs-modules"></a>Nod.js Moduler
Moduler är inläsningsbara JavaScript-paket som tillhandahåller specifika funktioner för ditt program. Moduler installeras vanligtvis med hjälp av **npm** kommandoradsverktyget, men vissa moduler (t.ex. http-modulen) tillhandahålls som en del av core Node.js-paketet.

När moduler installeras lagras de i **nodmodulkatalogen\_** i roten i programkatalogstrukturen. Varje modul i **nodmodulkatalogen\_** underhåller sin egen katalog som innehåller alla moduler som den är beroende av, och det här beteendet upprepas för varje modul hela vägen ner i beroendekedjan. Denna miljö gör att varje modul installerad för att ha sina egna versionskrav för de moduler den beror på, men det kan resultera i en ganska stor katalogstruktur.

Om du distribuerar **nodmodulkatalogen\_** som en del av programmet ökar distributionens storlek jämfört med att använda en **package.json-** eller **npm-shrinkwrap.json-fil.** Det garanterar dock att de versioner av modulerna som används i produktionen är desamma som de moduler som används under utveckling.

### <a name="native-modules"></a>Inbyggda moduler
Medan de flesta moduler är helt enkelt oformaterad-text JavaScript-filer, vissa moduler är plattformsspecifika binära bilder. Dessa moduler kompileras vid installationstiden, vanligtvis med hjälp av Python och nod-gyp. Eftersom Azure Cloud Services är beroende av mappen **nodmoduler\_** som distribueras som en del av programmet, bör alla inbyggda moduler som ingår som en del av de installerade modulerna fungera i en molntjänst så länge den installerades och kompilerades på ett Windows-utvecklingssystem.

Azure App Service stöder inte alla inbyggda moduler och kan misslyckas vid kompilering av moduler med specifika förutsättningar. Medan vissa populära moduler som MongoDB har valfria inhemska beroenden och fungerar bra utan dem, två lösningar visat sig framgångsrika med nästan alla inbyggda moduler som finns idag:

* Kör **npm-installation** på en Windows-dator som har alla den inbyggda modulens förutsättningar installerade. Distribuera sedan mappen skapade **nodmoduler\_** som en del av programmet till Azure App Service.

  * Innan du kompilerar kontrollerar du att din lokala Node.js-installation har matchande arkitektur och att versionen är så nära som möjligt till den som används i Azure (de aktuella värdena kan kontrolleras på körning från egenskaper **process.arch** och **process.version**).

* Azure App Service kan konfigureras för att köra anpassade bash- eller skalskript under distributionen, vilket ger dig möjlighet att köra anpassade kommandon och exakt konfigurera hur **npm-installation** körs. En video som visar hur du konfigurerar den miljön finns i [Skript för anpassad webbplatsdistribution med Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Använda en package.json-fil

**Package.json-filen** är ett sätt att ange beroenden på den högsta nivån som ditt program kräver så att värdplattformen kan installera beroenden, i stället för att du måste inkludera **mappen nodmoduler\_** som en del av distributionen. När programmet har distribuerats används **npm-installationskommandot** för att tolka **filen package.json** och installera alla beroenden som anges.

Under utvecklingen kan du använda parametrarna **--save**, **--save-dev**eller **--save-optional** när du installerar moduler för att lägga till en post för modulen i **filen package.json** automatiskt. Mer information finns i [npm-install](https://docs.npmjs.com/cli/install).

Ett potentiellt problem med **filen package.json** är att den bara anger versionen för beroenden på den högsta nivån. Varje modul som installeras kan eller inte kan ange vilken version av modulerna den är beroende av, och det är därför möjligt att du kan få en annan beroendekedja än den som används under utveckling.

> [!NOTE]
> Om filen package.json refererar till en inbyggd modul kan du se ett fel som liknar följande exempel när du publicerar programmet med Git om filen <b>package.json</b> refererar till en inbyggd modul:
> 
> npm ERR! module-name@0.6.0installera: "nod-gyp konfigurera build"
> 
> npm ERR! 'cmd "/c" "nod-gyp configure build"' misslyckades med 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Använda en npm-shrinkwrap.json-fil
**Filen npm-shrinkwrap.json** är ett försök att åtgärda modulens versionsbegränsningar för **filen package.json.** Momentet **package.json** endast innehåller versioner för moduler på den översta nivån, innehåller filen **npm-shrinkwrap.json** versionskraven för den fullständiga modulens beroendekedja.

När programmet är klart för produktion kan du låsa versionskraven och skapa en **npm-shrinkwrap.json-fil** med kommandot **npm shrinkwrap.** Det här kommandot använder de versioner som för närvarande är installerade i **mappen\_nodmoduler** och registrerar dessa versioner i filen **npm-shrinkwrap.json.** När programmet har distribuerats till värdmiljön används **npm-installationskommandot** för att tolka filen **npm-shrinkwrap.json** och installera alla beroenden som anges. Mer information finns i [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Om filen <b>npm-shrinkwrap.json</b> refererar till en inbyggd modul kan du se ett fel som liknar följande exempel när du publicerar programmet med Git:
> 
> npm ERR! module-name@0.6.0installera: "nod-gyp konfigurera build"
> 
> npm ERR! 'cmd "/c" "nod-gyp configure build"' misslyckades med 1
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när du förstår hur du använder Node.js-moduler med Azure kan du läsa om hur du [anger node.js-versionen,](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md) [skapar och distribuerar en Node.js-webbapp](app-service/app-service-web-get-started-nodejs.md)och hur du använder Azure [Command-Line Interface för Mac och Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Mer information finns i [Node.js Developer Center](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
