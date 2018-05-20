---
title: Arbeta med tillförlitlig samlingar | Microsoft Docs
description: Läs om bästa praxis för att arbeta med tillförlitlig samlingar.
services: service-fabric
documentationcenter: .net
author: rajak
manager: timlt
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2017
ms.author: rajak
ms.openlocfilehash: 2568e116fdb3f80976d49787877d2ecf68f128ef
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="working-with-reliable-collections"></a>Arbeta med Reliable Collections
Service Fabric erbjuder en tillståndskänslig programmeringsmodell som är tillgängliga för .NET-utvecklare via tillförlitliga samlingar. Mer specifikt ger Service Fabric tillförlitliga ordlista och tillförlitlig kön klasser. När du använder dessa klasser är ditt tillstånd partitionerad (för skalbarhet) replikeras (för tillgänglighet) och överförd inom en partition (för ACID-semantik). Nu ska vi titta på en vanlig användning av en tillförlitlig Ordlisteobjekt och se vilka dess faktiskt gör.

```csharp

///retry:

try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) {
   await Task.Delay(100, cancellationToken); goto retry;
}
```

Alla åtgärder på tillförlitliga ordlista objekt (förutom ClearAsync som inte går att ångra), kräver ett ITransaction-objekt. Det här objektet är kopplad till den eventuella och alla ändringar som du försöker göra att alla tillförlitliga ordlista och/eller tillförlitlig kö objekt inom en partition. Du skaffar ett ITransaction objekt genom att anropa partitionen har Statemanager's CreateTransaction metod.

ITransaction-objektet har skickats till en tillförlitlig ordlista AddAsync metod i koden ovan. Internt, ta ordlista metoder som accepterar en nyckel reader/writer lås som är associerade med nyckeln. Om metoden ändrar värdet för nyckeln, metoden tar ett skrivlås på nyckeln och om metoden läser endast från den nyckelvärde, är ett läslås vidtas på nyckeln. Eftersom AddAsync ändrar nyckelvärde till det nya, skickas i värdet, tas den nyckeln skrivskydd. Om 2 (eller högre) trådar försöker lägga till värden med samma nyckel samtidigt, en tråd kommer aktivera skrivskydd och andra trådar blockeras på Internet. Som standard blockerar metoder för upp till 4 sekunder att låsa; metoderna utlösa en TimeoutException efter 4 sekunder. Metoden överlagringar finns så att du kan skicka ett explicit timeout-värde om du föredrar.

Vanligtvis kan du skriva koden för att ta hänsyn till en TimeoutException som fångar in den och försök hela (som visas i koden ovan). I enkla kod ansluter jag bara Task.Delay skicka 100 millisekunder varje gång. Men i verkligheten kan du använda någon typ av exponentiell inte fördröjning i stället.

När låset förvärvas läggs AddAsync objektreferenser nyckel och värde i en internt tillfälliga ordlista som är associerade med ITransaction-objekt. Detta görs för att ge dig med Läs-your-äger-skrivningar semantik. Det vill säga när du anropar AddAsync returneras ett senare anrop till TryGetValueAsync (med samma ITransaction-objekt) värdet även om du inte har ännu har allokerats transaktionen. Därefter AddAsync Serialiserar din nyckel och värde objekt till byte-matriser och lägger till dessa byte-matriser i en loggfil på den lokala noden. Slutligen skickar AddAsync byte-matriser till de sekundära replikerna så att de har samma nyckel/värde-information. Även om nyckel/värde-information har skrivits till en loggfil, anses inte informationen om en del av ordlistan tills de är associerade med transaktionen genomförts.

I koden ovan genomför anropet till CommitAsync alla åtgärder i transaktionen. Mer specifikt läggs commit information i loggfilen på den lokala noden och skickar även commit-post till de sekundära replikerna. När ett kvorum (majoritet) repliker har svarat alla dataändringar betraktas som permanenta och alla som är associerade med nycklar som har ändras via objektet ITransaction läslås frigörs så att andra trådar/transaktioner kan ändra samma nycklar och deras värden.

Om CommitAsync inte anropas (vanligtvis på grund av ett undantag som genereras), hämtar ITransaction objektet bort. Vid avyttring objektets ogenomförda ITransaction Service Fabric läggs Avbryt information till den lokala noden loggfil och ingenting måste skickas till någon av de sekundära replikerna. Och sedan alla som är associerade med nycklar som har ändras via transaktionen låsen släpps.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Vanliga fallgropar och hur du undviker dem
Nu när du förstår hur tillförlitliga samlingar fungerar internt ska vi titta på några vanliga missbruk av dem. Se koden nedan:

```csharp
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

När du arbetar med en vanlig .NET-ordlista kan du lägga till ett nyckelvärde i ordlistan och ändra sedan värdet för en egenskap (till exempel LastLogin). Men fungerar den här koden inte med en tillförlitlig ordlista. Kom ihåg från den tidigare diskussionen anropet till AddAsync Serialiserar nyckel/värde-objekt till byte-matriser och sparar sedan matriser till en lokal fil och också skickas till de sekundära replikerna. Om du senare ändrar en egenskap ändras egenskapsvärdet i minnet. det påverkar inte den lokala filen eller data som skickas till replikerna. Om processen kraschar genereras i minnet direkt. När en ny process startas eller en annan replik blir primära är gamla egenskapens värde för vad som är tillgängligt.

Jag kan inte vara påfrestande tillräckligt hur lätt det är att se vilken typ av fel som visas ovan. Och du bara lära dig om fel när processen kraschar. Det korrekta sättet att skriva koden är att återföra två rader:


```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Här är ett annat exempel som visar vanliga fel:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Igen med vanlig .NET ordlistor koden ovan fungerar bra och är ett vanligt mönster: utvecklaren använder en nyckel för att leta upp ett värde. Om värdet finns ändrar utvecklaren värdet för en egenskap. Med tillförlitlig samlingar uppvisar men den här koden samma problem som redan diskuteras: **du inte ändra ett objekt när du har tilldelat till en tillförlitlig samling.**

