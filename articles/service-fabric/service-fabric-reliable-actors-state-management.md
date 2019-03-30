---
title: Reliable Actors tillstånd management | Microsoft Docs
description: Beskriver hur Reliable Actors tillstånd hanteras, sparas och replikeras för hög tillgänglighet.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 65dd47ab21ca4b1c50e0f17b73e7bc4eae8a96e8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665788"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors-tillståndshantering
Reliable Actors är single-threaded-objekt som kan kapsla både logik och tillstånd. Eftersom aktörer körs på Reliable Services, har de tillstånd på ett tillförlitligt sätt med hjälp av samma persistence och replikeringsmekanismer. På så sätt kan aktörer inte förlorar deras tillstånd efter fel vid återaktivering efter skräpinsamling eller när de flyttas mellan noder i ett kluster på grund av resurs belastningsutjämning eller uppgraderingar.

## <a name="state-persistence-and-replication"></a>Statliga persistence och replikering
Alla Reliable Actors anses *tillståndskänslig* eftersom varje aktör-instans som mappar till ett unikt ID. Det innebär att upprepade anrop till samma aktörs-ID dirigeras till samma aktör-instans. I ett tillståndslösa system kan däremot garanteras klientanrop inte ska dirigeras till samma server varje gång. Därför är aktörstjänster alltid tillståndskänsliga tjänster.

Även om aktörer anses tillståndskänslig betyder inte de måste lagra tillstånd på ett tillförlitligt sätt. Aktörer kan välja vilken sessionslägets beständighet och replikering baserat på deras data krav:

* **Sparade tillstånd**: Tillstånd sparat på disken och replikeras till tre eller fler repliker. Beständiga tillståndet är den mest varaktiga tillstånd, där tillstånd kan spara genom hela klustret avbrott.
* **Föränderliga tillstånd**: Tillstånd replikeras till tre eller fler repliker och endast sparas i minnet. Föränderliga state ger återhämtning mot nodfel och aktören fel och under uppgraderingar och belastningsutjämning för resursen. Dock tillstånd sparas inte till disk. Så om alla repliker förloras samtidigt, tillståndet förloras samt.
* **Inga bestående tillstånd**: Tillstånd är inte replikeras eller skrivs till disk, Använd endast för aktörer som inte behöver underhålla tillståndet på ett tillförlitligt sätt.

Varje nivå av persistence är helt enkelt en annan *tillståndsprovider* och *replikering* konfigurationen av din tjänst. Huruvida stav je zapsaný na disk beror på tillståndsprovider--komponenten i en tillförlitlig tjänst som lagrar tillstånd. Replikering är beroende av hur många repliker för en tjänst distribueras med. Precis som med Reliable Services kan tillståndsprovider såväl replikantal enkelt ställas in manuellt. Ramverket innehåller ett attribut som, när används på en aktör, väljer automatiskt en standardprovider för tillstånd och inställningar för replikantalet för att uppnå en av dessa tre persistenceinställningarna genererar automatiskt. Attributet StatePersistence ärvs inte av härledda klassen, varje typ av aktör måste ge sin StatePersistence-nivå.

### <a name="persisted-state"></a>Trvalý stav
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
Den här inställningen använder en tillståndsprovider som lagrar data på disken och ställer automatiskt in replikantalet för tjänsten till 3.

### <a name="volatile-state"></a>Föränderliga tillstånd
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
Den här inställningen används en i minne-endast-provider och replikantalet till 3.

### <a name="no-persisted-state"></a>Inga bestående tillstånd
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
Den här inställningen används en i minne-endast-provider och replikantalet till 1.

### <a name="defaults-and-generated-settings"></a>Genererade inställningar och standardvärden
När du använder den `StatePersistence` attribut, en tillståndsprovider väljs automatiskt åt dig vid körning när aktörstjänsten startar. Replikantalet, men anges vid kompilering av aktör build-verktyg för Visual Studio. Build-verktyg automatiskt generera en *standardtjänsten* för aktörstjänsten i ApplicationManifest.xml. Parametrar har skapats för **min replikuppsättning storlek** och **target replikuppsättning storlek**.

