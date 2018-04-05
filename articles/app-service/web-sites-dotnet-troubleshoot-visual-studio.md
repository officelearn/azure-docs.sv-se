---
title: Felsöka en webbapp i Azure App Service med Visual Studio
description: Lär dig hur du felsöker ett Azure-webbapp med hjälp av fjärrfelsökning, spårning och loggningsverktyg som är inbyggda Visual Studio 2013.
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
ms.openlocfilehash: 7973f4311095b7c87ccd2394b048ec92c50f32a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Felsöka en webbapp i Azure App Service med Visual Studio
## <a name="overview"></a>Översikt
Den här kursen visar hur du använder Visual Studio tools för att felsöka en webbapp i [Apptjänst](http://go.microsoft.com/fwlink/?LinkId=529714), genom att köra i [felsökningsläge](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) via fjärranslutning eller genom att visa programloggarna samt webbserverloggar.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Du får lära dig:

* Vilka Azure web app-hanteringsfunktioner är tillgängliga i Visual Studio.
* Hur du använder Visual Studio Fjärrvisning för att göra ändringar i en fjärransluten webbapp.
* Så här kör du felsökningsläget via fjärranslutning när ett projekt körs i Azure, både för en webbapp och ett Webbjobb.
* Hur du skapar programloggar och visa dem när programmet skapar dem.
* Så här visar du webbserverloggar, inklusive detaljerade felmeddelanden och spårning av misslyckade begäranden.
* Så att skicka diagnostikloggar till ett Azure Storage-konto och visa dem det.

Om du har Visual Studio Ultimate kan du också använda [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) för felsökning. IntelliTrace ingår inte i den här självstudiekursen.

## <a name="prerequisites"></a>Förhandskrav
Den här kursen fungerar med utvecklingsmiljö, webbprojektet och Azure-webbapp som du ställer in i [Kom igång med Azure och ASP.NET][GetStarted]. För avsnitten WebJobs behöver du det program som du skapar i [Kom igång med Azure WebJobs SDK][GetStartedWJ].

Kodexempel som visas i den här kursen är för ett webbprogram i C# MVC, men felsökning procedurerna är samma för Visual Basic och Web Forms-program.

Kursen förutsätter att du använder Visual Studio 2017. 

Direktuppspelningsloggar funktionen fungerar bara för program som är riktade till .NET Framework 4 eller senare.

## <a name="sitemanagement"></a>Hantering och konfiguration av Web app
Visual Studio har åtkomst till en delmängd av web app-hanteringsfunktioner och konfigurationsinställningar som är tillgängliga i den [Azure-portalen](http://go.microsoft.com/fwlink/?LinkId=529715). I det här avsnittet får du se vad som är tillgängligt med hjälp av **Server Explorer**. Om du vill se de senaste funktionerna i Azure-integrering testa **Cloud Explorer** också. Du kan öppna både windows från den **visa** menyn.

1. Om du inte redan är inloggad till Azure i Visual Studio högerklickar du på **Azure** och väljer Anslut till **Microsoft Azure-prenumeration** i **Server Explorer**.

    Ett alternativ är att installera ett certifikat som ger åtkomst till ditt konto. Om du väljer att installera ett certifikat, högerklickar du på den **Azure** nod i **Server Explorer**, och välj sedan **hantera och Filter prenumerationer** på snabbmenyn. I den **hantera Azure-prenumerationer** dialogrutan klickar du på den **certifikat** fliken och klicka sedan på **importera**. Följ instruktionerna för att hämta och sedan importera en prenumeration-fil (kallas även en *.publishsettings* fil) för din Azure-konto.

   > [!NOTE]
   > Om du hämtar en prenumerations-fil, spara den till en mapp utanför katalogerna källa kod (till exempel i mappen hämtade filer) och tar bort den när importen är klar. En obehörig användare som får åtkomst till prenumerationsfilen kan du redigera, skapa och ta bort Azure-tjänster.
   >
   >

    Mer information om hur du ansluter till Azure-resurser från Visual Studio finns [hantera konton, prenumerationer och administrativa roller](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. I **Server Explorer**, expandera **Azure** och expandera **Apptjänst**.
3. Expandera resursgruppen som innehåller den webbapp som du skapade i [skapa en ASP.NET-webbapp i Azure][app-service-web-get-started-dotnet.md], högerklicka på noden web app och klickar på **visningsinställningarna**.

    ![Visa inställningar i Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Den **Azure Web App** visas och du kan se det web app hantering och konfiguration uppgifter som är tillgängliga i Visual Studio.

    ![Azure Web App-fönster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    I den här kursen ska du använda loggning och spårning i listrutorna. Du använder också fjärrfelsökning men du använder en annan metod för att aktivera den.

    Information om rutorna App-inställningar och anslutningssträngar i det här fönstret finns [Azure Web Apps: hur programmet strängar och anslutningen strängar arbete](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Om du vill utföra en hanteringsaktivitet för webb-app som inte kan göras i det här fönstret klickar du på **öppna i hanteringsportalen** att öppna ett webbläsarfönster på Azure-portalen.

## <a name="remoteview"></a>Komma åt web app filer i Server Explorer
Normalt distribuerar ett webbprojekt med den `customErrors` flaggan i Web.config-filen som har angetts till `On` eller `RemoteOnly`, vilket betyder att du inte får en bra felmeddelande vid något går fel. För många fel är alla du får en sida som något av de följande:

**Serverfel i programmet '/':**

![Unhelpful felsida](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ett fel uppstod:**

![Unhelpful felsida](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Webbplatsen kan inte visa sidan**

![Unhelpful felsida](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Det enklaste sättet att ta reda på orsaken till felet är ofta att aktivera detaljerade felmeddelanden som först av föregående skärmbilderna beskriver hur du gör. Som kräver en ändring i distribuerade Web.config-filen. Du kan redigera den *Web.config* filen i projektet och distribuera projektet eller skapa en [Web.config-transformering](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) och distribuera en felsökningsversion, men det finns ett snabbare sätt: i **Solution Explorer** , du kan visa och redigera filer i remote webbapp med hjälp av direkt i *fjärrvy* funktion.

1. I **Server Explorer**, expandera **Azure**, expandera **Apptjänst**, expandera den resursgrupp som ditt webbprogram finns i och expandera sedan noden för din webbapp.

    Du ser noder som ger dig tillgång till webbappens innehållsfiler och loggfiler.
2. Expandera den **filer** nod och dubbelklicka på den *Web.config* fil.

    ![Öppna Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio öppnas Web.config-filen från remote webbapp och visar [Remote] bredvid filnamnet i namnlisten.
3. Lägg till följande rad i den `system.web` element:

    `<customErrors mode="Off"></customErrors>`

    ![Redigera Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Uppdatera webbläsaren som visas i unhelpful felmeddelandet och nu du få ett detaljerat felmeddelande, till exempel i följande exempel:

    ![Detaljerat felmeddelande](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Fel visas har skapats genom att lägga till den rad som visas i rött till *Views\Home\Index.cshtml*.)

Redigera filen Web.config är bara ett exempel på scenarier där möjligheten att läsa och redigera filer på Azure-webbapp gör felsökningen lättare.

## <a name="remotedebug"></a>Fjärråtkomst felsökning webbprogram
Om det detaljerade felmeddelandet inte ger tillräcklig information, och du kan inte återskapa felet lokalt, är ett annat sätt att felsöka fjärrköras i felsökningsläge. Du kan ange brytpunkter, ändra minne direkt, stega igenom koden och även ändra koden sökvägen.

Fjärrfelsökning fungerar inte i Express-versioner av Visual Studio.

Det här avsnittet visar hur du felsöka med projektet som du skapar i [skapa en ASP.NET-webbapp i Azure](app-service-web-get-started-dotnet.md).

1. Öppna webbprojekt som du skapade i [skapa en ASP.NET-webbapp i Azure](app-service-web-get-started-dotnet.md).

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
4. [Ange en brytpunkt](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) på den `ViewBag.Message` rad.

5. I **Solution Explorer**, högerklicka på projektet och klicka på **publicera**.

6. I den **profil** listrutan, Välj den profil som du använde i [skapa en ASP.NET-webbapp i Azure](app-service-web-get-started-dotnet.md). Klicka på inställningar.

7. I den **publicera** dialogrutan klickar du på den **inställningar** fliken och ändra sedan **Configuration** till **felsöka**, och klicka sedan på  **Spara**.

    ![Publicera i felsökningsläge](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Klicka på **Publicera**. Efter distributionen är klar och webbläsaren öppnar Azure-URL för ditt webbprogram, Stäng webbläsaren.

9. I **Server Explorer**, högerklicka på ditt webbprogram och klicka sedan på **koppla felsökare**.

    ![Koppla felsökare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Webbläsaren öppnas automatiskt till din startsida som körs i Azure. Du kan behöva vänta 20 sekunder eller liknande medan Azure konfigurerar servern för felsökning. Den här fördröjningen sker bara den första gången du kör i felsökningsläge på ett webbprogram i 48 timmar. När du startar felsökningen igen i samma period, det finns inte en fördröjning.

    > [!NOTE] 
    > Om du har några problem med att starta felsökningsprogrammet försök att göra det med hjälp av **Cloud Explorer** i stället för **Server Explorer**.
    >

10. Klicka på **om** på menyn.

     Visual Studio stoppar på brytpunkten och koden körs i Azure, inte på den lokala datorn.

11. Hovra över den `currentTime` variabeln för att se time-värdet.

     ![Visa variabeln i felsökningsläge som körs i Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Den tid som du ser är tiden i Azure-server som kan vara i en annan tidszon än den lokala datorn.

12. Ange ett nytt värde för den `currentTime` variabel, till exempel ”nu körs i Azure”.

13. Tryck på F5 för att fortsätta att köras.

     Om sidan som körs i Azure visar det nya värdet som du angett i variabeln currentTime.

     ![Om sidan med nytt värde](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Fjärråtkomst felsökning WebJobs
Det här avsnittet visar hur du felsöka med hjälp av projektet och webb-app som du skapar i [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Funktioner som visas i det här avsnittet är endast tillgänglig i Visual Studio 2013 med Update 4 eller senare.

Fjärrfelsökning fungerar bara med kontinuerliga Webbjobb. Schemalagda och på begäran WebJobs saknar stöd för felsökning.

1. Öppna webbprojekt som du skapade i [Kom igång med Azure WebJobs SDK][GetStartedWJ].

2. Öppna i projektet ContosoAdsWebJob *Functions.cs*.

3. [Ange en brytpunkt](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) på den första satsen i den `GnerateThumbnail` metoden.

    ![Ange brytpunkt](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. I **Solution Explorer**, högerklicka webbprojektet (inte projektet Webbjobb) och på **publicera**.

5. I den **profil** listrutan, Välj den profil som du använde i [Kom igång med Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Klicka på den **inställningar** fliken och ändra **Configuration** till **felsöka**, och klicka sedan på **publicera**.

    Visual Studio distribuerar webb- och Webbjobb projekt och webbläsaren öppnar Azure-URL för ditt webbprogram.

7. I **Server Explorer**, expandera **Azure > Apptjänst > resursgruppen > ditt webbprogram > WebJobs > kontinuerlig**, högerklicka på **ContosoAdsWebJob**.

8. Klicka på **koppla felsökare**.

    ![Koppla felsökare](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Webbläsaren öppnas automatiskt till din startsida som körs i Azure. Du kan behöva vänta 20 sekunder eller liknande medan Azure konfigurerar servern för felsökning. Den här fördröjningen sker bara den första gången du kör i felsökningsläge på ett webbprogram i 48 timmar. När du startar felsökningen igen i samma period, det finns inte en fördröjning.

9. Skapa en ny annons i webbläsare som öppnas till Contoso Ads-startsidan.

    Skapa en ad gör ett kömeddelande skapas som tas upp av Webbjobbet och behandlas. WebJobs SDK anropar när funktionen för att bearbeta kömeddelande kod träffar din brytpunkt.

10. När Felsökaren bryter vid din brytpunkt, kan du granska och ändra variabelvärden medan programmet körs i molnet. I följande bild visar felsökningsprogrammet innehållet i objektet blobInfo som skickades till den `GenerateThumbnail` metoden.

     ![blobInfo objekt i Felsökning](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Tryck på F5 för att fortsätta att köras.

     Den `GenerateThumbnail` metoden har skapat miniatyrbilden.

12. Uppdatera indexsidan och du ser miniatyren i webbläsaren.

13. Tryck på SKIFT + F5 för att stoppa felsökning i Visual Studio.

14. I **Server Explorer**, högerklicka på noden ContosoAdsWebJob och klickar på **visa instrumentpanelen**.

15. Logga in med dina autentiseringsuppgifter för Azure och klicka på webbjobbsnamnet att gå till sidan för din Webbjobb.

     ![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Instrumentpanelen visar att den `GenerateThumbnail` funktion som kördes nyligen.

     (Nästa gång du klickar på **visa instrumentpanelen**du inte behöver logga in och webbläsaren går direkt till sidan för din Webbjobb.)

16. Klicka på namnet på funktionen för att se information om funktionen körningen.

     ![Funktionsinformation](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Om din funktion [skrev loggar](https://github.com/Azure/azure-webjobs-sdk/wiki), du kan klicka på **ToggleOutput** se dem.

## <a name="notes-about-remote-debugging"></a>Information om fjärrfelsökning

* Du bör inte köra i felsökningsläge i produktion. Om ditt webbprogram för produktion inte skalas ut att flera serverinstanser förhindrar felsökning webbservern svarar på andra begäranden. Om du har flera web server-instanser, när du kopplar till felsökningsprogrammet, du får en slumpmässig instans och det finns inget sätt att säkerställa att webbläsaren efterföljande förfrågningar går du till samma instans. Dessutom du normalt distribuerar inte en felsökningsversion för produktion och kompileraren optimeringar för versionen versioner kan göra det omöjligt att visa vad som händer rad för rad i källkoden. För att felsöka problem i produktionen bästa resurs är program spårning och web server-loggar.
* Undvika lång stopp vid brytpunkter när remote felsökning. Azure behandlar en process som har stoppats för längre än några minuter som en process för svarar inte och stängs av.
* När du felsöker skickar servern data till Visual Studio som kan påverka kostnader för bandbredd. Information om bandbredd priser finns [priser för Azure](https://azure.microsoft.com/pricing/calculator/).
* Se till att den `debug` attribut för den `compilation` element i den *Web.config* fil har angetts till true. Den är inställd på true som standard när du publicerar en debug build-konfiguration.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Om du upptäcker att felsökaren inte gå till den kod som du vill felsöka kan behöva du ändra inställningen bara min kod.  Mer information finns i [begränsa gå till bara min kod](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* En timer startar på servern när du aktiverar funktionen felsökning och efter 48 timmar funktionen inaktiveras automatiskt. Den här gränsen 48 timmar görs grund för säkerhet och prestanda. Du kan enkelt aktivera funktionen igen så många gånger. Vi rekommenderar att du lämnar inaktiverad när du inte aktivt felsökning.
* Du kan manuellt koppla felsökaren till en process, inte bara web app processen (w3wp.exe). Mer information om hur du använder felsökningsläge i Visual Studio finns [felsökning i Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Översikt över diagnostikloggar
Ett ASP.NET-program som körs i en Azure-app kan skapa följande typer av loggar:

* **Programmet spårningsloggar**<br/>
  Programmet skapar de här loggarna genom att anropa metoder i den [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) klass.
* **Webbserverloggarna**<br/>
  Webbservern skapar en loggpost för varje HTTP-begäran till webbprogrammet.
* **Detaljerade felloggar för meddelandet**<br/>
  Webbservern skapar en HTML-sida med ytterligare information för den misslyckade HTTP-begäranden (begäranden som resulterar i statuskod: 400 eller högre).
* **Det gick inte begäran spårningsloggar**<br/>
  Webbservern skapar en XML-fil med detaljerad spårningsinformation för misslyckade begäranden för HTTP. Webbservern innehåller också en XSL-fil om du vill formatera XML-filen i en webbläsare.

Loggning påverkar prestandan för web app, så Azure ger dig möjlighet att aktivera eller inaktivera varje typ av loggen vid behov. Du kan ange att endast loggar över en viss nivå för allvarlighetsgrad ska skrivas för programloggar. När du skapar ett nytt webbprogram som standard alla loggning har inaktiverats.

Loggarna skrivs till filer i en *loggfiler* mapp i filsystemet för din webbapp och är tillgänglig via FTP. Webbserverloggarna- och programloggarna kan också vara skriven till ett Azure Storage-konto. Du kan behålla en större volym loggar i ett lagringskonto än vad som är möjligt i filsystemet. Du är begränsad till högst 100 megabyte loggar när du använder filsystemet. (Filen systemloggar är endast för kortsiktig kvarhållning. Azure tar bort gamla loggfiler för att göra plats för nya när gränsen har nåtts.)  

## <a name="apptracelogs"></a>Skapa och visa spårningsloggar för programmet
I det här avsnittet kan göra du följande:

* Lägg till spårning instruktioner i webbprojektet som du skapade i [Kom igång med Azure och ASP.NET][GetStarted].
* Visa loggarna när du kör projektet lokalt.
* Visa loggar som de genereras av det program som körs i Azure.

För information om hur du skapar program som loggar in WebJobs, se [hur du arbetar med Azure queue storage med WebJobs-SDK - hur du skriver loggar](https://github.com/Azure/azure-webjobs-sdk/wiki). Följande instruktioner för att visa loggar och kontrollera hur de lagras i Azure gäller även för programloggarna som skapats av WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Lägga till spårning instruktioner för programmet
1. Öppna *Controllers\HomeController.cs*, och Ersätt den `Index`, `About`, och `Contact` metoder med följande kod för att lägga till `Trace` instruktioner och en `using` -instruktion för `System.Diagnostics`:

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

2. Lägg till en `using System.Diagnostics;` uttryck överst i filen.

### <a name="view-the-tracing-output-locally"></a>Visa spårning utdata lokalt
1. Tryck på F5 för att köra programmet i felsökningsläge.

    Standard spårningslyssnaren skriver alla spårningsutdata till den **utdata** fönster, tillsammans med andra felsökningsresultat. Följande bild visar utdata från trace-satser som du lade till den `Index` metoden.

    ![Spårning i felsökningsfönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Följande steg visar hur du kan visa spårningsinformationen i en webbsida, utan att kompilera i felsökningsläge.
2. Öppna filen Web.config för programmet (det finns i projektmappen) och Lägg till en `<system.diagnostics>` element i slutet av filen precis före avslutande `</configuration>` element:

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
3. Lägg till en <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elementet trace</a> under `<system.web>` i filen Web.config, som i följande exempel:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Tryck på CTRL+F5 för att köra programmet.
5. Lägg till i adressfältet i webbläsarfönstret *trace.axd* till URL och tryck på RETUR (URL: en liknar http://localhost:53370/trace.axd).
6. På den **programmet Trace** klickar du på **visa information om** på den första raden (inte BrowserLink rad).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Den **begär information** visas, och i den **spårningsinformation** avsnitt visas utdata från trace-satser som du lade till den `Index` metoden.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Som standard `trace.axd` endast är tillgängligt lokalt. Om du vill göra den tillgänglig från en fjärransluten webbprogram, kan du lägga till `localOnly="false"` till den `trace` element i den *Web.config* -fil, som visas i följande exempel:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Men att aktivera `trace.axd` i en produktionsmiljö web app inte rekommenderas av säkerhetsskäl. I följande avsnitt ser du ett enklare sätt att läsa spårningsloggar i en Azure webbapp.

### <a name="view-the-tracing-output-in-azure"></a>Visa spårning utdata i Azure
1. I **Solution Explorer**, högerklicka webbprojektet och på **publicera**.
2. I den **Publicera webbplats** dialogrutan klickar du på **publicera**.

    När Visual Studio publicerar uppdateringen, öppnas ett webbläsarfönster på hemsidan (förutsatt att du inte avmarkera **Måladress** på den **anslutning** fliken).
3. I **Server Explorer**, högerklicka på ditt webbprogram och välj **visa strömning loggfiler**.

    ![Visa strömning loggfiler i snabbmenyn](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    Den **utdata** fönster visar att du är ansluten till tjänsten log-direktuppspelning och lägger till en Meddelanderad varje minut som går utan en logg för att visa.

    ![Visa strömning loggfiler i snabbmenyn](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. I webbläsarfönstret som visar startsidan program klickar du på **Kontakta**.

    Inom några sekunder Spåra utdata från Felnivån du lägga till den `Contact` metoden visas i den **utdata** fönster.

    ![Felspårning i utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio bara visar Felnivån spårningar eftersom det är standardinställningen när du aktiverar loggen övervakningstjänsten. När du skapar en ny Azure webbapp är all loggning inaktiverat som standard, som du såg när du har öppnat inställningssidan tidigare:

    ![Programmet logga ut](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Men om du har valt **visa strömning loggfiler**, Visual Studio automatiskt ändras **programmet Logging(File System)** till **fel**, vilket innebär att Felnivån loggar Hämta rapporterade. För att se alla dina spårningsloggar, du kan ändra inställningen till **utförlig**. När du väljer en allvarlighetsgrad som är lägre än fel rapporteras också alla loggar för högre nivåer av allvarlighetsgrad. Så när du väljer utförlig kan se du också information, varning och fel.  

1. I **Server Explorer**, högerklickar du på webbprogrammet och klickar sedan på **visningsinställningarna** som du gjorde tidigare.
2. Ändra **programloggning (filsystem)** till **utförlig**, och klicka sedan på **spara**.

    ![Anger spårningsnivå utförlig](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. I webbläsarfönstret som nu visas ditt **Kontakta** klickar du på **Start**, klicka på **om**, och klicka sedan på **Kontakta**.

    Inom några sekunder den **utdata** fönster visas alla spårning-utdata.

    ![Utförlig spårningsutdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    I det här avsnittet, aktiverat och inaktiverat loggning genom att använda Azure web app-inställningar. Du kan också aktivera och inaktivera Samlingsspårlyssnarna genom att ändra filen Web.config. Dock gör ändra filen Web.config tillämpningsdomän att återvinna, när du aktiverar loggning via webbappkonfigurationen inte göra det. Om problemet tar lång tid att återskapa, eller är tillfälligt, återvinning tillämpningsdomänen kan ”åtgärdas” och göra så att vänta tills det sker igen. Aktivera diagnostik i Azure kan du börja samla in information om fel omedelbart utan återvinning tillämpningsdomänen.

### <a name="output-window-features"></a>Utdata fönstret funktioner
Den **Microsoft Azure loggar** för den **utdata** fönstret har flera knappar och en textruta:

![Loggar fliken knappar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Dessa utför följande funktioner:

* Avmarkera den **utdata** fönster.
* Aktivera eller inaktivera automatisk radbyte.
* Starta eller stoppa övervakningen av loggar.
* Ange vilket loggar för att övervaka.
* Hämta loggarna.
* Filtrera loggarna baserat på en söksträng eller ett reguljärt uttryck.
* Stäng den **utdata** fönster.

Om du anger en söksträng eller det reguljära uttrycket filtrerar logginformation på klienten i Visual Studio. Det innebär att du kan ange kriterier när loggarna visas i den **utdata** fönstret och du kan ändra filtervillkoren utan att behöva återskapa loggarna.

## <a name="webserverlogs"></a>Visa webbserverloggar
Webbserverloggarna registrera alla HTTP-aktivitet för webbprogrammet. För att kunna se dem i den **utdata** fönster som du måste aktivera dem för webbappen och berätta att du vill övervaka dem för Visual Studio.

1. I den **Azure Web App Configuration** fliken som du har öppnat från **Server Explorer**, ändra Web Server-loggning till **på**, och klicka sedan på **spara**.

    ![Aktivera web server-loggning](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. I den **utdata** -fönstret klickar du på den **ange som Microsoft Azure loggar för att övervaka** knappen.

    ![Ange vilka Azure loggar för att övervaka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. I den **loggalternativ för Microsoft Azure** dialogrutan **Web server-loggar**, och klicka sedan på **OK**.

    ![Övervaka webbserverloggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. I webbläsarfönstret som visar webbappen klickar du på **Start**, klicka på **om**, och klicka sedan på **Kontakta**.

    Programloggarna vanligtvis visas först, följt av web server-loggar. Du måste kanske vänta en stund innan loggarna visas.

    ![Webbservern loggar i utdatafönstret](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

När du aktiverar webbserverloggar med hjälp av Visual Studio som standard skriver loggarna till filsystemet Azure. Alternativt kan använda du Azure-portalen för att ange webbservern loggar ska skrivas till en blob-behållare i ett lagringskonto.

Om du använder portalen till webbservern loggar till ett Azure storage-konto och sedan inaktivera har loggning i Visual Studio när du återaktivera loggning i Visual Studio kontoinställningarna lagring återställts.

## <a name="detailederrorlogs"></a>Visa detaljerade felloggar för meddelandet
Detaljerade felloggar ger ytterligare information om HTTP-förfrågningar som resulterar i svaret felkoder (400 eller senare). För att kunna se dem i den **utdata** och du måste aktivera dem för webbappen och berätta att du vill övervaka dem för Visual Studio.

1. I den **Azure Web App Configuration** fliken som du har öppnat från **Server Explorer**, ändra **detaljerade felmeddelanden** till **på**, och sedan Klicka på **spara**.

    ![Aktivera detaljerade felmeddelanden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. I den **utdata** -fönstret klickar du på den **ange som Microsoft Azure loggar för att övervaka** knappen.

3. I den **loggalternativ för Microsoft Azure** dialogrutan klickar du på **alla loggar**, och klicka sedan på **OK**.

    ![Övervaka alla loggar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. I adressfältet i webbläsarfönstret, lägga till ett extra tecken i URL: en kan orsaka ett 404-fel (till exempel `http://localhost:53370/Home/Contactx`), och tryck på RETUR.

    Efter några sekunder visas detaljerad fellogg i Visual Studio **utdata** fönster.

    ![Detaljerad fellogg - utdata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    CTRL + klicka på länken om du vill se loggutdata formaterad i en webbläsare:

    ![Detaljerad fellogg - fönster i webbläsaren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Hämta filen systemloggar
Loggar som du kan övervaka i den **utdata** fönster kan också hämtas som en *.zip* fil.

1. I den **utdata** -fönstret klickar du på **hämta strömning loggar**.

    ![Loggar fliken knappar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Utforskaren öppnar din *hämtar* mappen med den hämta filen som valts.

    ![Hämtade filen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrahera den *.zip* fil och du ser följande mappstrukturen:

    ![Hämtade filen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Programmet spårningsloggar finns i *.txt* filer i den *LogFiles\Application* mapp.
   * Webbserverloggarna är i *.log* filer i den *LogFiles\http\RawLogs* mapp. Du kan använda ett verktyg som [Loggparser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) visa och manipulera filerna.
   * Meddelande för detaljerade felloggar finns i *.html* filer i den *LogFiles\DetailedErrors* mapp.

    (Den *distributioner* mappen är för filer som skapas av källkontrollen publicering; den inte har något rör publicering med Visual Studio. Den *Git* mappen är för spår som rör källkontrollen publicering och loggen filen tjänst för liveströmning.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

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

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Visa spårningsloggar för misslyckade begäranden
Loggar för spårning av misslyckade begäranden är användbara när du behöver förstå information om hur IIS hanterar en HTTP-begäran i scenarier, till exempel skriva om eller autentisering problem med URL: en.

Azure webbappar som använder samma misslyckade begäranden spårning-funktioner som har varit tillgänglig med IIS 7.0 och senare. Du har inte åtkomst till IIS-inställningar som konfigurerar vilka fel får loggas men. När du aktiverar spårning av misslyckade begäranden fångas alla fel.

Du kan aktivera spårning av misslyckade begäranden med hjälp av Visual Studio, men du kan inte visa dem i Visual Studio. Dessa loggar är XML-filer. Strömmande Loggtjänsten övervakar endast filer som bedöms vara läsbar i läget oformaterad text: *.txt*, *.html*, och *.log* filer.

Du kan visa spårningsloggar för misslyckade begäranden i en webbläsare direkt via FTP- eller lokalt efter att använda en FTP-verktyget för att ladda ned dem till den lokala datorn. I det här avsnittet ska du visa dem i en webbläsare direkt.

1. I den **Configuration** för den **Azure Web App** fönster som du öppnade från **Server Explorer**, ändra **spårning av misslyckade begäranden** till **På**, och klicka sedan på **spara**.

    ![Aktivera spårning av misslyckade begäranden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Lägg till ett extra tecken i URL: en i adressfältet i webbläsarfönstret som visar webbprogrammet, och klicka på RETUR om du vill få ett 404-fel.

    Detta leder till en misslyckad begäran spårningsloggen skapas och följande steg visar hur du visar eller hämta loggen.

3. I Visual Studio, i den **Configuration** för den **Azure Web App** fönstret klickar du på **öppna i hanteringsportalen**.

4. I den [Azure-portalen](https://portal.azure.com) **inställningar** för ditt webbprogram, klickar du på **distributionsbehörigheterna**, och ange sedan ett nytt användarnamn och lösenord.

    ![Ny FTP-användarnamn och lösenord](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Du måste använda det fullständiga användarnamnet med webbprogramnamnet som prefix till den när du loggar in. Till exempel om du anger ”myid” som ett användarnamn och platsen är ”mittexempel” kan logga du in som ”myexample\myid”.
    >

5. Gå till den URL som visas under i ett nytt webbläsarfönster **FTP-värdnamn** eller **FTPS hostname** i den **översikt** för ditt webbprogram.

6. Logga in med hjälp av FTP-autentiseringsuppgifter som du skapade tidigare (inklusive web app namnprefixet för användarnamnet).

    Webbläsaren visar rotmappen på webbprogrammet.

7. Öppna den *loggfiler* mapp.

    ![Öppna loggfiler mapp](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Öppna mappen som heter W3SVC plus ett numeriskt värde.

    ![Öppna W3SVC-mappen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Mappen innehåller XML-filer för eventuella fel som loggas när du har aktiverat spårning av misslyckade begäranden och en XSL-fil som en webbläsare kan använda för att formatera XML.

    ![W3SVC-mappen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klicka på XML-filen för den misslyckade begäran som du vill visa spårningsinformation för.

    Följande bild visar en del av spårningsinformation för ett prov-fel.

    ![Spårning av misslyckade begäranden i webbläsare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Nästa steg
Du har sett hur Visual Studio gör det enkelt att visa loggar som skapats av en Azure-webbapp. Följande avsnitt innehåller länkar till fler resurser i Närliggande ämnen:

* Felsökning av Azure web app
* Felsökning i Visual Studio
* Fjärr-felsökning i Azure
* Spårning i ASP.NET-program
* När webbserverloggar
* Det gick inte att analysera spårningsloggar för begäran
* Felsökning av molntjänster

### <a name="azure-web-app-troubleshooting"></a>Felsökning av Azure web app
Mer information om hur du felsöker web apps i Azure App Service finns i följande resurser:

* [Så här övervakar du webbprogram](/manage/services/web-sites/how-to-monitor-websites/)
* [Undersöka minnesläckor i Azure Webbappar med Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Microsoft ALM blogginlägget om Visual Studio-funktioner för att analysera hanterade minnesproblem.
* [Azure web apps online verktyg du bör känna till om](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Blogginlägget Amit Apple.

Starta en tråd i något av följande forum för hjälp med en specifik felsökning fråga:

* [Azure-forum på webbplatsen ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Azure-forum på MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Felsökning i Visual Studio
Mer information om hur du använder felsökningsläge i Visual Studio finns [felsökning i Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) och [felsökning Tips med Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Fjärr-felsökning i Azure
Mer information om fjärrfelsökning för Azure-webbappar och WebJobs finns i följande resurser:

* [Introduktion till felsökning Azure App Service Web Apps Remote](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduktion till fjärranslutna felsökning Azure App Service Web Apps del 2 - inuti fjärrfelsökning](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduktion till fjärrfelsökning i Azure App Service Web Apps del 3 - miljö med flera instanser och GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs felsökning (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Om ditt program använder ett Azure Web API eller Mobile Services backend- och du behöver felsöka som finns [felsökning .NET-serverdel i Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Spårning i ASP.NET-program
Det finns inga omfattande och uppdaterad introduktioner för ASP.NET-spårning på Internet. Det bästa som du kan göra är Kom igång med gamla inledande material som skrivits för Web Forms eftersom MVC inte finns ännu och tillägg som med nyare blogg Överför till överföringswebbadressen som fokuserar på specifika problem. Vissa bra sätt att börja finns i följande resurser:

* [Övervakning och telemetri (skapa verkliga Molnappar med Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-bok kapitel med rekommendationer för spårning i Azure-molnprogram.
* [ASP.NET-spårning](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Gamla men fortfarande en bra resurs för en grundläggande introduktion till ämnet.
* [Samlingsspårlyssnarna](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Information om Samlingsspårlyssnarna men inte nämna de [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Genomgång: Integrera ASP.NET-spårning med System.Diagnostics spårning](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Den här artikeln finns även gamla men innehåller ytterligare information som inledande artikeln inte täcker.
* [Spårning i ASP.NET MVC Razor vyer](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Utöver spårning i Razor vyer Lär efter dig också att skapa ett filter för fel för att logga alla ohanterat undantag i ett MVC-program. Information om hur du loggar alla ohanterade undantag i ett Web Forms-program finns i Global.asax exemplet i [komplett exempel felhanterare](http://msdn.microsoft.com/library/bb397417.aspx) på MSDN. I MVC eller Web Forms om du vill logga vissa undantag, men låt standardvärdet ramen som hanterar börja gälla för dem, att fånga och skicka tillbaka som i följande exempel:

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

* [Strömmande diagnostik spårningsloggning från Azure Command Line (plus uppfattning!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Använda kommandoraden för att göra vad den här kursen visar hur du gör i Visual Studio. [Uppfattning](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) är ett verktyg för felsökning av ASP.NET-program.
* [Med Web Apps-loggning och diagnostik - med David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) och [Direktuppspelningsloggar från Web Apps - med David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videor av Scott hanselman, så får och David Ebbo.

För felloggning, i stället för att skriva egen kod spårning är att använda ett loggningsramverk med öppen källkod som [ELMAH](http://nuget.org/packages/elmah/). Mer information finns i [Scott Hanselman blogginlägg om ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Dessutom behöver du inte använda ASP.NET eller `System.Diagnostics` spårning för att hämta strömning loggar från Azure. Azure-webbappen strömning Loggtjänsten strömmar någon *.txt*, *.html*, eller *.log* filen som hittas i den *loggfiler* mapp. Därför kan du skapa egna loggning system som skriver till filsystemet för webbappen och filen strömmas och hämtas automatiskt. Allt du behöver göra är att skriva programkod som skapar filer i den *d:\home\logfiles* mapp.

### <a name="analyzing-web-server-logs"></a>När webbserverloggar
Mer information om hur du analyserar webbserverloggar finns i följande resurser:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Ett verktyg för att visa data i webbserverloggar (*.log* filer).
* [Felsöka IIS prestandaproblem och programfel med LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  En introduktion till verktyget Loggparser som du kan använda för att analysera webbloggar för servern.
* [Blogginlägg av Robert McMurray med hjälp av LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [HTTP-statuskod i IIS 7.0, IIS 7.5 och IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Det gick inte att analysera spårningsloggar för begäran
Webbplatsen Microsoft TechNet innehåller en [med hjälp av spårning av misslyckade begäranden](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) avsnittet, vilket kan vara till hjälp för att förstå hur du använder dessa loggar. Men fokuserar den här dokumentationen huvudsakligen om att konfigurera spårning av misslyckade begäranden i IIS, som inte är möjligt i Azure Web Apps.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
