---
title: Översikt över Azure App Service lokal Cache | Microsoft Docs
description: Den här artikeln beskrivs hur du aktiverar, ändra storlek på och fråga efter statusen för funktionen lokalt cacheminne för Azure App Service
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cephalin
ms.openlocfilehash: 6729c87dcc9a85e2e3ccb6b4822213d38e2ba6f7
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666122"
---
# <a name="azure-app-service-local-cache-overview"></a>Översikt över Azure App Service lokalt cacheminne

> [!NOTE]
> Lokal cache är inte stöds i funktionsappar eller behållarbaserad App Service-appar, till exempel på [App Service i Linux](containers/app-service-linux-intro.md).


Azure web appinnehåll lagras på Azure Storage och är visas upp på ett hållbart sätt som en resurs med innehåll. Den här designen är avsedd att fungera med en rad olika appar och har följande attribut:  

* Innehållet delas mellan flera instanser av virtuella datorer (VM) med webbapp.
* Innehållet är beständiga och kan ändras genom att köra webbappar.
* Loggfiler och diagnostikdata filer är tillgängliga under samma mappen.
* Publicering av nytt innehåll direkt uppdaterar innehållsmappen. Du kan visa samma innehåll via SCM-webbplatsen och webbapp som körs omedelbart (vanligtvis vissa tekniker, till exempel ASP.NET initiera en omstart av web app på vissa filändringar att hämta det senaste innehållet).

Även om många webbappar som använder en eller alla dessa funktioner, behöver webbappar bara en högpresterande, skrivskyddade innehållslagring som de kan köras från med hög tillgänglighet. De här apparna kan dra nytta av en VM-instans av en specifik lokal cache.

Funktionen lokalt cacheminne för Azure App Service tillhandahåller en webbvy rollen av ditt innehåll. Det här innehållet är ett cacheminne för skrivning-utan-borttagna objekt för din lagring av innehåll som har skapats asynkront på plats start. När cachen är klar kan växlas platsen för att köra mot det cachelagrade innehållet. Webbappar som körs på lokal Cache har följande fördelar:

