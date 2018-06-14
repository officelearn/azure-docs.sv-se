---
title: Skapa en webbapp i en Apptjänst-miljö v1
description: Lär dig att skapa webbprogram och app service-planer i en Apptjänst-miljö v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 1e8540409c6174ad02bd2d9d57c53e0279f49871
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386919"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Skapa en webbapp i en Apptjänst-miljö v1

> [!NOTE]
> Den här artikeln handlar om Apptjänstmiljö v1.  Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
> 

## <a name="overview"></a>Översikt
Den här kursen visar hur du skapar webbappar och apptjänstplaner i en [Apptjänstmiljö v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Om du vill veta hur du skapar en webbapp, men behöver inte göra det i en Apptjänst-miljö, se [skapa ett .NET-webbprogram](../app-service-web-get-started-dotnet.md) eller någon av följande relaterade självstudiekurser för andra språk och ramverk.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Den här kursen förutsätter att du har skapat en Apptjänst-miljö. Om du inte har gjort det ännu kan se [skapa en Apptjänst-miljö](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Skapa en webbapp
1. I den [Azure Portal](https://portal.azure.com/), klickar du på **skapar du en resurs > webb + mobilt > Webbapp**. 
   
    ![][1]
2. Välj din prenumeration.  
   
    Tänk på att om du vill skapa en app i din Apptjänst-miljö måste du använda samma prenumeration som du använde när du skapar miljön om du har flera prenumerationer. 
3. Välj eller skapa en Resursgrupp.
   
    *Resursgrupper* kan du hantera relaterade Azure-resurser som en enhet och är användbara när *rollbaserad åtkomstkontroll* (RBAC) regler för dina appar. Mer information finns i [översikt över Azure Resource Manager][ResourceGroups]. 
4. Välj eller skapa en App Service plan.
   
    *Apptjänstplaner* är hanterade uppsättningar av webbprogram.  Normalt tillämpas priset App Service-plan i stället för enskilda appar när du väljer prisnivå. I en ASE betalar för compute-instanser som allokerats till ASE i stället för att du har angett med ASP.  Om du vill skala antalet instanser av ett webbprogram som du skalar upp instanser av din Apptjänst påverkar plan och alla web apps i planen.  Vissa funktioner, till exempel plats platser eller VNET-integrering har också antal begränsningar i planen.  Mer information finns i [översikt över Azure App Service-planer](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Du kan identifiera App Service-planer i din ASE genom att titta på den plats som anges under namn för energischema.  
   
    ![][5]
   
    Välj det alternativ om du vill använda en App Service-plan som redan finns i din Apptjänst-miljö. Om du vill skapa en ny programtjänstplan finns i följande avsnitt i den här kursen [skapa en apptjänstplan i en Apptjänst-miljö](#createplan).
5. Ange namnet för din webbapp och klicka sedan på **skapa**. 
   
    Om din ASE använder en extern VIP URL-Adressen till en app i en ASE är: [*sitename*]. [ *namn på din Apptjänstmiljö*]. p.azurewebsites.net i stället för [*sitename*]. azurewebsites.net
   
    Om din ASE använder en intern VIP sedan Webbadressen till en app i den ASE är: [*sitename*]. [ *underdomän som angetts under skapande av ASE*]   
    När du har valt ASP under skapande av ASE underdomänen uppdatera nedan visas **namn**

## <a name="createplan"></a> Skapa en apptjänstplan
När du skapar en apptjänstplan i en Apptjänst-miljö skiljer worker-alternativ som det finns ingen delad arbetare i en ASE.  Personer som du måste använda är de som har tilldelats ASE av administratören.  Detta innebär att om du vill skapa en ny plan måste ha flera personer som allokerats till din ASE arbetspool än det totala antalet instanser för alla dina planer redan i den poolen, worker.  Du måste arbeta med ASE administratören att lägga till dem om du inte har tillräckligt med personer i din ASE worker-pool för att skapa ett schema.

En annan skillnaden med App Service-planer hos en Apptjänst-miljö är bristen på priser val.  När du har en Apptjänst-miljö betalar för beräkningsresurser som används av systemet och har inte lagts till kostnader för planerna i miljö.  När du skapar en apptjänstplan välja normalt en prisavtal som avgör din fakturering.  En Apptjänst-miljö är i grunden en privat plats där du kan skapa innehåll.  Du betalar för miljön och inte som värd för ditt innehåll.

I följande anvisningar visar hur du skapar en apptjänstplan när du skapar en webbapp som beskrivs i föregående avsnitt av kursen.

1. Klicka på **Skapa nytt** i planen markeringen UI och ange ett namn för din plan precis som vanligt utanför en ASE.
2. Välj ASE som du vill använda från väljaren för platsen.
   
    Eftersom en Apptjänst-miljö är i grunden en privat distributionsplats bör visas under plats. 
   
    ![][2]
   
    Efter valet av en ASE i Väljaren plats App Service-plan skapa UI uppdaterar du.  Platsen visas nu namnet på systemets ASE och regionen i och Väljaren prisnivå plan ersätts med en worker poolen väljare.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Att välja en arbetspool
Normalt finns i Azure App Service och utanför en Apptjänst-miljö 3 beräknings-storlekar som är tillgängliga i valet av en dedikerad prisplan.  På ett liknande sätt för en ASE kan du definiera upp till 3 pooler för anställda och ange storleken för beräkning som används för att worker-poolen.  Vad det innebär för innehavare av ASE är att i stället för att välja prisavtal med beräkning storlek för din App Service-plan måste du välja något som kallas en *arbetspool*.  

Pool-markeringen worker UI visar beräknings-storlek som används för att arbetspool under namnet.  Tillgängligt antal syftar på hur många compute-instanser som är tillgängliga för användning i poolen.  Den totala poolen kan faktiskt har fler förekomster än det antalet men det här värdet avser bara hur många används inte.  Om du behöver justera din Apptjänst-miljö att lägga till fler beräkningsresurser finns [konfigurera din Apptjänst-miljö](app-service-web-configure-an-app-service-environment.md).

![][4]

I det här exemplet ser du bara två arbetarpooler. Det beror på att administratören ASE endast allokerade värdar i de två arbetarpooler.  Tredje skulle visas när det finns virtuella datorer som har allokerats till den.  

## <a name="after-web-app-creation"></a>När webbappen
Det finns några överväganden för att köra webbappar och hantera programtjänstplaner i en ASE som måste beaktas.  

Som tidigare nämnts ägaren av ASE ansvarar för storleken på systemet och är därför också ansvarig för att säkerställa att det finns tillräckligt med kapacitet för värd för de önskade App Service-planerna. Om det finns inga tillgängliga Worker-arbeten kan du inte skapa App Service-plan.  Detta är även true om du vill skala upp ditt webbprogram.  Om du behöver fler instanser skulle sedan du behöva hämta Apptjänstmiljö administratören att lägga till flera personer.

När du har skapat din webbapp och programtjänstplanen är det en bra idé att skala upp.  I en ASE måste du alltid ha minst 2 instanser av din App Service-plan för att ge feltolerans för dina appar.  Skala en apptjänstplan i en ASE är samma som normalt via Användargränssnittet för App Service-plan.  Mer information om skalning, [så att skala ett webbprogram i en Apptjänst-miljö](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
