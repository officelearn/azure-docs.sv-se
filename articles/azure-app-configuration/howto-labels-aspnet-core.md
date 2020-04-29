---
title: Använd konfiguration per miljö
titleSuffix: Azure App Configuration
description: Använd etiketter för att tillhandahålla konfigurations värden per miljö
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056807"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Använd etiketter för att aktivera olika konfigurationer för olika miljöer

Många program måste använda olika konfigurationer för olika miljöer. Anta att ett program har ett konfigurations värde som definierar den anslutnings sträng som ska användas för backend-databasen. Programmets utvecklare använder en annan databas än den som används i produktionen. Databas anslutnings strängen som används av programmet måste ändras när programmet flyttas från utveckling till produktion.

I Azure App konfiguration kan du använda *Etiketter* för att definiera olika värden för samma nyckel. Du kan till exempel definiera en enskild nyckel med olika värden för *utveckling* och *produktion*. Du kan ange vilka etiketter som ska läsas in vid anslutning till app-konfigurationen.

För att demonstrera den här funktionen ändrar vi webbappen som skapats i [snabb start: skapa en ASP.net Core-app med Azure App konfiguration](./quickstart-aspnet-core-app.md) för att använda olika konfigurations inställningar för utveckling eller produktion. Slutför snabb starten innan du fortsätter.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Ange en etikett när du lägger till ett konfigurations värde

I Azure Portal går du till **konfigurations Utforskaren** och letar reda på *TestApp: Settings: FontColor* -nyckeln som du skapade i snabb starten. Välj snabb menyn och klicka sedan på **Lägg till värde**.

> [!div class="mx-imgBorder"]
> ![Meny alternativet Lägg till värde](media/labels-add-value.png)

På skärmen **Lägg till värde** anger du **värdet** **röd** och en **utvecklings** **etikett** . Lämna **innehålls typen** tom. Välj **Använd**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Läser in konfigurations värden med en angiven etikett

Som standard läser Azure App-konfiguration bara konfigurations värden utan etikett. Om du har definierat etiketter för dina konfigurations värden vill du ange vilka etiketter som ska användas när du ansluter till app-konfigurationen.

I det sista avsnittet skapade du ett annat konfigurations värde för *utvecklings* miljön. Du kan använda `HostingEnvironment.EnvironmentName` variabeln för att dynamiskt avgöra vilken miljö appen körs för närvarande i. Mer information finns [i använda flera miljöer i ASP.net Core](/aspnet/core/fundamentals/environments).

Läs in konfigurations värden med etiketten som motsvarar den aktuella miljön genom att skicka miljö namnet till `Select` -metoden:

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
> Kodfragmentet ovan läser in anslutnings strängen för app-konfigurationen från en miljö variabel `AppConfigConnectionString`som kallas. Se till att miljövariabeln är korrekt inställd.

`Select` Metoden anropas två gånger. Första gången laddar den konfigurations värden utan etikett. Sedan läser den in konfigurations värden med etiketten som motsvarar den aktuella miljön. Dessa miljörelaterade värden åsidosätter motsvarande värden utan etikett. Du behöver inte definiera miljöspecificerade värden för varje nyckel. Om en nyckel inte har ett värde med en etikett som motsvarar den aktuella miljön, används värdet utan etikett.

## <a name="testing-in-different-environments"></a>Testa i olika miljöer

Om du vill testa de olika konfigurations värdena `launchSettings.json` öppnar du filen `Properties` under katalogen. Leta upp `config` posten under `profiles`. I `environmentVariables` avsnittet anger du `ASPNETCORE_ENVIRONMENT` variabeln till. `Production`

Skapa och kör ditt program med de nya värdena som angetts.

```dotnetcli
dotnet build
dotnet run
```

Använd en webbläsare för att navigera till `http://localhost:5000`. Du ser att tecken färgen är svart.

![Webb program som körs med produktions konfigurationen](media/labels-website-prod.png)

Uppdatera `launchSettings.json` nu för att ange `ASPNETCORE_ENVIRONMENT` variabeln `Development`till. Kör `dotnet run` igen. Nu ser du att tecken färgen är röd. Detta beror på att programmet nu använder värdet för `TestApp:Settings:FontColor` som har `Development` etiketten. Alla andra konfigurations värden förblir samma som deras produktions värden.

![Webb program som kör med utvecklings konfiguration](media/labels-website-dev.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfiguration i ASP.NET Core](/aspnet/core/fundamentals/configuration/)
