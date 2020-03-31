---
title: Reliable Actors statlig förvaltning
description: Beskriver hur tillståndet För tillförlitliga aktörer hanteras, bevaras och replikeras för hög tillgänglighet.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348921"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors statlig förvaltning
Reliable Actors är entrådiga objekt som kan kapsla in både logik och tillstånd. Eftersom aktörer körs på tillförlitliga tjänster kan de underhålla tillståndet på ett tillförlitligt sätt med hjälp av samma mekanismer för beständighet och replikering. På så sätt förlorar inte skådespelarna sitt tillstånd efter fel, vid återaktivering efter skräpinsamling eller när de flyttas mellan noder i ett kluster på grund av resursbalansering eller uppgraderingar.

## <a name="state-persistence-and-replication"></a>Statens persistens och replikering
Alla tillförlitliga aktörer anses vara *tillståndskänsliga* eftersom varje aktörsinstans mappar till ett unikt ID. Det innebär att upprepade anrop till samma aktörs-ID dirigeras till samma aktörsinstans. I ett tillståndslöst system är däremot klientanrop inte garanterade att dirigeras till samma server varje gång. Av denna anledning är aktörstjänster alltid tillståndskänsliga tjänster.

Även om aktörer anses vara statsmedvetna, betyder det inte att de måste lagra tillstånd på ett tillförlitligt sätt. Aktörerna kan välja nivån på tillståndet persistens och replikering baserat på deras krav på datalagring:

* **Beständigt tillstånd**: Tillståndet sparas på disken och replikeras till tre eller flera repliker. Beständigt tillstånd är det mest hållbara tillståndslagringsalternativet, där tillståndet kan kvarstå genom fullständigt klusterutbrott.
* **Flyktigt tillstånd**: Tillståndet replikeras till tre eller flera repliker och hålls endast i minnet. Flyktigt tillstånd ger återhämtning mot nodfel och aktörsfel och under uppgraderingar och resursbalansering. Tillståndet sparas dock inte på disken. Så om alla repliker går förlorade på en gång, är staten förlorad också.
* **Inget beständigt tillstånd**: Tillståndet replikeras inte eller skrivs till disk, använd endast för aktörer som inte behöver underhålla tillståndet på ett tillförlitligt sätt.

Varje nivå av uthållighet är helt enkelt en annan *tillståndsprovider* och *replikeringskonfiguration* av din tjänst. Om tillståndet skrivs till disken beror på tillståndsprovidern – komponenten i en tillförlitlig tjänst som lagrar tillstånd. Replikering beror på hur många repliker en tjänst distribueras med. Precis som med tillförlitliga tjänster kan både tillståndsprovidern och replikantalet enkelt ställas in manuellt. Aktörsramverket tillhandahåller ett attribut som, när det används på en aktör, automatiskt väljer en standardtillståndsprovider och automatiskt genererar inställningar för replikantal för att uppnå en av dessa tre persistensinställningar. Attributet StatePersistence ärvs inte av härledd klass, varje aktörstyp måste ange sin statePersistence-nivå.

### <a name="persisted-state"></a>Beständigt tillstånd
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
Den här inställningen använder en tillståndsprovider som lagrar data på disk och ställer automatiskt in antalet tjänstrepliker till 3.

### <a name="volatile-state"></a>Flyktigt tillstånd
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
Den här inställningen använder en tillståndsprovider som endast används i minnet och anger antalet repliker till 3.

### <a name="no-persisted-state"></a>Inget kvarstående tillstånd
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
Den här inställningen använder en tillståndsprovider som endast används i minnet och anger antalet repliker till 1.

### <a name="defaults-and-generated-settings"></a>Standardinställningar och genererade inställningar
När du använder `StatePersistence` attributet väljs en tillståndsprovider automatiskt åt dig vid körning när aktörstjänsten startar. Replikantalet ställs dock in vid kompilering av Visual Studio-aktörens byggverktyg. Byggverktygen genererar automatiskt en *standardtjänst* för aktörstjänsten i ApplicationManifest.xml. Parametrar skapas för **min replikuppsättningsstorlek** och **målreplikuppsättningsstorlek**.

