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
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706508"
---
# <a name="resiliency-and-disaster-recovery"></a>Återhämtning och haveriberedskap

Konfiguration av Azure är för närvarande en regional tjänst. Varje konfiguration av lagring har skapats i en viss Azure-region. Ett regionomfattande strömavbrott påverkar alla butiker i samma region. Konfiguration av omfattas inte automatisk redundans till en annan region. Den här artikeln innehåller allmänna råd om hur du kan använda flera configuration butiker i Azure-regioner för att öka geo-skyddet av ditt program.

## <a name="high-availability-architecture"></a>Arkitektur för hög tillgänglighet

För att använda redundans över regioner, måste du skapa flera appbutiker konfigurationen i olika regioner. Med den här konfigurationen kan har ditt program minst en ytterligare konfiguration av lagra faller tillbaka om det primära lagret blir otillgänglig. Följande diagram visar topologin mellan programmet och dess primära och sekundära configuration butiker:

![GEO-redundant lager](./media/geo-redundant-app-configuration-stores.png)

Programmet läser in konfigurationen från både primära och sekundära butiker parallellt. Detta ökar risken för att få har konfigurationsdata. Du är ansvarig för att lagra data i båda butiker synkroniserade. I följande avsnitt beskrivs hur du kan skapa geo-återhämtning i ditt program.

## <a name="failover-between-configuration-stores"></a>Redundans mellan konfiguration

Ditt program inte är rent tekniskt kan köra en redundansväxling. Försök görs att hämta samma uppsättning konfigurationsdata från två Appkonfiguration butiker samtidigt. Ordna din kod så att den läser in från den sekundära store först och sedan lagra primärt. Den här metoden garanterar att konfigurationsdata i det primära lagret företräde när den är tillgänglig. Följande kodfragment visar hur du kan implementera den här ordningen i .NET Core-CLI:

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

Observera den `optional` parameter skickades till den `AddAzureAppConfiguration` funktion. När värdet `true`, den här parametern förhindrar att programmet inte kan fortsätta om funktionen inte kan läsa in konfigurationsdata.

## <a name="synchronization-between-configuration-stores"></a>Synkronisering mellan konfiguration

Det är viktigt att din geo-redundant konfiguration lagrar alla har samma uppsättning data. Du kan använda den **exportera** funktionen i konfiguration av att kopiera data från det primära lagret till sekundärt på begäran. Den här funktionen är tillgänglig via både Azure portal och CLI.

Från Azure-portalen kan du skicka en ändring till en annan konfiguration av lagring genom att följa dessa steg.

1. Gå till den **Import/Export** och sedan **exportera** > **Appkonfiguration** > **Target**  >  **Väljer du en resurs**.

2. I det nya bladet som öppnas anger du den prenumeration, resursgrupp och resursnamnet för sekundär och väljer sedan **tillämpa**.

3. Användargränssnittet uppdateras så att du kan välja vilka konfigurationsdata som du vill exportera till din sekundära store. Du kan lämna standardvärdet i tid eftersom och Ställ in både **från etikett** och **till etiketten** till samma värde. Välj **Använd**.

4. Upprepa föregående steg för alla konfigurationsändringar.

Använda Azure CLI för att automatisera detta. Följande kommando visar hur du exporterar en enkel konfigurationsändring från det primära lagret till sekundärt:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att utöka ditt program för att uppnå geo-elasticitet under körning för Appkonfiguration. Du kan också bädda in konfigurationsdata från Appkonfiguration när bygge eller distribution. Mer information finns i [integrera med en CI/CD-pipeline](./integrate-ci-cd-pipeline.md).

