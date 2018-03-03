---
title: "Reliable Actors tillstånd management | Microsoft Docs"
description: "Beskriver hur Reliable Actors tillstånd hanteras, beständiga och replikeras för hög tillgänglighet."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f196b2e54efc5ecbbd93e48e1f115edb99e5c858
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="reliable-actors-state-management"></a>Tillförlitliga aktörer tillståndshantering
Reliable Actors är enkeltrådad objekt som kan kapsla både logik och tillstånd. Eftersom aktörer körs på Reliable Services, upprätthålla de tillstånd på ett tillförlitligt sätt med hjälp av samma beständiga och replikeringsmekanismer. Det här sättet aktörer förlorar inte deras tillstånd efter fel på återaktivering efter skräpinsamling eller när de flyttas mellan noder i ett kluster på grund av resursen NLB eller uppgraderingar.

## <a name="state-persistence-and-replication"></a>Tillstånd beständighet och replikering
Alla Reliable Actors anses *stateful* eftersom varje instans aktören mappas till ett unikt ID. Det innebär att upprepade anrop till samma aktörs-ID dirigeras till samma aktören-instans. I ett system med tillståndslös däremot är klientanrop inte säkert att dirigeras till samma server varje gång. Därför är aktörstjänster alltid tillståndskänsliga tjänster.

Även om stateful som de måste lagra tillstånd på ett tillförlitligt sätt inte innebär betraktas som aktörer. Aktörer kan välja vilken beständighet och replikering baserat på deras data lagringskraven:

* **Beständiga tillståndet**: tillstånd sparat på disken och replikeras till tre eller flera repliker. Beständiga tillståndet är mest beständiga tillståndet lagringsalternativ, där tillståndet kan spara genom hela klustret avbrott.
* **Temporära tillstånd**: tillstånd ska replikeras till tre eller flera repliker och endast sparas i minnet. Temporär state ger återhämtning mot nodfel och aktören fel, och under uppgraderingar och resursen belastningsutjämning. Dock tillstånd sparas inte till disk. Så om alla repliker går förlorade på samma gång, tillståndet förloras samt.
* **Inga beständiga tillståndet**: tillstånd är inte replikeras eller skrivs till disk, Använd endast för aktörer som inte behöver upprätthålla tillstånd på ett tillförlitligt sätt.

Varje nivå av beständiga är helt enkelt en annan *tillståndsprovidern* och *replikering* konfigurationen av din tjänst. Om tillståndet skrivs till disk är beroende av tillståndsprovidern - komponenten i en tillförlitlig tjänst som lagrar tillstånd. Replikeringen beror på hur många repliker för en tjänst har distribuerats med. Precis som med Reliable Services kan både tillståndsprovidern och replikantalet enkelt ställas in manuellt. Aktören framework innehåller ett attribut som, när används på en aktör, väljs automatiskt en standardprovider för tillstånd och genererar automatiskt inställningarna för replikantalet för att uppnå ett av dessa tre beständiga inställningar. Attributet StatePersistence ärvs inte av härledd klass, varje aktören måste tillhandahålla StatePersistence nivån.

### <a name="persisted-state"></a>Beständiga tillståndet
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Inställningen använder en tillståndsprovidern som lagrar data på disken och anger automatiskt replikantalet tjänsten till 3.

### <a name="volatile-state"></a>Temporära tillstånd
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Den här inställningen används en provider i minne – endast och replikantalet till 3.

### <a name="no-persisted-state"></a>Inga sparade tillstånd
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Den här inställningen används en provider i minne – endast och replikantalet till 1.

### <a name="defaults-and-generated-settings"></a>Standardvärden och genererade inställningar
När du använder den `StatePersistence` attribut, en tillståndsprovidern väljs automatiskt för dig vid körning när tjänsten aktören startar. Replikantalet dock anges vid kompilering av Visual Studio aktören build tools. Build-verktyg automatiskt generera ett *standardtjänst* för tjänsten aktören i ApplicationManifest.xml. Parametrar skapas för **min replikuppsättningen storlek** och **mål replikuppsättningen storlek**.

Du kan ändra parametrarna manuellt. Men varje gång den `StatePersistence` attribut ändras, parametrarna är inställda på replik set storlek standardvärden för den valda `StatePersistence` attribut, åsidosätter eventuella tidigare värden. Med andra ord värdena som du anger i ServiceManifest.xml är *endast* åsidosättas vid byggning när du ändrar den `StatePersistence` attributvärdet.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Tillstånd manager
Varje instans aktören har sin egen tillståndshanterare: en ordlista-liknande datastruktur som lagras på ett tillförlitligt sätt nyckel/värde-par. Tillståndshanterarens är en omslutning runt en tillståndsprovidern. Du kan använda den för att lagra data oavsett vilket beständiga inställningen används. Den ger inte några garantier att en pågående tjänst aktören kan ändras från en inställning för temporär (i minnet-endast) tillstånd till en inställning för beständiga tillståndet med en rullande uppgradering och behålla data. Det är dock möjligt att ändra replikantalet för en tjänst som körs.

Tillstånd manager nycklar måste vara strängar. Värden är generisk och kan ha olika typer, inklusive anpassade typer. Värden som lagras i tillståndshanterarens måste vara datakontrakt serialiserbara eftersom de kan överföras via nätverket till andra noder vid replikering och kan skrivas till disk, beroende på en aktör tillståndsinställningen persistence.

Tillståndshanterarens visar vanliga ordlista metoder för att hantera tillstånd, liknar de som finns i tillförlitliga ordlistan.

