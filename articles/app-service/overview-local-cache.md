---
title: Lokal cache
description: Lär dig hur den lokala cachen fungerar i Azure App Service och hur du aktiverar, ändrar storlek på och ifrågasätter statusen för appens lokala cache.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227889"
---
# <a name="azure-app-service-local-cache-overview"></a>Översikt över lokal cache för Azure App Service

> [!NOTE]
> Lokal cache stöds inte i funktionsappar eller behållarappeappar, till exempel i [Windows-behållare](app-service-web-get-started-windows-container.md) eller på [App Service på Linux](containers/app-service-linux-intro.md).


Azure App Service-innehåll lagras på Azure Storage och visas upp på ett hållbart sätt som en innehållsresurs. Den här designen är avsedd att fungera med en mängd olika appar och har följande attribut:  

* Innehållet delas över flera virtuella datorinstanser (VM) i appen.
* Innehållet är hållbart och kan ändras genom att köra appar.
* Loggfiler och diagnostikdatafiler är tillgängliga under samma mapp för delat innehåll.
* Om du publicerar nytt innehåll uppdateras innehållsmappen direkt. Du kan omedelbart visa samma innehåll via SCM-webbplatsen och den appar som körs (vanligtvis vissa tekniker som ASP.NET initierar en omstart av appen på vissa filändringar för att få det senaste innehållet).

Många appar använder en eller alla dessa funktioner, men vissa appar behöver bara en högpresterande, skrivskyddad innehållsbutik som de kan köra från med hög tillgänglighet. Dessa appar kan dra nytta av en VM-instans av en specifik lokal cache.

Funktionen Lokal cache för Azure App Service ger en webbrollvy över ditt innehåll. Det här innehållet är en cache för att skriva men ignorera lagringsinnehållet som skapas asynkront på webbplatsen. När cacheminnet är klart växlas platsen för att köras mot det cachelagrade innehållet. Appar som körs på lokal cache har följande fördelar:

