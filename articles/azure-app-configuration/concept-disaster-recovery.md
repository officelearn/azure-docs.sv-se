---
title: Azure App återhämtning av konfiguration och haveri beredskap | Microsoft Docs
description: En översikt över hur du implementerar återhämtning och haveri beredskap med Azure App-konfiguration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: 291f6fe48d81397d293ab54a73e777831e25f6ea
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185285"
---
# <a name="resiliency-and-disaster-recovery"></a>Återhämtning och haveriberedskap

Azure App konfiguration är för närvarande en regional tjänst. Varje konfigurations Arkiv skapas i en viss Azure-region. Ett områdes omfattande avbrott påverkar alla butiker i den regionen. App-konfigurationen erbjuder inte automatisk redundans till en annan region. Den här artikeln innehåller allmän vägledning om hur du kan använda flera konfigurations lager i Azure-regioner för att öka geo-återhämtningen för ditt program.

## <a name="high-availability-architecture"></a>Arkitektur med hög tillgänglighet

Om du vill utnyttja redundans mellan regioner måste du skapa flera konfigurations lager för appar i olika regioner. Med den här installationen har ditt program minst en extra konfigurations lagring som går tillbaka om den primära lagringen blir otillgänglig. Följande diagram illustrerar topologin mellan ditt program och dess primära och sekundära konfigurations lager:

![Geo-redundanta lager](./media/geo-redundant-app-configuration-stores.png)

Ditt program läser in konfigurationen från både den primära och den sekundära lagrings plats parallellt. Detta ökar risken för att hämta konfigurations data. Du är ansvarig för att hålla data i båda butikerna synkroniserade. I följande avsnitt förklaras hur du kan bygga geo-återhämtning i ditt program.

## <a name="failover-between-configuration-stores"></a>Redundans mellan konfigurations lager

Tekniskt sett är ditt program inte att köra en redundansväxling. Det görs ett försök att hämta samma uppsättning konfigurations data från två program konfigurations lager samtidigt. Ordna koden så att den läses in från den sekundära butiken först och sedan till det primära lagret. Den här metoden säkerställer att konfigurations data i det primära lagret prioriteras när de är tillgängliga. Följande kodfragment visar hur du kan implementera den här ordningen i .NET Core CLI:

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
    }
```

Observera att parametern `optional` har överförts till funktionen `AddAzureAppConfiguration`. När värdet är `true`förhindrar den här parametern att programmet inte kan fortsätta om funktionen inte kan läsa in konfigurations data.

## <a name="synchronization-between-configuration-stores"></a>Synkronisering mellan konfigurations lager

Det är viktigt att den geo-redundanta konfigurationen lagrar alla har samma uppsättning data. Du kan använda funktionen **Exportera** i appen konfiguration för att kopiera data från den primära butiken till den sekundära på begäran. Den här funktionen är tillgänglig via både Azure Portal och CLI.

Från Azure Portal kan du skicka en ändring till ett annat konfigurations Arkiv genom att följa dessa steg.

1. Gå till fliken **Importera/exportera** och välj **Exportera** > app- **konfiguration** > **mål** > **Välj en resurs**.

2. På det nya bladet som öppnas anger du prenumerationen, resurs gruppen och resurs namnet för ditt sekundära Arkiv och väljer sedan **Använd**.

3. Användar gränssnittet har uppdaterats så att du kan välja vilka konfigurations data som du vill exportera till ditt sekundära arkiv. Du kan lämna standardvärdet för tid och ange både **etikett** och **etikett** till samma värde. Välj **Använd**.

4. Upprepa föregående steg för alla konfigurations ändringar.

Om du vill automatisera export processen använder du Azure CLI. Följande kommando visar hur du exporterar en enskild konfigurations ändring från den primära lagrings platsen till den sekundära:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kan förbättra ditt program för att uppnå geo-återhämtning under körningen av app-konfigurationen. Du kan också bädda in konfigurations data från App-konfiguration vid bygge eller distributions tid. Mer information finns i [integrera med en CI/CD-pipeline](./integrate-ci-cd-pipeline.md).

