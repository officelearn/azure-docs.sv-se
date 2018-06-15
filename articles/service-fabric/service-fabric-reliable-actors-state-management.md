---
title: Reliable Actors tillstånd management | Microsoft Docs
description: Beskriver hur Reliable Actors tillstånd hanteras, beständiga och replikeras för hög tillgänglighet.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 3cab4d87eacc7bce17da64cda213086c262179a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206206"
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

Exempel för att hantera aktörstillstånd läsa [åtkomst, spara och ta bort Reliable Actors statusen](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Bästa praxis
Här följer några rekommendationer och felsökningstips för att hantera dina aktörstillstånd.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Kontrollera tillståndet aktören som detaljerad som möjligt
Detta är avgörande för prestanda och resursanvändningen för ditt program. När alla skrivning/uppdateringar ”namngivna tillstånd” en aktörs serialiseras hela värdet som motsvarar det ”namngiven tillståndet” och skickas över nätverket till de sekundära replikerna.  Sekundära skriver den till lokal disk och svar tillbaka till den primära repliken. När den primära servern tar emot bekräftelser från ett kvorum av sekundära repliker, skriver tillståndet till den lokala disken. Anta till exempel att värdet är en klass som har 20 medlemmar och en storlek på 1 MB. Även om du bara ändrat något av klassmedlemmar som är av storlek 1 KB du hamnar betalar kostnaden för serialisering och nätverks- och skrivåtgärder för fullständig 1 MB. På liknande sätt, om värdet är en samling (t.ex en lista, matris eller ordlista), betalar du kostnaden för den kompletta samlingen även om du ändrar en av dess medlemmar. Gränssnittet StateManager i klassen aktören påminner om en ordlista. Du bör alltid modellen datastrukturen som representerar aktörstillstånd ovanpå den här ordlistan.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Hantera korrekt skådespelare, livscykel
Du bör ha Rensa princip om att hantera storleken på tillstånd i varje partition för en aktören-tjänst. Aktören tjänsten ska ha ett fast antal aktörer och återanvända en mycket som möjligt. Om du skapar nya aktörer kontinuerligt, måste du ta bort dem när de har sitt arbete. Aktören framework lagrar vissa metadata om varje aktören finns. Ta bort alla tillstånd för en aktör tas inte bort metadata om att aktören. Du måste ta bort aktören (finns [ta bort aktörer och deras tillstånd](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) ta bort all information om den lagras i systemet. Som en extra kontroll ska du fråga tjänsten aktören (se [uppräkning aktörer](service-fabric-reliable-actors-platform.md)) ibland att kontrollera antalet aktörer inom det förväntade intervallet.
 
Om du ser någonsin att databasens filstorlek för en tjänst aktören öka bortom den förväntade storleken, kontrollerar du att du följer riktlinjerna ovan. Om du följer dessa riktlinjer och är fortfarande databasen problem-filens storlek, bör du [skapar ett supportärende](service-fabric-support.md) med Produktteamet för att få hjälp.

## <a name="next-steps"></a>Nästa steg

Tillstånd som är lagrad i Reliable Actors måste serialiseras innan dess skrivits till disk och replikeras för hög tillgänglighet. Lär dig mer om [aktören typserialiseringen](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Därefter lär dig mer om [aktören diagnostik- och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md).
