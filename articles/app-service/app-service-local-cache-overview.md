---
title: "Översikt över Azure App Service lokalt cacheminne | Microsoft Docs"
description: "Den här artikeln beskriver hur du aktiverar, ändra storlek och fråga status för funktionen Azure App Service lokalt cacheminne"
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: 
tags: optional
keywords: 
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-local-cache-overview"></a>Översikt över Azure App Service lokalt cacheminne
Azure web appinnehåll lagras på Azure Storage och är anslutna upp en beständig sätt som ett delat innehåll. Den här designen är avsedd att fungera med en rad olika appar och har följande attribut:  

* Innehållet delas mellan flera virtuella (VM)-instanser av webbappen.
* Innehållet är beständiga och kan ändras genom att köra webbappar.
* Loggfiler och diagnostikdata filer finns i samma mapp för delat innehåll.
* Publicering av nytt innehåll direkt uppdaterar innehållsmappen. Du kan visa samma innehåll via webbplatsen SCM och webbapp som körs omedelbart (vanligtvis vissa tekniker, till exempel ASP.NET initiera en web app omstart på vissa filändringar att hämta det senaste innehållet).

När många webbappar som använder en eller flera av de här funktionerna, måste vissa webbprogram bara en hög prestanda, skrivskyddad innehållslagring som de kan köras från med hög tillgänglighet. De här apparna kan dra nytta av en VM-instans av en viss lokal cache.

Funktionen lokalt cacheminne för Azure App Service tillhandahåller en webbvy roll av ditt innehåll. Det här innehållet är en Skriv-men-ta bort cache lagring innehåll som har skapats på plats asynkront start. När cachen är klar, växlas platsen om du vill köra mot det cachelagra innehållet. Webbprogram som körs på den lokala cachen har följande fördelar:

* De är immun till latens som sker när de kommer åt innehåll på Azure Storage.
* De är immun planerade uppgraderingar eller oplanerade stillestånd och eventuella andra avbrott med Azure Storage som uppstår på servrar som betjänar innehållsdelen.
* De har färre omstarter av appen på grund av ändringar för storage-resursen.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Hur beteendet för Apptjänst ändras i lokalt cacheminne
* Det lokala cacheminnet är en kopia av mapparna /site och /siteextensions för webbappen. Den har skapats på den lokala VM-instansen på web app start. Storleken på den lokala cachen per webbprogrammet är begränsad till 300 MB som standard, men du kan öka den upp till 2 GB.
* Den lokala cachen är skrivskyddad. Alla ändringar ignoreras när webbprogrammet flyttar virtuella datorer eller hämtar startas om. Använd inte lokalt cacheminne för appar som kritiska data lagras i arkivet för innehåll.
* Webbprogram kan fortsätta att skriva loggfiler och diagnostikdata som för närvarande. Loggfiler och data, men lagras lokalt på den virtuella datorn. Sedan kopieras de över regelbundet till den delade innehållslagringen. Kopiera till den delade innehållslagringen är en bästa möjliga ansträngning--skrivåtgärder ryggstöd kan gå förlorade på grund av en plötslig kraschar för en VM-instans.
* Det finns en ändring i mappstrukturen i mapparna loggfiler och Data för webbprogram som använder lokal Cache. Det finns nu undermappar i mappen lagring loggfiler och Data som följer namngivningsmönstret ”UID” + tidsstämpel. Varje undermapp motsvarar en VM-instans där webbappen körs eller har körts.  
* Publicera ändringar i webbappen via de publishing mekanismer publicerar till den delade innehållslagringen. Detta är avsiktligt eftersom vi vill publicerat innehåll ska vara beständig. Den måste startas om för att uppdatera den lokala cachen på webbprogrammet. Verkar det som ett steg i mycket? Livscykeln sömlös finns information senare i den här artikeln.
* D:\Home pekar på den lokala cachen. D:\Local fortsätta att peka på VM-specifika tillfällig lagring.
* Innehåll standardvyn för SCM platsen fortsätter att vara som delad innehållslagring.

## <a name="enable-local-cache-in-app-service"></a>Aktivera lokal Cache i App Service
Du kan konfigurera lokal Cache med hjälp av en kombination av reserverade app-inställningar. Du kan konfigurera dessa app-inställningar med hjälp av följande metoder:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurera lokal Cache med hjälp av Azure portal
<a name="Configure-Local-Cache-Portal"></a>

