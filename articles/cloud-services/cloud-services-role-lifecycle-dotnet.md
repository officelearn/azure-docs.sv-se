---
title: Hantera livscykelhändelser för molntjänster | Microsoft-dokument
description: Lär dig hur livscykelmetoderna för en molntjänstroll kan användas i .NET
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: a6030cbb756525137497834ac911835033858401
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652081"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Anpassa livscykeln för en webb- eller arbetarroll i .NET
När du skapar en arbetarroll utökar du klassen [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) som innehåller metoder som du kan åsidosätta som gör att du kan svara på livscykelhändelser. För webbroller är den här klassen valfri, så du måste använda den för att svara på livscykelhändelser.

## <a name="extend-the-roleentrypoint-class"></a>Utöka klassen RoleEntryPoint
Klassen [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) innehåller metoder som anropas av Azure när den **startar,** **körs**eller **stoppar** en webb- eller arbetarroll. Du kan också åsidosätta dessa metoder för att hantera rollinitualisering, rollavstängningssekvenser eller körningstråden för rollen. 

När du utökar **RoleEntryPoint**bör du vara medveten om följande beteenden för metoderna:

* [Metoden OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) returnerar ett booleskt värde, så det är möjligt att returnera **false** från den här metoden.
  
   Om koden returnerar **false**avslutas rollprocessen plötsligt, utan att köra någon avstängningssekvens som du kan ha på plats. I allmänhet bör du undvika att returnera **false** från **OnStart-metoden.**
* Alla undantag som inte tas i ett överbelastning av en **RoleEntryPoint-metod** behandlas som ett ohanterat undantag.
  
   Om ett undantag inträffar inom en av livscykelmetoderna höjs händelsen [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) och sedan avslutas processen. När din roll har tagits offline startas den om av Azure. När ett ohanterat undantag inträffar utlöses inte [stoppa-händelsen](/previous-versions/azure/reference/ee758136(v=azure.100)) och **OnStop-metoden** anropas inte.

Om din roll inte startar, eller är återvinning mellan initiering, upptagen och stoppa tillstånd, kan din kod kasta ett ohanterat undantag inom en av livscykelhändelserna varje gång rollen startas om. I det här fallet använder du [unhandledException-händelsen](/dotnet/api/system.appdomain.unhandledexception) för att fastställa orsaken till undantaget och hantera det på rätt sätt. Din roll kan också vara tillbaka från metoden [Kör,](/previous-versions/azure/reference/ee772746(v=azure.100)) vilket gör att rollen startas om. Mer information om distributionstillstånd finns i [Vanliga problem som orsakar roller att återvinna](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Om du använder **Azure Tools för Microsoft Visual Studio för** att utveckla ditt program utökas klassen **RoleEntryPoint** automatiskt i *WebRole.cs* och *WorkerRole.cs* filer.
> 
> 

## <a name="onstart-method"></a>Metoden OnStart
**Metoden OnStart** anropas när din rollinstans är online av Azure. Medan OnStart-koden körs markeras rollinstansen som **Upptagen** och ingen extern trafik dirigeras till den av belastningsutjämnaren. Du kan åsidosätta den här metoden för att utföra initieringsarbete, till exempel implementera händelsehanterare och starta [Azure Diagnostics](cloud-services-how-to-monitor.md).

Om **OnStart** returnerar **true**initieras instansen och Azure anropar metoden **RoleEntryPoint.Run.** Om **OnStart** returnerar **false**avslutas rollen omedelbart, utan att köra några planerade avstängningssekvenser.

I följande kodexempel visas hur du åsidosätter **metoden OnStart.** Den här metoden konfigurerar och startar en diagnostikövervakare när rollinstansen startar och ställer in en överföring av loggningsdata till ett lagringskonto:

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

## <a name="onstop-method"></a>OnStop-metod
**OnStop-metoden** anropas när en rollinstans har tagits offline av Azure och innan processen avslutas. Du kan åsidosätta den här metoden för att anropa kod som krävs för att rollinstansen ska kunna stängas av på ett rent sätt.

> [!IMPORTANT]
> Kod som körs i **OnStop-metoden** har en begränsad tid att slutföra när den anropas av andra orsaker än en användarinitierad avstängning. När den här tiden har gått är processen avslutad, så du måste se till att koden i **OnStop-metoden** kan köras snabbt eller tolererar att den inte körs till slutförande. **OnStop-metoden** anropas när **händelsen Stoppa** har höjts.
> 
> 

## <a name="run-method"></a>Kör metod
Du kan åsidosätta metoden **Kör** för att implementera en tidskrävande tråd för din rollinstans.

Åsidosättning av metoden **Kör** krävs inte. standardimplementeringen startar en tråd som för alltid för alltid. Om du åsidosätter metoden **Kör** ska koden blockeras på obestämd tid. Om metoden **Kör** returneras återvinns rollen automatiskt graciöst. Med andra ord höjer Azure **händelsen Stoppa** och anropar **OnStop-metoden** så att dina avstängningssekvenser kan köras innan rollen tas offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementera ASP.NET livscykelmetoder för en webbroll
Du kan använda ASP.NET livscykelmetoderna, utöver de som tillhandahålls av klassen **RoleEntryPoint,** för att hantera initierings- och avstängningssekvenser för en webbroll. Detta kan vara användbart för kompatibilitetsändamål om du porterar ett befintligt ASP.NET-program till Azure. De ASP.NET livscykelmetoderna anropas inifrån **RoleEntryPoint-metoderna.** **Metoden\_Application Start** anropas när **metoden RoleEntryPoint.OnStart** har slutförts. **Metoden\_Application End** anropas innan **metoden RoleEntryPoint.OnStop** anropas.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapar ett molntjänstpaket](cloud-services-model-and-package.md).




