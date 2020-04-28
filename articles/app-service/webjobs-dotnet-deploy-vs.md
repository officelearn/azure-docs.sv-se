---
title: Utveckla och distribuera WebJobs med VS
description: Lär dig hur du utvecklar Azure WebJobs i Visual Studio och distribuerar dem till Azure App Service, inklusive att skapa en schemalagd aktivitet.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d73c858bdbfee7c5b7c7e31b62b1f601b7b6838a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866444"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Utveckla och distribuera webbjobb med hjälp av Visual Studio – Azure App Service

I den här artikeln beskrivs hur du använder Visual Studio för att distribuera ett konsolprogram till en webbapp i [App Service](overview.md) som ett [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Information om hur du distribuerar WebJobs med hjälp av [Azure-portalen](https://portal.azure.com)finns i [Köra bakgrundsuppgifter med WebJobs](webjobs-create.md).

Du kan publicera flera WebJobs i en enda webbapp. Kontrollera att varje WebJob i en webbapp har ett unikt namn.

Med version 3.x av [Azure WebJobs SDK](webjobs-sdk-how-to.md) kan du utveckla WebJobs som körs som antingen .NET Core-appar eller .NET Framework-appar, medan version 2.x endast stöder .NET Framework. Sättet som du distribuerar ett WebJobs-projekt på skiljer sig åt för .NET Core-projekt jämfört med .NET Framework-projekt.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs som .NET Core-konsolappar

När du använder version 3.x av WebJobs kan du skapa och publicera WebJobs som .NET Core-konsolappar. Stegvisa instruktioner för att skapa och publicera ett .NET Core-konsolprogram till Azure som ett WebJob finns [i Komma igång med Azure WebJobs SDK för händelsedriven bakgrundsbearbetning](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET Core WebJobs kan inte länkas till webbprojekt. Om du behöver distribuera WebJob med en webbapp bör du [skapa WebJob som en .NET Framework-konsolapp](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuera till Azure App Service

Genom att publicera en .NET Core WebJob to App Service från Visual Studio används samma verktyg som att publicera en ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Webbjob-typer

Som standard körs ett WebJob som publicerats från ett .NET Core-konsolprojekt endast när det utlöses eller på begäran. Du kan också uppdatera projektet så att det [körs enligt ett schema](#scheduled-execution) eller körs kontinuerligt.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Schemalagd körning

När du publicerar ett .NET Core-konsolprogram på Azure läggs en ny *settings.job-fil* till i projektet. Använd den här filen om du vill ange ett körningsschema för WebJob. Mer information finns i [Schemalägga ett utlöst WebJob](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Kontinuerlig körning

Du kan använda Visual Studio för att ändra WebJob så att den körs kontinuerligt när Always On är aktiverat i Azure.

1. Om du inte redan har gjort det [publicerar du projektet i Azure](#deploy-to-azure-app-service).

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. Välj **Inställningar**på fliken **Publicera** . 

1. Välj **Kontinuerlig** för **WebJob-typ**i dialogrutan **Profilinställningar** och välj **Spara**.

    ![Dialogrutan Publiceringsinställningar för ett WebJob-jobb](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Välj **Publicera om** du vill publicera webjob igen med de uppdaterade inställningarna.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs som .NET Framework-konsolappar  

När Visual Studio distribuerar ett WebJobs-aktiverat .NET Framework Console Application-projekt kopieras körningsfiler till rätt mapp i webbappen *(App_Data/jobb/kontinuerligt* för kontinuerliga WebJobs och *App_Data/jobb/utlöses* för schemalagda webbjobs eller webbjobs på begäran).

Ett WebJobs-aktiverat projekt har lagt till följande objekt:

* [Packaget Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet.
* En [webjob-publish-settings.json-fil](#publishsettings) som innehåller inställningar för distribution och schemaläggare. 

![Diagram som visar vad som läggs till i en konsolapp för att aktivera distribution som ett WebJob-program](./media/webjobs-dotnet-deploy-vs/convert.png)

Du kan lägga till dessa objekt i ett befintligt konsolprogramprojekt eller använda en mall för att skapa ett nytt WebJobs-aktiverat konsolprogram. 

Du kan distribuera ett projekt som ett WebJob av sig självt eller länka det till ett webbprojekt så att det distribueras automatiskt när du distribuerar webbprojektet. Om du vill länka projekt innehåller Visual Studio namnet på det WebJobs-aktiverade projektet i en [webjobs-list.json-fil](#webjobslist) i webbprojektet.

![Diagram som visar WebJob-projekt som länkar till webbprojekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Krav

Om du använder Visual Studio 2015 installerar du [Azure SDK för .NET (Visual Studio 2015).](https://azure.microsoft.com/downloads/)

Om du använder Visual Studio 2017 installerar du [Azure-utvecklingsarbetsbelastningen](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Aktivera WebJobs-distribution för ett befintligt konsolprogramprojekt

Du kan välja mellan två alternativ:

* [Aktivera automatisk distribution med ett webbprojekt](#convertlink).

  Konfigurera ett befintligt konsolprogram så att det distribueras automatiskt som ett WebJob-nätverk när du distribuerar ett webbprojekt. Använd det här alternativet när du vill köra WebJob i samma webbapp där du kör det relaterade webbprogrammet.

* [Aktivera distribution utan webbprojekt](#convertnolink).

  Konfigurera ett befintligt konsolprogramprojekt som ska distribueras som ett WebJob av sig självt, utan någon länk till ett webbprojekt. Använd det här alternativet när du vill köra ett WebJob-jobb i en webbapp av sig själv, utan att webbprogrammet körs i webbappen. Du kanske vill göra detta för att kunna skala dina WebJob-resurser oberoende av webbprogramresurserna.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Aktivera automatisk WebJobs-distribution med ett webbprojekt

1. Högerklicka på webbprojektet i **Solution Explorer**och klicka sedan på **Lägg till** > **befintligt projekt som Azure WebJob**.
   
    ![Befintligt projekt som Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Dialogrutan [Lägg till Azure WebJob](#configure) visas.
2. I listrutan **Projektnamn** väljer du det konsolprogram som du vill lägga till som ett WebJob-projekt.
   
    ![Välja projekt i dialogrutan Lägg till Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Fyll i dialogrutan [Lägg till Azure WebJob](#configure) och klicka sedan på **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Aktivera WebJobs-distribution utan webbprojekt
1. Högerklicka på konsolprogrammet i **Solution Explorer**och klicka sedan på Publicera som Azure **WebJob...**. 
   
    ![Publicera som Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Dialogrutan [Lägg till Azure WebJob](#configure) visas, med projektet markerat i rutan **Projektnamn.**
2. Fyll i dialogrutan [Lägg till Azure WebJob](#configure) och klicka sedan på **OK**.
   
   Guiden **Publicera webb** visas.  Om du inte vill publicera direkt stänger du guiden. De inställningar som du har angett sparas när du vill [distribuera projektet](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Skapa ett nytt WebJobs-aktiverat projekt
Om du vill skapa ett nytt WebJobs-aktiverat projekt kan du använda projektmallen Konsolprogram och aktivera WebJobs-distribution enligt beskrivningen i [föregående avsnitt](#convert). Som ett alternativ kan du använda WebJobs nya projektmall:

* [Använda webjobs-mallen för nya projekt för ett oberoende WebJob](#createnolink)
  
    Skapa ett projekt och konfigurera det så att det distribueras av sig själv som ett WebJob, utan någon länk till ett webbprojekt. Använd det här alternativet när du vill köra ett WebJob-jobb i en webbapp av sig själv, utan att webbprogrammet körs i webbappen. Du kanske vill göra detta för att kunna skala dina WebJob-resurser oberoende av webbprogramresurserna.
* [Använda WebJobs nya projektmall för ett WebJob-företag som är länkat till ett webbprojekt](#createlink)
  
    Skapa ett projekt som är konfigurerat för att distribueras automatiskt som ett WebJob när ett webbprojekt i samma lösning distribueras. Använd det här alternativet när du vill köra WebJob i samma webbapp där du kör det relaterade webbprogrammet.

> [!NOTE]
> WebJobs nya projektmall installerar automatiskt NuGet-paket och innehåller kod i *Program.cs* för [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Om du inte vill använda WebJobs SDK tar `host.RunAndBlock` du bort eller ändrar uttrycket i *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Använda webjobs-mallen för nya projekt för ett oberoende WebJob
1. Klicka på **Arkiv** > **nytt projekt**och klicka sedan på **Cloud** > Azure**WebJob (.NET Framework)** i dialogrutan Nytt **projekt** .
   
    ![Dialogrutan Nytt projekt med Mallen WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Följ anvisningarna som visas tidigare för att [göra konsolprogrammet till ett oberoende WebJobs-projekt](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Använda WebJobs nya projektmall för ett WebJob-företag som är länkat till ett webbprojekt
1. Högerklicka på webbprojektet i **Solution Explorer**och klicka sedan på **Lägg till** > **nytt Azure WebJob-projekt**.
   
    ![Ny menypost för Azure WebJob Project](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Dialogrutan [Lägg till Azure WebJob](#configure) visas.
2. Fyll i dialogrutan [Lägg till Azure WebJob](#configure) och klicka sedan på **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Dialogrutan Lägg till Azure WebJob
I dialogrutan **Lägg till Azure WebJob** kan du ange webjob-namn- och körlägesinställningen för ditt WebJob.The Add Azure WebJob dialog lets you enter the WebJob name and run mode setting for your WebJob. 

![Dialogrutan Lägg till Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Fälten i den här dialogrutan motsvarar fält i dialogrutan **Lägg till Webbjob** i Azure-portalen. Mer information finns i [Köra bakgrundsuppgifter med WebJobs](webjobs-create.md).

> [!NOTE]
> * Information om kommandoradsdistribution finns i [Aktivera kommandorad eller kontinuerlig leverans av Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Om du distribuerar ett WebJob-jobb och sedan bestämmer dig för att ändra typen av WebJob och distribuera om, måste du ta bort *filen webjobs-publish-settings.json.* Detta gör att Visual Studio visar publiceringsalternativen igen, så att du kan ändra typen av WebJob.
> * Om du distribuerar ett WebJob-läge och senare ändrar körningsläget från kontinuerligt till icke-kontinuerligt eller vice versa, skapar Visual Studio ett nytt WebJob i Azure när du distribuerar om. Om du ändrar andra schemaläggningsinställningar men lämnar körläget på samma sätt eller växlar mellan Schemalagd och På begäran, uppdaterar Visual Studio det befintliga jobbet i stället för att skapa ett nytt.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
När du konfigurerar ett konsolprogram för WebJobs-distribution installerar Visual Studio paketet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet och lagrar schemaläggningsinformation i en *webjob-publish-settings.json-fil* i mappen Egenskaper för projektet *Egenskaper* i WebJobs-projektet. Här är ett exempel på den filen:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Du kan redigera den här filen direkt och Visual Studio tillhandahåller IntelliSense. Filschemat lagras [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) på och kan visas där.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
När du länkar ett WebJobs-aktiverat projekt till ett webbprojekt lagrar Visual Studio namnet på WebJobs-projektet i en *webjobs-list.json-fil* i webbprojektets *egenskapsmapp.* Listan kan innehålla flera WebJobs-projekt, vilket visas i följande exempel:

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

Du kan redigera den här filen direkt och Visual Studio tillhandahåller IntelliSense. Filschemat lagras [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) på och kan visas där.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Distribuera ett WebJobs-projekt
Ett WebJobs-projekt som du har länkat till ett webbprojekt distribueras automatiskt med webbprojektet. Information om distribution av webbprojekt finns i **Hur du använder** > **appen Distribuera** i den vänstra navigeringen.

Om du vill distribuera ett WebJobs-projekt av sig själv högerklickar du på projektet i **Solution Explorer** och klickar på Publicera som **Azure WebJob...**. 

![Publicera som Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

För ett oberoende WebJob visas samma **Publiceringswebbguide** som används för webbprojekt, men med färre inställningar tillgängliga att ändra.

## <a name="scheduling-a-triggered-webjob"></a>Schemalägga ett utlöst webbjobb

WebJobs använder en *settings.job-fil* för att avgöra när ett WebJob körs. Använd den här filen om du vill ange ett körningsschema för WebJob. Följande exempel körs varje timme från 09.00 till 17.00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Den här filen måste finnas i roten på mappen WebJobs, längs sidan `wwwroot\app_data\jobs\triggered\{job name}` `wwwroot\app_data\jobs\continuous\{job name}`av WebJobs skript, till exempel eller . När du distribuerar ett WebJob `settings.job` från Visual Studio markerar du filegenskaperna som **Kopia om de är nyare**. 

När du [skapar ett WebJob från Azure-portalen](webjobs-create.md)skapas filen settings.job åt dig.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-uttryck

WebJobs använder samma CRON-uttryck för schemaläggning som timerutlösaren i Azure Functions. Mer information om CRON-stöd finns i [referensartikeln för timerutlösare](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>inställningar.jobbreferens

Följande inställningar stöds av WebJobs:

| **Inställning** | **Typ**  | **Beskrivning** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alla | Gör att jobbet kan köras på plats utan att först kopieras till en temp-mapp. Mer information finns i [WebJobs arbetskatalog](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Kontinuerlig igenkänning | Kör bara WebJobs på en enda instans när skalas ut. Mer information finns i [Ange ett kontinuerligt jobb som singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Utlöste | Kör WebJob på ett CRON-baserat schema. Mer information finns i [referensartikeln för timerutlösaren](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Alla | Tillåter kontroll över avstängningsbeteendet. Mer information finns i [Graciös avstängning](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om WebJobs SDK](webjobs-sdk-how-to.md)
