---
title: Så här skalar du en App i en Apptjänst-miljö
description: Skala en app i en Apptjänst-miljö
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.openlocfilehash: d04a5fce920dae25507cdf2f64832574e24c51dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836555"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Skala appar i en App Service Environment
Det finns vanligtvis tre saker du kan skala i Azure App Service:

* priser för planen
* Worker storlek 
* antal instanser.

Det finns inget behov av att välja eller ändra prisplanen i en ASE.  Vad gäller funktioner har den redan en Premium prissättning kapaciteten.  

Med avseende på worker storlekar tilldela ASE-administratör storleken på beräkningsresurser som ska användas för varje arbetspool.  Det innebär du kan ha Worker Pool 1 med P4 beräkningsresurser och Worker Pool 2 med P1 beräkningsresurser om så önskas.  De behöver inte vara i storlek ordning.  Mer information kring storlekarna och deras prisnivå finns i dokumentet här [priser för Azure Apptjänst][AppServicePricing].  Detta lämnar skalningsalternativ för webbprogram och Apptjänstplaner i en Apptjänst-miljö ska vara:

* val av Worker-pool
* antal instanser

Ändra antingen objektet görs via Gränssnittet lämpliga visas för dina ASE finns App Service-planer.  

![][1]

Du kan skala upp ASP utöver antalet tillgängliga beräkningsresurser i poolen worker som ASP finns i.  Du måste hämta ASE administratören lägga till dem om du behöver beräkningsresurser i den poolen, worker.  För information om hur du konfigurerar din ASE läsa information här: [så här konfigurerar du en Apptjänst-miljö][HowtoConfigureASE].  Du kanske också vill dra nytta av ASE Autoskala funktioner att lägga till kapacitet baserat på schema eller mått.  Om du vill få mer information om hur du konfigurerar Autoskala för ASE miljön själva finns [hur du konfigurerar Autoskala för en Apptjänstmiljö][ASEAutoscale].

Du kan skapa flera app service-planer med beräkningsresurser från olika arbetarpooler eller kan du använda samma worker-poolen.  Till exempel om du har (10) tillgängliga beräkningsresurser i Worker Pool 1, kan du skapa en apptjänstplan med (6) beräkningsresurser och en andra app service-plan som använder (4) beräkningsresurser.

### <a name="scaling-the-number-of-instances"></a>Skala antalet instanser
När du först skapar ditt webbprogram i en Apptjänst-miljö startar med 1-instans.  Du kan skala ut till ytterligare instanser för att tillhandahålla ytterligare beräkningsresurser för din app.   

Om din ASE har tillräckligt med kapacitet är detta ganska enkelt.  Du gå till din App Service-Plan som innehåller platser som du vill skala upp och välj skala.  Detta öppnar gränssnitt där du kan manuellt ange skalan för ASP eller konfigurera automatiska regler för ASP.  Att manuellt skala ditt program bara ange ***skala genom*** till ***instansantal som anges manuellt***.  Härifrån dra reglaget till önskat antal eller ange den i rutan bredvid skjutreglaget.  

![][2] 

Automatiska regler för ett ASP i en ASE fungerar på samma sätt som de normalt gör.  Du kan välja ***Prosessorprocent*** under ***skala genom*** och skapar automatiska regler för ASP baserat på CPU-procent eller du kan skapa mer komplexa regler med hjälp av ***schema-och prestanda***.  Se mer information om hur du konfigurerar Autoskala använda guiden här [skala en app i Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Val av Worker-Pool
Som tidigare nämnts används worker poolen markeringen från ASP-Gränssnittet.  Öppna bladet för ASP som du vill skala och välj arbetspool.  Du kommer se alla worker-pooler som du har konfigurerat i din Apptjänst-miljö.  Om du har bara en arbetspool kommer du bara se en pool i listan.  Om du vill ändra vilka arbetspool ASP är i välja du bara arbetspool som du vill att din App Service-Plan att flytta till.  

![][3]

Det är viktigt att se till att du har tillräcklig kapacitet för ASP innan du flyttar ASP från en arbetspool till en annan.  I listan över arbetarpooler, inte bara poolnamn worker visas men du kan också se hur många personer som är tillgängliga i den poolen, worker.  Kontrollera att det finns tillräckligt med tillgängliga som innehåller din Programtjänstplan instanser.  Om du behöver fler beräkningsresurser i arbetspool som du vill flytta till, får du ASE administratören lägga till dem.  

> [!NOTE]
> Flytta en ASP från en arbetspool gör att kalla startar av appar i att ASP.  Detta kan orsaka begäranden körs långsamt som din app är kall startats på de nya beräkningsresurserna.  Kallstart kan undvikas genom att använda den [programmet varmt in kapaciteten] [ AppWarmup] i Azure App Service.  Modulen programinitiering som beskrivs i artikel fungerar även för kallstart eftersom initieringsprocessen anropas också när appar är kall startats på nya beräkningsresurser. 
> 
> 

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [hur att skapa en Apptjänst-miljö][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
