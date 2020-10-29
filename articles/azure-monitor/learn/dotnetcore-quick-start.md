---
title: Snabb start ASP.NET Core – Azure Monitor Application Insights
description: Innehåller instruktioner för att snabbt konfigurera en ASP.NET Core-webbapp för övervakning med Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: bd45284bfea09935ea48445d816f3b91272f68fc
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925857"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Börja övervaka din ASP.NET Core-webbapp

Med Azure Application Insights kan du enkelt övervaka en webbapp för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. 

I den här snabb starten får du hjälp med att lägga till Application Insights SDK i ett befintligt ASP.NET Core-webbprogram. Läs mer om hur du konfigurerar Application Insights utan Visual Studio checka in den här [artikeln](../app/asp-net-core.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- [Installera Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) med följande arbets belastningar:
  - ASP.NET och webbutveckling
  - Azure Development
- [Installera .NET Core 2.0 SDK](https://dotnet.microsoft.com/download)
- Du behöver en Azure-prenumeration och en befintlig .NET Core-webbapp.

Om du inte har ett ASP.NET Core webb program kan du använda vår steg-för-steg-guide för att [skapa en ASP.net Core-app och lägga till Application Insights.](../app/asp-net-core.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla internetanslutna appar, oavsett om de körs lokalt eller i molnet. Gör så här om du vill börja granska dessa data:

1. Välj **skapa ett verktyg för resurs**  >  **utvecklare**  >  **Application Insights** .

   > [!NOTE]
   >Om det här är första gången du skapar en Application Insights-resurs kan du lära dig mer genom att gå till [skapa ett Application Insights resurs](../app/create-new-resource.md) dokument.

    En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

   | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namn för den nya resurs gruppen som värd för App Insights-data. Du kan skapa en ny resursgrupp eller välja en befintlig. |
   | **Plats** | East US | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa** .



## <a name="configure-app-insights-sdk"></a>Konfigurera App Insights SDK

1. Öppna **projektet** för ASP.NET Core-webbappen i Visual Studio > högerklicka på appnamnet i **Solution Explorer** > välj **Lägg till** > **Application Insights Telemetry** .

    ![lägg till Application Insights Telemetry](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klicka på knappen **Kom igång**

3. Välj ditt konto och din prenumeration > Välj den **befintliga resurs** som du skapade i Azure Portal > Klicka på **Registrera** .

4. Välj **projekt**  >  **Hantera NuGet paket**  >  **Källa: NuGet.org**  >  **Uppdatera** Application Insights SDK-paket till den senaste stabila versionen.

5. Välj **fel sökning**  >  **starta utan fel sökning** (Ctrl + F5) för att starta appen

    ![Skärm bild som visar meny alternativet starta utan fel sökning.](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Det tar 3–5 minuter innan data börjar visas i portalen. Om appen är en testapp för lågtrafik bör du komma ihåg att de flesta mått endast registreras när det finns aktiva begäranden eller åtgärder.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Öppna sidan Application Insights **Översikt** i Azure Portal genom att välja **Start** och under senaste resurser väljer du den resurs som du skapade tidigare för att visa information om ditt program som körs.

   ![översiktsvyn för Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klicka på **Programkarta** om du vill se en layout med beroendena mellan appens komponenter. Varje komponent visas KPI: er som belastning, prestanda, fel och varningar.

   ![Programkarta](./media/dotnetcore-quick-start/5appmap.png)

3. Klicka på ikonen program karta för **app Analytics** ![ ](./media/dotnetcore-quick-start/006.png) **i Analytics** . Då öppnas **Application Insights Analytics** , med ett funktionsrikt frågespråk för att analysera alla data som samlas in av Application Insights. I det här fallet skapas en fråga som återger antalet begäranden som ett diagram. Du kan skriva egna frågor för att analysera andra data.

   ![analysdiagram över användarbegäranden under en viss tidsperiod](./media/dotnetcore-quick-start/6analytics.png)

4. Gå tillbaka till sidan **Översikt** och undersök KPI-instrumentpanelerna.  På den här instrumentpanelen visas statistik om appens hälsotillstånd, inklusive antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar. 

   ![diagram med hälsotillståndsöversiktens tidslinje](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Klicka på **mått** till vänster. Använd Mät Utforskaren för att undersöka hälso tillståndet och användningen av resursen. Du kan klicka på **Lägg till nytt diagram** för att skapa ytterligare anpassade vyer eller välja **Redigera** för att ändra befintliga diagramtyper, höjd, färgpalett, grupperingar och mått. Du kan till exempel skapa ett diagram som visar den genomsnittliga webb sidans inläsnings tid genom att välja "webbläsarens sid inläsnings tid" från insamlingen mått och "AVG". Mer information om Azure Metrics Explorer finns i [komma igång med azure Metrics Explorer](../platform/metrics-getting-started.md).

     ![Fliken mått: genomsnittlig sid inläsnings tid i webbläsaren](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du är färdig med testningen kan du ta bort resurs gruppen och alla relaterade resurser. Följ stegen nedan.

> [!NOTE]
> Om du använde en befintlig resurs grupp kommer instruktionerna nedan inte att fungera och du behöver bara ta bort den enskilda Application Insights resursen. Kom ihåg att du tar bort en resurs grupp när alla underliggande resurser som är medlemmar i gruppen tas bort.

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup** .
2. På sidan resurs grupp klickar du på **ta bort** , skriver **myResourceGroup** i text rutan och klickar sedan på **ta bort** .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera körningsundantag](./tutorial-runtime-exceptions.md)