* De är immuna mot svarstider som uppstår när de kommer åt innehåll på Azure Storage.
* De är immuna mot planerade uppgraderingar eller oplanerade driftstopp och andra avbrott med Azure Storage som uppstår på servrar som betjänar innehållsresursen.
* De har färre omstarter av appen på grund av ändringar i lagringsresursen.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Så här ändrar den lokala cachen apptjänstens beteende
* _D:\home_ pekar på den lokala cachen, som skapas på vm-instansen när appen startar. _D:\local_ fortsätter att peka på den tillfälliga VM-specifika lagringen.
* Den lokala cachen innehåller en engångskopia av mapparna _/site_ och _/siteextensions_ i den delade innehållsarkivet, på _D:\home\site_ och _D:\home\siteextensions_. Filerna kopieras till den lokala cachen när appen startar. Storleken på de två mapparna för varje app är begränsad till 300 MB som standard, men du kan öka den med upp till 2 GB. Om de kopierade filerna överskrider storleken på den lokala cachen ignorerar App Service tyst lokal cache och läs från fjärrfilresursen.
* Den lokala cachen är läs-och skrivning. Alla ändringar ignoreras dock när appen flyttar virtuella datorer eller startas om. Använd inte den lokala cachen för appar som lagrar verksamhetskritiska data i innehållsarkivet.
* _D:\home\LogFiles_ och _D:\home\Data_ innehåller loggfiler och appdata. De två undermapparna lagras lokalt på vm-instansen och kopieras till det delade innehållsarkivet med jämna mellanrum. Appar kan spara loggfiler och data genom att skriva dem till dessa mappar. Kopian till den delade innehållsarkivet är dock bäst, så det är möjligt att loggfiler och data går förlorade på grund av en plötslig krasch av en VM-instans.
* [Loggströmning](troubleshoot-diagnostic-logs.md#stream-logs) påverkas av den bästa kopian. Du kan observera upp till en minuts fördröjning i de strömmade loggarna.
* I den delade innehållsarkivet ändras mappstrukturen för _mapparna LogFiles_ och _Data_ för appar som använder den lokala cachen. Det finns nu undermappar i dem som följer namngivningsmönstret för "unik identifierare" + tidsstämpel. Var och en av undermapparna motsvarar en VM-instans där appen körs eller har körts.
* Andra mappar i _D:\home_ finns kvar i den lokala cachen och kopieras inte till den delade innehållsarkivet.
* Appdistribution via valfri metod som stöds publicerar direkt till det varaktiga arkivet för delat innehåll. Om du vill uppdatera mapparna _D:\home\site_ och _D:\home\siteextensions_ i den lokala cachen måste appen startas om. Om du vill göra livscykeln sömlös läser du informationen senare i den här artikeln.
* Standardinnehållsvyn på SCM-webbplatsen fortsätter att vara den delade innehållsarkivets.

## <a name="enable-local-cache-in-app-service"></a>Aktivera lokal cache i apptjänsten
Du konfigurerar lokal cache med hjälp av en kombination av reserverade appinställningar. Du kan konfigurera dessa appinställningar med hjälp av följande metoder:

* [Azure-portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurera lokal cache med hjälp av Azure-portalen
<a name="Configure-Local-Cache-Portal"></a>

Du aktiverar lokal cache per webbapp med den här appinställningen:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Inställningar för Azure Portal-appar: Lokal cache](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurera lokal cache med hjälp av Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

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

## <a name="change-the-size-setting-in-local-cache"></a>Ändra storleksinställningen i Lokal cache
Som standard är den lokala cachestorleken **300 MB**. Detta inkluderar mapparna /site och /siteextensions som kopieras från innehållsarkivet, samt alla lokalt skapade loggar och datamappar. Om du vill öka den `WEBSITE_LOCAL_CACHE_SIZEINMB`här gränsen använder du appinställningen . Du kan öka storleken på upp till **2 GB** (2000 MB) per app.

## <a name="best-practices-for-using-app-service-local-cache"></a>Metodtips för användning av lokal cache för App Service
Vi rekommenderar att du använder lokal cache tillsammans med funktionen [Mellanlagringsmiljöer.](../app-service/deploy-staging-slots.md)

* Lägg till den `WEBSITE_LOCAL_CACHE_OPTION` *klibbiga* `Always` appinställningen med värdet i **produktionsplatsen.** Om du använder `WEBSITE_LOCAL_CACHE_SIZEINMB`lägger du även till den som en klibbig inställning i produktionsplatsen.
* Skapa en **mellanlagringsplats** och publicera till din mellanlagringsplats. Du anger vanligtvis inte mellanlagringsplatsen för att använda lokal cache för att aktivera en sömlös livscykel för build-deploy-test för mellanlagring om du får fördelarna med Lokal cache för produktionsplatsen.
* Testa din webbplats mot din Mellanlagringsplats.  
* När du är klar utfärdar du en [växlingsåtgärd](../app-service/deploy-staging-slots.md#Swap) mellan mellanlagrings- och produktionsplatserna.  
* Klibbiga inställningar inkluderar namn och klibbiga till en plats. Så när mellanlagringsplatsen byts ut till produktion ärver den appinställningarna för lokal cache. Den nyligen utbytta produktionsplatsen kommer att köras mot den lokala cachen efter några minuter och kommer att värmas upp som en del av slot warmup efter byte. Så när slotbytet är klart körs produktionsplatsen mot den lokala cachen.

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Hur vet jag om lokal cache gäller för min app?
Om din app behöver en högpresterande, tillförlitlig innehållsbutik, inte använder innehållsarkivet för att skriva kritiska data vid körning, och är mindre än 2 GB i total storlek, då svaret är "ja"! Om du vill få den totala storleken på mapparna /site och/siteextensions kan du använda webbplatstillägget "Azure Web Apps Disk Usage".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hur vet jag om min webbplats har bytt till att använda lokal cache?
Om du använder funktionen Lokal cache med mellanlagringsmiljöer slutförs inte växlingsåtgärden förrän lokal cache har värmts upp. Om du vill kontrollera om webbplatsen körs mot Lokal cache `WEBSITE_LOCALCACHE_READY`kan du kontrollera variabeln för arbetsprocessmiljön . Använd instruktionerna på variabelsidan [för arbetsprocessmiljö](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) för att komma åt arbetsprocessmiljövariabeln på flera instanser.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Jag har precis publicerat nya ändringar, men min app verkar inte ha dem. Varför det?
Om appen använder lokal cache måste du starta om webbplatsen för att få de senaste ändringarna. Vill du inte publicera ändringar på en produktionsplats? Se alternativen för kortplats i avsnittet tidigare metodtips.

### <a name="where-are-my-logs"></a>Var är mina loggar?
Med Lokal cache ser dina loggar och datamappar lite annorlunda ut. Strukturen för dina undermappar förblir dock densamma, förutom att undermapparna ligger inbäddat under en undermapp med formatet "unik VM-identifierare" + tidsstämpel.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Jag har aktiverat lokal cache, men min app startas fortfarande om. Vad beror det på? Jag trodde att Local Cache hjälpte till med frekventa appomstarter.
Lokal cache förhindrar att lagringsrelaterade app startar om. Appen kan dock fortfarande startas om under planerade infrastrukturuppgraderingar av den virtuella datorn. Den övergripande app omstart som du upplever med lokal cache aktiverad bör vara färre.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Utesluter lokal cache alla kataloger från att kopieras till den snabbare lokala enheten?
Som en del av steget som kopierar lagringsinnehållet utesluts alla mappar som har namnet databas. Detta hjälper till med scenarier där webbplatsinnehållet kan innehålla en databas för källkontroll som kanske inte behövs i den dagliga driften av appen. 
