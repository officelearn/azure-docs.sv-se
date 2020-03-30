---
title: Skapa en webbapp i ASE v1
description: Läs om hur du skapar webbappar i en App Service Environment v1. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c947617f0c27708e72f9bff92e2b0041473cd92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266200"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Skapa en webbapp i en App Service Environment v1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).
> 

## <a name="overview"></a>Översikt
Den här självstudien visar hur du skapar webbappar och App Service-abonnemang i en [App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Om du vill lära dig hur du skapar en webbapp men inte behöver göra det i en App Service-miljö läser du [Skapa en .NET-webbapp](../app-service-web-get-started-dotnet.md) eller en av de relaterade självstudierna för andra språk och ramverk.
> 
> 

## <a name="prerequisites"></a>Krav
Den här självstudien förutsätter att du har skapat en apptjänstmiljö. Om du inte har gjort det ännu läser du [Skapa en apptjänstmiljö](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Skapa en webbapp
1. Klicka på Skapa **en resurs > Web + Mobile > Web App**i [Azure](https://portal.azure.com/)Portal . 
   
    ![][1]
2. Välj din prenumeration.  
   
    Om du har flera prenumerationer vara medvetna om att för att skapa en app i apptjänstmiljön måste du använda samma prenumeration som du använde när du skapade miljön. 
3. Välj eller skapa en Resursgrupp.
   
    *Med resursgrupper* kan du hantera relaterade Azure-resurser som en enhet och är användbara när du upprättar *RBAC-regler (Role-Based Access Control)* för dina appar. Mer information finns i [Översikt över Azure Resource Manager][ResourceGroups]. 
4. Välj eller skapa en App Service plan.
   
    *App Service-planer* är hanterade uppsättningar av webbappar.  Normalt när du väljer prissättning tillämpas det pris som debiteras på App Service-planen i stället för på de enskilda apparna. I en ASE betalar du för beräkningsinstanserna som allokerats till ASE i stället för vad du har listat med din ASP.  Om du vill skala upp antalet instanser av en webbapp skalar du upp instanserna av App Service-planen och det påverkar alla webbappar i den planen.  Vissa funktioner som platsplatser eller VNET-integrering har också kvantitetsbegränsningar i planen.  Mer information finns i [översikt över Azure App Service-abonnemang](../overview-hosting-plans.md)
   
    Du kan identifiera App Service-abonnemangen i din ASE genom att titta på den plats som anges under planens namn.  
   
    ![][5]
   
    Om du vill använda en App Service-plan som redan finns i apptjänstmiljön väljer du den planen. Om du vill skapa en ny App Service-plan läser du följande avsnitt i den här självstudien, [Skapa en apptjänstplan i en apptjänstmiljö](#createplan).
5. Ange namnet på webbappen och klicka sedan på **Skapa**. 
   
    Om din ASE använder en extern VIP är webbadressen till en app i en ASE: [*sitename*]. [*Namnet på apptjänstmiljön*].p.azurewebsites.net i stället för [*sitename*].azurewebsites.net
   
    Om din ASE använder en intern VIP då webbadressen till en app i ase är: [*sitename*]. [*subdomän som anges under ASE skapande*]   
    När du har valt din ASP under ASE skapas kommer du att se underdomänuppdateringen nedan **Namn**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a>Skapa en apptjänstplan
När du skapar en apptjänstplan i en apptjänstmiljö är dina arbetsval olika eftersom det inte finns några delade arbetare i en ASE.  De arbetstagare du måste använda är de som har tilldelats ASE av administratören.  Det innebär att om du vill skapa en ny plan måste du ha fler arbetare allokerade till ase-arbetarpoolen än det totala antalet instanser i alla dina planer som redan finns i den arbetarpoolen.  Om du inte har tillräckligt med arbetare i ASE-arbetarpoolen för att skapa din plan måste du arbeta med ASE-administratören för att få dem tillagda.

En annan skillnad med App Service-planer som anordnas av en App Service Environment är bristen på prissättning val.  När du har en App Service-miljö betalar du för beräkningsresurser som används av systemet och inte har extra avgifter för planerna i den miljön.  Normalt när du skapar en App Service-plan väljer du en prisplan som avgör din fakturering.  En apptjänstmiljö är i huvudsak en privat plats där du kan skapa innehåll.  Du betalar för miljön och inte värd ditt innehåll.

Följande instruktioner visar hur du skapar en App Service-plan medan du skapar en webbapp som förklaras i föregående avsnitt i självstudien.

1. Klicka på **Skapa ny** i abonnemangsvalsgränssnittet och ange ett namn för din plan precis som du normalt skulle utanför en ASE.
2. Välj det ASE som du vill använda från din platsväljare.
   
    Eftersom en apptjänstmiljö i huvudsak är en privat distributionsplats visas den under Plats. 
   
    ![][2]
   
    När du har har markerat en ASE i platsväljaren uppdateras användargränssnittet för apptjänstplan.  Platsen visar nu namnet på ASE-systemet och den region det finns i, och prisplanen plockaren ersätts med en arbetare pool plockare.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Välja en arbetspool
Normalt i Azure App Service och utanför en App Service Environment, det finns 3 beräkningsstorlekar som är tillgängliga med valet av en dedikerad prisplan.  På ett liknande sätt kan du för en ASE definiera upp till 3 arbetspooler och ange den beräkningsstorlek som används för den arbetarpoolen.  Vad det innebär för klienter i ASE är att i stället för att välja en prisplan med beräkningsstorlek för apptjänstplanen väljer du vad som kallas en *arbetarpool*.  

Användargränssnittet för val av arbetspool visar den beräkningsstorlek som används för den arbetarpoolen under namnet.  Den tillgängliga kvantiteten avser hur många beräkningsinstanser som är tillgängliga för användning i poolen.  Den totala poolen kan faktiskt ha fler instanser än det här numret, men det här värdet refererar till helt enkelt hur många som inte används.  Om du behöver justera apptjänstmiljön för att lägga till fler beräkningsresurser, se [Konfigurera din apptjänstmiljö](app-service-web-configure-an-app-service-environment.md).

![][4]

I det här exemplet ser du bara två arbetspooler tillgängliga. Det beror på att ASE-administratören endast allokerade värdar i dessa två arbetspooler.  Den tredje skulle dyka upp när det finns virtuella datorer som tilldelats den.  

## <a name="after-web-app-creation"></a>Efter att webbappen har skapats
Det finns några saker att tänka på när du kör webbappar och hantera App Service-planer i en ASE som måste beaktas.  

Som nämnts tidigare är ägaren av ASE ansvarig för systemets storlek och som ett resultat av detta är de också ansvariga för att se till att det finns tillräcklig kapacitet för att vara värd för de önskade App Service-planerna. Om det inte finns några tillgängliga arbetare kan du inte skapa din App Service-plan.  Detta gäller även för att skala upp din webbapp.  Om du behöver fler instanser måste du få administratören för App Service Environment att lägga till fler arbetare.

När du har skapat webbappen och App Service-planen är det en bra idé att skala upp den.  I en ASE måste du alltid ha minst 2 instanser av apptjänstplanen för att ge feltolerans för dina appar.  Att skala en App Service-plan i en ASE är samma som vanligt via apptjänstplansgränssnittet.  Om du vill ha mer information om skalning kan [du skala en webbapp i en apptjänstmiljö](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
