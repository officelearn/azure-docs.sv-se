---
title: Hantera Livscykelhändelser för molntjänst | Microsoft Docs
description: Lär dig hur livscykel-metoderna i en molntjänstroll kan användas i .NET
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 56f7b5e3b303ce68868f15528d1ec200919b52aa
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001566"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Anpassa livscykeln för en webb- eller arbetsroll i .NET
När du skapar en arbetsroll kan du utöka den [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) klassen som innehåller metoder för att åsidosätta som du kan svara på Livscykelhändelser. För webbroller kan den här klassen är valfritt, så du måste använda den för att svara på Livscykelhändelser.

## <a name="extend-the-roleentrypoint-class"></a>Utöka klassen RoleEntryPoint
Den [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) klassen innehåller metoder som anropas av Azure när det **startar**, **körs**, eller **stoppar** en web- eller worker-roll. Du kan också åsidosätta dessa metoder för att hantera rollen initieringen eller roll-avstängning sekvenser tråden för körning av rollen. 

När du utökar **RoleEntryPoint**, bör du vara medveten om följande beteenden av metoder:

* Den [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) och [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) metoder returnerar ett booleskt värde, så det är möjligt att returnera **FALSKT** från dessa metoder.
  
   Om din kod returnerar **FALSKT**, roll processen avslutas tvärt, utan att köra flera avstängning har på plats. I allmänhet bör du undvika att returnera **FALSKT** från den **OnStart** metod.
* Någon ofångat undantag i en överlagring för en **RoleEntryPoint** metoden behandlas som ett ohanterat undantag.
  
   Om ett undantag inträffar inom någon av metoderna livscykel Azure höjer de [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) händelse och processen avslutas. När din roll har varit offline, startas av Azure. Om ett ohanterat undantag inträffar den [stoppar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) inte inträffar händelsen och **OnStop** metoden inte anropas.

Om din roll startar inte, eller återanvänds mellan initierar, upptagen och stoppa tillstånd, kan din kod utlöste ett ohanterat undantag i någon av de Livscykelhändelser för varje gång rollen startas om. I det här fallet den [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) händelse att ta reda på orsaken för undantaget och hantera på rätt sätt. Din roll kan också returnera från den [kör](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoden, vilket gör att rollen startas om. Mer information om distributionstillstånd finns [vanliga problem som orsak roller till återvinning](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Om du använder den **Azure Tools för Microsoft Visual Studio** för att utveckla ditt program kan projektmallar rollen automatiskt utöka den **RoleEntryPoint** klassen för dig, i den  *WebRole.cs* och *WorkerRole.cs* filer.
> 
> 

## <a name="onstart-method"></a>OnStart-metoden
Den **OnStart** metoden anropas när rollinstansen är online med Azure. Medan OnStart koden körs rollinstansen har markerats som **upptagen** och ingen extern trafik dirigeras till den av belastningsutjämnaren. Du kan åsidosätta den här metoden om du vill utföra initieringen arbeten, till exempel implementera händelsehanterare och starta [Azure Diagnostics](cloud-services-how-to-monitor.md).

Om **OnStart** returnerar **SANT**, instansen är initierad och tjänsten Azure anropar den **RoleEntryPoint.Run** metod. Om **OnStart** returnerar **FALSKT**, rollen avslutas direkt, utan att köra alla planerad avstängning sekvenser.

I följande kodexempel visas hur du kan åsidosätta den **OnStart** metod. Den här metoden konfigurerar och startar en diagnostikövervakare när rollinstansen startas och ställer in en överföring av loggningsdata till ett lagringskonto:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop metod
Den **OnStop** metoden anropas när en rollinstans har varit offline av Azure och innan processen avslutas. Du kan åsidosätta den här metoden för att anropa kod som krävs för din rollinstans att korrekt stängas av.

> [!IMPORTANT]
> Kod som körs i den **OnStop** metoden har en begränsad tid att slutföra när den anropas för av andra orsaker än en användarinitierad avstängning. Efter den här tiden processen avslutas, så måste du se till att koden i den **OnStop** metoden kan köra snabbt eller kan tolerera körs inte kan slutföras. Den **OnStop** metoden anropas efter den **stoppar** inträffar händelsen.
> 
> 

## <a name="run-method"></a>Metoden Kör
Du kan åsidosätta den **kör** metod för att implementera en tidskrävande tråd för rollinstansen.

Åsidosätta den **kör** metod krävs inte, standardimplementering startar en tråd som vilar alltid. Om du åsidosätter den **kör** metod, din kod ska blockera på obestämd tid. Om den **kör** metoden returnerar rollen återanvänds automatiskt ett smidigt sätt, d.v.s. Azure genererar den **stoppar** händelse och anropar den **OnStop** metod så att din Stäng av aktivitetssekvenser kan köras innan rollen tas offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementera metoder för ASP.NET-livscykeln för en webbroll
Du kan använda ASP.NET livscykel metoder, förutom det som tillhandahålls av den **RoleEntryPoint** klass, för att hantera initieringen och Stäng av sekvenser för en webbroll. Detta kan vara användbart för kompatibilitet om du portar ett befintligt ASP.NET-program till Azure. ASP.NET livscykel metoderna anropas inifrån den **RoleEntryPoint** metoder. Den **programmet\_starta** metoden anropas efter den **RoleEntryPoint.OnStart** metoden har slutförts. Den **programmet\_slutet** metoden anropas innan den **RoleEntryPoint.OnStop** metoden anropas.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapa ett molntjänstpaket](cloud-services-model-and-package.md).

