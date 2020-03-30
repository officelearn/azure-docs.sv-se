---
title: Använd konfiguration per miljö
titleSuffix: Azure App Configuration
description: Använda etiketter för att ange konfigurationsvärden per miljö
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056807"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Använda etiketter för att aktivera olika konfigurationer för olika miljöer

Många program måste använda olika konfigurationer för olika miljöer. Anta att ett program har ett konfigurationsvärde som definierar anslutningssträngen som ska användas för backend-databasen. Programmets utvecklare använder en annan databas än den som används i produktionen. Databasanslutningssträngen som används av programmet måste ändras när programmet flyttas från utveckling till produktion.

I Azure App-konfiguration kan du använda *etiketter* för att definiera olika värden för samma nyckel. Du kan till exempel definiera en enskild nyckel med olika värden för *utveckling* och *produktion*. Du kan ange vilka etiketter som ska läsas in när du ansluter till appkonfiguration.

För att demonstrera den här funktionen ändrar vi webbappen som skapats i [Snabbstart: Skapa en ASP.NET Core-app med Azure App-konfiguration](./quickstart-aspnet-core-app.md) för att använda olika konfigurationsinställningar för utveckling kontra produktion. Slutför snabbstarten innan du fortsätter.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Ange en etikett när du lägger till ett konfigurationsvärde

I Azure-portalen går du till **Configuration Explorer** och letar reda på *nyckeln TestApp:Settings:FontColor* som du skapade i snabbstarten. Markera snabbmenyn och klicka sedan på **Lägg till värde**.

> [!div class="mx-imgBorder"]
> ![Menyalternativ för Lägg till värde](media/labels-add-value.png)

På skärmen **Lägg till värde** anger du ett värde **för** **rött** och en **etikett för** **utveckling**. Lämna **innehållstypen** tom. Välj **Använd**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Läsa in konfigurationsvärden med en angiven etikett

Som standard läser Azure App-konfigurationen bara in konfigurationsvärden utan etikett. Om du har definierat etiketter för dina konfigurationsvärden bör du ange vilken eller vilka etiketter som ska användas när du ansluter till appkonfiguration.

I det sista avsnittet skapade du ett annat konfigurationsvärde för *utvecklingsmiljön.* Du använder `HostingEnvironment.EnvironmentName` variabeln för att dynamiskt avgöra vilken miljö appen körs i. Mer information finns [i Använda flera miljöer i ASP.NET Core](/aspnet/core/fundamentals/environments).

Läs in konfigurationsvärden med etiketten som motsvarar den `Select` aktuella miljön genom att överföra miljönamnet till metoden:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Kodavsnittet ovan läser in anslutningssträngen för `AppConfigConnectionString`appkonfiguration från en miljövariabel som kallas . Se till att den här miljövariabeln är korrekt inställd.

Metoden `Select` anropas två gånger. Första gången läses konfigurationsvärden in utan etikett. Sedan läser den in konfigurationsvärden med etiketten som motsvarar den aktuella miljön. Dessa miljöspecifika värden åsidosätter alla motsvarande värden utan etikett. Du behöver inte definiera miljöspecifika värden för varje nyckel. Om en nyckel inte har ett värde med en etikett som motsvarar den aktuella miljön används värdet utan etikett.

## <a name="testing-in-different-environments"></a>Testning i olika miljöer

Om du vill testa de `launchSettings.json` olika `Properties` konfigurationsvärdena öppnar du filen under katalogen. Leta `config` reda `profiles`på posten under . I `environmentVariables` avsnittet anger `ASPNETCORE_ENVIRONMENT` du `Production`variabeln till .

Med de nya värdena inställda, bygga och köra ditt program.

```dotnetcli
dotnet build
dotnet run
```

Använd en webbläsare för `http://localhost:5000`att navigera till . Du kommer att märka att teckenfärgen är svart.

![Webbprogram som körs med produktionskonfiguration](media/labels-website-prod.png)

Nu `launchSettings.json` uppdatera för `ASPNETCORE_ENVIRONMENT` att `Development`ställa in variabeln till . Kör `dotnet run` igen. Du kommer att märka att teckenfärgen nu är röd. Detta beror på `TestApp:Settings:FontColor` att programmet nu använder `Development` värdet för det som har etiketten. Alla andra konfigurationsvärden förblir desamma som produktionsvärdena.

![Webbprogram som körs med utvecklingskonfiguration](media/labels-website-dev.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfiguration i ASP.NET kärna](/aspnet/core/fundamentals/configuration/)
