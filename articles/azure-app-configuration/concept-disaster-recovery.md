---
title: Återhämtning och haveriberedskap för Azure App-konfiguration
description: Lean hur du implementerar återhämtning och haveriberedskap med Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523772"
---
# <a name="resiliency-and-disaster-recovery"></a>Återhämtning och haveriberedskap

Azure App Configuration är för närvarande en regional tjänst. Varje konfigurationsarkiv skapas i en viss Azure-region. Ett avbrott i hela regionen påverkar alla butiker i den regionen. Appkonfiguration erbjuder inte automatisk redundans till en annan region. Den här artikeln innehåller allmän vägledning om hur du kan använda flera konfigurationsarkiv i Azure-regioner för att öka programmets geoåterför återhämtning.

## <a name="high-availability-architecture"></a>Arkitektur med hög tillgänglighet

Om du vill realisera redundans mellan regioner måste du skapa flera appkonfigurationsarkiv i olika regioner. Med den här inställningen har programmet minst ett ytterligare konfigurationsarkiv att falla tillbaka på om det primära arkivet blir otillgängligt. Följande diagram illustrerar topologin mellan programmet och dess primära och sekundära konfigurationsarkiv:

![Geo-redundanta butiker](./media/geo-redundant-app-configuration-stores.png)

Programmet läser in konfigurationen från både primära och sekundära butiker parallellt. Om du gör det ökar risken för att konfigurationsdata hämtas. Du är ansvarig för att hålla data i båda butikerna synkroniserade. I följande avsnitt beskrivs hur du kan skapa geoåterförsåtare i ditt program.

## <a name="failover-between-configuration-stores"></a>Redundans mellan konfigurationsarkiv

Tekniskt sett kör inte ditt program en redundans. Den försöker hämta samma uppsättning konfigurationsdata från två App Configuration-butiker samtidigt. Ordna koden så att den laddas från den sekundära butiken först och sedan den primära butiken. Den här metoden säkerställer att konfigurationsdata i det primära arkivet har företräde när den är tillgänglig. Följande kodavsnitt visar hur du kan implementera det här arrangemanget i .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET-kärna 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Lägg `optional` märke till `AddAzureAppConfiguration` att parametern skickades in i funktionen. När den `true`här parametern är inställd på det förhindrar den att programmet inte fortsätter om funktionen inte kan läsa in konfigurationsdata.

## <a name="synchronization-between-configuration-stores"></a>Synkronisering mellan konfigurationsarkiv

Det är viktigt att dina geo-redundanta konfigurationslager alla har samma uppsättning data. Du kan använda funktionen **Exportera** i appkonfiguration för att kopiera data från det primära arkivet till det sekundära på begäran. Den här funktionen är tillgänglig via både Azure-portalen och CLI.

Från Azure-portalen kan du skicka en ändring till ett annat konfigurationsarkiv genom att följa dessa steg.

1. Gå till fliken **Importera/exportera** och välj **Exportera** > **appkonfigurationsmål** > **Target** > **Välj en resurs**.

1. I det nya bladet som öppnas anger du prenumerationen, resursgruppen och resursnamnet för det sekundära arkivet och väljer sedan **Använd**.

1. Användargränssnittet uppdateras så att du kan välja vilka konfigurationsdata du vill exportera till ditt sekundära arkiv. Du kan lämna standardtidsvärdet som det är och ange både **Från-etikett** och **Till-etikett** till samma värde. Välj **Använd**.

1. Upprepa föregående steg för alla konfigurationsändringar.

Om du vill automatisera den här exportprocessen använder du Azure CLI. Följande kommando visar hur du exporterar en enda konfigurationsändring från det primära arkivet till det sekundära:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kan utöka ditt program för att uppnå geo-återhämtning under körning för appkonfiguration. Du kan också bädda in konfigurationsdata från Appkonfiguration vid bygg- eller distributionstid. Mer information finns i [Integrera med en CI/CD-pipeline](./integrate-ci-cd-pipeline.md).
