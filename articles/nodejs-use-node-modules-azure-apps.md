---
title: Arbeta med Node. js-moduler
description: Lär dig hur du arbetar med Node. js-moduler när du använder Azure App Service eller Cloud Services.
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
ms.openlocfilehash: 8621dde43ff38cee15feb1d30b6ea80ab681c855
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594788"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Använda Node.js-moduler med Azure-program
Det här dokumentet ger vägledning om hur du använder Node. js-moduler med program som finns på Azure. Det ger vägledning om hur du säkerställer att programmet använder en specifik version av en modul samt hur du använder inbyggda moduler med Azure.

Om du redan är van vid att använda Node. js-moduler, **Package. JSON** -och **NPM-shrinkwrap. JSON-** filer, ger följande information en snabb översikt över vad som beskrivs i den här artikeln:

* Azure App Service förstår **Package. JSON** **-och NPM-shrinkwrap. JSON-** filer och kan installera moduler baserat på poster i dessa filer.

* Azure Cloud Services förväntar sig att alla moduler ska installeras i utvecklings miljön och att **katalogen\_Node-moduler** ska ingå som en del av distributions paketet. Det går att aktivera stöd för installation av moduler med hjälp av **Package. JSON** eller **NPM-shrinkwrap. JSON** -filer på Cloud Services; den här konfigurationen kräver dock anpassning av de standard skript som används av moln tjänst projekt. Ett exempel på hur du konfigurerar den här miljön finns i [Start uppgift i Azure så här kör du NPM installera för att undvika distribution av Node-moduler](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Azure Virtual Machines diskuteras inte i den här artikeln eftersom distributions miljön i en virtuell dator är beroende av det operativ system som den virtuella datorn har.
> 
> 

## <a name="nodejs-modules"></a>Node. js-moduler
Moduler är inbelastnings bara JavaScript-paket som tillhandahåller specifika funktioner för ditt program. Moduler installeras vanligt vis med kommando rads verktyget **NPM** , men vissa moduler (t. ex. http-modulen) tillhandahålls som en del av paketet core Node. js.

När moduler är installerade lagras de i katalogen **Node\_-moduler** i roten i din program katalog struktur. Varje modul i katalogen **Node\_-moduler** underhåller sin egen katalog som innehåller alla moduler som den är beroende av, och det här beteendet upprepas för varje modul, oavsett hur beroende kedjan är. I den här miljön kan varje modul installeras för att ha sina egna versions krav för de moduler som är beroende av, men det kan leda till en stor katalog struktur.

Om du **distribuerar\_noden för Node-moduler** som en del av programmet ökar storleken på distributionen när den jämförs med en **Package. JSON** **-eller NPM-shrinkwrap. JSON-** fil. men det garanterar att de versioner av modulerna som används i produktionen är desamma som modulerna som används för utveckling.

### <a name="native-modules"></a>Inbyggda moduler
De flesta moduler är bara JavaScript-filer med oformaterad text, men vissa moduler är plattformsspecifika binära avbildningar. Dessa moduler kompileras vid installations tiden, vanligt vis med python och Node-Gyp. Eftersom Azure Cloud Services förlitar sig på mappen **\_moduler** som distribueras som en del av programmet, bör alla inbyggda moduler som ingår i de installerade modulerna fungera i en moln tjänst så länge den har installerats och kompilerats i ett Windows Development-system.

Azure App Service har inte stöd för alla inbyggda moduler, och kan Miss kan Miss Missing när moduler kompileras med specifika krav. Vissa populära moduler som MongoDB har valfria inbyggda beroenden och fungerar bra utan dem, men två lösningar har visat sig uppfylla varandra med nästan alla inbyggda moduler som är tillgängliga idag:

* Kör **NPM-installationen** på en Windows-dator som har alla förutsättningar för inbyggd modul installerad. Distribuera sedan mappen skapade **Node\_-moduler** som en del av programmet för att Azure App Service.

  * Innan du kompilerar kontrollerar du att den lokala Node. js-installationen har en matchande arkitektur och att versionen är så nära som möjligt för den som används i Azure (de aktuella värdena kan kontrol leras vid körning från egenskaperna **process. båge** och **process. version**).

* Azure App Service kan konfigureras för att köra anpassade bash eller Shell-skript under distributionen, vilket ger dig möjlighet att köra anpassade kommandon och exakt konfigurera hur **NPM-installationen** körs. En video som visar hur du konfigurerar miljön finns i avsnittet om [distributions skript för anpassad webbplats med kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Använda en Package. JSON-fil

**Package. JSON** -filen är ett sätt att ange de toppnivå beroenden som programmet kräver, så att värd plattformen kan installera beroenden, i stället för att du måste inkludera mappen **\_moduler** som en del av distributionen. När programmet har distribuerats används **installations kommandot NPM** för att tolka **Package. JSON** -filen och installera alla beroenden som anges.

Under utvecklingen kan du använda parametrarna **--Save**, **--Save-dev**eller **--Save-optional** när du installerar moduler för att lägga till en post för modulen i **Package. JSON** -filen automatiskt. Mer information finns i [NPM-install](https://docs.npmjs.com/cli/install).

Ett möjligt problem med **Package. JSON** -filen är att den bara anger versionen för toppnivå beroenden. Varje modul som installeras kan eller kan inte ange vilken version av moduler den är beroende av, och därför kan det hända att du får en annan beroende kedja än den som används i utvecklingen.

> [!NOTE]
> När du distribuerar till Azure App Service, om din <b>Package. JSON</b> -fil refererar till en inbyggd modul kan du se ett fel som liknar följande exempel när du publicerar programmet med git:
> 
> NPM fel! module-name@0.6.0installera: ' Node-Gyp konfigurera build '
> 
> NPM fel! "cmd"/c "" Node-Gyp Configure Build "misslyckades med 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Använda en NPM-shrinkwrap. JSON-fil
**NPM-shrinkwrap. JSON-** filen är ett försök att adressera versions begränsningarna för modulerna i **Package. JSON** -filen. Även om **Package. JSON** -filen bara innehåller versioner för modulerna på den översta nivån, innehåller **NPM-shrinkwrap. JSON-** filen versions kraven för den fullständiga modulens beroende kedja.

När programmet är redo för produktion kan du låsa versions kraven och skapa en **NPM-shrinkwrap. JSON-** fil med hjälp av kommandot **NPM shrinkwrap** . Det här kommandot använder de versioner som för närvarande är installerade i **noden\_moduler** och registrerar dessa versioner i filen **NPM-shrinkwrap. JSON** . När programmet har distribuerats till värd miljön används **NPM install** -kommandot för att parsa **NPM-shrinkwrap. JSON-** filen och installera alla beroenden som anges. Mer information finns i [NPM-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Om din <b>NPM-shrinkwrap. JSON-</b> fil refererar till en inbyggd modul när du distribuerar till Azure App Service kan du se ett fel som liknar följande exempel när du publicerar programmet med git:
> 
> NPM fel! module-name@0.6.0installera: ' Node-Gyp konfigurera build '
> 
> NPM fel! "cmd"/c "" Node-Gyp Configure Build "misslyckades med 1
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du använder Node. js-moduler med Azure får du lära dig hur du [anger Node. js-versionen](https://github.com/squillace/nodejs-microservice), [skapar och distribuerar en Node. js-webbapp](app-service/app-service-web-get-started-nodejs.md)och [hur du använder Azures kommando rads gränssnitt för Mac och Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Mer information finns i [Node.js Developer Center](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
