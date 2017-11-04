---
title: "Skapa din första Azure aktören-baserade-mikrotjänster i C# | Microsoft Docs"
description: "Den här självstudiekursen vägleder dig genom stegen för att skapa, felsöka och distribuera en enkel aktören-baserad tjänst med hjälp av Service Fabric Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 7e24cb902cb3f863931fc0be5e0f178707e806b6
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="getting-started-with-reliable-actors"></a>Komma igång med Reliable Actors
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-actors-get-started.md)
> * [Java i Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

Den här artikeln beskriver grunderna i Azure Service Fabric Reliable Actors och vägleder dig genom att skapa, felsöka och distribuera ett enkelt tillförlitliga aktören program i Visual Studio.

## <a name="installation-and-setup"></a>Installation och konfiguration
Innan du börjar bör du kontrollera att du har Service Fabric-utvecklingsmiljö ställa in på din dator.
Om du behöver konfigurera det finns detaljerad information på [hur du ställer in utvecklingsmiljön](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Grundläggande begrepp
För att komma igång med Reliable Actors behöver du bara förstå några grundläggande begrepp:

* **Aktörstjänst**. Reliable Actors paketeras i Reliable Services som kan distribueras i Service Fabric-infrastrukturen. Aktörsinstanser aktiveras i en namngiven tjänst-instans.
* **Aktörsregistrering**. Som med Reliable Services måste en Reliable Actor-tjänst registreras med Service Fabric Runtime. Utöver det måste aktörstypen registreras med Actor Runtime.
* **Aktörsgränssnitt**. Aktörsgränssnittet används till att definiera ett offentligt gränssnitt av stark typ för en aktör. I Reliable Actor-modellterminologin definierar aktörsgränssnittet de typer av meddelanden som aktören kan förstå och bearbeta. Aktörsgränssnittet används av andra aktörer och klientprogram för att ”skicka” (asynkrona) meddelanden till aktören. Reliable Actors kan implementera flera gränssnitt.
* **ActorProxy-klass**. ActorProxy-klassen används av klientprogram för att anropa metoderna som exponeras via aktörsgränssnittet. ActorProxy-klassen har två viktiga funktioner:
  
  * Namnmatchning: Den kan leta reda på aktören i klustret (hitta noden i klustret där den finns).
  * Felhantering: Den kan försöka utföra metodanrop igen och matcha aktörsplatsen igen efter, till exempel, ett fel som kräver att aktören flyttas till en annan nod i klustret.

Följande regler som gäller aktörsgränssnitt är värda att nämna:

* Aktörsgränssnittsmetoder får inte överbelastas.
* Aktörsgränssnittsmetoder får inte ha parametrarna out, ref eller optional.
* Allmänna gränssnitt stöds inte.

## <a name="create-a-new-project-in-visual-studio"></a>Skapa ett nytt projekt i Visual Studio
Starta Visual Studio 2015 eller Visual Studio 2017 som administratör och skapa ett nytt projekt för Service Fabric-program:

![Service Fabric-verktyg för Visual Studio – nytt projekt][1]

Du kan välja vilken typ av projekt som du vill skapa i dialogrutan nästa.

![Service Fabric-projektmallar][5]

Vi använder tjänsten Service Fabric Reliable Actors för för HelloWorld-projektet.

När du har skapat lösningen, bör du se följande struktur:

![Struktur för Service Fabric-projekt][2]

## <a name="reliable-actors-basic-building-blocks"></a>Grundläggande byggstenar i Reliable Actors
En typisk Reliable Actors lösning består av tre projekt:

* **Projektet för konsolprogrammet (MyActorApplication)**. Detta är projektet som paket alla tjänster tillsammans för distribution. Den innehåller den *ApplicationManifest.xml* och PowerShell-skript för att hantera programmet.
* **Gränssnittet projektet (MyActor.Interfaces)**. Detta är projektet som innehåller gränssnittsdefinitionen av för aktören. Du kan definiera de gränssnitt som ska användas av aktörer i lösningen i MyActor.Interfaces-projektet. Gränssnitten aktören kan definieras i ett projekt med ett namn, men gränssnittet definierar aktören kontraktet som delas av aktören implementering och klienterna som anropar aktören så vanligtvis är det praktiskt att definiera den i en sammansättning som är separat från aktören implementering och kan delas av flera projekt.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Aktören service-projekt (MyActor)**. Det här är de projekt används för att definiera Service Fabric-tjänsten som ska vara värd för aktören. Den innehåller implementeringen av aktören. En implementering av aktören är en klass som härleds från bastypen `Actor` och implementerar gränssnitt som är definierade i MyActor.Interfaces-projektet. En aktörklass måste också implementera en konstruktor som accepterar ett `ActorService` instans och en `ActorId` och skickar dem till grundläggande `Actor` klass. Detta ger konstruktorn beroendeinmatning plattform beroenden.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Aktörstjänsten måste vara registrerad med en tjänsttyp i Service Fabric Runtime. För att aktörstjänsten ska kunna köra dina aktörsinstanser måste aktörstypen också registreras hos aktörstjänsten. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Om du startar från ett nytt projekt i Visual Studio och du bara ha en aktören definition, ingår registreringen som standard i koden som skapar i Visual Studio. Om du definierar andra aktörer i tjänsten som du behöver lägga till aktören registreringen genom att använda:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> Service Fabric aktörer runtime skickar vissa [händelser och prestandaräknare som rör aktören metoder](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). De är användbara i diagnostik- och prestandaövervakning.
> 
> 

## <a name="debugging"></a>Felsökning
Service Fabric-verktyg för Visual Studio stöder felsökning på den lokala datorn. Du kan starta en felsökning genom att träffa på F5. Visual Studio skapar (vid behov) paket. Den distribuerar programmet på det lokala Service Fabric-klustret och kopplar felsökning.

Under distributionsprocessen, kan du se förloppet på den **utdata** fönster.

![Service Fabric felsökning utdatafönstret][3]

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hur Reliable Actors använda Service Fabric-plattformen](service-fabric-reliable-actors-platform.md).

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
