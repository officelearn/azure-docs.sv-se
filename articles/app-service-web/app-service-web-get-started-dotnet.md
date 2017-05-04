---
title: "Skapa din första ASP.NET-webbapp i Azure på fem minuter | Microsoft Docs"
description: "Distribuera en enkel ASP.NET-app och se hur enkelt det är att köra webbappar i App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 24e9f1d7bdf4401d009ba04fb62351b6abda6079
ms.lasthandoff: 04/21/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Skapa din första ASP.NET-webbapp i Azure på fem minuter

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Den här snabbstarten hjälper dig att distribuera din första ASP.NET-webbapp via [Azure App Service](../app-service/app-service-value-prop-what-is.md) på bara några minuter. När du har gått igenom kursen har du en enkel webbapp som körs i molnet.

![ASP.NET-webbapp i Azure App Service](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## <a name="before-you-begin"></a>Innan du börjar

Den här kursen visar hur du använder Visual Studio 2017 för att skapa och distribuera en ASP.NET-webbapp till Azure. Om du inte har Visual Studio 2017 installerad kan du ladda ned och använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **utan kostnad**. Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa ett nytt projekt i Visual Studio med `Ctrl`+`Shift`+`N`.

I dialogrutan **Nytt projekt** klickar du på **Visual C# > Webb > ASP.NET-webbtillämpningsprogram (.NET Framework)**.

Ge programmet namnet **myFirstAzureWebApp** och klicka på **OK**.
   
![Dialogrutan Nytt projekt](./media/app-service-web-get-started-dotnet/new-project.png)

Du kan distribuera alla typer av ASP.NET-webbappar till Azure. I den här kursen väljer du **MVC**-mallen och ser till att autentiseringen är inställd på **Ingen autentisering**.
      
Klicka på **OK**.

![Dialogrutan Nytt ASP.NET-projekt](./media/app-service-web-get-started-dotnet/select-mvc-template.png)

## <a name="publish-to-azure"></a>Publicera till Azure

Högerklicka på **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-get-started-dotnet/solution-explorer-publish.png)

Se till att **Microsoft Azure App Service** är markerat och klicka på **Publicera**.

![Publicera från projektöversiktssidan](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Detta öppnar dialogrutan **Skapa App Service** där du får hjälp med att skapa alla Azure-resurser du behöver för att köra din ASP.NET-webbapp i Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

I dialogrutan **Skapa App Service** klickar du på **Lägg till ett konto** och logga sedan in på din Azure-prenumeration. Om du redan är inloggad på ett Microsoft-konto kontrollerar du att kontot tillhör din Azure-prenumeration. Om kontot inte tillhör din Azure-prenumeration klickar du på den för att lägga till rätt konto.
   
![Logga in på Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

När du har loggat in kan du skapa alla resurser du behöver för din Azure-webbapp i den här dialogrutan.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Först behöver du en _resursgrupp_. 

> [!NOTE] 
> En resursgrupp är en logisk behållare som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i.
>
>

Klicka på **Ny** bredvid **Resursgrupp**.

Ge resursgruppen namnet **myResourceGroup** och klicka på **OK**.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Azure-webbappen behöver också en _App Service-plan_. 

> [!NOTE]
> En App Service-plan representerar den samling fysiska resurser som dina appar finns i. Alla appar som är tilldelade till en App Service-plan delar de resurser som definierats av planen. Det innebär att du kan minska kostnaderna när du har flera appar i en plan. 
>
> App Service-planer definierar följande:
>
> - Region (Europa, norra; USA, östra; Asien, sydöstra)
> - Instansstorlek (liten, medel, stor)
> - Skalningsantal (en, två eller tre instanser osv.) 
> - SKU (Kostnadsfri, Delad, Basic, Standard, Premium)
>
>

Klicka på **Ny** bredvid **App Service-plan**. 

I dialogrutan **Configure App Service Plan** (Konfigurera App Service-plan) anger du följande inställningar för den nya App Service-planen:

- **App Service-plan**: Ange **myAppServicePlan**. 
- **Plats**: Välj **Europa, västra** eller någon annan region.
- **Storlek**: Välj **Kostnadsfri** eller någon annan [prisnivå](https://azure.microsoft.com/pricing/details/app-service/).

Klicka på **OK**.

![Skapa en ny App Service-plan](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

## <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

Nu är det enda som återstår att namnge webbappen. Ange ett unikt appnamn i **Web App Name** (Namn på webbapp). Det här namnet kommer att användas som en del av standard-DNS-namnet för din app (`<app_name>.azurewebsites.net`), så det måste vara unikt för alla appar i Azure. Du kan senare mappa ett anpassat domännamn till din app innan du exponerar den för användarna.

Du kan också acceptera namnet som genereras automatiskt (det är redan unikt).

Klicka på **Skapa** för att börja skapa Azure-resurser.

![Ange webbappnamn](./media/app-service-web-get-started-dotnet/web-app-name.png)

När guiden är klar med att skapa Azure-resurserna publiceras din ASP.NET-webbapp automatiskt på Azure för första gången och den publicerade Azure-webbappen startas i din standardwebbläsare.

![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/published-azure-web-app.png)

I URL:en används det webbappnamn som du angav tidigare, med formatet `http://<app_name>.azurewebsites.net`. 

Grattis, din första ASP.NET-webbapp körs live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Det är enkelt att uppdatera och omdistribuera till Azure. Låt oss uppdatera startsidan.

Öppna **Views\Home\Index.cshtml** från **Solution Explorer**.

Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela taggen med följande kod:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

Klicka på **Publicera** på publiceringssidan.

När Visual Studio är klar startas den uppdaterade Azure-webbappen i webbläsaren.

![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## <a name="manage-your-new-azure-web-app"></a>Hantera din nya Azure-webbapp

Gå till Azure Portal och titta på webbappen du nyss skapade. 

Logga in på [https://portal.azure.com](https://portal.azure.com).

Klicka på **Apptjänster** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-get-started-dotnet/access-portal.png)

Nu visas webbappens _blad_ (en portalsida som öppnas vågrätt). 

Sidan **Översikt** visas som standard på webbappens blad. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på bladet kan du se olika konfigurationssidor som du kan öppna. 

![App Service-blad på Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Flikarna på bladet innehåller många bra funktioner som du kan lägga till i webbappen. I listan nedan kan du se några av möjligheterna:

- Mappa ett anpassat DNS-namn
- Bind ett anpassat SSL-certifikat
- Konfigurera kontinuerlig distribution
- Skala upp
- Lägg till användarautentisering

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort din första Azure-webbapp klickar du på **Ta bort** på sidan **Översikt**. Men det finns ett bättre sätt att ta bort allt som du har skapat i den här snabbstarten. Klicka på resursgruppen på sidan **Översikt** för att öppna bladet för webbappen. 

![Öppna resursgruppen från App Service-bladet](./media/app-service-web-get-started-dotnet/access-resource-group.png)

På resursgruppbladet kan du se både App Service-planen och den App Service-app som Visual Studio har skapat åt dig. 

Klicka på **Ta bort** överst på bladet. 

<!--![Delete resource group in Azure portal](./media/app-service-web-get-started-dotnet/delete-resource-group.png)-->

Bekräfta genom att skriva resursgruppnamnet **myResourceGroup** i textrutan på bekräftelsebladet och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Utforska färdiga [PowerShell-skript för Web Apps](app-service-powershell-samples.md).