Du kan aktivera lokalt cacheminne på grundval av per webbprogram med hjälp av appinställningen:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure portal appinställningar: lokalt cacheminne](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurera lokal Cache med hjälp av Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

"properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Ändra inställningen storlek i det lokala cacheminnet
Som standard är den lokala cachestorleken **300 MB**. Detta inkluderar /site och /siteextensions mappar som kopieras från arkivet innehåll, samt alla lokalt skapade mappar loggar och data. Om du vill öka gränsen för den här inställningen app `WEBSITE_LOCAL_CACHE_SIZEINMB`. Du kan öka storleken upp till **2 GB** (2 000 MB) per webbprogrammet.

## <a name="best-practices-for-using-app-service-local-cache"></a>Metodtips för App Service lokalt cacheminne
Vi rekommenderar att du använder lokal Cache tillsammans med den [Mellanlagringsmiljöer](../app-service/web-sites-staged-publishing.md) funktion.

* Lägg till den *Fäst* appinställningen `WEBSITE_LOCAL_CACHE_OPTION` med värdet `Always` till din **produktion** plats. Om du använder `WEBSITE_LOCAL_CACHE_SIZEINMB`, Lägg också till den som en Fäst inställning till din produktionsplatsen.
* Skapa en **mellanlagring** fack och publicera för mellanlagrings-plats. Du normalt anger inte mellanlagringsplatsen använda lokalt cacheminne för att aktivera en sömlös build distribuera test livscykel för mellanlagring om fördelarna med lokalt cacheminne för produktionsplatsen.
* Testa platsen mot mellanlagrings-plats.  
* När du är klar kan du utfärda ett [växlingsåtgärden](../app-service/web-sites-staged-publishing.md#Swap) mellan mellanlagring och produktion fack.  
* Fäst inställningar inkluderar namn och fäst till en plats. Så när mellanlagring facket hämtar växlas över till produktion, ärver den lokala cachen app-inställningar. Nyligen växlats produktionsplatsen ska köras mot den lokala cachen efter några minuter och kommer att varmkörts som en del av fack upp efter växlingen. När fack växlingen är klar, så körs din produktionsplatsen mot den lokala cachen.

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Hur tar jag reda på om det gäller lokalt cacheminne för webbappen?
Om ditt webbprogram måste en högpresterande, tillförlitlig innehållslagring, använder inte arkivet innehåll för att skriva kritiska data vid körning och är mindre än 2 GB i total storlek, sedan är svaret ”Ja”! För att få den totala storleken på mapparna /site och /siteextensions, kan du använda webbplatstillägg ”Azure Web Apps diskanvändning”.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hur vet jag om min webbplats har bytt till med hjälp av lokal Cache?
Om du använder funktionen lokalt cacheminne med Mellanlagringsmiljöer slutföras byte inte förrän lokal Cache är varmkörts. Du kan kontrollera miljövariabeln worker process för att kontrollera om webbplatsen körs mot lokal Cache `WEBSITE_LOCALCACHE_READY`. Följ instruktionerna på den [worker process miljövariabeln](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) sidan för att få åtkomst till miljövariabeln worker process på flera instanser.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Jag publicerade bara nya ändringar, men webbappen verkar inte ha dem. Varför?
Om ditt webbprogram använder lokalt cacheminne, måste du starta om webbplatsen för att få de senaste ändringarna. Vill du inte publicera ändringar i en produktionsmiljö plats? Se fack alternativen ovan för bästa praxis.

### <a name="where-are-my-logs"></a>Var finns Mina loggar?
Med lokalt cacheminne ser dina loggar och mappar lite annorlunda ut. Dock förblir strukturen för din undermappar detsamma, förutom att undermapparna nestled under en undermapp med formatet ”VM-UID” + tidsstämpel.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Jag har lokal Cache aktiverad, men webbappen fortfarande hämtar startas om. Varför är? Jag tror lokalt cacheminne hjälpt med frekventa appen startas om.
Lokal Cache förhindra omstarter av lagringsrelaterade web app. Ditt webbprogram kan dock fortfarande genomgå omstarter under planerade infrastruktur uppgraderingar av den virtuella datorn. Övergripande program startas som uppstår på lokal Cache aktiverad ska vara färre.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Utesluter lokalt cacheminne några kataloger som ska kopieras till en snabbare lokal enhet?
Som en del av det steg som kopierar innehållet för lagring, är en mapp som heter databasen exkluderad. Detta hjälper med scenarier där webbplatsens innehåll kan innehålla en källkontroll som inte kanske behövs i den dagliga driften av webbappen. 