Du kan ändra dessa parametrar manuellt. Men varje gång den `StatePersistence` attribut ändras, parametrarna är inställda på repliken set storlek standardvärden för de valda `StatePersistence` attribut, åsidosätter alla föregående värden. Med andra ord de värden som du angav i ServiceManifest.xml är *endast* åsidosätts vid Byggtiden när du ändrar den `StatePersistence` attributvärde.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="state-manager"></a>Tillståndshanteraren
Varje aktör instans har sin egen tillståndshanterare: en ordlista-liknande datastruktur som lagrar på ett tillförlitligt sätt nyckel/värde-par. Tillstånd-manager är en omslutning runt en tillståndsprovider. Du kan använda den för att lagra data oavsett vilket persistence inställningen används. Det ger inte några garantier att en aktiv actor-tjänst kan ändras från en föränderliga (i minnet-endast) tillståndsinställningen till en bestående tillståndsinställningen med en rullande uppgradering och behålla data. Det är dock möjligt att ändra replikantalet för en pågående tjänst.

Tillståndshanteraren nycklar måste vara strängar. Värden är allmänna och kan vara valfri, inklusive anpassade typer. Värden som lagras i tillståndet manager måste vara datakontrakt serialiserbara eftersom de kan överföras via nätverket till andra noder vid replikering och kan skrivas till disk, beroende på en aktör tillståndsinställningen för persistence.

Tillstånd-manager visar vanliga ordlista metoder för att hantera tillstånd, liknar dem som finns i en tillförlitlig ordlista.

Exempel på Hantera aktörstillstånd läsa [åtkomst, sparar och tar bort Reliable Actors tillstånd](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Bästa praxis
Här följer några rekommendationer och felsökningstips för att hantera dina aktörstillstånd.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Kontrollera aktörstillstånd som detaljerad som möjligt
Detta är viktigt för prestanda och användning av ditt program. När det finns skrivning/uppdateringar ”namngivna tillstånd” för en aktör, är det hela värdet som motsvarar det aktuella ”namngivna tillståndet” serialiseras och skickas över nätverket till de sekundära replikerna.  Sekundära skriva det till lokal disk och svar tillbaka till den primära repliken. När den primära servern tar emot bekräftelser från ett kvorum av sekundära repliker, skriver tillståndet till den lokala disken. Anta exempelvis att värdet är en klass som har 20 medlemmar och en storlek på 1 MB. Även om du bara ändrat något av klassmedlemmar som är av storlek på 1 KB slutet betala kostnaden för serialisering och nätverks- och diskkonfiguration skrivningar för fullständig 1 MB. På samma sätt, om värdet är en samling (till exempel en lista, en matris eller en ordlista), betalar du kostnaden för den kompletta samlingen även om du ändrar en av dess medlemmar. StateManager-gränssnittet för aktörsklassen är som en ordlista. Du bör alltid modellera datastruktur som representerar aktörstillstånd ovanpå ordlistan.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Korrekt hantera livscykeln för den aktör
Du bör ha en tydlig policy om att hantera storlek på tillstånd i varje partition för en aktörstjänsten. Actor-tjänst bör ha ett fast antal aktörer och återanvända dem så mycket som möjligt. Om du skapar nya aktörer kontinuerligt, måste du ta bort dem när de är klar med sitt arbete. Ramverket lagrar vissa metadata om varje skådespelare som finns. Tar bort alla tillståndet för en aktör tar inte bort metadata om den aktören. Du måste ta bort aktören (se [tar bort aktörer och deras tillstånd](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) ta bort all information om den lagras i systemet. Som en extra kontroll, bör du fråga aktörstjänsten (se [räkna upp aktörer](service-fabric-reliable-actors-enumerate.md)) ibland för att se till att antalet aktörer ligger inom det förväntade intervallet.
 
Om du ser någonsin att ökar databasens filstorlek för en aktör Service utöver den förväntade storleken, se till att du följer riktlinjerna ovan. Om du följer dessa riktlinjer och är fortfarande databasen filen storlek problem, bör du [öppna ett supportärende](service-fabric-support.md) med produktteam för att få hjälp.

## <a name="next-steps"></a>Nästa steg

Tillstånd som lagras i Reliable Actors måste serialiseras innan dess skrivs till och replikerade för hög tillgänglighet. Läs mer om [aktören typserialisering](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Därefter lär dig mer om [aktören diagnostik och övervakning av programprestanda](service-fabric-reliable-actors-diagnostics.md).