Det korrekta sättet att uppdatera ett värde i en tillförlitlig samling är att hämta en referens till det befintliga värdet och Överväg att objektet som refereras till av den här referensen inte ändras. Skapa sedan ett nytt objekt som är en exakt kopia av det ursprungliga objektet. Nu kan du ändra tillståndet för den här nya objekt och Skriv det nya objektet i samlingen så att den hämtar serialiseras till byte-matriser läggs till en lokal fil och skickas till replikerna. När du utför ändringar har i minnet objekt, den lokala filen och alla repliker exakt samma tillstånd. Alla är bra!

Koden nedan visar det korrekta sättet att uppdatera ett värde i en tillförlitlig samling:

```csharp

using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser =
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiera ändras datatyper för att förhindra programmerare fel
Vi rekommenderar vill vi gärna kompilatorn att rapportera fel när du av misstag skapa kod som mutates tillståndet för ett objekt som du bör du överväga att ändras. Men C#-kompilatorn har inte möjlighet att göra detta. Så för att undvika potentiell programmerare buggar, rekommenderar vi starkt att du definierar vilka du använder med tillförlitlig samlingar som inte ändras typer. Det innebär mer specifikt kan du hålla dig till core värdetyper (till exempel siffror [Int32, UInt64, etc.], DateTime, Guid, TimeSpan och liknande). Och naturligtvis kan du också använda sträng. Det är bäst att undvika samlingsegenskaper som serialisering och avserialisering av dem kan ofta kan försämra prestanda. Men om du vill använda samlingsegenskaper rekommenderar vi användning av. ändras samlingar nätverksbibliotek ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Det här biblioteket är tillgängliga för nedladdning från http://nuget.org. Vi rekommenderar också försegling klasser och skrivskydda fält när det är möjligt.

Typen användarinformationen nedan visar hur du definierar en oåterkalleliga typen genom att dra nytta av dessa rekommendationer.

```csharp

[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }

   [DataMember]
   public readonly String Email;

   // Ideally, this would be a readonly field but it can't be because OnDeserialized
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

ItemId typen är också en oåterkalleliga typen som visas här:

```csharp

[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Schemaversionshantering (uppgraderingar)
Internt, serialisera tillförlitliga samlingar med objekten. NET'S DataContractSerializer. De serialiserade objekt sparas på den primära repliken lokal disk och även skickas till de sekundära replikerna. Då tjänsten utvecklas, är det troligt att du vill ändra typ av data (schema) din tjänst kräver. Du måste närma versioning av dina data med försiktighet. Först och främst kan kunna du alltid avbryta serialiseringen gamla data. Mer specifikt det innebär att koden deserialisering måste vara oändligt bakåtkompatibla: Version 333 av koden för tjänsten måste kunna fungerar på data som placeras i en tillförlitlig samling av version 1 av koden för tjänsten 5 år sedan.

Service-koden är dessutom uppgraderade en domän i taget. Under en uppgradering kan du alltså ha två olika versioner av koden för tjänsten körs samtidigt. Måste undviker du att den nya versionen av koden för tjänsten använder det nya schemat som äldre versioner av koden för tjänsten inte kanske kan hantera det nya schemat. När det är möjligt bör du utforma varje version av din tjänst ska framåtkompatibla av version 1. Det innebär mer specifikt att V1 av koden för tjänsten ska kunna bortse från alla schemaelement det explicit inte hanteras. Det måste dock att kunna spara alla data som inte uttryckligen behöver veta om och bara skriva tillbaka ut när du uppdaterar en ordlistans nyckel eller ett värde.

> [!WARNING]
> Medan du kan ändra schemat för en nyckel, måste du kontrollera att din nyckel-hash-kod och som är lika med algoritmer är stabil. Om du ändrar hur något av dessa algoritmer fungerar kan du inte slå upp nyckeln i tillförlitliga ordlistan någonsin igen.
>
>

Du kan också utföra vad vanligtvis kallas en 2-fas uppgradering. Med en 2-fas i uppgraderingen du uppgradera din tjänst från V1 till V2: V2 innehåller koden som vet hur du arbetar med ny schemaändring men det här programmet inte köras. När koden V2 läser V1 data, fungerar på den och skriver V1-data. Sedan, när uppgraderingen är klar över alla uppgraderingsdomäner du kan på något sätt signalerar till instanser för V2 att uppgraderingen har slutförts. (Detta är ett sätt att signal att distribuera en uppgradering av configuration; det här är vad är detta en 2-fas uppgradering.) Nu, V2-instanser kan läsa V1 data, konvertera den till V2 data, fungerar på det och skriva ut den som V2-data. När andra instanser läsa V2 data, de behöver inte att konvertera den, de bara fungerar på den och skriva ut V2-data.

## <a name="next-steps"></a>Nästa steg
Läs om hur du skapar vidarebefordra kompatibel datakontrakt i [framåt-kompatibel datakontrakt](https://msdn.microsoft.com/library/ms731083.aspx).

Information om metodtips för versionshantering datakontrakt finns [Data kontraktet versionshantering](https://msdn.microsoft.com/library/ms731138.aspx).

Information om hur du implementerar version feltoleranta datakontrakt finns [Version feltoleranta serialisering återanrop](https://msdn.microsoft.com/library/ms733734.aspx).

Information om hur du skapar en datastruktur som kan samverka för flera versioner finns [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
