---
title: "Azure Service Bus Geo-katastrofåterställning | Microsoft Docs"
description: "Hur du använder geografiska regioner för redundans och utföra katastrofåterställning i Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Azure Service Bus Geo-återställning (förhandsgranskning)

När regionala Datacenter eventuellt driftstopp, är det viktigt för databearbetning fortsätta att arbeta i en annan region eller datacenter. Därför *Geo-återställning* och *georeplikering* är viktiga funktioner för företag. Azure Service Bus stöder både Geo-återställning och Geo-replikering på namnområdesnivån. 

Geo-disaster recovery preview finns för närvarande bara i två regioner (**norra centrala USA** och **södra centrala USA)**.

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Den [bästa praxis för isolering program mot Service Bus-avbrott och katastrofer](service-bus-outages-disasters.md) artikel gör en åtskillnad mellan ”avbrott” och ”katastrofer”, vilket är viktigt att notera. En *avbrott* är temporär otillgänglighet Azure Service Bus och kan påverka vissa komponenter av tjänsten, till exempel ett meddelandearkiv eller även hela datacentret. Men när problemet har åtgärdats, blir Service Bus tillgänglig igen. Normalt orsakar avbrott inte meddelanden eller andra data förlorade. Ett exempel på sådana avbrott kan vara ett strömavbrott i datacentret.

