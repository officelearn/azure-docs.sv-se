---
title: Utveckla och distribuera WebJobs med hjälp av Visual Studio – Azure
description: Lär dig hur du utvecklar och distribuerar du Azure WebJobs till Azure App Service med Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d67a564f4f3e0af3cf02b280036374d892f830e5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731505"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Utveckla och distribuera WebJobs med hjälp av Visual Studio – Azure App Service

## <a name="overview"></a>Översikt

Det här avsnittet beskriver hur du använder Visual Studio för att distribuera ett konsolprogramsprojekt till en webbapp i [Apptjänst](overview.md) som en [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Information om hur du distribuerar WebJobs genom att använda den [Azure-portalen](https://portal.azure.com), se [kör bakgrundsaktiviteter med WebJobs](webjobs-create.md).

När Visual Studio distribuerar ett WebJobs-aktiverade konsolprogramsprojekt, utför två uppgifter:

* Kopierar körningsfiler till rätt mapp i webbapp (*App_Data/jobb/continuous* för kontinuerliga WebJobs *App_Data/jobb/triggered* för schemalagda och på begäran WebJobs).
* Ställer in [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) jobb för WebJobs som är schemalagda att köras vid specifika tidpunkter. (Detta krävs inte för kontinuerliga WebJobs.)

Ett WebJobs-aktiverade projekt har följande funktioner har lagts till:

* Den [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paketet.
* En [webjob publicera settings.json](#publishsettings) -fil som innehåller inställningar för distribution och scheduler. 

![Diagram som visar vad som har lagts till i en Konsolapp för att aktivera distribution som ett WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Du kan lägga till dessa objekt i en befintlig konsolprogramsprojekt eller använda en mall för att skapa ett nytt projekt för WebJobs-aktiverade konsolprogram. 

Du kan distribuera ett projekt som ett WebJob ensamt eller länka det till ett webbprojekt så att den distribuerar automatiskt när du distribuerar webbprojektet. Om du vill länka projekt, Visual Studio innehåller namnet på WebJobs-aktiverade projektet i en [webjobs list.json](#webjobslist) filen i webbprojektet.

![Diagram över WebJob-projekt som länkar till webbprojekt](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Förutsättningar

Om du använder Visual Studio 2015, installera den [Azure SDK för .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Om du använder Visual Studio 2017, installera den [arbetsbelastningen Azure development](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> Aktivera WebJobs distribution för ett befintligt projekt konsolprogram

Du kan välja mellan två alternativ:

* [Aktivera automatisk distribution med ett webbprojekt](#convertlink).

  Konfigurera en befintlig konsolprogramsprojekt så att den automatiskt distribueras som ett WebJob när du distribuerar ett webbprojekt. Använd det här alternativet när du vill köra ditt WebJob i samma webbapp som du kör relaterade webbprogrammet.

* [Aktivera distribution utan ett webbprojekt](#convertnolink).

  Konfigurera en befintlig konsolprogramsprojekt ska distribueras som ett WebJob, med ingen länk till ett webbprojekt. Använd det här alternativet när du vill köra ett Webbjobb i en webbapp, där inga webbprogram som körs i webbappen. Du kanske vill göra detta för att kunna skala ditt WebJob-resurser oberoende av resurser i ditt webbprogram.

### <a id="convertlink"></a> Aktivera automatisk WebJobs-distribution med ett webbprojekt

1. Högerklicka på webbprojektet i **Solution Explorer**, och klicka sedan på **Lägg till** > **befintligt projekt som Azure WebJob**.
   
    ![Befintligt projekt som Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    Den [lägga till Azure-Webbjobb](#configure) dialogrutan visas.
2. I den **projektnamn** listrutan, väljer konsolprogrammet projektet ska läggas till som ett WebJob.
   
    ![Att markera projekt i dialogrutan Lägg till Azure-Webbjobb](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Slutför den [lägga till Azure-Webbjobb](#configure) dialogrutan och klicka sedan på **OK**. 

### <a id="convertnolink"></a> Aktivera WebJobs distribution utan ett webbprojekt
1. Högerklicka på konsolprogramprojektet i **Solution Explorer**, och klicka sedan på **Publicera som Azure WebJob...** . 
   
    ![Publicera som Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    Den [lägga till Azure-Webbjobb](#configure) dialogruta, med projekt som valts i den **projektnamn** box.
2. Slutför den [lägga till Azure-Webbjobb](#configure) dialogrutan och klicka sedan på **OK**.
   
   Den **Publicera webbplats** guiden visas.  Om du inte vill publicera omedelbart kan du stänga guiden. De inställningar som du har angett sparas för när du vill [distribuera projektet](#deploy).

## <a id="create"></a>Skapa ett nytt projekt för WebJobs-aktiverad
Om du vill skapa ett nytt projekt för WebJobs-aktiverad, du kan använda projektmall konsolprogram och aktivera WebJobs distribution enligt beskrivningen i [ovan](#convert). Alternativt kan använda du mallen WebJobs nytt projekt:

* [Använda WebJobs nytt projekt mallen för ett oberoende WebJob](#createnolink)
  
    Skapa ett projekt och konfigurera den att distribuera fristående som ett Webbjobb med ingen länk till ett webbprojekt. Använd det här alternativet när du vill köra ett Webbjobb i en webbapp, där inga webbprogram som körs i webbappen. Du kanske vill göra detta för att kunna skala ditt WebJob-resurser oberoende av resurser i ditt webbprogram.
* [Använda WebJobs nytt projekt mallen för ett Webbjobb som är länkad till ett webbprojekt](#createlink)
  
    Skapa ett projekt som är konfigurerad för att distribuera automatiskt som ett WebJob när ett webbprojekt i samma lösning distribueras. Använd det här alternativet när du vill köra ditt WebJob i samma webbapp som du kör relaterade webbprogrammet.

> [!NOTE]
> Mallen WebJobs nytt projekt som automatiskt installerar NuGet-paket och innehåller koden i *Program.cs* för den [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Om du inte vill använda WebJobs-SDK, ta bort eller ändra den `host.RunAndBlock` instruktionen i *Program.cs*.
> 
> 

### <a id="createnolink"></a> Använda WebJobs nytt projekt mallen för ett oberoende WebJob
1. Klicka på **filen** > **nytt projekt**, och sedan i den **nytt projekt** dialogrutan klickar du på **molnet**  >   **Azure WebJob (.NET Framework)**.
   
    ![Dialogrutan Nytt projekt som visar WebJob-mall](./media/websites-dotnet-deploy-webjobs/np.png)
2. Följ anvisningarna som visas tidigare [gör konsolprogrammet projicera ett oberoende WebJobs-projekt](#convertnolink).

### <a id="createlink"></a> Använda WebJobs nytt projekt mallen för ett Webbjobb som är länkad till ett webbprojekt
1. Högerklicka på webbprojektet i **Solution Explorer**, och klicka sedan på **Lägg till** > **nytt projekt i Azure-Webbjobb**.
   
    ![Nya Azure WebJob menyn projektposten](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    Den [lägga till Azure-Webbjobb](#configure) dialogrutan visas.
2. Slutför den [lägga till Azure-Webbjobb](#configure) dialogrutan och klicka sedan på **OK**.

## <a id="configure"></a>Dialogrutan Lägg till Azure-Webbjobb
Den **lägga till Azure-Webbjobb** dialogrutan låter dig ange Webbjobbets namn och kör inställning för ditt WebJob. 

![Azure-Webbjobb dialogrutan Lägg till](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Fälten i den här dialogrutan motsvarar fälten på den **lägger till Webbjobb** dialogrutan i Azure Portal. Mer information finns i [kör bakgrundsaktiviteter med WebJobs](webjobs-create.md).

> [!NOTE]
> * Information om kommandoradsverktyget distribution finns i [aktiverar kommandoraden eller kontinuerlig leverans av Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Om du distribuerar ett WebJob och sedan bestämmer du vill ändra typen av WebJob och distribuera om, måste du ta bort den *webjobs publicera settings.json* fil. Det gör Visual Studio som visar publiceringsalternativ igen, så att du kan ändra typ av WebJob.
> * Om du distribuerar ett WebJob och senare ändrar körningsläge från kontinuerlig till icke-kontinuerliga eller tvärtom, skapar Visual Studio ett nytt WebJob i Azure när du distribuerar om. Om du ändrar andra inställningar för schemaläggning men lämna körningsläge samma eller växla mellan schemalagd och på begäran, Visual Studio befintliga jobbet i stället skapa ett nytt.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
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

## <a id="webjobslist"></a>webjobs-list.json
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

## <a id="deploy"></a>Distribuera ett WebJobs-projekt
Ett WebJobs-projekt som du har länkat till ett webbprojekt distribueras automatiskt med webbprojektet. Läs om hur webbdistribution för projektet **anvisningar hjälper** > **distribuera app** i det vänstra navigeringsfönstret.

Om du vill distribuera ett WebJobs-projekt genom att själva, högerklicka på projektet i **Solution Explorer** och klicka på **Publicera som Azure WebJob...** . 

![Publicera som Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)

För ett oberoende WebJob samma **Publicera webbplats** guiden som används för webbprojekt visas, men med färre tillgängliga inställningar att ändra.
