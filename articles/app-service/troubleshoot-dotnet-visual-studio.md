---
title: Felsöka en app med Visual Studio – Azure App Service
description: Lär dig mer om att felsöka en App Service-app med fjärrfelsökning, spårning och loggningsverktyg som är inbyggda i Visual Studio 2013.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5e749f68aba48ac258363a0a03e3474e1e28b064
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876780"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Felsök en app i Azure App Service med Visual Studio
## <a name="overview"></a>Översikt
Den här självstudien visar hur du använder Visual Studio-verktyg för att felsöka en app i [Apptjänst](https://go.microsoft.com/fwlink/?LinkId=529714), genom att köra i [felsökningsläge](https://docs.microsoft.com/visualstudio/debugger/) via fjärranslutning eller genom att visa programloggar och webbserverloggar.

Du får lära dig:

* Vilka app-hanteringsfunktioner är tillgängliga i Visual Studio.
* Hur du använder Visual Studio fjärrvy för att göra snabba ändringar i en fjärransluten app.
* Så här kör du felsökningsläget via en fjärranslutning när ett projekt körs i Azure, både för en app och ett Webbjobb.
* Hur du skapar spårningsloggar för program och visa dem när programmet är att skapa dem.
* Så här visar du webbserverloggar, inklusive detaljerade felmeddelanden och spårning av misslyckade begäranden.
* Så här att skicka diagnostikloggar till Azure Storage-kontot och visa dem det.

Om du har Visual Studio Ultimate kan du också använda [IntelliTrace](https://msdn.microsoft.com/library/vstudio/dd264915.aspx) för felsökning. IntelliTrace ingår inte i den här självstudien.

## <a name="prerequisites"></a>Förhandskrav
Den här självstudien fungerar med utvecklingsmiljö, webbprojektet och App Service-app som du har konfigurerat i [skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md). För WebJobs-avsnitt behöver du det program som du skapar i [Kom igång med Azure WebJobs SDK][GetStartedWJ].

Kodexempel som visas i den här självstudien är för ett C# MVC-webbprogram, men åtgärderna för felsökning är desamma för Visual Basic och Web Forms-program.

Självstudien förutsätter att du använder Visual Studio 2017. 

Direktuppspelningsloggarna funktionen fungerar bara för program som är riktade till .NET Framework 4 eller senare.

## <a name="sitemanagement"></a>Konfiguration och hantering
Visual Studio har åtkomst till en delmängd av app-hanteringsfunktioner och konfigurationsinställningar som är tillgängliga i den [Azure-portalen](https://go.microsoft.com/fwlink/?LinkId=529715). I det här avsnittet får du se vad som är tillgängligt med hjälp av **Server Explorer**. Om du vill se de senaste funktionerna i Azure-integrering, prova **Cloud Explorer** också. Du kan öppna både windows från den **visa** menyn.

1. Om du inte redan loggat in till Azure i Visual Studio, högerklickar du på **Azure** och välja Anslut till **Microsoft Azure-prenumeration** i **Server Explorer**.

    Ett alternativ är att installera ett certifikat som ger åtkomst till ditt konto. Om du väljer att installera ett certifikat, högerklickar du på den **Azure** nod i **Server Explorer**, och välj sedan **hantera och filtrera prenumerationer** på snabbmenyn. I den **hantera Microsoft Azure-prenumerationer** dialogrutan klickar du på den **certifikat** fliken och klicka sedan på **Import**. Följ instruktionerna för att hämta och importera en prenumeration-fil (kallas även en *.publishsettings* fil) för din Azure-konto.

   > [!NOTE]
   > Om du hämtar en prenumerations-fil, spara den till en mapp utanför katalogerna källa kod (till exempel i mappen nedladdningar) och tar bort den när importen är klar. En obehörig användare som får åtkomst till prenumerationsfilen kan redigera, skapa och ta bort dina Azure-tjänster.
   >
   >

    Mer information om hur du ansluter till Azure-resurser från Visual Studio finns i [hantera konton, prenumerationer och administrativa roller](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. I **Server Explorer**, expandera **Azure** och expandera **Apptjänst**.
3. Expandera resursgruppen som innehåller den app som du skapade i [skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md), högerklicka på app-noden och klickar på **visningsinställningarna**.

    ![Visa inställningar i Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Den **Azure Web App** visas fliken och du kan se det app-hanterings- och uppgifter som är tillgängliga i Visual Studio.

    ![Azure Web App-fönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    I de här självstudierna ska du använda loggning och spårning av listrutorna. Du använder också fjärrfelsökning men du använder en annan metod för att aktivera den.

    Läs om hur rutorna Appinställningar och anslutningssträngar i det här fönstret [Azure App Service: Hur Programsträngar och anslutningen strängar Work](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Om du vill utföra en hanteringsaktivitet för app som inte kan utföras i det här fönstret klickar du på **öppna i hanteringsportalen** att öppna ett webbläsarfönster på Azure Portal.

## <a name="remoteview"></a>Komma åt appen filer i Server Explorer
Du normalt distribuerar ett webbprojekt med den `customErrors` flaggan i Web.config-filen som har angetts till `On` eller `RemoteOnly`, vilket innebär att du inte får felmeddelandet användbart när något går fel. För många fel är allt du får en sida som något av följande:

**Serverfel i programmet '/':**

![Exempelbild felsida](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ett fel uppstod:**

![Exempelbild felsida](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Webbplatsen kan inte visas på sidan**

![Exempelbild felsida](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Det enklaste sättet att hitta orsaken till felet är ofta att aktivera detaljerade felmeddelanden som först av de föregående skärmbilderna beskriver hur du gör. Som kräver en ändring i distribuerade Web.config-filen. Du kan redigera den *Web.config* filen i projektet och distribuera om projektet eller skapa en [Web.config-transformering](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) och distribuera en felsökningsversion, men det finns ett snabbare sätt: i **Solution Explorer** , du kan visa och redigera filer i appen fjärråtkomst med hjälp av direkt i *fjärrvy* funktionen.

1. I **Server Explorer**, expandera **Azure**, expandera **Apptjänst**, expandera resursgruppen som din app finns i, och sedan expanderar du noden för din app.

    Ser du noder som ger dig tillgång till appens innehållsfiler och loggfiler.
2. Expandera den **filer** noden och dubbelklickar på den *Web.config* fil.

    ![Öppna Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio öppnas Web.config-filen från remote app och visar [Remote] bredvid namnet på filen i namnlisten.
3. Lägg till följande rad i den `system.web` element:

    `<customErrors mode="Off"></customErrors>`

    ![Redigera Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Uppdatera webbläsaren som visar exempelbild felmeddelandet och nu du få ett detaljerat felmeddelande, till exempel i följande exempel:

    ![Detaljerat felmeddelande](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Felet som visas har skapats genom att lägga till den rad som visas i rött att *Views\Home\Index.cshtml*.)

Redigera filen Web.config är bara ett exempel på scenarier där möjligheten att läsa och redigera filer på App Service-appen gör felsökningen lättare.

## <a name="remotedebug"></a>Felsökning fjärrappar
Om det detaljerade felmeddelandet ger inte tillräckligt med information du kan inte återskapa felet lokalt, är ett annat sätt att felsöka att fjärrköra i felsökningsläge. Du kan ange brytpunkter, ändra minne direkt, gå igenom koden och även ändra kod sökvägen.

Fjärrfelsökning fungerar inte i Express-versioner av Visual Studio.

Det här avsnittet visar hur du felsöker med projekt som du skapar i [skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Öppna de webový projekt som du skapade i [skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md).

2. Öppna *Controllers\HomeController.cs*.

3. Ta bort den `About()` metoden och infoga följande kod i dess ställe.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```

4. [Konfigurera en brytpunkt](https://docs.microsoft.com/visualstudio/debugger/) på den `ViewBag.Message` rad.

5. I **Solution Explorer**, högerklicka på projektet och klicka på **publicera**.

6. I den **profil** listrutan, Välj den profil som du använde i [skapa en ASP.NET-app i Azure App Service](app-service-web-get-started-dotnet-framework.md). Klicka på inställningar.

7. I den **publicera** dialogrutan klickar du på den **inställningar** fliken och ändra sedan **Configuration** till **felsöka**, och klicka sedan på  **Spara**.

    ![Publicera i felsökningsläge](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Klicka på **Publicera**. Efter distributionen har slutförts och webbläsaren öppnar Azure-URL: en för din app, Stäng webbläsaren.

9. I **Server Explorer**, högerklicka på din app och klicka sedan på **bifoga felsökningsprogrammet**.

    ![Koppla felsökare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Webbläsaren öppnas automatiskt till din startsida som körs i Azure. Du kan behöva vänta 20 sekunder medan Azure konfigureras servern för felsökning. Den här fördröjningen sker bara första gången du kör i felsökningsläge i en app i 48 timmar. När du startar felsökning igen i samma period, det finns inte en fördröjning.

    > [!NOTE] 
    > Om du har problem med att starta felsökningsprogrammet försöker göra det med hjälp av **Cloud Explorer** i stället för **Server Explorer**.
    >

10. Klicka på **om** på menyn.

     Visual Studio stoppar på brytpunkten och koden körs i Azure, inte på den lokala datorn.

11. Hovra över den `currentTime` variabeln för att se time-värdet.

     ![Visa variabel i felsökningsläge som körs i Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Den tid som du ser är tiden i Azure-server som kan finnas i en annan tidszon än den lokala datorn.

12. Ange ett nytt värde för den `currentTime` variabel, till exempel ”nu körs i Azure”.

13. Tryck på F5 för att fortsätta köras.

     Om sidan som körs i Azure visar det nya värdet som du har angett i variabeln currentTime.

     ![Om sidan med nytt värde](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Fjärrfelsökning av WebJobs
Det här avsnittet visar hur du felsöker med projektet och app som du skapar i [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

De funktioner som visas i det här avsnittet är endast tillgänglig i Visual Studio 2013 Update 4 eller senare.

Fjärrfelsökning fungerar bara med kontinuerliga WebJobs. Schemalagda och på begäran WebJobs stöd inte för felsökning.

1. Öppna de webový projekt som du skapade i [Kom igång med Azure WebJobs SDK][GetStartedWJ].

2. Öppna i projektet ContosoAdsWebJob *Functions.cs*.

3. [Konfigurera en brytpunkt](https://docs.microsoft.com/visualstudio/debugger/) på den första instruktionen i det `GnerateThumbnail` metod.

    ![Ange brytpunkt](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. I **Solution Explorer**, högerklicka på webbprojektet (inte WebJob-projekt) och klicka på **publicera**.

5. I den **profil** listrutan, Välj den profil som du använde i [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klicka på den **inställningar** fliken och ändra **Configuration** till **felsöka**, och klicka sedan på **publicera**.

    Visual Studio distribuerar webb- och WebJob-projekt och webbläsaren öppnas på Azure-URL: en för din app.

7. I **Server Explorer**, expandera **Azure > App Service > din resursgrupp > din app > WebJobs > kontinuerlig**, högerklicka på **ContosoAdsWebJob**.

8. Klicka på **koppla felsökare**.

    ![Koppla felsökare](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Webbläsaren öppnas automatiskt till din startsida som körs i Azure. Du kan behöva vänta 20 sekunder medan Azure konfigureras servern för felsökning. Den här fördröjningen sker bara första gången du kör i felsökningsläge på en app i 48 timmar. När du startar felsökning igen i samma period, det finns inte en fördröjning.

9. Skapa en ny ad i webbläsare som öppnas till Contoso Ads-startsidan.

    Skapa en ad orsakar ett kömeddelande som ska skapas, som hämtas av Webbjobbet och behandlas. WebJobs SDK anropar när funktionen för att bearbeta kömeddelande kod träffar din brytpunkt.

10. När Felsökaren bryter på din brytpunkt, kan du granska och ändra variabelvärden medan programmet körs i molnet. I följande bild visar felsökningsprogrammet innehållet i objektet blobInfo som skickades till den `GenerateThumbnail` metoden.

     ![blobInfo-objekt på felsökare](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Tryck på F5 för att fortsätta köras.

     Den `GenerateThumbnail` metoden har skapat miniatyrbilden.

12. Uppdatera indexsidan i webbläsaren, och du se miniatyrbilden.

13. Tryck på SKIFT + F5 för att stoppa felsökningen i Visual Studio.

14. I **Server Explorer**, högerklicka på noden ContosoAdsWebJob och klickar på **Vyinstrumentpanel**.

15. Logga in med dina autentiseringsuppgifter för Azure och sedan på webbjobbsnamnet att gå till sidan för ditt WebJob.

     ![Klicka på ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Instrumentpanelen visar att den `GenerateThumbnail` funktioner kördes senast.

     (Nästa gång du klickar på **Vyinstrumentpanel**du inte behöver logga in och webbläsaren går direkt till sidan för ditt WebJob.)

16. Klicka på funktionsnamnet att se information om körning funktion.

     ![Funktionsinformation](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Om din funktion [skrev loggar](https://github.com/Azure/azure-webjobs-sdk/wiki), du kan klicka på **ToggleOutput** finns.

## <a name="notes-about-remote-debugging"></a>Information om fjärrfelsökning

* Kör i felsökningsläge i produktionen rekommenderas inte. Om din produktionsapp inte skalas ut till flera serverinstanser förhindrar felsökning webbservern svara på andra begäranden. Om du har flera webbserverinstanser, när du ansluter till felsökning kan du få en slumpmässig instans och finns det inget sätt att säkerställa att webbläsaren efterföljande begäranden går till samma instans. Dessutom vanligtvis distribueras inte en felsökningsversion till produktion och kompilatorn optimeringar för versionen versioner kan göra det omöjligt att visa vad som händer rad för rad i källkoden. För felsökning av produktionsproblem i, bästa resursen är program spårning och webbserverloggar.
* Undvika långa stopp vid brytpunkter när remote felsökning. Azure behandlar en process som har stoppats mer än ett par minuter som en process som inte svarar och stängs av.
* När du felsöker skickar servern data till Visual Studio, som kan påverka bandbreddsavgifter. Läs om hur bandbreddsavgifter [priser för Azure](https://azure.microsoft.com/pricing/calculator/).
* Se till att den `debug` attributet för den `compilation` elementet i den *Web.config* -filen har angetts till true. Det är inställt på true som standard när du publicerar en versionskonfiguration för felsökning.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Om du upptäcker att felsökningsprogrammet inte Stega in koden som du vill felsöka, kan du behöva ändra inställningen bara min kod.  Mer information finns i [ange om du vill felsöka endast användarkod med bara min kod i Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code).
* En timer börjar på servern när du aktiverar funktionen felsökning och efter 48 timmar funktionen inaktiveras automatiskt. Den här gränsen 48 timmar görs skäl för säkerhet och prestanda. Du kan enkelt aktivera funktionen igen så många gånger som helst. Vi rekommenderar dock inaktiveras när du inte aktivt felsöker.
* Du kan manuellt koppla felsökaren till en process, inte bara app-processen (w3wp.exe). Mer information om hur du använder felsökningsläge i Visual Studio finns i [felsökning i Visual Studio](https://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Översikt över diagnostikloggar
Ett ASP.NET-program som körs i en App Service-app kan skapa följande typer av loggar:

* **Spårningsloggar för program**<br/>
  Programmet skapar de här loggarna genom att anropa metoder i den [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) klass.
* **Webbserverloggar**<br/>
  Webbservern skapar en loggpost för varje HTTP-begäran till appen.
* **Detaljerade felloggar för meddelande**<br/>
  Webbservern skapar en HTML-sida med ytterligare information för misslyckade HTTP-förfrågningar (begäranden som resulterar i statuskod 400 eller större).
* **Det gick inte begära spårning av loggar**<br/>
  Webbservern skapar en XML-fil med detaljerad spårningsinformation för misslyckade HTTP-förfrågningar. Webbservern innehåller också en XSL-fil för att formatera XML-koden i en webbläsare.

Loggar påverkar prestanda, så Azure får du möjligheten att aktivera eller inaktivera varje typ av loggen efter behov. Programloggar, kan du ange att endast loggar över en viss nivå för allvarlighetsgrad ska skrivas. När du skapar en ny app som standard all loggning är inaktiverad.

Loggarna skrivs till filer i en *LogFiles* mapp i filsystemet för din app och är tillgängliga via FTP. Webbserverloggar och programloggarna kan också skrivas till ett Azure Storage-konto. Du kan behålla en större mängd loggar i ett storage-konto än vad som är möjligt i filsystemet. Du är begränsad till högst 100 megabyte loggar när du använder filsystemet. (Filen systemloggar är endast för kortsiktig kvarhållning. Azure tar bort gamla loggfiler för att göra plats för nya när gränsen har nåtts.)  

## <a name="apptracelogs"></a>Skapa och visa spårningsloggar för program
I det här avsnittet ska göra du följande uppgifter:

* Lägg till Spårningsuppgifter i webbprojektet som du skapade i [Kom igång med Azure och ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Visa loggarna när du kör projektet lokalt.
* Visa loggarna som de skapas av programmet som körs i Azure.

Information om hur du skapar program loggar i WebJobs finns i [hur du arbetar med Azure-kölagring med WebJobs-SDK - hur du skriver loggar](https://github.com/Azure/azure-webjobs-sdk/wiki). Följande instruktioner för att visa loggar och styra hur de lagras i Azure gäller också programloggar som skapats av WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Lägga till Spårningsuppgifter i programmet
1. Öppna *Controllers\HomeController.cs*, och Ersätt den `Index`, `About`, och `Contact` metoder med följande kod för att lägga till `Trace` instruktioner och en `using` instruktionen för `System.Diagnostics`:

```c#
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

2. Lägg till en `using System.Diagnostics;` instruktion högst upp i filen.

### <a name="view-the-tracing-output-locally"></a>Visa spårningsdata som lokalt
1. Tryck på F5 för att köra programmet i felsökningsläge.

    Standard trace listener skriver alla spårningsutdata till den **utdata** fönstret tillsammans med andra felsökningsresultat. Följande bild visar utdata från spårningsinstruktioner som du lade till den `Index` metoden.

    ![Spårning i felsökningsfönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Följande steg visar hur du visar spårningsinformation på en webbsida, utan att kompilera i felsökningsläge.
2. Öppna programmets Web.config-file (som finns i projektmappen) och Lägg till en `<system.diagnostics>` element i slutet av filen precis före avslutande `</configuration>` element:

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

Den `WebPageTraceListener` kan du visa spårningsutdata genom att bläddra till `/trace.axd`.
3. Lägg till en <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trace elementet</a> under `<system.web>` i Web.config-filen, till exempel i följande exempel:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Tryck på CTRL+F5 för att köra programmet.
5. I adressfältet i webbläsarfönstret, lägger du till *trace.axd* i Webbadressen och tryck på RETUR (URL: en liknar http://localhost:53370/trace.axd).
6. På den **programspårning** klickar du på **visa information om** på den första raden (inte BrowserLink rad).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Den **information om begäran** visas, och i den **spårningsinformation** avsnitt som du ser utdata från spårningsinstruktioner som du lade till den `Index` metoden.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Som standard `trace.axd` endast är tillgängligt lokalt. Om du vill göra den tillgänglig från en fjärransluten app kan du lägga till `localOnly="false"` till den `trace` elementet i den *Web.config* filen enligt i följande exempel:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Men att aktivera `trace.axd` i en app är rekommenderas inte av säkerhetsskäl. I följande avsnitt visas ett enklare sätt att läsa spårningsloggar i en App Service-app.

### <a name="view-the-tracing-output-in-azure"></a>Visa spårning av utdata i Azure
1. I **Solution Explorer**, högerklicka på webbprojektet och klickar på **publicera**.
2. I den **Publicera webbplats** dialogrutan klickar du på **publicera**.

    När Visual Studio publicerar uppdateringen, öppnas ett webbläsarfönster till din startsida (förutsatt att du inte rensa **mål-URL** på den **anslutning** fliken).
3. I **Server Explorer**, högerklicka på din app och välj **visa Streaming loggar**.

    ![Visa Streaming loggar i snabbmenyn](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    Den **utdata** visar att du är ansluten till loggströmningstjänsten och lägger till en meddelanderaden varje minut som går utan en logg ska visas.

    ![Visa Streaming loggar i snabbmenyn](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. I webbläsarfönstret som visar din startsida för programmet, klickar du på **Kontakta**.

    Inom några sekunder, spåra utdata från Felnivån du lade till den `Contact` metod visas i den **utdata** fönster.

    ![Fel vid spårning i utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio visar endast Felnivån spårningar eftersom det är standardinställningen när du aktiverar loggen övervakningstjänsten. När du skapar en ny App Service-app är all loggning inaktiverad som standard, som du fick när du har öppnat inställningssidan tidigare:

    ![Programmet utloggning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Men om du har valt **visa Streaming loggar**, Visual Studio automatiskt ändrats **programmet Logging(File System)** till **fel**, vilket innebär att Felnivån loggar Hämta rapporteras. Du kan ändra den här inställningen om du vill se alla dina spårningsloggar **utförlig**. När du väljer en allvarlighetsgrad som är lägre än fel rapporteras även alla loggar för högre nivåer av allvarlighetsgrad. Så när du väljer utförlig kan se du också information, varning och felloggar.  

5. I **Server Explorer**, högerklicka på appen och klicka sedan på **visningsinställningarna** som du gjorde tidigare.
6. Ändra **Programinloggning (filsystem)** till **utförlig**, och klicka sedan på **spara**.

    ![Inställningen spårningsnivån till utförlig](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. I webbläsarfönstret som visar nu din **Kontakta** klickar du på **Start**, klicka sedan på **om**, och klicka sedan på **Kontakta**.

    Inom några sekunder i **utdata** fönstret visar alla dina spårningsdata.

    ![Detaljerad spårningsinformation](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    I det här avsnittet ska du aktiverat och inaktiverat loggning genom att använda app-inställningar. Du kan också aktivera och inaktivera spårning lyssnare genom att ändra filen Web.config. Dock gör ändra Web.config-filen app-domänen att återvinna, även om att aktivera loggning via appkonfigurationen inte göra det. Om problemet tar lång tid att återskapa, eller är tillfälligt, återvinning app-domänen kan ”åtgärda” det och tvinga dig att vänta tills det händer igen. Aktivera diagnostik i Azure kan du börja samla in information om felet omedelbart utan återvinning app-domänen.

### <a name="output-window-features"></a>Utdata fönstret funktioner
Den **Microsoft Azure Logs** fliken den **utdata** fönstret har flera knappar och en textruta:

![Loggar fliken knappar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Dessa utför följande funktioner:

* Rensa den **utdata** fönster.
* Aktivera eller inaktivera automatiskt radbyte.
* Starta eller stoppa övervakningen av loggar.
* Ange som loggar in övervaka.
* Ladda ned loggar.
* Filterloggar baserat på en söksträng eller ett reguljärt uttryck.
* Stäng den **utdata** fönster.

Om du anger en söksträng eller reguljärt uttryck, filtrerar loggningsinformation på klienten i Visual Studio. Det innebär att du kan ange kriterier när loggarna visas i den **utdata** fönster och du kan ändra filtreringskriterier utan att behöva återskapa loggarna.

## <a name="webserverlogs"></a>Visa webbserverloggar
Webbserverloggar registrera alla HTTP-aktivitet för appen. Om du vill se dem i den **utdata** fönstret som du måste aktivera dem för appen och berätta att du vill övervaka dem för Visual Studio.

1. I den **Azure Webbappkonfigurationen** fliken som du öppnade från **Server Explorer**, ändra Web Server-loggning till **på**, och klicka sedan på **spara**.

    ![Aktivera webbserverloggning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. I den **utdata** fönstret klickar du på den **ange som Microsoft Azure loggar för att övervaka** knappen.

    ![Ange vilka Azure-loggar att övervaka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. I den **loggningsalternativ för Microsoft Azure** dialogrutan **Web serverloggar**, och klicka sedan på **OK**.

    ![Övervaka webbserverloggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. I webbläsarfönstret som visar appen klickar du på **Start**, klicka sedan på **om**, och klicka sedan på **Kontakta**.

    Programloggarna Allmänt visas först, följt av webbserverloggarna. Du måste kanske vänta en stund innan loggarna visas.

    ![Webbservern loggar i utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

När du först aktivera webbserverloggar med hjälp av Visual Studio som standard skriver loggarna till filsystemet Azure. Alternativt kan kan du använda Azure-portalen för att ange webbservern loggar ska skrivas till en blob-behållare i ett lagringskonto.

Om du använder portalen för att aktivera webbservern som loggning till ett Azure storage-konto och sedan inaktivera har loggning i Visual Studio när du återaktiverar loggning i Visual Studio inställningarna för ditt storage-konto återställts.

## <a name="detailederrorlogs"></a>Visa detaljerade felloggar för meddelande
Detaljerade felloggar ger ytterligare information om HTTP-begäranden som resulterar i fel svarskoder (400 eller senare). Om du vill se dem i den **utdata** fönstret så måste du aktivera dem för appen och berätta för Visual Studio som du vill övervaka dem.

1. I den **Azure Webbappkonfigurationen** fliken som du öppnade från **Server Explorer**, ändra **detaljerade felmeddelanden** till **på**, och sedan Klicka på **spara**.

    ![Aktivera detaljerade felmeddelanden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. I den **utdata** fönstret klickar du på den **ange som Microsoft Azure loggar för att övervaka** knappen.

3. I den **loggningsalternativ för Microsoft Azure** dialogrutan klickar du på **alla loggar**, och klicka sedan på **OK**.

    ![Övervaka alla loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. I adressfältet i webbläsarfönstret, lägger du till ett extra tecken i URL: en kan orsaka ett 404-fel (till exempel `http://localhost:53370/Home/Contactx`), och tryck på RETUR.

    Efter några sekunder visas detaljerad felloggen i Visual Studio **utdata** fönster.

    ![Detaljerad felloggen - utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    CTRL + klicka på länken om du vill visa loggutdata formaterade i en webbläsare:

    ![Detaljerad felloggen - fönster i webbläsaren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Ladda ned filen systemloggar
Alla loggar som du kan övervaka i den **utdata** fönster kan också hämtas som en *.zip* fil.

1. I den **utdata** fönstret klickar du på **hämta Streaming loggar**.

    ![Loggar fliken knappar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Utforskaren öppnas på din *hämtar* mappen med den hämta filen som valts.

    ![Nedladdade filen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrahera den *.zip* -filen, och se följande mappstrukturen:

    ![Nedladdade filen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Spårningsloggar för program som finns i *.txt* filer i den *LogFiles\Application* mapp.
   * Webbserverloggarna är i *.log* filer i den *LogFiles\http\RawLogs* mapp. Du kan använda ett verktyg som [Loggparser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) att visa och ändra dessa filer.
   * Detaljerade felloggar för meddelandet finns i *.html* filer i den *LogFiles\DetailedErrors* mapp.

    (Den *distributioner* mappen är för filer som skapas av källkontroll, publicera; inte är något som är kopplat till Visual Studio-publicering. Ju *Git* mappen är för spårningar som rör källkontroll publicering och loggen filen direktuppspelningstjänst.)  

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
## <a name="failedrequestlogs"></a>Visa loggar för spårning av misslyckade begäranden
Loggar för spårning av misslyckade begäranden är användbara när du behöver förstå information om hur IIS hanterar en HTTP-förfrågan i scenarier, t.ex problem med URL: en skriva om eller autentisering.

App Service-appar använda samma misslyckade begäranden spårning-funktioner som har varit tillgänglig med IIS 7.0 och senare. Du har inte åtkomst till de IIS-inställningar som konfigurerar vilka fel loggas, men. När du aktiverar spårning av misslyckade begäranden, som alla fel avbildas.

Du kan aktivera spårning av misslyckade begäranden med hjälp av Visual Studio, men du kan inte visa dem i Visual Studio. Dessa loggar är XML-filer. Strömmande Loggtjänsten övervakar endast filer som bedöms som öppnas i läget oformaterad text: *.txt*, *.html*, och *.log* filer.

Du kan visa loggar för spårning av misslyckade begäranden i en webbläsare direkt via FTP eller lokalt när du har använt ett FTP-verktyg för att ladda ned dem till din lokala dator. I det här avsnittet ska du visa dem i en webbläsare direkt.

1. I den **Configuration** fliken den **Azure Web App** fönster som du öppnade från **Server Explorer**, ändra **spårning av misslyckade begäranden** till **På**, och klicka sedan på **spara**.

    ![Aktivera spårning av misslyckade begäranden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Lägga till ett extra tecken i URL: en i adressfältet i webbläsarfönstret som visar appen, och klicka på RETUR för att utlösa ett 404-fel.

    Detta leder till en misslyckad förfrågan spårningsloggen som ska skapas och följande steg visar hur du visar eller ladda ned loggen.

3. I Visual Studio i den **Configuration** fliken den **Azure Web App** fönstret klickar du på **öppna i hanteringsportalen**.

4. I den [Azure-portalen](https://portal.azure.com) **inställningar** för din app och klicka på **distributionsbehörigheterna**, och ange sedan ett nytt användarnamn och lösenord.

    ![Ny FTP-användarnamn och lösenord](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Du måste använda det fullständiga användarnamnet med appnamn som föregår det när du loggar in. Exempel: Om du anger ”myid” som ett användarnamn och platsen är ”mittexempel” kan du logga in som ”myexample\myid”.
    >

5. I ett nytt webbläsarfönster går du till den URL som visas under **FTP-värdnamnet** eller **FTPS-värdnamn** i den **översikt** för din app.

6. Logga in med FTP-autentiseringsuppgifter som du skapade tidigare (inklusive app-namnprefixet för användarnamnet).

    Webbläsaren visar rotmappen för appen.

7. Öppna den *LogFiles* mapp.

    ![Öppna mappen LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Öppna mappen som heter W3SVC plus ett numeriskt värde.

    ![Öppna W3SVC-mapp](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Mappen innehåller XML-filer för eventuella fel som har loggats när du aktiverade spårning av misslyckade begäranden och en XSL-fil som kan använda för att formatera XML-filen i en webbläsare.

    ![W3SVC-mapp](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klicka på XML-filen för den misslyckade begäran som du vill se spårningsinformation för.

    Följande bild visar en del av spårningsinformationen för ett prov-fel.

    ![Spårning av misslyckade begäranden i webbläsare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Nästa steg
Du har sett hur Visual Studio gör det enkelt att visa loggar som skapats av en App Service-app. Följande avsnitt innehåller länkar till fler resurser på Närliggande ämnen:

* App Service-felsökning
* Felsökning i Visual Studio
* Fjärrfelsökning i Azure
* Spårning i ASP.NET-program
* Analysera webbserverloggar
* Det gick inte att analysera spårningsloggar för begäran
* Debugging Cloud Services

### <a name="app-service-troubleshooting"></a>App Service-felsökning
Mer information om felsökning av appar i Azure App Service finns i följande resurser:

* [Så här övervakar du appar](web-sites-monitor.md)
* [Undersöka minnesläckor i Azure App Service med Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Microsoft ALM blogginlägget om Visual Studio-funktioner för att analysera hanterade minnesproblem.
* [Azure App Service-webbverktyg som du bör känna till om](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blogginlägget Amit Apple.

Starta en tråd för hjälp med en specifik fråga för felsökning, i något av följande forum:

* [Azure-forumet på ASP.NET-webbplats](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Azure-forum på MSDN](https://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Felsökning i Visual Studio
Mer information om hur du använder felsökningsläge i Visual Studio finns i [felsökning i Visual Studio](https://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) och [felsökning Tips med Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Fjärrfelsökning i Azure
Mer information om fjärrfelsökning för App Service-appar och WebJobs finns i följande resurser:

* [Introduktion till fjärranslutna felsökning Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduktion till fjärranslutna felsökning Azure App Service en del 2 – i fjärrfelsökning](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduktion till fjärrfelsökning på Azure App Service-del 3 - miljö för flera instanser och GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs felsökning (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Om din app använder en Azure webb-API eller Mobile Services backend-server och du behöver att felsöka som finns i [felsökning .NET-serverdel i Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Spårning i ASP.NET-program
Det finns inga omfattande och uppdaterad introduktioner för ASP.NET-spårning på Internet. Det bästa du kan göra är Kom igång med gamla inledande material som är skrivna för Web Forms eftersom MVC inte finns ännu, och komplettera som med nyare blogg gör ett inlägg som fokuserar på specifika problem. Vissa lämpliga platser att starta finns i följande resurser:

* [Övervakning och telemetri (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-boken kapitel med rekommendationer för spårning i Azure molnprogram.
* [ASP.NET-spårning](https://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Gamla men fortfarande en bra resurs för en grundläggande introduktion till ämnet.
* [Spåra lyssnare](https://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Information om spårning lyssnare men inte nämner det [WebPageTraceListener](https://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Genomgång: Knyts System.Diagnostics spårning av ASP.NET-spårning](https://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Den här artikeln är också gamla, men innehåller ytterligare information som inte tas upp den inledande artikeln.
* [Spårning i ASP.NET MVC Razor-vyer](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Utöver spårning i Razor-vyer förklarar också hur du skapar ett fel-filter för att logga alla ohanterade undantag i ett MVC-program i inlägget. Information om hur du loggar in alla ohanterade undantag i ett Web Forms-program finns i exemplet Global.asax i [komplett exempel för felhanterare](https://msdn.microsoft.com/library/bb397417.aspx) på MSDN. I MVC eller webbformulär om du vill logga vissa undantag, men låt standardramverk hantering av gälla för dem, kan du fånga upp och skicka tillbaka som i följande exempel:

``` c#
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

* [Strömmande diagnostik spårningsloggning från Azure-kommandorad (plus glimt!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Hur du använder kommandoraden för att göra vad den här självstudien visar hur du gör i Visual Studio. [Glimt](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) är ett verktyg för felsökning av ASP.NET-program.
* [Med Web Apps-loggning och diagnostik – med David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) och [Direktuppspelningsloggar från Web Apps - med David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videor med Scott Hanselman och David Ebbo.

För felloggning, är ett alternativ till att skriva egen kod för spårning av att använda ett loggningsramverk för av öppen källkod som [ELMAH](https://nuget.org/packages/elmah/). Mer information finns i [Scott Hanselman blogginlägg om ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Dessutom behöver du inte använder ASP.NET eller `System.Diagnostics` spårning för att hämta streaming loggar från Azure. App Service-appen streaming Loggtjänsten strömmar någon *.txt*, *.html*, eller *.log* filen som hittas i den *LogFiles* mapp. Därför kan du kan skapa dina egna system för loggning som skriver till filsystemet för appen och din fil strömmas och ned automatiskt. Allt du behöver göra är att skriva koden som skapar filer i den *d:\home\logfiles* mapp.

### <a name="analyzing-web-server-logs"></a>Analysera webbserverloggar
Mer information om att analysera webbserverloggar finns i följande resurser:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Ett verktyg för att visa data i webbserverloggar (*.log* filer).
* [Felsöka IIS prestandaproblem eller fel i program med hjälp av LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  En introduktion till verktyget Parser för loggar som du kan använda för att analysera webbserverloggar.
* [Blogginlägg av Robert McMurray om hur du använder LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [HTTP-statuskod i IIS 7.0, IIS 7.5 och IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Det gick inte att analysera spårningsloggar för begäran
Webbplatsen Microsoft TechNet innehåller en [med hjälp av spårning av misslyckade begäranden](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) avsnittet, vilket kan vara till hjälp för att förstå hur du använder dessa loggar. Men fokuserar den här dokumentationen främst information om hur du konfigurerar spårning av misslyckade begäranden i IIS, som du inte kan göra i Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