En *katastrofåterställning* definieras som permanenta eller långsiktig förlust av en Service Bus [skalningsenhet](service-bus-architecture.md#service-bus-scale-units) eller datacenter. Datacentret kan bli inte tillgänglig igen eller kan vara avstängd för timmar eller dagar. Exempel på sådana katastrofer är brand, överbelasta eller jordbävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden eller andra data. Men i de flesta fall bör det finnas ingen dataförlust och meddelanden som kan återställas när Datacenter är säkerhetskopiera.

Geo-disaster recovery-funktion i Azure Service Bus är en lösning för katastrofåterställning. Koncept och arbetsflödet som beskrivs i den här artikeln gäller katastrofåterställning scenarier och inte tillfälligt eller tillfälliga avbrott.  

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Funktionen disaster recovery implementerar metadata katastrofåterställning och bygger på den primära servern och en sekundär disaster recovery-namnområden. Observera att Geo disaster recovery-funktionen är tillgänglig för [Premium-namnområden](service-bus-premium-messaging.md) endast. Du behöver inte göra ändringar anslutning sträng som anslutningen görs via ett alias.

Följande villkor används i den här artikeln:

-  *Alias*: huvudsakliga anslutningssträngen.

-  *Primära och sekundära namnområdet*: Beskriver de namnområden som motsvarar aliaset. Den primära servern är ”aktiv” och tar emot meddelanden, sekundärt är ”passiva” och inte ta emot meddelanden. Metadata mellan de båda är synkroniserade, så att både sömlöst kan godkänna meddelanden utan några program kodändringar.

-  *Metadata*: din representation av objekt i Azure Service Bus. Vi stöder för närvarande endast metadata.

-  *Redundans*: att aktivera sekundär namnområdet. Du måste hämta meddelanden från tidigare primära namnområdet när den blir tillgänglig igen och sedan ta bort namnområdet. Om du vill skapa en annan växling vid fel, lägger du till en ny sekundär namnrymd kopplingen. Om du vill återanvända tidigare primära namnområdet efter en redundansväxling måste du först ta bort alla befintliga entiteter från namnområdet. Se till att ta emot alla meddelanden innan du gör det.

## <a name="failover-workflow"></a>Arbetsflöde för växling vid fel

Följande avsnitt är en översikt över hela processen med att ställa in inledande växling vid fel och hur du går framåt från den tidpunkten.

![1][]

Du först ställa in en primär och sekundär namnområde och sedan skapa ett par. Länkning av den här får du ett alias som du kan använda för att ansluta. Eftersom du använder ett alias, behöver du inte ändra anslutningssträngar. Endast nya namnområden kan läggas till redundans para ihop. Slutligen måste du lägga till vissa utlösaren logik (till exempel vissa affärslogik som identifierar om namnområdet är inte tillgängligt och initierar växling vid fel). Du kan söka efter namnområde tillgänglighet med hjälp av den [meddelandet surfning](message-browsing.md) möjligheterna för Service Bus.

När du har ställt in övervakning och katastrofåterställning, kan du titta på failover-processen. Om utlösaren initierar en växling vid fel, eller du påbörja redundans manuellt, krävs två steg:

1. Vid en annan avbrott som du vill kunna redundansväxla igen. Därför kan ställa in ett andra passiva namnområde och uppdatera kopplingen. 
2. Hämta meddelanden från tidigare primära namnområde när det nya namnområdet är tillgänglig. Efter att återanvända eller ta bort det gamla primära namnområdet.

![2][]

## <a name="set-up-disaster-recovery"></a>Konfigurera haveriberedskap

Det här avsnittet beskrivs hur du skapar din Service Bus Geo-disaster recovery kod. Om du vill göra det, behöver du två namnområdena på oberoende platser. till exempel Syd USA och norra centrala USA. I följande exempel används Visual Studio 2017.

1.  Skapa en ny **konsolen App(.Net Framework)** projekt i Visual Studio och ge det ett namn, till exempel **SBGeoDR**.

2.  Installera följande NuGet-paket:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Kontrollera att versionen av Newtonsoft.Json NuGet-paketet som du använder är version 10.0.3.

3.  Lägg till följande `using` instruktioner i koden:

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Ändra din `main()` metod för att lägga till två premium-namnområden:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Aktivera kopplingen mellan de två namnområdena och hämta det alias du senare använda för att ansluta till dina enheter:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Du har konfigurerat två parad namnområden. Du kan nu skapa entiteter för att se metadata för synkroniseringen. Om du vill utföra en växling vid fel omedelbart därefter kan du tillåta tid att synkronisera metadata. Du kan lägga till en kort vilolägestiden, till exempel:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

Du kan nu lägga till enheter via portalen eller via Azure Resource Manager och se hur de synkroniserar. Om inte din plan för redundans manuellt, bör du skapa en app som övervakar ditt primära namnområde och initierar redundans om den inte är tillgänglig. 

## <a name="initiate-a-failover"></a>Initiera redundans

Följande kod visar hur du påbörja en växling:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

När du utlösa växling vid fel, Lägg till ett nytt passiva namnområde och återupprätta kopplingen. Koden för att skapa ett nytt par visas i föregående avsnitt. Dessutom kan du ta bort meddelandena från det gamla primära namnområdet när redundansväxlingen är klar. Exempel på hur du tar emot meddelanden från en kö finns [Kom igång med köer](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Så här inaktiverar du Geo-katastrofåterställning

Följande kod visar hur du inaktiverar länkning av namnområde:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

Följande kod tar bort det alias som du skapade:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>När du har en växling vid fel (återställning)

Efter en växling, utför du följande två steg:

1.  Skapa ett nytt passiva sekundära namnområde. Koden som visas i föregående avsnitt.
2.  Ta bort meddelandena från kön.

## <a name="alias-connection-string-and-test-code"></a>Alias sträng och testa kod-anslutningsfel

Om du vill testa redundans-processen kan du skriva ett exempelprogram som skickar meddelanden till primära namnområdet med alias. Gör du genom att se till att du hämtar anslutningssträngen alias från ett aktiva namnområdet. Det finns inget gränssnitt att direkt hämta anslutningssträngen med den aktuella förhandsversionen. Följande exempelkod ansluter före och efter växling vid fel:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Nästa steg

- Geo-återställning finns [här REST API-referensen](/rest/api/servicebus/disasterrecoveryconfigs).
- Kör Geo-återställning [på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Geo-återställning finns [exempel som skickar meddelanden till ett alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande artiklar:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST-API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
