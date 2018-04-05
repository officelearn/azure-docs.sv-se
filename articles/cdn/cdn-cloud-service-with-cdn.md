---
title: Integrera en Azure-molntjänst med Azure CDN | Microsoft Docs
description: Lär dig hur du distribuerar en molnbaserad tjänst som hanterar innehåll från en integrerad Azure CDN-slutpunkt
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f131eb021d85766f12b0fb6cb8b5a07f965f9c97
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="intro"></a> Integrera en tjänst i molnet med Azure CDN
En tjänst i molnet kan integreras med Azure CDN betjänar allt innehåll från Molntjänsten plats. Den här metoden ger följande fördelar:

* Enkelt distribuera och uppdatera avbildningar, skript och matmallar i din molntjänst projekt kataloger
* Lätt att uppgradera NuGet-paket i din molntjänst, t.ex jQuery eller Bootstrap versioner
* Hantera ditt webbprogram och din CDN-hanterat innehåll alla från samma Visual Studio-gränssnitt
* Arbetsflöde för enhetlig distribution för webbprogrammet och din CDN-hanterat innehåll
* Integrera ASP.NET paketering och minification med Azure CDN

## <a name="what-you-will-learn"></a>Vad får du lära dig
I den här självstudien får du lära dig hur man:

* [Integrera Azure CDN-slutpunkten med Molntjänsten och hantera statiskt innehåll i webbsidor från Azure CDN](#deploy)
* [Konfigurera inställningar för cachelagring för statiskt innehåll i Molntjänsten](#caching)
* [Hantera innehåll från domänkontrollanten åtgärder via Azure CDN](#controller)
* [Fungera tillsammans och minified innehåll via Azure CDN samtidigt som skriptet upplevelse i Visual Studio-felsökning](#bundling)
* [Konfigurera reserv skripten och CSS när Azure CDN är offline](#fallback)

## <a name="what-you-will-build"></a>Vad du ska skapa
Du distribuerar en cloud service-webbroll med ASP.NET MVC-mallen, lägga till kod för att hantera innehåll från en integrerad Azure CDN som en bild, domänkontrollant åtgärd resultat och standard JavaScript och CSS-filer, och även skriva kod för att konfigurera reserven mekanism för paket hanteras i händelse av att CDN är offline.

## <a name="what-you-will-need"></a>Vad du behöver
Den här självstudiekursen har följande krav:

* En aktiv [Microsoft Azure-konto](/account/)
* Visual Studio 2015 med [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> Du behöver ett Azure-konto för att kunna slutföra den här guiden:
> 
> * Du kan [öppna ett Azure-konto gratis](https://azure.microsoft.com/pricing/free-trial/) – du får kredit du kan använda för att testa Azure-betaltjänster och även när de används upp kan du behålla kontot och använda kostnadsfria Azure-tjänster, till exempel Websites.
> * Du kan [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -din MSDN-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>Distribuera en tjänst i molnet
I det här avsnittet ska du distribuera standard mall för ASP.NET MVC-program i Visual Studio 2015 till en cloud service-webbroll och integrera den med en ny CDN-slutpunkt. Följ anvisningarna nedan:

1. I Visual Studio 2015, skapar du en ny Azure-molntjänst på menyraden genom att gå till **Arkiv > Nytt > Projekt > molntjänster > Azure Cloud Service**. Ge det ett namn och klicka på **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Välj **ASP.NET Web Role** och klicka på den **>** knappen. Klicka på OK.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Välj **MVC** och på **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Nu publicera den här webbroll till en Azure-molntjänst. Högerklicka på molntjänstprojektet och välj **publicera**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Om du ännu inte signerats i Microsoft Azure klickar du på den **Lägg till ett konto...**  listrutan och klicka på den **Lägg till ett konto** menyalternativet.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. Logga in med Microsoft-konto som du använde för att aktivera din Azure-konto på sidan logga in.
7. När du har loggat in, klickar du på **nästa**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Förutsatt att du inte har skapat ett cloud service eller storage-konto kan du skapa både i Visual Studio. I den **skapa Molntjänsten och kontot** dialogrutan Skriv önskade tjänstens namn och välj sedan önskad region. Klicka på **Skapa**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. Kontrollera konfigurationen i inställningssidan publicera och klickar på **publicera**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > Publiceringsprocessen för molntjänster tar lång tid. Den aktivera webbdistribution för alla roller alternativet kan göra felsökning Molntjänsten mycket snabbare genom att tillhandahålla snabb (men tillfälliga) uppdateringar till Web-roller. Mer information om det här alternativet, se [publicering av en tjänst i molnet med hjälp av Azure-verktyg](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    När den **Microsoft Azure-aktivitetsloggen** ser att Publiceringsstatus **slutförd**, skapar du en CDN-slutpunkt som är integrerad med den här Molntjänsten.
   
   > [!WARNING]
   > Om distribuerad Molntjänsten visas ett felmeddelande när du har publicerat, är förmodligen eftersom den molntjänst som du har distribuerat en [gästen OS som inte innehåller .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Du kan undvika det här problemet genom att [distribuerar .NET 4.5.2 som en startåtgärd](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil
En CDN-profil är en samling CDN-slutpunkter.  Varje profil innehåller en eller flera CDN-slutpunkter.  Du kanske vill använda flera profiler för att organisera dina CDN-slutpunkter efter Internetdomän, webbapp eller andra kriterier.

> [!TIP]
> Om du redan har en CDN-profil som du vill använda för den här självstudiekursen, fortsätter du till [skapa en ny CDN-slutpunkt](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt
**Skapa en ny CDN-slutpunkt för ditt lagringskonto**

1. Gå till din CDN-profil på [Azure-portalen](https://portal.azure.com).  Du kanske fäste den på instrumentpanelen i föregående steg.  Om du inte hittar den klicka du på **Bläddra**, sedan på **CDN-profiler** och sedan på den profil som du vill lägga till slutpunkten till.
   
    Bladet för CDN-profilen visas.
   
    ![CDN-profil][cdn-profile-settings]
2. Klicka på knappen **Lägg till slutpunkt**.
   
    ![Knappen Lägg till slutpunkt][cdn-new-endpoint-button]
   
    Bladet **Lägg till slutpunkt** visas.
   
    ![Bladet Lägg till slutpunkt][cdn-add-endpoint]
3. Ange ett **namn** för den här CDN-slutpunkten.  Det här namnet används för att komma åt dina cachelagrade resurser i domänen `<EndpointName>.azureedge.net`.
4. I den **typ av ursprung** listrutan *Molntjänsten*.  
5. I den **ursprungsvärdnamn** listrutan, Välj din molntjänst.
6. Lämnar du standardinställningarna för **ursprungssökväg**, **ursprungsvärdadress**, och **protokollet ursprung port**.  Du måste ange minst ett protokoll (HTTP eller HTTPS).
7. Klicka på knappen **Lägg till** för att skapa en ny slutpunkt.
8. När slutpunkten har skapats visas den i en lista över slutpunkter för profilen. I listvyn visas bara URL:en som ska användas för att komma åt cachelagrat innehåll, samt ursprungsdomänen.
   
    ![CDN-slutpunkt][cdn-endpoint-success]
   
   > [!NOTE]
   > Slutpunkten blir omedelbart inte tillgängliga för användning.  Det kan ta upp till 90 minuter för registreringen ska spridas via nätverket CDN. Användare som försöker använda domännamnet CDN direkt får statuskod 404 tills innehållet är tillgängligt via CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Testa CDN-slutpunkten
När Publiceringsstatus är **slutförd**, öppna ett webbläsarfönster och navigera till **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. URL: en är i min installationsprogrammet:

    http://camservice.azureedge.net/Content/bootstrap.css

Som motsvarar ursprung följande URL i CDN-slutpunkten:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

När du navigerar till **http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, beroende på din webbläsare, uppmanas du att hämta eller öppna bootstrap.css som följde från ditt publicerade webbprogram.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

På samma sätt kan du komma åt valfri offentligt tillgänglig URL på **http://*&lt;serviceName >*.cloudapp.net/**rak från CDN-slutpunkten. Exempel:

* En .js-fil i sökvägen/Script
* Varje innehållsfil från /Content sökväg
* En domänkontrollant/åtgärd
* Om frågesträngen är aktiverad på en URL med frågesträngar din CDN-slutpunkten

Med konfigurationen ovan kan du i själva verket värd hela Molntjänsten från **http://*&lt;cdnName >*.azureedge.net/**. Om jag navigerar till **http://camservice.azureedge.net/**, jag åtgärd resultatet från Home-Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Detta innebär inte, men att det alltid är en bra idé att hantera en hela molntjänst via Azure CDN. 

En CDN med statiska leveransoptimering inte nödvändigtvis snabba upp överföringen av dynamisk tillgångar som inte är avsedda att cachelagras eller uppdateras väldigt ofta eftersom CDN måste hämta en ny version av tillgången från den ursprungliga servern ofta. I det här scenariot kan du aktivera [dynamiska plats Acceleration](cdn-dynamic-site-acceleration.md) optimering (DSA) på din CDN-slutpunkt som använder olika metoder för att påskynda överföringen av ej Cacheable ställs dynamiska tillgångar. 

Om du har en plats med en blandning av statiska och dynamiska innehåll kan välja du att hantera din statiskt innehåll från CDN med en statisk optimering typ (till exempel Internet leverans) och för att hantera dynamiskt innehåll direkt från den ursprungliga servern eller via ett CDN-slutpunkten w : te DSA optimering aktiverat-fall till fall. Du har redan sett hur du kommer åt enskilda innehållsfiler från CDN-slutpunkten för detta ändamål. Jag visas hur du har en specifik domänkontrollant åtgärd via en viss CDN-slutpunkt i fungera innehåll från domänkontrollanten åtgärder via Azure CDN.

Alternativt är att avgöra vilket innehåll som fungerar från Azure CDN för fall till fall i Molntjänsten. Du har redan sett hur du kommer åt enskilda innehållsfiler från CDN-slutpunkten för detta ändamål. Jag visar hur att hantera en specifik domänkontrollant åtgärd via CDN-slutpunkten i [innehåll från domänkontrollanten åtgärder via Azure CDN](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Konfigurera cachelagringsalternativ för statiska filer i Molntjänsten
Du kan ange hur du vill att statiskt innehåll cachelagras i CDN-slutpunkten med Azure CDN-integration i Molntjänsten. Det gör du genom att öppna *Web.config* från din webbroll projekt (t.ex. WebRole1) och Lägg till en `<staticContent>` elementet så att `<system.webServer>`. XML-koden nedan konfigurerar cache för att gå ut inom tre dagar.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

När du gör detta kommer alla statiska filer i din molntjänst se samma regel i CDN-cachen. För mer detaljerad kontroll över inställningar för cachelagring för att lägga till en *Web.config* till en mapp och Lägg till dina inställningar. Till exempel lägga till en *Web.config* filen till den *\Content* mapp och Ersätt innehållet med följande XML-filen:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Den här inställningen gör att alla statiska filer från den *\Content* mappen cachelagras för 15 dagar.

Mer information om hur du konfigurerar den `<clientCache>` element, se [klientcachen &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

I [innehåll från domänkontrollanten åtgärder via Azure CDN](#controller), jag visas också hur du kan konfigurera inställningar för cachelagring för domänkontrollant åtgärden resulterar i CDN-cachen.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Hantera innehåll från domänkontrollanten åtgärder via Azure CDN
Det är relativt enkelt att hantera innehåll från domänkontrollanten åtgärder via Azure CDN när du integrerar en cloud service-webbroll med Azure CDN. Tjänsten direkt via Azure CDN (som visas ovan) än betjänar ditt moln [Maarten Balliauw](https://twitter.com/maartenballiauw) visar hur du gör det med ett roligt MemeGenerator domänkontrollant i [minskar svarstiden på webben med Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Jag kommer att återskapa den här.

Anta att i ditt moln tjänsten som du vill generera memes baserat på ett barn Chuck Norris bild (foto av [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) så här:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Du har en enkel `Index` åtgärd som gör att kunder kan ange superlativ i bilden, sedan genererar meme när de gör ett inlägg till åtgärden. Eftersom det är Chuck Norris förväntar du dig den här sidan ska bli wildly populära globalt. Detta är ett bra exempel på betjänar delvis dynamiskt innehåll med Azure CDN.

Följ stegen ovan för att konfigurera den här åtgärden för domänkontrollant:

1. I den *\Controllers* mapp, skapa en ny .cs-fil som kallas *MemeGeneratorController.cs* och Ersätt innehållet med följande kod. Se till att ersätta den markerade delen med CDN-namn.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Högerklicka i standard `Index()` och väljer **Lägg till vy**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Godkänner inställningarna nedan och klickar på **Lägg till**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Öppna den nya *Views\MemeGenerator\Index.cshtml* och Ersätt innehållet med följande enkel HTML-koden för att skicka superlativ:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Publicera Molntjänsten igen och gå till **http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** i webbläsaren.

När du skickar formulärvärden till `/MemeGenerator/Index`, `Index_Post` åtgärdsmetod returnerar en länk till den `Show` åtgärdsmetod med respektive inkommande identifierare. När du klickar på länken kommer du till följande kod:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Om din lokala felsökare, får du regelbundet debug-upplevelse med en lokal omdirigering. Om den körs i Molntjänsten ska den omdirigera till:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Som motsvarar följande ursprung URL på CDN-slutpunkten:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Du kan sedan använda den `OutputCacheAttribute` attributet för den `Generate` metod för att ange hur åtgärden resultatet ska cachelagras, som hanterar Azure CDN. Koden nedan ange en giltighetstid för cache 1 timme (3 600 sekunder).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

På samma sätt kan du hantera innehåll från något domänkontrollant i Molntjänsten via din Azure CDN med alternativet för önskad cachelagring.

I nästa avsnitt hur I du hantera paketerade och minified skript och CSS via Azure CDN.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrera ASP.NET paketering och minification med Azure CDN
Skript och CSS matmallar ändras sällan och är särskilda kandidater för Azure CDN-cachen. Betjänar hela webbrollen via Azure CDN är det enklaste sättet att integrera paketering och minification med Azure CDN. Dock som du inte kanske vill göra detta, visas I hur du göra det när bevarar önskade develper upplevelsen av ASP.NET paketering och minification, som:

* Bra debug problem
* Effektiv distribution
* Omedelbar uppdateringar till klienter för skriptet/CSS versionsuppgraderingar
* Fallback mekanism när CDN-slutpunkten misslyckas
* Minimera kodändring

I den **WebRole1** projekt som du skapade i [integrera Azure CDN-slutpunkten med din Azure-webbplatsen och hantera statiskt innehåll i webbsidor från Azure CDN](#deploy)öppnar *App_Start\ BundleConfig.cs* och ta en titt på den `bundles.Add()` metodanrop.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

Först `bundles.Add()` instruktionen lägger till ett skript-paket på den virtuella katalogen `~/bundles/jquery`. Öppna *Views\Shared\_Layout.cshtml* att se hur paket skripttypen återges. Du ska kunna hitta Razor följande kodrad:

    @Scripts.Render("~/bundles/jquery")

När den här Razor-koden körs i Azure-webbroll blir en `<script>` tagg för paket-skriptet liknar följande:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Men när det körs i Visual Studio genom att skriva `F5`, den kommer att visas varje skriptfilen i paketet individuellt (i fallet endast en skriptfil finns i paketet):

    <script src="/Scripts/jquery-1.10.2.js"></script>

På så sätt kan du felsöka JavaScript-kod i din utvecklingsmiljö medan vilket minskar antalet samtidiga klientanslutningar (paketering) och förbättra filen hämta prestanda (minification) i produktion. Det är en bra funktion för att bevara med Azure CDN-integration. Dessutom eftersom renderade paketet innehåller redan en automatiskt genererad versionssträng måste du vill replikera funktionen därför när du uppdaterar din jQuery version via NuGet den kan uppdateras på klientsidan så snart som möjligt.

Följ stegen nedan för att integration ASP.NET paketering och minification med CDN-slutpunkten.

1. Tillbaka i *App_Start\BundleConfig.cs*, ändra den `bundles.Add()` metoder för att använda en annan [paket konstruktorn](http://msdn.microsoft.com/library/jj646464.aspx), som anger en CDN-adress. Om du vill göra detta måste du ersätta den `RegisterBundles` metoddefinition med följande kod:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Se till att ersätta `<yourCDNName>` med namnet på Azure CDN.
   
    Inställningen i klartext, `bundles.UseCdn = true` och lagt till en noggrant utformad CDN-URL i varje paket. Till exempel första konstruktorn i koden:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    är samma som:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Den här konstruktorn visar ASP.NET paketering och minification att återge enskilda filer när felsöks lokalt men använda den angivna CDN-adressen till skriptet i fråga. Observera dock två viktiga egenskaper med den här noggrant utformad CDN-URL:
   
   * Ursprung för denna CDN-URL är `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, vilket är att den virtuella katalogen för skript-paket i Molntjänsten.
   * Eftersom du använder en CDN-konstruktorn innehåller skripttypen CDN för paket inte längre skapas automatiskt Versionsträngen i den återgivna URL: en. Manuellt måste du generera en unik versionssträng varje gång skriptet paketet ändras för att tvinga en cache-miss på Azure CDN. Unik versionssträngen måste vara konstant via livslängd för distributionen för att maximera cacheträffar vid Azure CDN när paketet har distribuerats på samma gång.
   * Frågesträngen v = < W.X.Y.Z > hämtar från *Properties\AssemblyInfo.cs* i din webbrollsprojektet. Du kan ha ett arbetsflöde för distribution som innehåller ökar Sammansättningsversionen varje gång du publicerar till Azure. Eller, du kan bara ändra *Properties\AssemblyInfo.cs* i projektet att räkna upp Versionsträngen automatiskt varje gång du skapar kan använda jokertecknet ' *'. Exempel:
     
        [assembly: AssemblyVersion("1.0.0.*")]
     
     Andra strategi att förenkla Generera en unik sträng för livslängd för en distribution som fungerar här.
2. Publicera Molntjänsten och gå till hemsidan.
3. Visa HTML-koden för sidan. Du ska kunna se CDN-URL som renderas med en unik versionssträng varje gång du publicerar ändringarna på Molntjänsten. Exempel:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. I Visual Studio debug Molntjänsten i Visual Studio genom att skriva `F5`.,
5. Visa HTML-koden för sidan. Du kommer fortfarande att se varje skriptfilen individuellt återges så att du får en konsekvent debug upplevelse i Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Fallback mekanism för CDN-URL: er
När Azure CDN-slutpunkten misslyckas av någon anledning, vill webbsidan vara smart att komma åt webbservern ursprung som återställningsalternativ för inläsning av JavaScript eller starttjänsten. Det är tillräckligt allvarligt för att förlora bilder på webbplatsen eftersom det inte CDN finns, men mycket allvarligare förlorar avgörande sidan funktionalitet som tillhandahålls av skripten och formatmallar.

Den [paket](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) klassen innehåller en egenskap som kallas [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) som gör att du kan konfigurera återställningsplats mekanismen för CDN-fel. Följ stegen nedan om du vill använda den här egenskapen:

1. Öppna i din webbrollsprojektet *App_Start\BundleConfig.cs*, där du lade till en CDN-URL i varje [paket konstruktorn](http://msdn.microsoft.com/library/jj646464.aspx), och gör följande markerade ändringar att lägga till återställningsplats mekanism till standardinställningarna paket:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    När `CdnFallbackExpression` är null, skript är injekteras i HTML för att testa om paketet har lästs in och, om inte, komma åt paketet direkt från webbservern ursprung. Den här egenskapen måste anges till ett JavaScript-uttryck testar om respektive CDN-paketet har lästs in korrekt. Det uttryck som behövs för att testa varje paket skiljer sig enligt innehållet. För standard paket ovan:
   
   * `window.jquery` har definierats i jquery-{version} .js
   * `$.validator` har definierats i jquery.validate.js
   * `window.Modernizr` har definierats i modernizer-{version} .js
   * `$.fn.modal` har definierats i bootstrap.js
     
     Du kanske har lagt märke till att jag inte har angett CdnFallbackExpression för den `~/Cointent/css` paket. Detta beror på att det finns för närvarande en [programfel i System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) som lägger in en `<script>` taggen för återställningsplats CSS i stället för den förväntade `<link>` tagg.
     
     Det är emellertid en bra [Style paket reserv](https://github.com/EmberConsultingGroup/StyleBundleFallback) erbjuds av [Ember samråd grupp](https://github.com/EmberConsultingGroup).
2. Om du vill använda lösningen för CSS, skapa en ny .cs-fil i din webbrollsprojektet *App_Start* mapp med namnet *StyleBundleExtensions.cs*, och Ersätt innehållet med den [kod från GitHub ](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. I *App_Start\StyleFundleExtensions.cs*, byta namn på namnområdet till din webbroll namn (t.ex. **WebRole1**).
4. Gå tillbaka till `App_Start\BundleConfig.cs` och ändra senaste `bundles.Add` instruktion med följande markerade koden:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Den här nya tilläggsmetoden använder samma idé att mata in skriptet i HTML för att kontrollera DOM för den en matchande klassnamn och Regelnamn regeln värdet som definierats i CSS-paket och faller tillbaka till webbservern ursprung om det inte går att hitta matchningen.
5. Publicera Molntjänsten igen och gå till hemsidan.
6. Visa HTML-koden för sidan. Du ska hitta inmatat skript liknar följande:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Observera att inmatat skript för paket-CSS fortfarande innehåller felaktiga kvarleva från den `CdnFallbackExpression` egenskapen i rad:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Men eftersom den första delen av den. uttrycket returnerar alltid true (på rad ovanför som), funktionen document.write() aldrig ska köras.

## <a name="more-information"></a>Mer information
* [Översikt över Azure Content Delivery Network (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Med hjälp av Azure CDN](cdn-create-new-endpoint.md)
* [ASP.NET paketering och Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
