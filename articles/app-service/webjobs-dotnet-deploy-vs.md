---
title: Utveckla och distribuera WebJobs med hjälp av Visual Studio – Azure
description: Lär dig hur du utvecklar och distribuerar du Azure WebJobs till Azure App Service med Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832407"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Utveckla och distribuera WebJobs med hjälp av Visual Studio – Azure App Service

Den här artikeln förklarar hur du använder Visual Studio för att distribuera ett konsolprogramsprojekt till en webbapp i [Apptjänst](overview.md) som en [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Information om hur du distribuerar WebJobs genom att använda den [Azure-portalen](https://portal.azure.com), se [kör bakgrundsaktiviteter med WebJobs](webjobs-create.md).

Du kan publicera en enkel webbapp för flera WebJobs. Se till att varje Webbjobb i en webbapp har ett unikt namn.

Version 3.x för den [Azure WebJobs SDK](webjobs-sdk-how-to.md) kan du utveckla WebJobs som körs som .NET Core-appar, medan version 2.x stöder endast .NET Framework. Hur du distribuerar ett WebJobs-projekt är olika .NET Core-projekt jämfört med .NET Framework som.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs som .NET Core-konsolappar

När du använder version 3.x för WebJobs, kan du skapa och publicera WebJobs som .NET Core-konsolappar. Stegvisa anvisningar om hur du skapar och publicerar en .NET Core-konsolprogram till Azure som ett WebJob finns i [Kom igång med Azure WebJobs-SDK för händelsedrivna Bakgrundsbearbetning](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET core WebJobs kan inte kopplas till webbprojekt. Om du behöver distribuera ditt WebJob med en webbapp, bör du [skapa ditt WebJob som en .NET Framework-konsolapp](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuera till Azure App Service

Publicera ett .NET Core WebJob på App Service från Visual Studio använder samma verktyg som publicerar en ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob-typer

Som standard publicerade ett WebJob från en .NET Core konsollprojekt körs bara när de utlöses eller på begäran. Du kan också uppdatera projektet till [körs enligt ett schema](#scheduled-execution) eller köras kontinuerligt.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Schemalagda körning

När du publicerar ett .NET Core-konsolprogram som Azure, en ny *settings.job* fil läggs till i projektet. Använd den här filen om du vill ange en körschemat för ditt WebJob. Mer information finns i [schemalägga ett utlösta Webbjobb](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Kontinuerlig körning

Du kan använda Visual Studio för att ändra Webbjobb ska köras kontinuerligt när AlwaysOn är aktiverat i Azure.

1. Om du inte redan gjort det, [publicera projektet på Azure](#deploy-to-azure-app-service).

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I den **publicera** fliken **inställningar**. 

1. I den **profilinställningar** dialogrutan Välj **kontinuerlig** för **WebJob typ**, och välj **spara**.

    ![Publicera dialogrutan Inställningar för ett Webbjobb](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Välj **publicera** att publicera Webbjobb med de uppdaterade inställningarna.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs som .NET Framework-konsolappar  

När Visual Studio distribuerar ett WebJobs-aktiverad .NET Framework-konsolprogram projekt, utför två uppgifter:

* Kopierar körningsfiler till rätt mapp i webbapp (*App_Data/jobb/continuous* för kontinuerliga WebJobs och *App_Data/jobb/triggered* för schemalagd eller på begäran WebJobs).
* Ställer in [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) jobb för WebJobs som är schemalagda att köras vid specifika tidpunkter. (Detta krävs inte för kontinuerliga WebJobs.)

Ett WebJobs-aktiverade projekt har följande funktioner har lagts till:

* Den [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paketet.
* En [webjob publicera settings.json](#publishsettings) -fil som innehåller inställningar för distribution och scheduler. 

![Diagram som visar vad som har lagts till i en Konsolapp för att aktivera distribution som ett WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Du kan lägga till dessa objekt i en befintlig konsolprogramsprojekt eller använda en mall för att skapa ett nytt projekt för WebJobs-aktiverade konsolprogram. 

Du kan distribuera ett projekt som ett WebJob ensamt eller länka det till ett webbprojekt så att den distribuerar automatiskt när du distribuerar webbprojektet. Om du vill länka projekt, Visual Studio innehåller namnet på WebJobs-aktiverade projektet i en [webjobs list.json](#webjobslist) filen i webbprojektet.

![Diagram över WebJob-projekt som länkar till webbprojekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Nödvändiga komponenter

Om du använder Visual Studio 2015, installera den [Azure SDK för .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Om du använder Visual Studio 2017, installera den [arbetsbelastningen Azure development](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

### <a id="convert"></a> Aktivera WebJobs distribution för ett befintligt projekt konsolprogram

Du kan välja mellan två alternativ:

* [Aktivera automatisk distribution med ett webbprojekt](#convertlink).

  Konfigurera en befintlig konsolprogramsprojekt så att den automatiskt distribueras som ett WebJob när du distribuerar ett webbprojekt. Använd det här alternativet när du vill köra ditt WebJob i samma webbapp som du kör relaterade webbprogrammet.

* [Aktivera distribution utan ett webbprojekt](#convertnolink).

  Konfigurera en befintlig konsolprogramsprojekt ska distribueras som ett WebJob, med ingen länk till ett webbprojekt. Använd det här alternativet när du vill köra ett Webbjobb i en webbapp, där inga webbprogram som körs i webbappen. Du kanske vill göra detta för att kunna skala ditt WebJob-resurser oberoende av resurser i ditt webbprogram.

#### <a id="convertlink"></a> Aktivera automatisk WebJobs-distribution med ett webbprojekt

1. Högerklicka på webbprojektet i **Solution Explorer**, och klicka sedan på **Lägg till** > **befintligt projekt som Azure WebJob**.
   
    ![Befintligt projekt som Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Den [lägga till Azure-Webbjobb](#configure) dialogrutan visas.
2. I den **projektnamn** listrutan, väljer konsolprogrammet projektet ska läggas till som ett WebJob.
   
    ![Att markera projekt i dialogrutan Lägg till Azure-Webbjobb](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Slutför den [lägga till Azure-Webbjobb](#configure) dialogrutan och klicka sedan på **OK**. 

#### <a id="convertnolink"></a> Aktivera WebJobs distribution utan ett webbprojekt
1. Högerklicka på konsolprogramprojektet i **Solution Explorer**, och klicka sedan på **Publicera som Azure WebJob...** . 
   
    ![Publicera som Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Den [lägga till Azure-Webbjobb](#configure) dialogruta, med projekt som valts i den **projektnamn** box.
2. Slutför den [lägga till Azure-Webbjobb](#configure) dialogrutan och klicka sedan på **OK**.
   
   Den **Publicera webbplats** guiden visas.  Om du inte vill publicera omedelbart kan du stänga guiden. De inställningar som du har angett sparas för när du vill [distribuera projektet](#deploy).

### <a id="create"></a>Skapa ett nytt projekt för WebJobs-aktiverad
Om du vill skapa ett nytt projekt för WebJobs-aktiverad, du kan använda projektmall konsolprogram och aktivera WebJobs distribution enligt beskrivningen i [ovan](#convert). Alternativt kan använda du mallen WebJobs nytt projekt:

* [Använda WebJobs nytt projekt mallen för ett oberoende WebJob](#createnolink)
  
    Skapa ett projekt och konfigurera den att distribuera fristående som ett Webbjobb med ingen länk till ett webbprojekt. Använd det här alternativet när du vill köra ett Webbjobb i en webbapp, där inga webbprogram som körs i webbappen. Du kanske vill göra detta för att kunna skala ditt WebJob-resurser oberoende av resurser i ditt webbprogram.
* [Använda WebJobs nytt projekt mallen för ett Webbjobb som är länkad till ett webbprojekt](#createlink)
  
    Skapa ett projekt som är konfigurerad för att distribuera automatiskt som ett WebJob när ett webbprojekt i samma lösning distribueras. Använd det här alternativet när du vill köra ditt WebJob i samma webbapp som du kör relaterade webbprogrammet.

> [!NOTE]
> Mallen WebJobs nytt projekt som automatiskt installerar NuGet-paket och innehåller koden i *Program.cs* för den [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Om du inte vill använda WebJobs-SDK, ta bort eller ändra den `host.RunAndBlock` instruktionen i *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Använda WebJobs nytt projekt mallen för ett oberoende WebJob
1. Klicka på **filen** > **nytt projekt**, och sedan i den **nytt projekt** dialogrutan klickar du på **molnet**  >   **Azure WebJob (.NET Framework)**.
   
    ![Dialogrutan Nytt projekt som visar WebJob-mall](./media/webjobs-dotnet-deploy-vs/np.png)
2. Följ anvisningarna som visas tidigare [gör konsolprogrammet projicera ett oberoende WebJobs-projekt](#convertnolink).

#### <a id="createlink"></a> Använda WebJobs nytt projekt mallen för ett Webbjobb som är länkad till ett webbprojekt
1. Högerklicka på webbprojektet i **Solution Explorer**, och klicka sedan på **Lägg till** > **nytt projekt i Azure-Webbjobb**.
   
    ![Nya Azure WebJob menyn projektposten](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Den [lägga till Azure-Webbjobb](#configure) dialogrutan visas.
2. Slutför den [lägga till Azure-Webbjobb](#configure) dialogrutan och klicka sedan på **OK**.

### <a id="configure"></a>Dialogrutan Lägg till Azure-Webbjobb
Den **lägga till Azure-Webbjobb** dialogrutan låter dig ange Webbjobbets namn och kör inställning för ditt WebJob. 

![Azure-Webbjobb dialogrutan Lägg till](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Fälten i den här dialogrutan motsvarar fälten på den **lägger till Webbjobb** dialogrutan i Azure Portal. Mer information finns i [kör bakgrundsaktiviteter med WebJobs](webjobs-create.md).

> [!NOTE]
> * Information om kommandoradsverktyget distribution finns i [aktiverar kommandoraden eller kontinuerlig leverans av Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Om du distribuerar ett WebJob och sedan bestämmer du vill ändra typen av WebJob och distribuera om, måste du ta bort den *webjobs publicera settings.json* fil. Det gör Visual Studio som visar publiceringsalternativ igen, så att du kan ändra typ av WebJob.
> * Om du distribuerar ett WebJob och senare ändrar körningsläge från kontinuerlig till icke-kontinuerliga eller tvärtom, skapar Visual Studio ett nytt WebJob i Azure när du distribuerar om. Om du ändrar andra inställningar för schemaläggning men lämna körningsläge samma eller växla mellan schemalagd och på begäran, Visual Studio befintliga jobbet i stället skapa ett nytt.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
När du konfigurerar ett konsolprogram för distribution av WebJobs, Visual Studio installeras den [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet paket- och lagrar schemaläggning av informationen i en *webjob publicera settings.json*  filen i projektet *egenskaper* mappen projektets WebJobs. Här är ett exempel på filen:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Du kan redigera den här filen direkt Visual Studio ger IntelliSense. Filschemat lagras på [ https://schemastore.org ](https://schemastore.org/schemas/json/webjob-publish-settings.json) och kan visa.  

### <a id="webjobslist"></a>webjobs-list.json
När du länkar ett WebJobs-aktiverade projekt till ett webbprojekt Visual Studio lagrar namnet på WebJobs-projekt i en *webjobs list.json* filen i webbprojektet *egenskaper* mapp. Listan kan innehålla flera WebJobs-projekt som du ser i följande exempel:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Du kan redigera den här filen direkt Visual Studio ger IntelliSense. Filschemat lagras på [ https://schemastore.org ](https://schemastore.org/schemas/json/webjobs-list.json) och kan visa.

### <a id="deploy"></a>Distribuera ett WebJobs-projekt
Ett WebJobs-projekt som du har länkat till ett webbprojekt distribueras automatiskt med webbprojektet. Läs om hur webbdistribution för projektet **anvisningar hjälper** > **distribuera app** i det vänstra navigeringsfönstret.

Om du vill distribuera ett WebJobs-projekt genom att själva, högerklicka på projektet i **Solution Explorer** och klicka på **Publicera som Azure WebJob...** . 

![Publicera som Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

För ett oberoende WebJob samma **Publicera webbplats** guiden som används för webbprojekt visas, men med färre tillgängliga inställningar att ändra.

## <a name="scheduling-a-triggered-webjob"></a>Schemalägga ett utlösta Webbjobb

WebJobs använder en *settings.job* fil att avgöra när ett Webbjobb körs. Använd den här filen om du vill ange en körschemat för ditt WebJob. I följande exempel körs varje timme från 9: 00 till 17: 00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Den här filen måste finnas i roten på mappen WebJobs, längs sidan ditt WebJob skript, till exempel `wwwroot\app_data\jobs\triggered\{job name}` eller `wwwroot\app_data\jobs\continuous\{job name}`. När du distribuerar ett WebJob från Visual Studio, markera din `settings.job` filegenskaper som **kopiera om nyare**. 

När du [skapa ett WebJob i Azure Portal](webjobs-create.md), filen settings.job skapas åt dig.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-uttryck

WebJobs använder samma CRON-uttryck för schemaläggning som timerutlösare i Azure Functions. Läs mer om CRON-stödet i den [timer utlösaren referensartikeln](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>Setting.job referens

Följande inställningar stöds av WebJobs:

| **Inställning** | **Typ**  | **Beskrivning** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alla | Tillåter att jobbet ska köras på plats utan att först kopieras till en tillfällig mapp. Mer information finns i [WebJobs arbetskatalog](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Kontinuerlig igenkänning | Endast köra WebJobs på en enda instans vid utskalning. Mer information finns i [ställa in en kontinuerlig jobb som singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Utlöst | Köra Webbjobbet på ett CRON-baserade schema. Mer information finns i den [timer utlösaren referensartikeln](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Alla | Kan du styra beteendet avstängning. Mer information finns i [avslutning](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om WebJobs-SDK](webjobs-sdk-how-to.md)
