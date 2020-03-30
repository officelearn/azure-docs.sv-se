---
title: Skala en app i ASE v1
description: Skala en app i en apptjänstmiljö. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 43849ca7084f2237c37ad537c50f4e94ac4ea7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688670"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Skala appar i en apptjänstmiljö v1
I Azure App Service finns det normalt tre saker du kan skala:

* prisplan
* arbetarstorlek 
* antalet instanser.

I en ASE behöver du inte välja eller ändra prisplanen.  När det gäller kapacitet är det redan på en Premium prissättning kapacitet nivå.  

När det gäller arbetarstorlekar kan ASE-administratören tilldela storleken på beräkningsresursen som ska användas för varje arbetspool.  Det innebär att du kan ha Arbetarpool 1 med P4-beräkningsresurser och Arbetarpool 2 med P1-beräkningsresurser om så önskas.  De behöver inte vara i storleksordning.  Mer information om storlekarna och deras priser finns i dokumentet här [Azure App Service Pricing][AppServicePricing].  Detta gör att skalningsalternativen för webbappar och apptjänstplaner i en apptjänstmiljö blir:

* val av arbetspool
* antal instanser

Ändra något av objekten sker via lämpligt användargränssnitt som visas för din ASE värd App Service Plans.  

![][1]

Du kan inte skala upp asp-programmet utöver antalet tillgängliga beräkningsresurser i arbetspoolen som asp:en är i.  Om du behöver beräkningsresurser i den arbetarpoolen måste du få ASE-administratören att lägga till dem.  Information om hur du konfigurerar om DIN ASE finns i informationen här: [Så här konfigurerar du en App Service-miljö][HowtoConfigureASE].  Du kanske också vill dra nytta av ase-funktionerna för automatisk skalning för att lägga till kapacitet baserat på schema eller mått.  Om du vill ha mer information om hur du konfigurerar automatisk skalning för själva ASE-miljön finns [i Så här konfigurerar du automatisk skalning för en App Service Environment][ASEAutoscale].

Du kan skapa flera apptjänstplaner med beräkningsresurser från olika arbetspooler eller använda samma arbetspool.  Om du till exempel har (10) tillgängliga beräkningsresurser i Arbetarpool 1 kan du välja att skapa en apptjänstplan med (6) beräkningsresurser och en andra apptjänstplan som använder (4) beräkningsresurser.

### <a name="scaling-the-number-of-instances"></a>Skala antalet instanser
När du först skapar din webbapp i en App Service-miljö börjar den med en instans.  Du kan sedan skala ut till ytterligare instanser för att tillhandahålla ytterligare beräkningsresurser för din app.   

Om din ASE har tillräckligt med kapacitet så är detta ganska enkelt.  Du går till apptjänstplanen som innehåller de webbplatser som du vill skala upp och välja Skala.  Detta öppnar användargränssnittet där du manuellt kan ställa in skalan för DIN ASP eller konfigurera regler för automatisk skalning för din ASP.  Om du vill skala appen manuellt anger du skala ***efter*** ***ett instansantal som jag anger manuellt***.  Härifrån drar du antingen skjutreglaget till önskad kvantitet eller anger det i rutan bredvid skjutreglaget.  

![][2] 

Reglerna för automatisk skalning för en ASP i en ASE fungerar på samma sätt som de gör normalt.  Du kan välja ***CPU-procent*** under ***Skala efter*** och skapa regler för automatisk skalning för din ASP baserat på CPU-procent eller så kan du skapa mer komplexa regler med hjälp av ***schema- och prestandaregler***.  Om du vill se mer fullständig information om hur du konfigurerar automatisk skalning använder du guiden här [Skala en app i Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Val av arbetspool
Som nämnts tidigare används valet av arbetarpool från ASP-användargränssnittet.  Öppna bladet för den ASP som du vill skala och välj arbetarpool.  Du kommer att se alla arbetspooler som du har konfigurerat i apptjänstmiljön.  Om du bara har en arbetspool ser du bara den pool som anges.  Om du vill ändra vilken arbetspool din ASP finns i väljer du helt enkelt den arbetarpool som du vill att appserviceplanen ska flyttas till.  

![][3]

Innan du flyttar din ASP från en arbetspool till en annan är det viktigt att se till att du har tillräcklig kapacitet för din ASP.  I listan över arbetspooler visas inte bara arbetarpoolnamnet, utan du kan också se hur många arbetare som är tillgängliga i den arbetarpoolen.  Kontrollera att det finns tillräckligt många instanser tillgängliga för att innehålla din appserviceplan.  Om du behöver mer beräkningsresurser i den arbetarpool som du vill flytta till, så få ASE-administratören att lägga till dem.  

> [!NOTE]
> Om du flyttar en ASP från en arbetspool blir det kalla starter för apparna i asp-programmet.  Detta kan orsaka att begäranden körs långsamt när appen är kallstartade på de nya beräkningsresurserna.  Kallstart kan undvikas med hjälp av [programmets uppvärmningsfunktion][AppWarmup] i Azure App Service.  Programinitualiseringsmodulen som beskrivs i artikeln fungerar också för kallstarter eftersom initieringsprocessen också anropas när appar är kalla startade på nya beräkningsresurser. 
> 
> 

## <a name="getting-started"></a>Komma igång
Lär dig hur du skapar [en apptjänstmiljö][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