Du kan ändra dessa parametrar manuellt. Men varje `StatePersistence` gång attributet ändras anges parametrarna till standardstorleksvärdena för replikuppsättning för det valda attributet, `StatePersistence` vilket åsidosätter eventuella tidigare värden. Med andra ord åsidosätts de värden som du *only* anger i ServiceManifest.xml `StatePersistence` först vid byggtillfället när du ändrar attributvärdet.

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

## <a name="state-manager"></a>Statligt ansvarig
Varje aktörsinstans har sin egen tillståndshanterare: en ordlisteliknande datastruktur som på ett tillförlitligt sätt lagrar nyckel-/värdepar. Tillståndshanteraren är ett omslag runt en tillståndsprovider. Du kan använda den för att lagra data oavsett vilken beständighetsinställning som används. Det ger inga garantier för att en tjänst som körs aktör kan ändras från en flyktig (endast i minnet) till en beständig tillståndsinställning genom en rullande uppgradering samtidigt som data bevaras. Det är dock möjligt att ändra replikantalet för en tjänst som körs.

Tangenter för tillståndshanteraren måste vara strängar. Värden är generiska och kan vara vilken typ som helst, inklusive anpassade typer. Värden som lagras i tillståndshanteraren måste vara serialiserbara för datakontrakt eftersom de kan överföras över nätverket till andra noder under replikeringen och kan skrivas till disk, beroende på aktörens inställning för tillståndsbeständighet.

Tillståndshanteraren exponerar vanliga ordlistemetoder för att hantera tillstånd, liknande dem som finns i Tillförlitlig ordlista.

Exempel på hantering av aktörstillstånd läser du [Åtkomst, sparar och tar bort tillståndet Tillförlitliga aktörer](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Bästa praxis
Här är några förslag på metoder och felsökningstips för att hantera aktörstillståndet.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Gör aktörstillståndet så detaljerat som möjligt
Detta är avgörande för prestanda och resursanvändning för ditt program. När det finns någon skriv/uppdatering till "namngivet tillstånd" för en aktör, är hela värdet som motsvarar det "namngivna tillståndet" serialiserat och skickas över nätverket till sekundära repliker.  Sekundärerna skriver den till lokal disk och svarar tillbaka till den primära repliken. När den primära tar emot bekräftelser från kvorum av sekundära repliker, skriver den tillståndet till sin lokala disk. Anta till exempel att värdet är en klass som har 20 medlemmar och en storlek på 1 MB. Även om du bara ändrat en av klassmedlemmarna som är av storlek 1 KB, slutar det med att du betalar kostnaden för serialisering och nätverks- och diskskrivningar för hela 1 MB. Om värdet är en samling (till exempel en lista, matris eller ordlista) betalar du kostnaden för hela samlingen även om du ändrar en av dess medlemmar. StateManager-gränssnittet för aktörsklassen är som en ordbok. Du bör alltid modellera datastrukturen som representerar aktörstillstånd ovanpå den här ordlistan.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Korrekt hantera skådespelarens livscykel
Du bör ha en tydlig princip om hur stor tillståndsstorleken är i varje partition i en aktörstjänst. Din aktör tjänst bör ha ett fast antal aktörer och återanvända dem så mycket som möjligt. Om du kontinuerligt skapar nya aktörer måste du ta bort dem när de är klara med sitt arbete. Aktörsramverket lagrar vissa metadata om varje aktör som finns. Om du tar bort alla tillstånd för en aktör tas metadata inte bort om aktören. Du måste ta bort aktören (se [ta bort aktörer och deras tillstånd)](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)för att ta bort all information om den som lagras i systemet. Som en extra kontroll bör du fråga aktörstjänsten (se [räkna upp aktörer)](service-fabric-reliable-actors-enumerate.md)då och då för att se till att antalet aktörer ligger inom det förväntade intervallet.
 
Om du någonsin ser att databasfilstorleken för en aktörstjänst ökar utöver den förväntade storleken kontrollerar du att du följer föregående riktlinjer. Om du följer dessa riktlinjer och fortfarande är problem med databasfilstorleken bör du [öppna en supportbiljett](service-fabric-support.md) med produktteamet för att få hjälp.

## <a name="next-steps"></a>Nästa steg

Tillstånd som lagras i Reliable Actors måste serialiseras innan det skrivs till disk och replikeras för hög tillgänglighet. Läs mer om [serialisering av aktörstyp](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Läs sedan mer om [skådespelares diagnostik och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md).
