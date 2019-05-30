---
title: Azure Appkonfiguration återhämtning och disaster recovery | Microsoft Docs
description: En översikt över hur du implementerar återhämtning och disaster recovery med konfiguration av Azure.
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
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394029"
---
# <a name="resiliency-and-disaster-recovery"></a>Återhämtning och haveriberedskap

Konfiguration av Azure är för närvarande en regional tjänst. Varje konfiguration av lagring har skapats i en viss Azure-region. Ett regionomfattande strömavbrott påverkar alla butiker i samma region. Konfiguration av omfattas inte automatisk redundans till en annan region. Den här artikeln innehåller allmänna råd om hur du kan använda flera configuration butiker i Azure-regioner för att öka geo-skyddet av ditt program.

## <a name="high-availability-architecture"></a>Arkitektur med hög tillgänglighet

För att använda redundans över regioner, måste du skapa flera appbutiker konfigurationen i olika regioner. Med den här konfigurationen kan ditt program har minst en ytterligare konfiguration av lagra faller tillbaka till med det primära lagret blir otillgänglig. Nedan visas ett diagram som visar topologin mellan programmet och dess primära och sekundära configuration butiker.

![GEO-redundant lager](./media/geo-redundant-app-configuration-stores.png)

Programmet läser in konfigurationen från både primära och sekundära butiker parallellt. Detta ökar risken för att få har konfigurationsdata avsevärt. Du är ansvarig för att lagra data i båda butiker synkroniserade. I följande avsnitt beskrivs hur du kan skapa geo-återhämtning i ditt program.

## <a name="failover-between-configuration-stores"></a>Redundans mellan konfiguration

Tekniskt sett ditt program kör inte en redundansväxling. Försök görs att hämta samma uppsättning konfigurationsdata från två configuration appbutiker samtidigt. Ordna din kod så att den läser in först från arkivet sekundära först och sedan lagra primärt. Den här metoden garanterar att konfigurationsdata i det primära lagret högre prioritet när de är tillgängliga. Kodfragmentet nedan visar hur du kan implementera detta i .NET Core.

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

Observera den `optional` parameter skickades till den `AddAzureAppConfiguration` funktion. När värdet `true`, den här parametern hindrar programmet från inte kan fortsätta om funktionen inte kan läsa in konfigurationsdata.

## <a name="synchronization-between-configuration-stores"></a>Synkronisering mellan konfiguration

Det är viktigt att din geo-redundant konfiguration lagrar alla har samma uppsättning data. Du kan använda den **exportera** funktionen i konfiguration av att kopiera data från det primära lagret till den sekundära på begäran. Den här funktionen är tillgänglig via både Azure portal och CLI.

Från Azure-portalen kan du skicka en ändring till en annan konfiguration av lagring genom att följa dessa steg:

1. Gå till **Import/Export** fliken **exportera**väljer **Appkonfiguration** som den **Target** tjänsten, klicka på  **Välj en resurs**.

2. I det nya bladet som har öppnat upp, ange prenumeration, resursgrupp och resursnamnet för din sekundära store och sedan på **tillämpa**.

3. Användargränssnittet kommer att uppdateras så att du kan välja vilka konfigurationsdata som du vill exportera till din sekundära store. Du kan lämna standardvärdet i tid eftersom och Ställ in både **från etikett** och **etikett** till samma värde. Klicka på **Verkställ**.

4. Upprepa stegen ovan för alla konfigurationsändringar.

Du kan du automatisera exportera med hjälp av Azure CLI. Kommandot nedan visar hur du exporterar en enkel konfigurationsändring från det primära lagret till sekundärt.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att utöka ditt program för att uppnå geo-elasticitet under körning för Appkonfiguration. Alternativt kan du bädda in konfigurationsdata från Appkonfiguration när bygge eller distribution. Mer information finns i [integrera med en CI/CD-pipeline](./integrate-ci-cd-pipeline.md).

