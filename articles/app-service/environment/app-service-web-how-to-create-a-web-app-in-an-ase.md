---
title: Skapa en webbapp i en App Service Environment v1 - Azure
description: Lär dig hur du skapar web apps och app service-planer i en App Service Environment v1
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
ms.custom: seodec18
ms.openlocfilehash: dcb8ef54cf6816ec3bcedc90790948d6eb58e42f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274538"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Skapa en webbapp i en App Service Environment v1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).
> 

## <a name="overview"></a>Översikt
Den här självstudien visar hur du skapar webbappar och App Service-planer i en [App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Om du vill lära dig hur du skapar en webbapp, men behöver inte göra det i en App Service Environment, se [skapa en .NET-webbapp](../app-service-web-get-started-dotnet.md) eller någon av de relaterade guider för övriga språk och ramverk.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Den här självstudien förutsätter att du har skapat en App Service Environment. Om du inte har gjort det redan finns i [skapa en App Service Environment](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Skapa en webbapp
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapa en resurs > webb + mobilt > Webbapp**. 
   
    ![][1]
2. Välj din prenumeration.  
   
    Tänk på att om du vill skapa en app i App Service Environment, måste du använda samma prenumeration som du använde när du skapar miljön om du har flera prenumerationer. 
3. Välj eller skapa en Resursgrupp.
   
    *Resursgrupper* kan du hantera relaterade Azure-resurser som en enhet och är användbara när upprättar *rollbaserad åtkomstkontroll* (RBAC) regler för dina appar. Mer information finns i [översikt över Azure Resource Manager][ResourceGroups]. 
4. Välj eller skapa en App Service plan.
   
    *App Service-planer* är en hanterad uppsättning webbappar.  Normalt när du väljer priser, tillämpas priset till App Service-planen i stället för enskilda appar. I en ase-miljö betalar du för de beräkningsinstanser som allokerats till ASE i stället för att du har angett med ASP.  Om du vill skala upp antalet instanser av en webbapp som du skala upp instanser av din App Service påverkar plan och alla web apps i den här planen.  Vissa funktioner, till exempel plats för webbplatsen eller VNET-integrering har också kvantitet begränsningar i planen.  Mer information finns i [översikt över Azure App Service-planer](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Du kan identifiera App Service-planerna i din ASE genom att titta på den plats som anges under planens namn.  
   
    ![][5]
   
    Om du vill använda en App Service-plan som redan finns i din App Service Environment, väljer du den här planen. Om du vill skapa en ny App Service-plan, se följande avsnitt i den här självstudien [skapa en App Service-plan i en App Service Environment](#createplan).
5. Ange namn för din webbapp och klicka sedan på **skapa**. 
   
    Om din ASE använder en extern VIP Webbadressen till en app i en ASE är: [*sitename*]. [ *namn på App Service Environment*]. p.azurewebsites.net i stället för [*sitename*]. azurewebsites.net
   
    Om din ASE använder intern VIP och sedan Webbadressen till en app i att ASE är: [*sitename*]. [ *underdomän har angetts under ASE-generering*]   
    När du har valt ASP under ASE-generering underdomänen uppdatera nedan visas **namn**

## <a name="createplan"></a> Skapa en App Service-plan
När du skapar en App Service-plan i en App Service Environment skiljer valen worker sig eftersom det finns ingen delad arbetare i en ase-miljö.  Arbetare som du måste använda är de som har tilldelats ASE av administratören.  Det innebär att om du vill skapa en ny plan måste ha ytterligare arbeten som allokeras till din ASE arbetarpool än det totala antalet instanser för alla dina planer redan i den arbetarpoolen.  Om du inte har tillräckligt med arbetare i arbetarpoolen din ASE att skapa ett schema kan behöva du arbeta med din ASE-administratör för att lägga till dem.

En annan skillnaden med App Service-planer som är värd för en App Service Environment är bristen på val av.  När du har en App Service Environment du betala för beräkningsresurser som används av systemet och har inte har lagts till avgifter för planerna i den miljön.  När du skapar en App Service plan väljer du normalt en prisplanen som avgör din fakturering.  En App Service Environment är i grunden en privat plats där du kan skapa innehåll.  Du betalar för miljön och inte som värd för ditt innehåll.

Följande instruktioner visar hur du skapar en App Service plan när du skapar en webbapp som beskrivs i föregående avsnitt av kursen.

1. Klicka på **Skapa ny** i Planera val av användargränssnitt och ange ett namn för din plan precis som vanligt utanför en ase-miljö.
2. Välj ASE som du vill använda från platsen Väljaren.
   
    Eftersom en App Service Environment är i grunden en privat distributionsplats bör visas under plats. 
   
    ![][2]
   
    Efter valet av en ASE i väljaren för platsen, skapa för App Service-plan Användargränssnittet uppdaterar du.  Platsen visar nu namnet på ASE-systemet och regionen den tillhör och väljaren för prissättning plan ersätts med en väljare för worker-pool.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Att välja en arbetarpool
Normalt finns i Azure App Service och utanför en App Service Environment 3 storlekar som är tillgängliga med valet av en dedikerad prisplanen.  På liknande sätt, för en ASE kan du definiera upp till 3 pooler med arbetare och ange beräkningsstorleken på som används för den arbetarpoolen.  Vad det innebär för klienter på ase-miljön är det i stället för att välja en prisplanen med beräkningsstorleken för App Service-planen, du väljer något som kallas en *arbetarpool*.  

Pool-markeringen worker Användargränssnittet visar beräkningsstorleken som används för den arbetarpoolen under namnet.  Antalet tillgängliga syftar på hur många compute-instanser är tillgängliga för användning i poolen.  Den totala poolen kan faktiskt har flera instanser än det antalet men det här värdet avser bara hur många inte används.  Om du behöver justera din App Service Environment att lägga till mer beräkningsresurser Se [konfigurera App Service Environment](app-service-web-configure-an-app-service-environment.md).

![][4]

I det här exemplet ser du bara två arbetarpooler. Det beror på att ASE administratören bara tilldelas värdar i de två arbetarpooler.  Tredje skulle visas när det finns virtuella datorer allokeras till den.  

## <a name="after-web-app-creation"></a>När du skapa en webbapp
Det finns några saker för att köra webbappar och hantera App Service-planer i en ASE som måste beaktas.  

Som tidigare nämnts ägaren av ase: N ansvarar för storleken på systemet och därmed de ansvarar också för att säkerställa att det finns tillräckligt med kapacitet som värd för de önskade App Service-planerna. Om det finns inga tillgängliga arbeten kan du inte skapa App Service-planen.  Detta är också sant för att skala upp din webbapp.  Om du behöver fler instanser sedan behöver du hämta din App Service Environment-administratören att lägga till ytterligare arbeten.

När du har skapat din webbapp och App Service-plan är det en bra idé att skala upp.  I en ASE måste du alltid ha minst 2 instanser av din App Service-plan för att ge feltolerans för dina appar.  Skala en App Service-plan i en ASE är samma som normalt via Användargränssnittet för App Service-planen.  Mer information om skalning, [skala en webbapp i en App Service Environment](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