## <a name="accessing-state"></a>Åtkomst till tillstånd
Tillstånd kan nås via tillståndshanterarens av nyckeln. Tillstånd manager metoder är alla asynkrona eftersom de kan kräva disk-i/o när aktörer beständiga tillstånd. Vid första åtkomst cachelagras tillstånd objekt i minnet. Upprepa åtkomst operations access-objekt direkt från minnet och returnera synkront utan att det medför i/o eller asynkrona kontexten-växla omkostnader. Ett tillstånd objekt tas bort från cachen i följande fall:

* En aktörsmetod utlöser ett undantag när det hämtar ett objekt från hanteraren tillstånd.
* En aktör återaktiveras när de har inaktiverats eller efter fel.
* Tillstånd providern sidor status till disk. Det här problemet beror på tillstånd providerns implementering. Standardprovidern för tillstånd för den `Persisted` har problemet.

Du kan hämta tillstånd genom att använda ett standard- *hämta* åtgärden utlöser `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) om det inte finns en post för nyckeln:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Du kan också hämta tillstånd med hjälp av en *TryGet* metod som inte genereras om det inte finns en post för en nyckel:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="saving-state"></a>Sparar tillstånd
Tillstånd manager hämtning metoder returnerar en referens till ett objekt i lokalt minne. Ändra det här objektet i lokala minnet enbart orsakar inte den sparas varaktigt. När ett objekt som hämtats från tillståndshanterarens och ändras, måste den igen i tillståndshanterarens sparas varaktigt.

Du kan infoga tillstånd med hjälp av en ovillkorlig *ange*, som motsvarar den `dictionary["key"] = value` syntax:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Du kan lägga till tillstånd med hjälp av en *Lägg till* metod. Den här metoden genererar `InvalidOperationException`(C#) eller `IllegalStateException`(Java) vid försök att lägga till en nyckel som redan finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Du kan också lägga till tillstånd med hjälp av en *TryAdd* metod. Den här metoden genereras inget när den försöker lägga till en nyckel som redan finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

I slutet av en aktörsmetod sparar tillståndshanterarens automatiskt alla värden som har lagts till eller ändrats av en insert eller update-åtgärd. ”Spara” kan innehålla beständighet till disk och replikering, beroende på inställningarna som används. Värden som inte har ändrats beständiga eller replikeras inte. Om inga värden har ändrats, spara åtgärden händer ingenting. Om du sparar misslyckas ändringstillstånd ignoreras och det ursprungliga tillståndet läses.

Du kan också spara tillstånd manuellt genom att anropa den `SaveStateAsync` metod i basen aktören:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="removing-state"></a>Ta bort tillstånd
Du kan ta bort tillstånd permanent från en aktör tillstånd manager genom att anropa den *ta bort* metod. Den här metoden genererar `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) vid försök att ta bort en nyckel som inte finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Du kan också ta bort tillstånd permanent med hjälp av den *TryRemove* metod. Den här metoden genereras inget när den försöker ta bort en nyckel som inte finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="best-practices"></a>Bästa praxis
Här följer några rekommendationer och felsökningstips för att hantera dina aktörstillstånd.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Kontrollera tillståndet aktören som detaljerad som möjligt
Detta är avgörande för prestanda och resursanvändningen för ditt program. När alla skrivning/uppdateringar ”namngivna tillstånd” en aktörs serialiseras hela värdet som motsvarar det ”namngiven tillståndet” och skickas över nätverket till de sekundära replikerna.  Sekundära skriver den till lokal disk och svar tillbaka till den primära repliken. När den primära servern tar emot bekräftelser från ett kvorum av sekundära repliker, skriver tillståndet till den lokala disken. Anta till exempel att värdet är en klass som har 20 medlemmar och en storlek på 1 MB. Även om du bara ändrat något av klassmedlemmar som är av storlek 1 KB du hamnar betalar kostnaden för serialisering och nätverks- och skrivåtgärder för fullständig 1 MB. På liknande sätt, om värdet är en samling (t.ex en lista, matris eller ordlista), betalar du kostnaden för den kompletta samlingen även om du ändrar en av dess medlemmar. Gränssnittet StateManager i klassen aktören påminner om en ordlista. Du bör alltid modellen datastrukturen som representerar aktörstillstånd ovanpå den här ordlistan.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Hantera korrekt skådespelare, livscykel
Du bör ha Rensa princip om att hantera storleken på tillstånd i varje partition för en aktören-tjänst. Aktören tjänsten ska ha ett fast antal aktörer och återanvända en mycket som möjligt. Om du skapar nya aktörer kontinuerligt, måste du ta bort dem när de har sitt arbete. Aktören framework lagrar vissa metadata om varje aktören finns. Ta bort alla tillstånd för en aktör tas inte bort metadata om att aktören. Du måste ta bort aktören (finns [ta bort aktörer och deras tillstånd](service-fabric-reliable-actors-lifecycle.md#deleting-actors-and-their-state)) ta bort all information om den lagras i systemet. Som en extra kontroll ska du fråga tjänsten aktören (se [uppräkning aktörer](service-fabric-reliable-actors-platform.md)) ibland att kontrollera antalet aktörer inom det förväntade intervallet.
 
Om du ser någonsin att databasens filstorlek för en tjänst aktören öka bortom den förväntade storleken, kontrollerar du att du följer riktlinjerna ovan. Om du följer dessa riktlinjer och är fortfarande databasen problem-filens storlek, bör du [skapar ett supportärende](service-fabric-support.md) med Produktteamet för att få hjälp.

## <a name="next-steps"></a>Nästa steg

Tillstånd som är lagrad i Reliable Actors måste serialiseras innan dess skrivits till disk och replikeras för hög tillgänglighet. Lär dig mer om [aktören typserialiseringen](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Därefter lär dig mer om [aktören diagnostik- och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md).
