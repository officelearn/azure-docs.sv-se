---
title: "Hantera Molntjänsten Livscykelhändelser | Microsoft Docs"
description: "Lär dig hur livscykel metoder för en tjänst i molnet roll kan användas i .NET"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: eb78c05df3b3cdf3887334c11bdabd5cebb74747
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Anpassa livscykeln för en webb- eller arbetsroll i .NET
När du skapar en arbetsroll kan du utöka den [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) klassen som innehåller metoder för att åsidosätta att svara på Livscykelhändelser. Den här klassen är valfritt, för webbroller som måste använda för att svara på Livscykelhändelser.

## <a name="extend-the-roleentrypoint-class"></a>Utöka klassen RoleEntryPoint
Den [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) klassen innehåller metoder som anropas av Azure när den **startar**, **kör**, eller **stoppar** rollen webb eller arbetare. Alternativt kan du åsidosätta dessa metoder för att hantera rollen initiering, roll avstängning sekvenser eller tråden körning av rollen. 

När du utökar **RoleEntryPoint**, bör du vara medveten om följande beteenden av metoder:

* Den [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) och [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) metoder returnerar ett booleskt värde, så det är möjligt att returnera **FALSKT** från dessa metoder.
  
   Om din kod returnerar **FALSKT**roll processen avslutas plötsligt, utan att köra alla stängas ned har på plats. I allmänhet bör du undvika returnerar **FALSKT** från den **OnStart** metod.
* Någon utan felhantering undantag inom en överlagring av en **RoleEntryPoint** metoden behandlas som ett ohanterat undantag.
  
   Om ett undantag inträffar inom någon av metoderna livscykel, Azure höjer den [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) händelse och processen avslutas. När din roll har varit offline, startas av Azure. Om ett ohanterat undantag inträffar den [stoppar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) händelsen inte inträffar och **OnStop** -metoden inte anropas.

Om din roll startar inte, eller återvinning mellan initierar upptagen och stoppa tillstånd, kan din kod utlöser ett ohanterat undantag i en av Livscykelhändelser varje gång rollen startas om. I det här fallet använder den [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) händelsen och ta reda på orsaken till undantaget och hantera korrekt. Din roll kan också returnera från den [kör](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metod, vilket gör att rollen ska starta om. Mer information om distributionstillstånd finns [vanliga problem som orsakar roller till återvinning](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Om du använder den **Azure Tools för Microsoft Visual Studio** för att utveckla ditt program projektmallar rollen Utöka automatiskt de **RoleEntryPoint** klassen för dig, i den *WebRole.cs* och *WorkerRole.cs* filer.
> 
> 

## <a name="onstart-method"></a>OnStart-metoden
Den **OnStart** metoden anropas när din rollinstans är online med Azure. Medan OnStart koden körs rollinstansen som har markerats som **upptagen** och ingen extern trafik dirigeras till den av belastningsutjämnaren. Du kan åsidosätta den här metoden om du vill utföra initieringsarbete, till exempel implementera händelsehanterare och starta [Azure Diagnostics](cloud-services-how-to-monitor.md).

Om **OnStart** returnerar **SANT**instansen har initierats och Azure anropar den **RoleEntryPoint.Run** metod. Om **OnStart** returnerar **FALSKT**, rollen omedelbart, avslutar utan att köra alla planerad avstängning sekvenser.

Följande kodexempel visar hur du åsidosätter den **OnStart** metod. Den här metoden konfigurerar och startar en diagnostikövervakare när instansen startar och ställer in en överföring av loggningsdata till ett lagringskonto:

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

## <a name="onstop-method"></a>OnStop-metoden
Den **OnStop** metoden anropas när en rollinstans har varit offline i Azure och innan processen avslutas. Du kan åsidosätta den här metoden om du vill anropa kod som krävs för din instans av serverroll korrekt stängas.

> [!IMPORTANT]
> Kod som körs i den **OnStop** metoden har en begränsad tid att slutföra när den anropas av andra orsaker än användarinitierad avstängning. Efter den här tiden processen avslutas, så måste du se till att koden i den **OnStop** metod kan köra snabbt eller kan tolerera körs inte kan slutföras. Den **OnStop** metoden anropas efter den **stoppar** händelsen inträffar.
> 
> 

## <a name="run-method"></a>Metoden Kör
Du kan åsidosätta den **kör** metod för att implementera en tidskrävande tråd för din rollinstans.

Åsidosätta den **kör** metoden är inte obligatoriska; standardimplementering startar en tråd som alltid i viloläge. Om du åsidosätta den **kör** metoden koden ska blockera på obestämd tid. Om den **kör** metoden returnerar rollen återanvänds automatiskt smidigt; med andra ord genererar Azure den **stoppar** händelse och anrop i **OnStop** metoden så att din avstängning sekvenser kan utföras förrän rollen tas offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementera metoder för ASP.NET-livscykeln för en webbroll
Du kan använda ASP.NET livscykel metoder, förutom de som finns i **RoleEntryPoint** klass för att hantera initiering och avstängning sekvenser för en webbroll. Detta kan vara användbart för kompatibilitet om du överföra ett befintligt ASP.NET-program till Azure. ASP.NET livscykel metoderna anropas inifrån den **RoleEntryPoint** metoder. Den **programmet\_starta** metoden anropas efter den **RoleEntryPoint.OnStart** metod har avslutats. Den **programmet\_End** metoden anropas innan den **RoleEntryPoint.OnStop** metoden anropas.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapa ett paket för cloud service](cloud-services-model-and-package.md).

