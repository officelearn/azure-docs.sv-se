---
title: Felsöka med Visual Studio
description: Lär dig hur du felsöker en App Service-app med hjälp av fjärrfelsökning, spårning och loggningsverktyg som är inbyggda i Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 3305cfb81980984574961b2a84a056f5d1879ead
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280058"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Felsöka en app i Azure App Service med Visual Studio
## <a name="overview"></a>Översikt
Den här självstudien visar hur du använder Visual Studio-verktyg för att felsöka en app i [App Service](https://go.microsoft.com/fwlink/?LinkId=529714), genom att köra i [felsökningsläge](https://docs.microsoft.com/visualstudio/debugger/) på distans eller genom att visa programloggar och webbserverloggar.

Du får lära dig att:

* Vilka apphanteringsfunktioner som är tillgängliga i Visual Studio.
* Så här använder du Visual Studio-fjärrvyn för att göra snabba ändringar i en fjärrapp.
* Så här fjärrkörs felsökningsläge medan ett projekt körs i Azure, både för en app och för ett WebJob.How to run debug mode remotely while a project is running in Azure, both for an app and for a WebJob.
* Så här skapar du programspårningsloggar och visar dem medan programmet skapar dem.
* Så här visar du webbserverloggar, inklusive detaljerade felmeddelanden och spårning av misslyckade begäranden.
* Så här skickar du diagnostikloggar till ett Azure Storage-konto och visar dem där.

Om du har Visual Studio Ultimate kan du också använda [IntelliTrace](/visualstudio/debugger/intellitrace) för felsökning. IntelliTrace omfattas inte av den här självstudien.

## <a name="prerequisites"></a><a name="prerequisites"></a>Krav
Den här självstudien fungerar med den utvecklingsmiljö, webbprojekt och App Service-app som du konfigurerar i [Skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md). För WebJobs-avsnitten behöver du programmet som du skapar i [Kom igång med Azure WebJobs SDK][GetStartedWJ].

Kodexemplen som visas i den här självstudien är för ett C# MVC-webbprogram, men felsökningsprocedurerna är desamma för Visual Basic- och Web Forms-program.

Självstudien förutsätter att du använder Visual Studio 2019. 

Funktionen för direktuppspelningsloggar fungerar bara för program som är inriktade på .NET Framework 4 eller senare.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Appkonfiguration och apphantering
Visual Studio ger åtkomst till en delmängd av apphanteringsfunktionerna och konfigurationsinställningarna som är tillgängliga i [Azure-portalen](https://go.microsoft.com/fwlink/?LinkId=529715). I det här avsnittet ser du vad som är tillgängligt med Server **Explorer**. Om du vill se de senaste Azure-integrationsfunktionerna kan du prova **Cloud Explorer** också. Du kan öppna båda **View** fönstren från Visa-menyn.

1. Om du inte redan är inloggad på Azure i Visual Studio högerklickar du på **Azure** och väljer Anslut till **Microsoft Azure-prenumeration** i **Server Explorer**.

    Ett alternativ är att installera ett hanteringscertifikat som ger åtkomst till ditt konto. Om du väljer att installera ett **Azure** certifikat högerklickar du på Azure-noden i **Serverutforskaren**och väljer sedan **Hantera och filtrera prenumerationer** på snabbmenyn. Klicka på fliken Certifikat i dialogrutan **Hantera Microsoft Azure-prenumerationer** och klicka sedan på **Importera**. **Certificates** Följ anvisningarna för att hämta och importera sedan en prenumerationsfil (kallas även *en .publishsettings-fil)* för ditt Azure-konto.

   > [!NOTE]
   > Om du hämtar en prenumerationsfil sparar du den i en mapp utanför källkodskatalogerna (till exempel i mappen Nedladdningar) och tar sedan bort den när importen är klar. En obehörig användare som får åtkomst till prenumerationsfilen kan redigera, skapa och ta bort dina Azure-tjänster.
   >
   >

    Mer information om hur du ansluter till Azure-resurser från Visual Studio finns i [Hantera konton, prenumerationer och administrativa roller](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. Expandera **Azure** och expandera **App Service**i **Server Explorer**.
3. Expandera resursgruppen som innehåller appen som du skapade i [Skapa en ASP.NET app i Azure App Service](app-service-web-get-started-dotnet-framework.md)och högerklicka sedan på appnoden och klicka på Visa **inställningar**.

    ![Visa inställningar i Utforskaren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Fliken **Azure Web App** visas och där kan du se de apphanterings- och konfigurationsuppgifter som är tillgängliga i Visual Studio.

    ![Azure Web App-fönster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    I den här självstudien använder du listrutan loggning och spårning. Du kommer också att använda fjärrfelsökning, men du använder en annan metod för att aktivera den.

    Information om appinställningarna och anslutningssträngarna i det här fönstret finns i [Azure App Service: Så fungerar programsträngar och anslutningssträngar](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Om du vill utföra en apphanteringsuppgift som inte kan göras i det här fönstret klickar du på **Öppna i Hanteringsportal** för att öppna ett webbläsarfönster till Azure-portalen.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Komma åt appfiler i Server Explorer
Du distribuerar vanligtvis ett `customErrors` webbprojekt med flaggan i `On` filen `RemoteOnly`Web.config inställd på eller , vilket innebär att du inte får något användbart felmeddelande när något går fel. För många fel, allt du får är en sida som en av följande:

**Serverfel i /'-programmet:**

![Felsida som inte kan vara till någon hjälp](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ett fel uppstod:**

![Felsida som inte kan vara till någon hjälp](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Webbplatsen kan inte visa sidan**

![Felsida som inte kan vara till någon hjälp](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Ofta det enklaste sättet att hitta orsaken till felet är att aktivera detaljerade felmeddelanden, som den första av de föregående skärmdumpar förklarar hur man gör. Det kräver en ändring i den distribuerade Web.config-filen. Du kan redigera *web.config-filen* i projektet och distribuera om projektet, eller skapa en [Web.config-transformering](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) och distribuera en felsökningsversion, men det finns ett snabbare sätt: i **Solution Explorer**kan du direkt visa och redigera filer i fjärrappen med hjälp av *fjärrvyfunktionen.*

1. Expandera **Azure**i **Server Explorer**, expandera **App Service**, expandera resursgruppen som appen finns i och expandera sedan noden för din app.

    Du ser noder som ger dig åtkomst till appens innehållsfiler och loggfiler.
2. Expandera noden **Filer** och dubbelklicka på filen *Web.config.*

    ![Öppna Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio öppnar filen Web.config från fjärrappen och visar [Remote] bredvid filnamnet i namnlisten.
3. Lägg till följande `system.web` rad i elementet:

    `<customErrors mode="Off"></customErrors>`

    ![Redigera Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Uppdatera webbläsaren som visar felmeddelandet om ohjälpsam, och nu får du ett detaljerat felmeddelande, till exempel följande exempel:

    ![Detaljerat felmeddelande](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Felet som visas skapades genom att lägga till raden som visas i rött *i Vyer\Home\Index.cshtml*.)

Att redigera filen Web.config är bara ett exempel på scenarier där möjligheten att läsa och redigera filer i App Service-appen gör felsökningen enklare.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Fjärrfelsökning av appar
Om det detaljerade felmeddelandet inte innehåller tillräckligt med information och du inte kan återskapa felet lokalt, är ett annat sätt att felsöka att köras i felsökningsläge på distans. Du kan ange brytpunkter, ändra minnet direkt, stega igenom koden och till och med ändra kodsökvägen.

Fjärrfelsökning fungerar inte i Express-utgåvor av Visual Studio.

Det här avsnittet visar hur du felsöker på distans med det projekt som du skapar i [Skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Öppna webbprojektet som du skapade i [Skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Öppna *handkontroller\HomeController.cs*.

1. Ta `About()` bort metoden och infoga följande kod på dess ställe.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Ställ in en](https://docs.microsoft.com/visualstudio/debugger/) `ViewBag.Message` brytpunkt på raden.

1. Högerklicka på projektet i **Solution Explorer**och klicka på **Publicera**.

1. I listrutan **Profil** väljer du samma profil som du använde i [Skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md). Klicka sedan på Inställningar.

1. Klicka på fliken **Inställningar** i dialogrutan **Publicera** och ändra sedan **Konfiguration** till **Felsökning**och klicka sedan på **Spara**.

    ![Publicera i felsökningsläge](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klicka på **Publicera**. När distributionen är klar och webbläsaren öppnas till Azure-URL:en för din app stänger du webbläsaren.

1. Högerklicka på appen i **Server Explorer**och klicka sedan på **Bifoga felsökning**.

    ![Bifoga felsökning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Webbläsaren öppnas automatiskt till din startsida som körs i Azure. Du kanske måste vänta 20 sekunder eller så medan Azure konfigurerar servern för felsökning. Den här fördröjningen inträffar bara första gången du kör i felsökningsläge på en app i en 48-timmarsperiod. När du börjar felsöka igen under samma period finns det ingen fördröjning.

    > [!NOTE] 
    > Om du har några problem med att starta felsökningsprogrammet kan du försöka göra det med Hjälp av **Cloud Explorer** i stället för **Server Explorer**.
    >

1. Klicka på **Om** i menyn.

    Visual Studio stannar på brytpunkten och koden körs i Azure, inte på din lokala dator.

1. Hovra `currentTime` över variabeln för att se tidsvärdet.

    ![Visa variabel i felsökningsläge som körs i Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Tiden du ser är Azure-servertiden, som kan finnas i en annan tidszon än den lokala datorn.

1. Ange ett nytt `currentTime` värde för variabeln, till exempel "Körs nu i Azure".

1. Tryck på F5 för att fortsätta köra.

     Sidan Om som körs i Azure visar det nya värdet som du har angett i variablen currentTime.

     ![Om sida med nytt värde](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a>Fjärrfelsökning WebJobs
Det här avsnittet visar hur du felsöker på distans med hjälp av projektet och appen som du skapar i [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funktionerna som visas i det här avsnittet är endast tillgängliga i Visual Studio 2013 med uppdatering 4 eller senare.

Fjärrfelsökning fungerar bara med kontinuerliga WebJobs. Schemalagda och on-demand WebJobs stöder inte felsökning.

1. Öppna webbprojektet som du skapade i [Kom igång med Azure WebJobs SDK][GetStartedWJ].

2. Öppna *Functions.cs*i projektet ContosoAdsWebJob .

3. [Ange en brytpunkt](https://docs.microsoft.com/visualstudio/debugger/) för den `GnerateThumbnail` första satsen i metoden.

    ![Ange brytpunkt](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. Högerklicka på webbprojektet (inte WebJob-projektet) i **Solution Explorer**och klicka på **Publicera**.

5. I listrutan **Profil** väljer du samma profil som du använde i [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klicka på fliken **Inställningar** och ändra **konfiguration** till **Felsökning**och klicka sedan på **Publicera**.

    Visual Studio distribuerar webb- och WebJob-projekt och webbläsaren öppnas för Azure-URL:en för din app.

7. Expandera **Server Explorer** **Azure > App Service > din resursgrupp > din app > WebJobs > Kontinuerlig**och högerklicka sedan på **ContosoAdsWebJob**.

8. Klicka på **Bifoga felsökning**.

    ![Bifoga felsökning](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Webbläsaren öppnas automatiskt till din startsida som körs i Azure. Du kanske måste vänta 20 sekunder eller så medan Azure konfigurerar servern för felsökning. Den här fördröjningen inträffar bara första gången du kör i felsökningsläge på en app i en 48-timmarsperiod. När du börjar felsöka igen under samma period finns det ingen fördröjning.

9. Skapa en ny annons i webbläsaren som öppnas till startsidan för Contoso Ads.

    Om du skapar en annons skapas ett kömeddelande som hämtas av WebJob och bearbetas. När WebJobs SDK anropar funktionen för att bearbeta kömeddelandet träffar koden brytpunkten.

10. När felsökaren bryts vid brytpunkten kan du undersöka och ändra variabla värden medan programmet kör molnet. I följande bild visar felsökningsprogrammet innehållet i blobInfo-objektet som `GenerateThumbnail` skickades till metoden.

     ![blobInfo-objekt i felsökningsprogrammet](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Tryck på F5 för att fortsätta köra.

     Metoden `GenerateThumbnail` är klar med att skapa miniatyren.

12. I webbläsaren uppdaterar du indexsidan så visas miniatyren.

13. Tryck på SKIFT+F5 i Visual Studio för att sluta felsöka.

14. Högerklicka på noden ContosoAdsWebJob i **Server Explorer**och klicka på **Visa instrumentpanel**.

15. Logga in med dina Azure-autentiseringsuppgifter och klicka sedan på WebJob-namnet för att gå till sidan för ditt WebJob.

     ![Klicka på ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Instrumentpanelen visar `GenerateThumbnail` att funktionen nyligen har körts.

     (Nästa gång du klickar på **Visa instrumentpanel**behöver du inte logga in och webbläsaren går direkt till sidan för webjob.)

16. Klicka på funktionsnamnet om du vill se information om funktionskörningen.

     ![Funktionsinformation](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Om funktionen [skrev loggar](https://github.com/Azure/azure-webjobs-sdk/wiki)kan du klicka på **ToggleOutput** för att se dem.

## <a name="notes-about-remote-debugging"></a>Information om fjärrfelsökning

* Det rekommenderas inte att köra i felsökningsläge i produktion. Om produktionsappen inte skalas ut till flera serverinstanser förhindrar felsökning webbservern att svara på andra begäranden. Om du har flera webbserverinstanser får du en slumpmässig instans när du ansluter till felsökaren och du kan inte se till att efterföljande webbläsarbegäranden går till samma instans. Du distribuerar vanligtvis inte en felsökningsversion till produktion, och kompilatoroptimeringar för versionsversioner kan göra det omöjligt att visa vad som händer rad för rad i källkoden. För felsökning av produktionsproblem är den bästa resursen programspårning och webbserverloggar.
* Undvik långa stopp vid brytpunkter vid fjärrfelsökning. Azure behandlar en process som stoppas längre än några minuter som en process som inte svarar och stänger av den.
* När du felsöker skickar servern data till Visual Studio, vilket kan påverka bandbreddsavgifterna. Information om bandbreddshastighet finns i [Azure Pricing](https://azure.microsoft.com/pricing/calculator/).
* Kontrollera att `debug` attributet `compilation` för elementet i *filen Web.config* är inställt på true. Den är inställd på true som standard när du publicerar en felsökningsversionskonfiguration.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Om du upptäcker att felsökaren inte går in i koden som du vill felsöka kan du behöva ändra inställningen Just My Code.  Mer information finns i Ange om endast [användarkod ska felsökas med Just My Code i Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* En timer startar på servern när du aktiverar fjärrfelsökningsfunktionen, och efter 48 timmar stängs funktionen av automatiskt. Den här 48-timmarsgränsen görs av säkerhets- och prestandaskäl. Du kan enkelt slå på funktionen igen så många gånger du vill. Vi rekommenderar att du lämnar den inaktiverad när du inte aktivt felsöker.
* Du kan manuellt koppla felsökningaren till valfri process, inte bara appprocessen (w3wp.exe). Mer information om hur du använder felsökningsläge i Visual Studio finns [i Felsökning i Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Översikt över diagnostikloggar
Ett ASP.NET program som körs i en App Service-app kan skapa följande typer av loggar:

* **Loggar för programspårning**<br/>
  Programmet skapar dessa loggar genom att anropa metoder för klassen [System.Diagnostics.Trace.](/dotnet/api/system.diagnostics.trace)
* **Webbserverloggar**<br/>
  Webbservern skapar en loggpost för varje HTTP-begäran till appen.
* **Detaljerade felmeddelandeloggar**<br/>
  Webbservern skapar en HTML-sida med ytterligare information för misslyckade HTTP-begäranden (begäranden som resulterar i statuskod 400 eller senare).
* **Spårade loggar för misslyckade begäranden**<br/>
  Webbservern skapar en XML-fil med detaljerad spårningsinformation för misslyckade HTTP-begäranden. Webbservern tillhandahåller också en XSL-fil för att formatera XML-koden i en webbläsare.

Loggning påverkar appens prestanda, så Azure ger dig möjlighet att aktivera eller inaktivera varje typ av logg efter behov. För programloggar kan du ange att endast loggar över en viss allvarlighetsgrad ska skrivas. När du skapar en ny app inaktiveras all loggning som standard.

Loggar skrivs till filer i en *LogFiles* mapp i filsystemet i din app och är tillgängliga via FTP. Webbserverloggar och programloggar kan också skrivas till ett Azure Storage-konto. Du kan behålla en större mängd loggar i ett lagringskonto än vad som är möjligt i filsystemet. Du är begränsad till maximalt 100 megabyte loggar när du använder filsystemet. (Filsystemloggar är endast för kortvarig kvarhållning. Azure tar bort gamla loggfiler för att göra plats för nya när gränsen har nåtts.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Skapa och visa programspårningsloggar
I det här avsnittet gör du följande:

* Lägg till spårningssatser i webbprojektet som du skapade i [Kom igång med Azure och ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Visa loggarna när du kör projektet lokalt.
* Visa loggarna när de genereras av programmet som körs i Azure.

Information om hur du skapar programloggar i WebJobs finns i [Så här arbetar du med Azure-kölagring med WebJobs SDK - Hur du skriver loggar](https://github.com/Azure/azure-webjobs-sdk/wiki). Följande instruktioner för att visa loggar och styra hur de lagras i Azure gäller även för programloggar som skapats av WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Lägga till spårningssatser i programmet
1. Öppna *Controllers\HomeController.cs*och `Index`ersätt `About`, `Contact` och metoderna med följande `Trace` kod `using` för `System.Diagnostics`att lägga till satser och en sats för:

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Lägg `using System.Diagnostics;` till en sats överst i filen.

### <a name="view-the-tracing-output-locally"></a>Visa kalkeringsutdata lokalt
1. Tryck på F5 för att köra programmet i felsökningsläge.

    Standardspårningslyssnaren skriver alla spårningsutdata till **utdatafönstret,** tillsammans med andra Felsökningsutdata. Följande bild visar utdata från spårningssatserna `Index` som du har lagt till i metoden.

    ![Spårning i felsökningsfönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Följande steg visar hur du visar spårningsutdata på en webbsida, utan att kompilera i felsökningsläge.
1. Öppna filen Web.config (den som finns i projektmappen) och lägg till ett `<system.diagnostics>` `</configuration>` element i slutet av filen strax före det avslutande elementet:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

Du `WebPageTraceListener` kan visa spårningsutdata `/trace.axd`genom att bläddra till .
1. Lägg till ett `<system.web>` <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">spårningselement</a> under i filen Web.config, till exempel följande exempel:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Tryck på CTRL+F5 för att köra programmet.
1. I adressfältet i webbläsarfönstret lägger du till *trace.axd* i WEBBADRESSEN och `http://localhost:53370/trace.axd`trycker sedan på Retur (webbadressen liknar ).
1. Klicka på **Visa information** på den första raden på sidan **Programspårning.**

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Sidan **Begär information** visas och i avsnittet **Spårningsinformation** visas utdata från spårningssatserna som du har lagt till i `Index` metoden.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Som standard `trace.axd` är endast tillgänglig lokalt. Om du vill göra den tillgänglig från en `localOnly="false"` fjärrapp kan du lägga till elementet `trace` i filen *Web.config,* som visas i följande exempel:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Det rekommenderas `trace.axd` dock inte att aktivera i en produktionsapp av säkerhetsskäl. I följande avsnitt visas ett enklare sätt att läsa spårningsloggar i en App Service-app.

### <a name="view-the-tracing-output-in-azure"></a>Visa spårningsutdata i Azure
1. Högerklicka på webbprojektet i **Solution Explorer**och klicka på **Publicera**.
2. Klicka på **Publicera**i dialogrutan **Publicera webb.**

    När Visual Studio har publicerat uppdateringen öppnas ett webbläsarfönster på startsidan (förutsatt att du inte har **rensat måladressen** på fliken **Anslutning).**
3. Högerklicka på appen i **Server Explorer**och välj **Visa strömmande loggar**.

    ![Visa strömmande loggar på snabbmenyn](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **Utdatafönstret** visar att du är ansluten till loggströmningstjänsten och lägger till en meddelanderad varje minut som går utan en logg att visa.

    ![Visa strömmande loggar på snabbmenyn](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Klicka på **Kontakt**i webbläsarfönstret som visar programmets startsida .

    Inom några sekunder visas utdata från spårningen på `Contact` felnivå som du har lagt till i metoden i **utdatafönstret.**

    ![Felspårning i utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio visar bara spårningar på felnivå eftersom det är standardinställningen när du aktiverar loggövervakningstjänsten. När du skapar en ny App Service-app inaktiveras all loggning som standard, som du såg när du öppnade inställningssidan tidigare:

    ![Programloggning av](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Men när du valde **Visa strömmande loggar**ändrade Visual Studio automatiskt **Application Logging(File System)** till **Error**, vilket innebär att felnivåloggar rapporteras. Om du vill visa alla dina spårningsloggar kan du ändra den här inställningen till **Utförlig**. När du väljer en allvarlighetsgrad som är lägre än fel rapporteras även alla loggar för högre allvarlighetsgrad. Så när du väljer utförlig, ser du också information, varning och felloggar.  

5. Högerklicka på appen i **Server Explorer**och klicka sedan på **Visa inställningar** som du gjorde tidigare.
6. Ändra **programloggning (filsystem)** till **Utförisk**och klicka sedan på **Spara**.

    ![Ställa in spårningsnivå till Utförlig](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Klicka på **Start**i webbläsarfönstret som nu visar sidan **Kontakt** och klicka sedan på **Om**och klicka sedan på **Kontakt**.

    Inom några sekunder visar **utdatafönstret** alla dina spårningsutdata.

    ![Utförlig spårningsutdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    I det här avsnittet har du aktiverat och inaktiverat loggning med hjälp av appinställningar. Du kan också aktivera och inaktivera spårningslyssnare genom att ändra filen Web.config. Om du ändrar filen Web.config kan appdomänen dock återvinnas, samtidigt som loggning via appkonfigurationen inte gör det. Om problemet tar lång tid att reproducera, eller är intermittent, kan återvinning av appdomänen "fixa" den och tvinga dig att vänta tills den händer igen. Om du aktiverar diagnostik i Azure kan du börja samla in felinformation direkt utan att återvinna appdomänen.

### <a name="output-window-features"></a>Funktioner för utdatafönster
Fliken **Microsoft Azure-loggar** i **utdatafönstret** har flera knappar och en textruta:

![Knapparna för fliken Loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Dessa utför följande funktioner:

* Rensa **utdatafönstret.**
* Aktivera eller inaktivera radbrytning.
* Starta eller stoppa övervakningsloggar.
* Ange vilka loggar som ska övervakas.
* Ladda ner loggar.
* Filtrera loggar baserat på en söksträng eller ett reguljärt uttryck.
* Stäng **utdatafönstret.**

Om du anger en söksträng eller ett reguljärt uttryck filtrerar Visual Studio loggningsinformationen hos klienten. Det innebär att du kan ange villkoren när loggarna har visats i **utdatafönstret** och du kan ändra filtreringsvillkoren utan att behöva återskapa loggarna.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Visa webbserverloggar
Webbserverloggar registrerar all HTTP-aktivitet för appen. Om du vill se dem i **utdatafönstret** måste du aktivera dem för appen och tala om för Visual Studio att du vill övervaka dem.

1. På fliken **Konfiguration av Azure Web App** som du öppnade från Server **Explorer**ändrar du Webbserverloggning till **På**och klickar sedan på **Spara**.

    ![Aktivera webbserverloggning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Klicka på knappen **Ange vilka Microsoft Azure-loggar som ska övervakas** i **utdatafönstret.**

    ![Ange vilka Azure-loggar som ska övervakas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. I dialogrutan **Microsoft Azure-loggningsalternativ** väljer du **Webbserverloggar**och klickar sedan på **OK**.

    ![Övervaka webbserverloggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. I webbläsarfönstret som visar appen klickar du på **Start**och sedan på **Om**och sedan på **Kontakt**.

    Programloggarna visas vanligtvis först, följt av webbserverloggarna. Du kanske måste vänta ett tag på att loggarna ska visas.

    ![Webbserverloggar i utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

När du först aktiverar webbserverloggar med Visual Studio skriver Azure som standard loggarna till filsystemet. Som ett alternativ kan du använda Azure-portalen för att ange att webbserverloggar ska skrivas till en blob-behållare i ett lagringskonto.

Om du använder portalen för att aktivera webbserverloggning till ett Azure-lagringskonto och sedan inaktiverar loggning i Visual Studio återställs dina lagringskontoinställningar när du återaktiverar loggning i Visual Studio.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Visa detaljerade felmeddelandeloggar
Detaljerade felloggar innehåller ytterligare information om HTTP-begäranden som resulterar i felsvarskoder (400 eller högre). För att kunna se dem i **utdatafönstret** måste du aktivera dem för appen och tala om för Visual Studio att du vill övervaka dem.

1. På fliken **Konfiguration av Azure Web App** som du öppnade från Server **Explorer**ändrar **du Detaljerade felmeddelanden** till **På**och klickar sedan på **Spara**.

    ![Aktivera detaljerade felmeddelanden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Klicka på knappen **Ange vilka Microsoft Azure-loggar som ska övervakas** i **utdatafönstret.**

3. Klicka på Alla loggar i dialogrutan **Microsoft Azure Loggningsalternativ** och klicka sedan på **All logs** **OK**.

    ![Övervaka alla loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Lägg till ett extra tecken i URL:en i adressfältet i webbläsarfönstret och `http://localhost:53370/Home/Contactx`tryck på Retur.

    Efter flera sekunder visas den detaljerade felloggen i fönstret Visual **Studio-utdata.**

    ![Detaljerad fellogg - Utdatafönster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Kontroll+klicka på länken för att se loggutdata som är formaterade i en webbläsare:

    ![Detaljerad fellogg - webbläsarfönster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Ladda ner filsystemloggar
Alla loggar som du kan övervaka i **utdatafönstret** kan också hämtas som en *ZIP-fil.*

1. Klicka på Hämta **strömmande loggar**i **utdatafönstret** .

    ![Knapparna för fliken Loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Filutforskaren öppnas i mappen *Nedladdningar* med den nedladdade filen markerad.

    ![Nedladdad fil](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrahera *ZIP-filen* och du ser följande mappstruktur:

    ![Nedladdad fil](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Programspårningsloggar finns i *TXT-filer* i mappen *LogFiles\Application.*
   * Webbserverloggar finns i *.log-filer* i mappen *LogFiles\http\RawLogs.* Du kan använda ett verktyg som [Log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) för att visa och ändra dessa filer.
   * Detaljerade felmeddelandeloggar finns i *HTML-filer* i mappen *LogFiles\DetailedErrors.*

     (Mappen distributioner är för filer som *skapats* av källkontrollpublicering, den har inget som har något samband med Visual Studio-publicering. *Git-mappen* är avsedd för spårningar relaterade till källkontrollpublicering och loggfilsstreamingtjänsten.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Visa spårade loggar för misslyckade begäranden
Spårningsloggar för misslyckade begäranden är användbara när du behöver förstå information om hur IIS hanterar en HTTP-begäran, i scenarier som URL-omskrivning eller autentiseringsproblem.

Apptjänstappar använder samma misslyckade spårningsfunktioner för begäran som har varit tillgängliga med IIS 7.0 och senare. Du har dock inte tillgång till IIS-inställningarna som konfigurerar vilka fel som loggas. När du aktiverar spårning av misslyckade begäranden fångas alla fel in.

Du kan aktivera spårning av misslyckade begäranden med Visual Studio, men du kan inte visa dem i Visual Studio. Dessa loggar är XML-filer. Streamingloggtjänsten övervakar endast filer som anses läsbara i oformaterad textläge: *.txt,* *.html*och *.log-filer.*

Du kan visa misslyckade spårningsloggar för begäran i en webbläsare direkt via FTP eller lokalt efter att du har använt ett FTP-verktyg för att hämta dem till den lokala datorn. I det här avsnittet ska du visa dem i en webbläsare direkt.

1. På **fliken Konfiguration** i **azure web app-fönstret** som du öppnade från Server **Explorer**ändrar du Spårning **av misslyckad begäran** till **På**och klickar sedan på **Spara**.

    ![Aktivera spårning av misslyckade begäranden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Lägg till ett extra tecken i URL:en i adressfältet i webbläsarfönstret som visar appen och klicka på Retur för att orsaka ett 404-fel.

    Detta medför att en misslyckad spårningslogg för begäran skapas och följande steg visar hur du visar eller hämtar loggen.

3. Klicka på Öppna i **Hanteringsportal** på fliken Konfiguration i **Azure Web App** i Visual **Studio.**

4. På sidan Inställningar för [Azure-portalen](https://portal.azure.com) **Settings** för din app klickar du på **Distributionsuppgifter**och anger sedan ett nytt användarnamn och lösenord.

    ![Nytt FTP-användarnamn och lösenord](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > När du loggar in måste du använda det fullständiga användarnamnet med appnamnet som är prefixat till det. Om du till exempel anger "myid" som användarnamn och webbplatsen är "myexample", loggar du in som "myexample\myid".
    >

5. I ett nytt webbläsarfönster går du till webbadressen som visas under **FTP-värdnamn** eller **FTPS-värdnamn** på sidan **Översikt** för din app.

6. Logga in med de FTP-autentiseringsuppgifter som du skapade tidigare (inklusive appnamnsprefixet för användarnamnet).

    Webbläsaren visar appens rotmapp.

7. Öppna mappen *LogFiles.*

    ![Öppna mappen Loggfiler](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Öppna mappen med namnet W3SVC plus ett numeriskt värde.

    ![Öppna W3SVC-mappen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Mappen innehåller XML-filer för alla fel som har loggats efter att du har aktiverat spårning av misslyckade begäranden och en XSL-fil som en webbläsare kan använda för att formatera XML-koden.

    ![W3SVC-mapp](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klicka på XML-filen för den misslyckade begäran som du vill visa spårningsinformation för.

    Följande bild visar en del av spårningsinformationen för ett exempelfel.

    ![Spårning av misslyckade begäranden i webbläsaren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Nästa steg
Du har sett hur Visual Studio gör det enkelt att visa loggar som skapats av en App Service-app. I följande avsnitt finns länkar till fler resurser om relaterade ämnen:

* Felsökning av apptjänst
* Felsökning i Visual Studio
* Fjärrfelsökning i Azure
* Spåra i ASP.NET-program
* Analysera webbserverloggar
* Analysera spårade loggar för misslyckade begäranden
* Felsöka molntjänster

### <a name="app-service-troubleshooting"></a>Felsökning av apptjänst
Mer information om felsökning av appar i Azure App Service finns i följande resurser:

* [Övervaka appar](web-sites-monitor.md)
* [Undersöka minnesläckor i Azure App Service med Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Microsoft ALM blogginlägg om Visual Studio funktioner för att analysera hanterade minnesproblem.
* [Onlineverktyg för Azure App Service som du bör känna till](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blogginlägg av Amit Apple.

Om du vill ha hjälp med en specifik felsökningsfråga startar du en tråd i något av följande forum:

* [Azure-forumet på ASP.NET webbplats](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Azure-forumet på Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Felsökning i Visual Studio
Mer information om hur du använder felsökningsläge i Visual Studio finns [i Felsökning i Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) och [felsökningstips med Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Fjärrfelsökning i Azure
Mer information om fjärrfelsökning för App Service-appar och WebJobs finns i följande resurser:

* [Introduktion till fjärrfelsökning av Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduktion till fjärrfelsökning av Azure App Service del 2 - Felsökning av fjärråtkomst](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduktion till fjärrfelsökning på Azure App Service del 3 - Multi-Instance miljö och GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Felsökning av WebJobs (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Om din app använder ett Azure Web API eller Mobile Services back-end och du behöver felsöka det läser du [Felsökning av .NET-serverdel i Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Spåra i ASP.NET-program
Det finns inga grundliga och aktuella introduktioner för att ASP.NET spårning tillgänglig på Internet. Det bästa du kan göra är att komma igång med gamla introduktionsmaterial skrivet för webbformulär eftersom MVC inte fanns ännu, och komplettera det med nyare blogginlägg som fokuserar på specifika frågor. Några bra ställen att börja på är följande resurser:

* [Övervakning och telemetri (Skapa verkliga molnappar med Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)<br>
  E-bok kapitel med rekommendationer för spårning i Azure molnprogram.
* [ASP.NET spårning](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Gammal men fortfarande en bra resurs för en grundläggande introduktion till ämnet.
* [Spåra lyssnare](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Information om spårningslyssnare men nämner inte [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Genomgång: Integrera ASP.NET spårning med System.Diagnostics Tracing](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Den här artikeln är också gammal, men innehåller ytterligare information som den inledande artikeln inte täcker.
* [Spårning i ASP.NET MVC Razor Views](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Förutom spårning i Rakknivsvyer förklarar inlägget också hur du skapar ett felfilter för att logga alla ohanterade undantag i ett MVC-program. Information om hur du loggar alla ohanterade undantag i ett webbformulärprogram finns i exemplet Global.asax i [Fullständigt exempel för felhanterare](/previous-versions/bb397417(v=vs.140)) på MSDN. Om du vill logga vissa undantag men låta standardbildshanteringen gälla för dem i MVC- eller webbformulär kan du fånga upp och återsvälta som i följande exempel:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Strömmande diagnostik spårning loggning från Azure Command Line (plus Glimt!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Hur man använder kommandoraden för att göra vad den här självstudien visar hur man gör i Visual Studio. [Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) är ett verktyg för felsökning ASP.NET program.
* [Använda Webbappar Loggning och diagnostik - med David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) och [Streaming Loggar från Web Apps - med David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videor av Scott Hanselman och David Ebbo.

För felloggning är ett alternativ till att skriva din egen spårningskod att använda ett loggningsramverk med öppen källkod, till exempel [ELMAH](https://nuget.org/packages/elmah/). För mer information, se [Scott Hanselmans blogginlägg om ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Du behöver inte heller använda ASP.NET eller `System.Diagnostics` spårning för att hämta strömmande loggar från Azure. Loggtjänsten för App Service-appen streamar alla *.html* *.txt,html*eller *.log-fil* som hittas i mappen *Loggfiler.* Därför kan du skapa ett eget loggningssystem som skriver till appens filsystem och filen strömmas och hämtas automatiskt. Allt du behöver göra är att skriva programkod som skapar filer i mappen *d:\home\logfiles.*

### <a name="analyzing-web-server-logs"></a>Analysera webbserverloggar
Mer information om hur du analyserar webbserverloggar finns i följande resurser:

* [LogParser (olikartade)](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Ett verktyg för att visa data i webbserverloggar (*.log-filer).*
* [Felsöka prestandaproblem eller programfel för IIS med LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  En introduktion till verktyget Logga parser som du kan använda för att analysera webbserverloggar.
* [Blogginlägg av Robert McMurray om hur du använder LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [HTTP-statuskoden i IIS 7.0, IIS 7.5 och IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analysera spårade loggar för misslyckade begäranden
Webbplatsen Microsoft TechNet innehåller avsnittet [Spårning av misslyckade begäranden](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) som kan vara användbart för att förstå hur du använder dessa loggar. Den här dokumentationen fokuserar dock främst på att konfigurera spårning av misslyckade begäranden i IIS, vilket du inte kan göra i Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