* De är immun till fördröjningar som uppstår när de har åtkomst till innehåll på Azure Storage.
* De är immun planerade uppgraderingar eller oplanerade stillestånd och eventuella andra avbrott med Azure Storage som uppstår på servrar som betjänar innehållsresursen.
* De har färre omstarter av appen på grund av ändringar för storage-resurs.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hur ändrar det lokala cacheminnet beteendet för App Service
* _D:\home_ pekar på den lokala cachen som skapas på den Virtuella datorinstansen när appen startas. _D:\Local_ fortsätter att peka mot tillfällig lagring för virtuella datorer.
* Den lokala cachen innehåller en enstaka kopia av den _/platskonfiguration_ och _/siteextensions_ mappar till den delade innehållslagringen i _D:\home\site_ och _D:\home\ siteextensions_respektive. Filerna kopieras till den lokala cachen när appen startas. Storleken på två mappar för varje app är begränsad till 300 MB som standard, men du kan öka det upp till 2 GB.
* Den lokala cachen är skrivskyddad. Varje ändring ignoreras när appen flyttar virtuella datorer eller hämtar startas om. Använd inte den lokala cachen för appar som lagrar verksamhetskritiska data i arkivet för innehåll.
* _D:\home\LogFiles_ och _D:\home\Data_ innehåller loggfiler och appdata. Två undermappar lagras lokalt på den Virtuella datorinstansen och kopieras till den delade innehållslagringen med jämna mellanrum. Appar kan spara loggfiler och data genom att skriva dem till dessa mappar. Kopiera till den delade innehållslagringen är dock bästa, så det är möjligt för loggfiler och data går förlorade på grund av en plötslig krascher för en VM-instans.
* [Loggströmningen](web-sites-enable-diagnostic-log.md#streamlogs) påverkas av den bästa kopian. Du kan se upp till en minuts fördröjning i loggarna strömmas.
* I den delade innehållslagringen görs en ändring i mappstrukturen för det _LogFiles_ och _Data_ mappar för appar som använder den lokala cachen. Det finns nu undermappar i dem som följer namngivningsmönstret ”unika identifierare” + tidsstämpel. Varje undermapp motsvarar en VM-instans där appen körs eller har körts.
* Andra mappar i _D:\home_ finns kvar i den lokala cachen och inte kopieras till den delade innehållslagringen.
* App-distribution med valfri metod som stöds publicerar direkt till varaktiga delade innehållslagringen. Uppdatera den _D:\home\site_ och _D:\home\siteextensions_ appen behöver startas mappar i den lokala cachen. Se information nedan om du vill göra livscykeln sömlös.
* Innehåll standardvyn för SCM-webbplatsen fortsätter till att den delade innehållslagringen.

## <a name="enable-local-cache-in-app-service"></a>Aktivera lokal Cache i App Service
Du kan konfigurera lokal Cache genom att använda en kombination av reserverade appinställningar. Du kan konfigurera dessa inställningar med hjälp av följande metoder:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurera lokal Cache med hjälp av Azure-portalen
<a name="Configure-Local-Cache-Portal"></a>

Du kan aktivera lokal Cache på basis av per-web-app med hjälp av den här appinställningen: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Appinställningar för Azure portal: lokalt cacheminne](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

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

## <a name="change-the-size-setting-in-local-cache"></a>Ändra storlek på inställningen i lokalt cacheminne
Som standard den lokala cachestorleken är **300 MB**. Detta omfattar /site och /siteextensions mappar som kopieras från arkivet innehåll, samt alla lokalt skapade mappar loggar och data. För att öka den här gränsen kan du använda appinställningen `WEBSITE_LOCAL_CACHE_SIZEINMB`. Du kan öka storleken upp till **2 GB** (2 000 MB) per webbapp.

## <a name="best-practices-for-using-app-service-local-cache"></a>Metodtips för App Service lokalt cacheminne
Vi rekommenderar att du använder lokal Cache tillsammans med den [Mellanlagringsmiljöer](../app-service/web-sites-staged-publishing.md) funktionen.

* Lägg till den *sticky* appinställningen `WEBSITE_LOCAL_CACHE_OPTION` med värdet `Always` till din **produktion** fack. Om du använder `WEBSITE_LOCAL_CACHE_SIZEINMB`, Lägg också till den som en fäst till din produktionsplatsen.
* Skapa en **mellanlagring** fack och publicera till din mellanlagringsplats. Du vanligtvis anger inte mellanlagringsplatsen till att använda lokal Cache för att aktivera en sömlös build distribuera test livscykel för att mellanlagra om du får fördelarna med lokal Cache för produktionsplatsen.
* Testa din webbplats mot din mellanlagringsplats.  
* När du är klar kan du utfärda en [växlingsåtgärden](../app-service/web-sites-staged-publishing.md#Swap) mellan din utvecklings- och platser.  
* Fäst inställningarna omfattar namn och Fäst mot en plats. När mellanlagringsplats hämtar växlas till produktion, ärver så appinställningar lokalt cacheminne. Nyligen bytts ut produktionsplatsen körs mot den lokala cachen efter ett par minuter och kommer värmas upp som en del av fack värma upp efter växlingen. När växlingen är klar, så körs din produktionsplatsen mot det lokala cacheminnet.

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Hur vet jag om lokal Cache gäller för min webbapp?
Om webbappen behöver en högpresterande, tillförlitlig innehållslagring, använder inte arkivet innehåll för att skriva kritiska data vid körning och är mindre än 2 GB i total storlek, sedan är svaret ”yes”! För att få den totala storleken på mapparna /site och /siteextensions kan använda du webbplatstillägg ”Azure Web Apps diskanvändning”.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hur vet jag om min webbplats har ändrats till med hjälp av lokal Cache?
Om du använder funktionen lokalt cacheminne med Mellanlagringsmiljöer, slutföras växlingen inte förrän lokalt cacheminne värmas upp. Om du vill kontrollera om webbplatsen körs mot lokala cacheminnet, du kan kontrollera miljövariabeln worker processen `WEBSITE_LOCALCACHE_READY`. Följ instruktionerna på den [worker processen miljövariabeln](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) sidan för att få åtkomst till miljövariabeln worker processen på flera instanser.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Jag publicerat precis nya ändringar, men min webbapp verkar inte ha dem. Varför?
Om din webbapp använder lokal Cache, måste du starta om webbplatsen för att få de senaste ändringarna. Vill du publicera ändringar i en produktionsplatsen inte? Se alternativ plats i föregående avsnitt med bästa praxis.

### <a name="where-are-my-logs"></a>Var finns min loggarna?
Med lokalt cacheminne ser dina loggar och mappar lite annorlunda. Men detsamma strukturen för din undermappar, förutom att undermapparna är nestled under en undermapp med formatet ”VM-UID” + tidsstämpel.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Jag har lokal Cache aktiverad, men min webbapp fortfarande hämtar startas om. Varför är som? Jag tror att lokal Cache har hjälpt med frekventa appen startas om.
Lokal Cache förhindra omstarter av storage-relaterade web app. Din webbapp kan dock fortfarande genomgå omstarter under planerade infrastruktur uppgraderingar av den virtuella datorn. Övergripande app startas som uppstår på lokal Cache-aktiverade bör vara färre.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Utesluter lokalt cacheminne alla kataloger från att kopieras till en snabbare lokal enhet?
Som en del av det steg som kopierar innehållet för lagring, är en mapp som heter databasen exkluderad. Detta hjälper dig med scenarier där webbplatsens innehåll kan innehålla ett källkontrollscentrallager som inte kan behövas i dagliga driften av webbappen. 
