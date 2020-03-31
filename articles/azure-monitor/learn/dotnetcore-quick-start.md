---
title: Snabbstart ASP.NET Core – Azure Monitor Application Insights
description: Innehåller instruktioner för att snabbt konfigurera en ASP.NET Core Web App för övervakning med Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894821"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Börja övervaka din ASP.NET Core-webbapp

Med Azure Application Insights kan du enkelt övervaka en webbapp för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. 

Den här snabbstarten guidar dig genom att lägga till Application Insights SDK i ett befintligt ASP.NET Core-webbprogram. Om du vill veta mer om att konfigurera Programstatistik utan Visual Studio-kassan den här [artikeln](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

- [Installera Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) med följande arbetsbelastningar:
  - ASP.NET och webbutveckling
  - Azure Development
- [Installera .NET Core 2.0 SDK](https://dotnet.microsoft.com/download)
- Du behöver en Azure-prenumeration och en befintlig .NET Core-webbapp.

Om du inte har ett ASP.NET Core-webbprogram kan du använda vår steg-för-steg-guide för att [skapa en ASP.NET Core-app och lägga till Application Insights.](../../azure-monitor/app/asp-net-core.md)

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla internetanslutna appar, oavsett om de körs lokalt eller i molnet. Gör så här om du vill börja granska dessa data:

1. Välj **Skapa en resurs** > **Developer tools** > **Application Insights**.

   > [!NOTE]
   >Om det är första gången du skapar en Application Insights-resurs kan du lära dig mer genom att besöka [doc-dokumentet Skapa en application insights-resurs.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

    En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

   | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namn på den nya resursgruppen som ska vara värd för App Insights-data. Du kan skapa en ny resursgrupp eller välja en befintlig. |
   | **Location** | USA, östra | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.



## <a name="configure-app-insights-sdk"></a>Konfigurera App Insights SDK

1. Öppna **projektet** för ASP.NET Core-webbappen i Visual Studio > högerklicka på appnamnet i **Solution Explorer** > välj **Lägg till** > **Application Insights Telemetry**.

    ![lägg till Application Insights Telemetry](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klicka på knappen **Kom igång**

3. Välj ditt konto och din prenumeration > Välj den **befintliga resurs** som du skapade i Azure-portalen > Klicka på **Registrera**.

4. Välj**Package-paketkälla för** > **Update**  > **Projekthanterar** > NuGet:- nuget.org uppdatera SDK-paketen Application Insights till den senaste stabila versionen. **Project**

5. Välj **Felsökningsstart** > **utan felsökning** (Ctrl+F5) för att starta appen

    ![översiktsvyn för Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Det tar 3–5 minuter innan data börjar visas i portalen. Om appen är en testapp för lågtrafik bör du komma ihåg att de flesta mått endast registreras när det finns aktiva begäranden eller åtgärder.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Öppna sidan Översikt **över programinsikter** i Azure-portalen genom att välja **Start** och under de senaste resurserna väljer du den resurs som du skapade tidigare för att visa information om ditt program som körs.

   ![översiktsvyn för Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klicka på **Programkarta** om du vill se en layout med beroendena mellan appens komponenter. Varje komponent visas KPI: er som belastning, prestanda, fel och varningar.

   ![Programkarta](./media/dotnetcore-quick-start/5appmap.png)

3. Klicka på ikonen ![ **Programkarta** ](./media/dotnetcore-quick-start/006.png) **i**App Analytics Visa i Analytics . Då öppnas **Application Insights Analytics**, med ett funktionsrikt frågespråk för att analysera alla data som samlas in av Application Insights. I det här fallet skapas en fråga som återger antalet begäranden som ett diagram. Du kan skriva egna frågor för att analysera andra data.

   ![analysdiagram över användarbegäranden under en viss tidsperiod](./media/dotnetcore-quick-start/6analytics.png)

4. Gå tillbaka till sidan **Översikt** och undersök KPI-instrumentpanelerna.  På den här instrumentpanelen visas statistik om appens hälsotillstånd, inklusive antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar. 

   ![diagram med hälsotillståndsöversiktens tidslinje](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Till vänster klicka på **Mått**. Använd statistikutforskaren för att undersöka resursens hälsa och utnyttjande. Du kan klicka på **Lägg till nytt diagram** för att skapa ytterligare anpassade vyer eller välja **Redigera** för att ändra befintliga diagramtyper, höjd, färgpalett, grupperingar och mått. Du kan till exempel skapa ett diagram som visar den genomsnittliga inläsningstiden för webbläsarsidan genom att välja "Inläsningstid för webbläsarsidan" från listrutan mått och "Medel" från aggregering. Mer information om Azure Metrics Explorer besöker [Komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Fliken Mått: Genomsnittligt tidsschema för inläsning av webbläsarsida](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Extern steg-för-steg-video om [att konfigurera Application Insights med .NET Core och Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) från grunden.
- Extern steg-för-steg-video om [att konfigurera Application Insights med .NET Core och Visual Studio Code](https://youtu.be/ygGt84GDync) från grunden.

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med testningen kan du ta bort resursgruppen och alla relaterade resurser. För att göra detta följ stegen nedan.

> [!NOTE]
> Om du har använt en befintlig resursgrupp fungerar inte instruktionerna nedan och du måste bara ta bort den enskilda Application Insights-resursen. Tänk på att alla underylingsresurser som är medlemmar i gruppen tas bort när du tar bort en resursgrupp.

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På resursgruppssidan klickar du på **Ta bort,** skriver **myResourceGroup** i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera körningsundantag](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
