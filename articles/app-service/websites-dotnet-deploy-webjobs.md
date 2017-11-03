---
title: Utveckla och distribuera med Visual Studio - Azure WebJobs
description: "Lär dig hur du utvecklar och distribuerar Azure WebJobs till Azure App Service med Visual Studio."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga
ms.openlocfilehash: 59a453aab82d2f78bcc04774e26c8a557b8dbb26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Utveckla och distribuera WebJobs med Visual Studio - Azure App Service

## <a name="overview"></a>Översikt

Det här avsnittet beskriver hur du använder Visual Studio för att distribuera ett konsolprogram projekt till en webbapp i [Apptjänst](app-service-web-overview.md) som en [Azure Webjobs](http://go.microsoft.com/fwlink/?LinkId=390226). Information om hur du distribuerar WebJobs med hjälp av den [Azure-portalen](https://portal.azure.com), se [kör bakgrundsaktiviteter med WebJobs](web-sites-create-web-jobs.md).

När Visual Studio distribuerar ett WebJobs-aktiverade konsolprogram projekt, utför två aktiviteter:

* Runtime filer kopieras till rätt mapp i webbapp (*App_Data/jobb/kontinuerlig* för kontinuerliga Webbjobb *App_Data/jobb/utlöst* för WebJobs schemalagda och på begäran).
* Ställer in [Azure schemaläggare](#scheduler) för WebJobs som är schemalagda att köras vid en viss tidpunkt. (Detta behövs inte för kontinuerliga Webbjobb.)

Ett WebJobs-aktiverade projekt har lagts till följande objekt:

* Den [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paketet.
* En [webbjobb publicera settings.json](#publishsettings) -fil som innehåller inställningar för distribution och Schemaläggaren. 

![Diagram över vad som har lagts till i en Konsolapp för att aktivera distribution som ett Webbjobb](./media/websites-dotnet-deploy-webjobs/convert.png)

Du kan lägga till dessa objekt till ett befintligt projekt konsolprogram eller Använd en mall för att skapa ett nytt projekt WebJobs-aktiverade program. 

Du kan distribuera ett projekt som ett Webbjobb ensamt eller länka det till ett webbprojekt så att den distribuerar automatiskt när du distribuerar webbprojektet. Om du vill länka projekt, Visual Studio innehåller namnet på WebJobs-aktiverade projektet i en [webjobs list.json](#webjobslist) filen i webbprojektet.

![Diagram över Webbjobb projekt som länkar till webbprojekt](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Krav

Om du använder Visual Studio 2015, installera den [Azure SDK för .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Om du använder Visual Studio 2017, installera den [Azure-utveckling arbetsbelastning](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a>Aktivera WebJobs-distribution för ett befintligt projekt konsolprogram

Du kan välja mellan två alternativ:

* [Aktivera automatisk distribution med ett webbprojekt](#convertlink).

  Konfigurera ett befintligt projekt konsolprogram så att den distribuerar automatiskt som ett Webbjobb när du distribuerar ett webbprojekt. Använd det här alternativet om du vill köra din Webbjobbet i samma webbapp som du kör relaterade webbprogrammet.

* [Aktivera distributionen utan ett webbprojekt](#convertnolink).

  Konfigurera ett befintligt projekt konsolprogram att distribuera som ett Webbjobb, med någon länk till ett webbprojekt. Använd det här alternativet när du vill köra ett Webbjobb i en webbapp, med Inga webbprogram som körs i webbprogrammet. Du kanske vill göra detta för att kunna skala resurserna Webbjobb oberoende av webbtillämpningsresurser.

### <a id="convertlink"></a>Aktivera automatisk WebJobs-distribution med ett webbprojekt

1. Högerklicka på webbprojekt i **Solution Explorer**, och klicka sedan på **Lägg till** > **befintligt projekt som Azure Webjobs**.
   
    ![Befintligt projekt som Azure Webbjobbet](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    Den [lägga till Azure Webjobs](#configure) dialogrutan visas.
2. I den **projektnamn** listrutan, Välj konsolprogrammet projektet ska läggas till som ett Webbjobb.
   
    ![Att markera projekt i dialogrutan Lägg till Azure Webbjobbet](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Slutför den [lägga till Azure Webjobs](#configure) dialogrutan och klicka sedan på **OK**. 

### <a id="convertnolink"></a>Aktivera WebJobs distribution utan ett webbprojekt
1. Högerklicka på projektet konsolprogram i **Solution Explorer**, och klicka sedan på **Publicera som Azure Webjobs...** . 
   
    ![Publicera som Azure Webbjobbet](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    Den [lägga till Azure Webjobs](#configure) dialogruta visas med projektet som väljs i den **projektnamn** rutan.
2. Slutför den [lägga till Azure Webjobs](#configure) dialogrutan och klicka sedan på **OK**.
   
   Den **Publicera webbplats** guiden visas.  Om du inte vill publicera omedelbart stänga guiden. De inställningar som du har angett sparas för när du vill [distribuera projektet](#deploy).

## <a id="create"></a>Skapa ett nytt projekt WebJobs-aktiverad
Om du vill skapa ett nytt projekt WebJobs-aktiverade, kan du använda projektmall konsolprogram och aktivera WebJobs distribution enligt beskrivningen i [i föregående avsnitt](#convert). Alternativt kan använda du WebJobs nytt projekt mallen:

* [Använd WebJobs nytt projekt mall för en oberoende Webbjobbet](#createnolink)
  
    Skapa ett projekt och konfigurera den för att distribuera fristående som ett Webbjobb med ingen länk till ett webbprojekt. Använd det här alternativet när du vill köra ett Webbjobb i en webbapp, med Inga webbprogram som körs i webbprogrammet. Du kanske vill göra detta för att kunna skala resurserna Webbjobb oberoende av webbtillämpningsresurser.
* [Använd WebJobs nytt projekt mall för ett Webbjobb som är kopplad till ett webbprojekt](#createlink)
  
    Skapa ett projekt som är konfigurerad för att distribuera automatiskt som ett Webbjobb när ett webbprojekt i samma lösning som har distribuerats. Använd det här alternativet om du vill köra din Webbjobbet i samma webbapp som du kör relaterade webbprogrammet.

> [!NOTE]
> Mallen WebJobs nytt projekt som automatiskt installerar NuGet-paket och innehåller koden i *Program.cs* för den [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Om du inte vill använda WebJobs-SDK, ta bort eller ändra den `host.RunAndBlock` instruktionen i *Program.cs*.
> 
> 

### <a id="createnolink"></a>Använd WebJobs nytt projekt mall för en oberoende Webbjobbet
1. Klicka på **filen** > **nytt projekt**, och klicka sedan på den **nytt projekt** klickar du på **moln**  >   **Azure Webjobs (.NET Framework)**.
   
    ![Dialogrutan Nytt projekt som visar Webbjobb mall](./media/websites-dotnet-deploy-webjobs/np.png)
2. Följ anvisningarna som visas tidigare till [Se konsolprogrammet projicera ett oberoende WebJobs-projekt](#convertnolink).

### <a id="createlink"></a>Använd WebJobs nytt projekt mall för ett Webbjobb som är kopplad till ett webbprojekt
1. Högerklicka på webbprojekt i **Solution Explorer**, och klicka sedan på **Lägg till** > **nytt projekt för Azure Webjobs**.
   
    ![Nya menypost för Azure Webjobs-projekt](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    Den [lägga till Azure Webjobs](#configure) dialogrutan visas.
2. Slutför den [lägga till Azure Webjobs](#configure) dialogrutan och klicka sedan på **OK**.

## <a id="configure"></a>Dialogrutan Lägg till Azure Webbjobbet
Den **lägga till Azure Webjobs** dialogrutan låter dig ange webbjobbsnamnet och kör inställning för din Webbjobb. 

![Azure Webjobs dialogrutan Lägg till](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Fälten i den här dialogrutan motsvarar fält på den **lägger till Webbjobb** dialogrutan på Azure-portalen. Mer information finns i [kör bakgrundsaktiviteter med WebJobs](web-sites-create-web-jobs.md).

> [!NOTE]
> * Information om kommandoradsverktyget distribution finns [aktiverar kommandoraden eller kontinuerlig leverans Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Om du distribuerar ett Webbjobb och sedan bestämmer du vill ändra typen av Webbjobb och distribuera om du behöver ta bort den *webjobs publicera settings.json* fil. Detta gör Visual Studio visa publiceringsalternativ igen, så att du kan ändra typ av Webbjobb.
> * Om du distribuerar ett Webbjobb och senare ändrar läget Kör från kontinuerlig till icke-kontinuerliga och vice versa skapar Visual Studio ett nytt Webbjobb i Azure när du distribuerar. Om du ändrar andra schemainställningarna men lämna körningsläge samma eller växla mellan schemalagda och på begäran, Visual Studio uppdaterar befintliga jobbet i stället skapa en ny.
> 
> 

## <a id="publishsettings"></a>webbjobbet publicera settings.json
När du konfigurerar ett konsolprogram för distribution av WebJobs Visual Studio installerar den [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet paket- och lagrar schemainformation i en *webbjobb publicera settings.json*  filen i projektet *egenskaper* mappen WebJobs-projektet. Här är ett exempel på filen:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Du kan redigera den här filen direkt och Visual Studio har IntelliSense. Schemat filen lagras på [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) och kan visa.  

## <a id="webjobslist"></a>webjobs list.json
När du länkar ett WebJobs-aktiverade projekt till ett webbprojekt Visual Studio lagrar namnet på WebJobs-projekt i en *webjobs list.json* filen i webbprojektet *egenskaper* mapp. Listan kan innehålla flera WebJobs projekt som visas i följande exempel:

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

Du kan redigera den här filen direkt och Visual Studio har IntelliSense. Schemat filen lagras på [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) och kan visa.

## <a id="deploy"></a>Distribuera ett WebJobs-projekt
Ett WebJobs-projekt som du har länkats till ett webbprojekt distribuerar automatiskt med webbprojektet. Information om web project distribution finns **anvisningar hjälper** > **distribuera appen** i det vänstra navigeringsfönstret.

Högerklicka på projektet i för att distribuera ett projekt WebJobs ensamt **Solution Explorer** och på **Publicera som Azure Webjobs...** . 

![Publicera som Azure Webbjobbet](./media/websites-dotnet-deploy-webjobs/paw.png)

För en oberoende Webbjobb samma **Publicera webbplats** guiden som används för webbprojekt visas, men med färre inställningar som är tillgängliga för att ändra.
