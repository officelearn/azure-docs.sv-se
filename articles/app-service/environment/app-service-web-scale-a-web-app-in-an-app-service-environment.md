---
title: Så här skalar du en App i App Service Environment
description: Skala en app i App Service Environment
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
ms.openlocfilehash: 3b2f93c9763805377ed534d59b1025a5aa1a32e8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955538"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Skala appar i en App Service Environment
Det finns vanligtvis tre saker du kan skala i Azure App Service:

* Prisavtal
* arbetsstorlek 
* antalet instanser.

I en ASE finns inget behov att välja eller ändra prisplanen.  När det gäller funktioner har den redan en Premium prissättningsnivå kapaciteten.  

Med avseende på worker storlekar, kan ASE-administratör tilldela storleken på beräkningsresurs som ska användas för varje arbetarpool.  Det innebär att du kan ha Worker Pool 1 med P4 beräkningsresurser och Worker Pool 2 med P1 beräkningsresurser, om så önskas.  De behöver inte vara i storlek ordning.  Mer information om storlekarna och deras priser finns i dokumentet här [priser för Azure Apptjänst][AppServicePricing].  Detta lämnar skalningsalternativ för web apps och App Service-planer i en App Service Environment ska vara:

* val av Worker-pool
* antal instanser

Ändra något av objekten görs via rätt användargränssnitt som visas för din ASE finns App Service-planer.  

![][1]

Du kan skala upp ASP utöver antalet tillgängliga beräkningsresurser i arbetarpoolen som ASP finns i.  Om du behöver beräkningsresurser i den arbetarpoolen måste du hämta din ASE-administratör att lägga till dem.  Information om omkonfigurera din ASE finns information här: [så här konfigurerar du en App Service environment][HowtoConfigureASE].  Du kanske också vill dra nytta av ASE-funktionerna för automatisk skalning att lägga till kapacitet baserat på schema eller mått.  För att få mer information om hur du konfigurerar automatisk skalning för ASE-miljön själva i [så här konfigurerar du automatisk skalning för en App Service Environment][ASEAutoscale].

Du kan skapa flera app service-planer med hjälp av beräkningsresurser från olika arbetarpooler eller du kan använda samma arbetarpoolen.  Till exempel om du har (10) tillgängliga beräkningsresurser i Worker Pool 1, kan du skapa en app service-plan med (6) beräkningsresurser och en andra app service-plan som använder (4) beräkningsresurs.

### <a name="scaling-the-number-of-instances"></a>Skalning av antalet instanser
När du först skapa din webbapp i en App Service Environment startar med 1 instans.  Du kan skala ut till fler instanser att ge ytterligare beräkningsresurser för din app.   

Om din ASE har tillräckligt med kapacitet är detta ganska enkelt.  Du gå till din App Service-Plan som innehåller de platser som du vill skala upp och välj skala.  Då öppnas Gränssnittet där du kan manuellt ange skalan för ASP eller konfigurera regler för automatisk skalning för ASP.  Manuellt skala ditt program bara ange ***skala genom att*** till ***ett instansantal som anges manuellt***.  Härifrån dra skjutreglaget till önskat antal eller Skriv in den i rutan bredvid skjutreglaget.  

![][2] 

Regler för automatisk skalning för ett ASP i en ASE fungerar på samma sätt som de gör normalt.  Du kan välja ***CPU-procent*** under ***skala genom att*** och skapa regler för automatisk skalning för ASP baserat på CPU-procent eller du kan skapa mer komplexa regler med hjälp av ***schema och Prestandaregler*** .  Du vill ha utförligare information om hur du konfigurerar automatisk skalning använder guiden här [skala en app i Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Val av Worker-Pool
Som tidigare nämnts används worker pool markeringen i ASP-användargränssnittet.  Öppna bladet för ASP som du vill skala och välj arbetarpool.  Du ser alla arbetarpooler som du har konfigurerat i din App Service Environment.  Om du har endast en arbetarpool kommer du bara se en pool i listan.  Om du vill ändra vilka arbetarpool ASP är i välja du helt enkelt arbetarpoolen som du vill att din App Service-Plan att flytta till.  

![][3]

Det är viktigt att se till att du har tillräcklig kapacitet för ASP innan du flyttar dina ASP från en arbetarpool till en annan.  I listan över arbetarpooler, inte bara worker-poolnamn visas men du kan också se hur många arbetare är tillgängliga i den arbetarpoolen.  Se till att det inte finns tillräckligt med instanser som är tillgängliga som innehåller din App Service-Plan.  Om du behöver fler beräkningsresurser i arbetarpoolen som du vill flytta till, får du din ASE-administratör att lägga till dem.  

> [!NOTE]
> Flytta en ASP från en arbetarpool leder kall startar appar i att ASP.  Detta kan orsaka begäranden körs långsamt som din app är kalla igång med nya beräkningsresurser.  Kallstart kan undvikas genom att använda den [program varma upp funktionen] [ AppWarmup] i Azure App Service.  Modulen programinitiering som beskrivs i artikel fungerar även för kallstarter eftersom initieringsprocessen anropas också när appar är kalla igång med nya beräkningsresurser. 
> 
> 

## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [hur du skapar en App Service Environment][HowtoCreateASE]

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
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
