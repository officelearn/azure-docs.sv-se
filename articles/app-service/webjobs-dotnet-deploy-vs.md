---
title: Utveckla och distribuera WebJobs med VS
description: Lär dig hur du utvecklar Azure WebJobs i Visual Studio och distribuerar dem till Azure App Service, inklusive hur du skapar en schemalagd aktivitet.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 971ab39ddf4b383f92f0cd81e5258ad357e76e99
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857075"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Utveckla och distribuera webbjobb med hjälp av Visual Studio – Azure App Service

Den här artikeln förklarar hur du använder Visual Studio för att distribuera ett konsol programs projekt till en webbapp i [App Service](overview.md) som ett [Azure-webbjobb](https://go.microsoft.com/fwlink/?LinkId=390226). Information om hur du distribuerar WebJobs med hjälp av [Azure Portal](https://portal.azure.com)finns i [köra bakgrunds aktiviteter med WebJobs](webjobs-create.md).

Du kan publicera flera WebJobs i en enda webbapp. Se till att varje webb jobb i en webbapp har ett unikt namn.

Med version 3. x av [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) kan du utveckla WebJobs som körs som antingen .net Core-appar eller .NET Framework appar, medan version 2. x bara stöder .NET Framework. Hur du distribuerar ett WebJobs-projekt skiljer sig åt för .NET Core-projekt jämfört med .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs som .NET Core Console-appar

När du använder version 3. x av webbjobben kan du skapa och publicera webbjobb som .NET Core Console-appar. Stegvisa instruktioner för att skapa och publicera ett .NET Core-konsolprogram till Azure som ett webb jobb finns i [komma igång med Azure WEBJOBS SDK för händelse driven bakgrunds bearbetning](webjobs-sdk-get-started.md).

> [!NOTE]
> Det går inte att länka .NET Core-webbjobb till webb projekt. Om du behöver distribuera ditt webbjobb med en webbapp, bör du [skapa ditt webbjobb som en .NET Framework-konsol program](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuera till Azure App Service

Publicering av ett .NET Core-webbjobb till App Service från Visual Studio använder samma verktyg som publicering av en ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Webb jobb typer

Som standard körs ett webbjobb som publicerats från ett .NET Core-konsol projekt bara när det utlöses eller på begäran. Du kan också uppdatera projektet så att det [körs enligt ett schema](#scheduled-execution) eller köras kontinuerligt.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Schemalagd körning

När du publicerar ett .NET Core-konsolprogram till Azure läggs en ny *inställning. job* -fil till i projektet. Använd den här filen för att ange ett körnings schema för ditt webb jobb. Mer information finns i [Schemalägga ett utlöst webb jobb](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Kontinuerlig körning

Du kan använda Visual Studio för att ändra att webb jobbet körs kontinuerligt när Always On är aktiverat i Azure.

1. [Publicera projektet till Azure](#deploy-to-azure-app-service)om du inte redan gjort det.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. På fliken **publicera** väljer du **Inställningar**. 

1. I dialog rutan **profil inställningar** väljer du **kontinuerlig** för **webb jobb typ**och väljer **Spara**.

    ![Dialog rutan publicera inställningar för ett webb jobb](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Välj **publicera** för att publicera webbjobbet igen med de uppdaterade inställningarna.

## <a name="webjobs-as-net-framework-console-apps"></a>Webbjobb som .NET Framework-konsol program  

När Visual Studio distribuerar ett WebJobs-aktiverat .NET Framework-konsol program projekt kopieras runtime-filer till lämplig mapp i webbappen (*App_Data/Jobs/Continuous* för kontinuerliga WebJobs och *App_Data/Jobs/triggered* för de schemalagda webbjobben eller på begäran.

Ett WebJobs-aktiverat projekt har följande objekt tillagda:

* [Microsoft. Web. WebJobs. Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paketet.
* En [webjob-publish-settings.jspå](#publishsettings) en fil som innehåller distributions-och Scheduler-inställningar. 

![Diagram som visar vad som läggs till i en konsol app för att aktivera distribution som ett webb jobb](./media/webjobs-dotnet-deploy-vs/convert.png)

Du kan lägga till dessa objekt i ett befintligt konsol programs projekt eller använda en mall för att skapa ett nytt webbjobb-aktiverat konsol program projekt. 

Du kan distribuera ett projekt som ett webb jobb separat eller länka det till ett webb projekt så att det automatiskt distribueras när du distribuerar webb projektet. För att länka projekt innehåller Visual Studio namnet på det WebJobs-aktiverade projektet i en [webjobs-list.jspå](#webjobslist) filen i webbprojektet.

![Diagram över projekt för webb jobb som länkar till webb projekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Krav

Om du använder Visual Studio 2015 installerar du [Azure SDK för .net (Visual studio 2015)](https://azure.microsoft.com/downloads/).

Om du använder Visual Studio 2017 installerar du [arbets belastningen Azure Development](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Aktivera distribution av WebJobs för ett befintligt konsol program projekt

Du kan välja mellan två alternativ:

* [Aktivera automatisk distribution med ett webb projekt](#convertlink).

  Konfigurera ett befintligt konsol program projekt så att det automatiskt distribueras som ett webb jobb när du distribuerar ett webb projekt. Använd det här alternativet om du vill köra ditt webb jobb i samma webbapp som du kör det relaterade webb programmet i.

* [Aktivera distribution utan ett webb projekt](#convertnolink).

  Konfigurera ett befintligt konsol programs projekt som ska distribueras som ett webb jobb utan någon länk till ett webb projekt. Använd det här alternativet om du vill köra ett webbjobb i en webbapp med sig själv, utan att något webb program körs i webbappen. Du kanske vill göra detta för att kunna skala dina webb jobbs resurser oberoende av dina webb programs resurser.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Aktivera automatisk distribution av WebJobs med ett webb projekt

1. Högerklicka på webb projektet i **Solution Explorer**och klicka sedan på **Lägg till**  >  **befintligt projekt som Azure-webbjobb**.
   
    ![Befintligt projekt som Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Dialog rutan [Lägg till Azure-webbjobb](#configure) visas.
2. I list rutan **projekt namn** väljer du det konsol program projekt som ska läggas till som ett webb jobb.
   
    ![Välja projekt i dialog rutan Lägg till Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Slutför dialog rutan [Lägg till Azure-webbjobb](#configure) och klicka sedan på **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Aktivera distribution av WebJobs utan ett webb projekt
1. Högerklicka på projektet konsol program i **Solution Explorer**och klicka sedan på **Publicera som Azure-webbjobb..**.. 
   
    ![Publicera som Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Dialog rutan [Lägg till Azure-webbjobb](#configure) visas med projektet markerat i rutan **projekt namn** .
2. Slutför dialog rutan [Lägg till Azure-webbjobb](#configure) och klicka sedan på **OK**.
   
   Guiden **Publicera webbplats** visas.  Om du inte vill publicera omedelbart stänger du guiden. De inställningar du har angett sparas för när du vill [distribuera projektet](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Skapa ett nytt WebJobs-aktiverat projekt
Om du vill skapa ett nytt WebJobs-aktiverat projekt kan du använda projekt mal len konsol program och aktivera distribution av WebJobs enligt beskrivningen i [föregående avsnitt](#convert). Som ett alternativ kan du använda mallen WebJobs New-Project:

* [Använd mallen WebJobs New-Project för ett oberoende webbjobb](#createnolink)
  
    Skapa ett projekt och konfigurera det för att distribuera det som ett webb jobb, utan länk till ett webb projekt. Använd det här alternativet om du vill köra ett webbjobb i en webbapp med sig själv, utan att något webb program körs i webbappen. Du kanske vill göra detta för att kunna skala dina webb jobbs resurser oberoende av dina webb programs resurser.
* [Använd mallen WebJobs New-Project för ett webb jobb som är länkat till ett webb projekt](#createlink)
  
    Skapa ett projekt som är konfigurerat för att distribuera automatiskt som ett webb jobb när ett webb projekt i samma lösning distribueras. Använd det här alternativet om du vill köra ditt webb jobb i samma webbapp som du kör det relaterade webb programmet i.

> [!NOTE]
> Med mallen WebJobs New-Project installeras automatiskt NuGet-paket och innehåller kod i *program.cs* för [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Om du inte vill använda WebJobs SDK tar du bort eller ändrar `host.RunAndBlock` instruktionen i *program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Använd mallen WebJobs New-Project för ett oberoende webbjobb
1. Klicka på **filen**  >  **nytt projekt**och klicka sedan på **Cloud** **New Project**  >  **Azure-webbjobb (.NET Framework)** i dialog rutan nytt projekt.
   
    ![Dialog rutan nytt projekt med webbjobb-mall](./media/webjobs-dotnet-deploy-vs/np.png)
2. Följ anvisningarna som visas ovan för att [göra konsol programmet till projekt ett oberoende projekt för WebJobs](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Använd mallen WebJobs New-Project för ett webb jobb som är länkat till ett webb projekt
1. Högerklicka på webb projektet i **Solution Explorer**och klicka sedan på **Lägg till**  >  **nytt Azure-webbjobb-projekt**.
   
    ![Nytt projekt meny alternativ för Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Dialog rutan [Lägg till Azure-webbjobb](#configure) visas.
2. Slutför dialog rutan [Lägg till Azure-webbjobb](#configure) och klicka sedan på **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Dialog rutan Lägg till Azure-webbjobb
I dialog rutan **Lägg till Azure-webbjobb** kan du ange webb jobbets namn och läge för körnings läge för ditt webb jobb. 

![Dialog rutan Lägg till Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Fälten i den här dialog rutan motsvarar fält i dialog rutan **Lägg till webbjobb** i Azure Portal. Mer information finns i [köra bakgrunds aktiviteter med WebJobs](webjobs-create.md).

> [!NOTE]
> * Information om distribution av kommando raden finns i [Aktivera kommando rads-eller kontinuerlig leverans av Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Om du distribuerar ett webb jobb och sedan bestämmer dig för att ändra typ av webb jobb och omdistribuera måste du ta bort *webjobs-publish-settings.jspå* filen. Detta gör att Visual Studio visar publicerings alternativen igen, så att du kan ändra typ av webb jobb.
> * Om du distribuerar ett webb jobb och senare ändrar körnings läget från kontinuerlig till icke-kontinuerlig eller vice versa, skapar Visual Studio ett nytt webbjobb i Azure när du distribuerar om. Om du ändrar andra schema inställningar men låter körnings läget vara samma eller växla mellan schemalagda och på begäran, uppdaterar Visual Studio det befintliga jobbet i stället för att skapa ett nytt.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.jspå
När du konfigurerar ett konsol program för distribution av WebJobs, installerar Visual Studio [Microsoft. Web. WebJobs. publicera](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paket och lagrar schemaläggnings information i en *webjob-publish-settings.jspå* filen i mappen Project *Properties* i projektet WebJobs. Här är ett exempel på filen:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Du kan redigera den här filen direkt och Visual Studio innehåller IntelliSense. Fil schemat lagras på [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) och kan visas där.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.jspå
När du länkar ett WebJobs-aktiverat projekt till ett webb projekt, lagrar Visual Studio namnet på WebJobs-projektet i en *webjobs-list.jspå* filen i webbprojektets *egenskaps* -mapp. Listan kan innehålla flera jobb projekt, som du ser i följande exempel:

```json
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
```

Du kan redigera den här filen direkt och Visual Studio innehåller IntelliSense. Fil schemat lagras på [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) och kan visas där.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Distribuera ett WebJobs-projekt
Ett WebJobs-projekt som du har länkat till ett webb projekt distribueras automatiskt med webbprojektet. Information om distribution av webb projekt finns i **instruktions guider**  >  **Distribuera App** i det vänstra navigerings fältet.

Om du vill distribuera ett WebJobs-projekt själva högerklickar du på projektet i **Solution Explorer** och klickar på **Publicera som Azure-webbjobb...**. 

![Publicera som Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/paw.png)

Samma **publicerings webb** guide som används för webb projekt visas för ett oberoende webb jobb, men med färre inställningar som kan ändras.

## <a name="scheduling-a-triggered-webjob"></a>Schemalägga ett utlöst webbjobb

WebJobs använder en *Settings. job* -fil för att avgöra när ett webb jobb körs. Använd den här filen för att ange ett körnings schema för ditt webb jobb. Följande exempel körs varje timme från och med 9 till 5 PM:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Den här filen måste finnas i roten i mappen WebJobs, tillsammans med webbjobbets skript, till exempel `wwwroot\app_data\jobs\triggered\{job name}` eller `wwwroot\app_data\jobs\continuous\{job name}` . När du distribuerar ett webb jobb från Visual Studio markerar du `settings.job` fil egenskaperna som **Kopiera om nyare**. 

När du [skapar ett webb jobb från Azure Portal](webjobs-create.md)skapas inställningar. job-filen åt dig.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-uttryck

WebJobs använder samma CRON-uttryck för schemaläggning som timer-utlösaren i Azure Functions. Mer information om CRON-support finns i [referens artikeln om timer-utlösare](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>inställningar. jobb referens

Följande inställningar stöds av WebJobs:

| **Inställning** | **Typ**  | **Beskrivning** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alla | Tillåter att jobbet körs på plats utan att först kopieras till en Temp-mapp. Mer information finns i [WebJobs arbets katalog](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Kontinuerlig igenkänning | Kör bara webbjobben på en enskild instans vid utskalning. Läs mer i [Ange ett kontinuerligt jobb som singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Utlöses | Kör webb jobbet på ett CRON-baserat schema. Mer information finns i [referens artikeln om timer-utlösare](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Alla | Tillåter kontroll av avstängnings beteendet. Läs mer i en [korrekt avstängning](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om WebJobs SDK](webjobs-sdk-how-to.md)
