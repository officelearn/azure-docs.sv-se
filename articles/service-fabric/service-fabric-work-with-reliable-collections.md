---
title: Arbeta med Reliable Collections | Microsoft Docs
description: Lär dig Metodtips för att arbeta med Reliable Collections.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: jeanpaul.connock
editor: ''
ms.assetid: 39e0cd6b-32c4-4b97-bbcf-33dad93dcad1
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/12/2019
ms.author: twhitney
ms.openlocfilehash: e7f0219919fe0569633cc85b89a1a91b1704b269
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114832"
---
# <a name="working-with-reliable-collections"></a>Arbeta med Reliable Collections
Service Fabric erbjuder en tillståndskänslig programmeringsmodell som är tillgängliga för .NET-utvecklare via tillförlitliga samlingar. Mer specifikt tillhandahåller Service Fabric tillförlitlig ordlista och tillförlitlig kö klasser. När du använder de här klasserna är din delstat partitionerade (för skalbarhet), replikeras (för tillgänglighet) och överförda inom en partition (för ACID-semantik). Nu ska vi titta på en normal användning av en tillförlitlig ordlista-objekt och se vad det faktiskt gör.

```csharp
try
{
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction())
   {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync isn't called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException)
{
   // choose how to handle the situation where you couldn't get a lock on the file because it was 
   // already in use. You might delay and retry the operation
}
```

Alla åtgärder på tillförlitlig ordlista objekt (förutom ClearAsync, vilket inte är inte kan ångras), kräver ett ITransaction-objekt. Det här objektet är associerat med den eventuella och alla ändringar som du försöker göra till en tillförlitlig ordlista och/eller tillförlitlig kö objekt inom en enda partition. Du erhållit en ITransaction objekt genom att anropa partitionen är Statemanager's CreateTransaction metod.

I koden ovan skickas ITransaction-objektet till en tillförlitlig ordlista AddAsync-metoden. Internt, använder ordlista metoder som accepterar en nyckel ett reader/skrivlås kopplat till nyckeln. Om metoden ändrar nyckelns värde, metoden tar ett skrivlås på nyckeln och om metoden läser endast från nyckelns värde, är ett läslås vidtas på nyckeln. Eftersom AddAsync ändrar nyckelvärde till det nya, skickas i värdet, tas den nyckeln skrivskydd. Så om 2 (eller mer) trådar försöker lägga till värden med samma nyckel på samma gång, en tråd skaffar / skrivskydd och andra trådarna blockeras. Som standard blockerar metoder för upp till 4 sekunder att hämta Lås; efter 4 sekunder, metoderna som resulterar i en TimeoutException. Metoden överlagringar finns så att du kan skicka en explicit timeout-värdet om du föredrar.

Vanligtvis kan du skriva koden för att reagera på en TimeoutException genom att fånga det och försöker göra om hela åtgärden (som visas i koden ovan). Min enkel kod ansluter jag bara Task.Delay skicka 100 millisekunder varje gång. Men i verkligheten kan du kanske använda någon typ av exponentiell backoff-fördröjning i stället.

När låset förvärvas lägger AddAsync objektreferenser nyckel och värde i en intern tillfälliga ordlista som är associerade med ITransaction-objekt. Detta görs för att ge dig med Läs-dina-äger-skrivningar semantik. Det vill säga när du anropar AddAsync, returneras ett senare anrop till TryGetValueAsync (med samma ITransaction-objekt) värdet även om du inte har ännu har allokerats transaktionen. Därefter AddAsync Serialiserar din nyckel och värde objekt till byte-matriser och lägger till dessa byte-matriser i en loggfil på den lokala noden. Slutligen skickar AddAsync byte-matriser till de sekundära replikerna så att de har samma nyckel/värde-information. Även om informationen om nyckel/värde har skrivits till en loggfil, anses inte informationen som en del av ordlistan tills transaktionen som de är associerade med genomförts.

I koden ovan genomför anropet till CommitAsync alla åtgärder för den transaktionen. Mer specifikt läggs commit information i loggfilen på den lokala noden och skickar också commit-post till de sekundära replikerna. När ett kvorum (majoritet) replik har svarat alla dataändringar betraktas permanent och eventuella lås som är associerade med nycklar som har ändras via ITransaction-objektet blir tillgängliga så att andra trådar/transaktioner kan ändra samma nycklar och deras värden.

Om CommitAsync inte anropas (vanligtvis på grund av ett undantag som genereras), hämtar ITransaction-objektet bort. När du tar bort ett ogenomförda ITransaction-objekt, Service Fabric läggs Avbryt information till den lokala noden loggfil och inget måste skickas till någon av de sekundära replikerna. Och sedan alla lås som är associerade med nycklar som har ändras via transaktionen släpps.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Vanliga fallgropar när och hur man undviker dem
Nu när du förstår hur tillförlitliga samlingar fungerar internt, låt oss ta en titt på några vanliga missbruk av dem. Se koden nedan:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

När du arbetar med en vanlig .NET-ordlista kan du lägga till en nyckel/värde i ordlistan och ändra sedan värdet för en egenskap (till exempel LastLogin). Men fungerar den här koden inte korrekt med en tillförlitlig ordlista. Kom ihåg från den tidigare diskussionen anropet till AddAsync Serialiserar nyckel/värde-objekt till byte-matriser och sparar matriser i en lokal fil och också skickar dem till de sekundära replikerna. Om du senare ändrar en egenskap, ändras egenskapens värde i minnet. det påverkar inte den lokala filen eller data som skickas till replikerna. Om processen kraschar genereras Nyheter i minnet direkt. När en ny process startar eller om en annan replik blir primära är gamla egenskapens värde för vad som är tillgängligt.

Jag kan inte vara påfrestande tillräckligt hur enkelt det är att se vilken typ av misstag som anges ovan. Och endast lär du dig att felet när processen kraschar. Det korrekta sättet att skriva koden är helt enkelt att vända de två raderna:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Här är ett annat exempel som visar ett vanligt fel:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Återigen med den vanliga .NET ordlistor koden ovan fungerar bra och är ett vanligt mönster: utvecklare använder en nyckel för att leta upp ett värde. Om värdet finns ändrar utvecklaren värdet för en egenskap. Med reliable collections uppvisar men den här koden samma problem som redan pratat: **måste du inte ändra ett objekt när du har gett den till en tillförlitlig samling.**

Det korrekta sättet att uppdatera ett värde i en tillförlitlig samling är att hämta en referens till det befintliga värdet och tänka på det objekt som refereras till av den här referensen som inte kan ändras. Skapa sedan ett nytt objekt som är en exakt kopia av det ursprungliga objektet. Nu kan du ändra tillståndet för den här nya objekt och Skriv det nya objektet i samlingen så att den hämtar serialiserat till byte-matriser sist i lokal fil och skickas till replikerna. När du genomför den ändring eller ändringar har InMemory-objekt, den lokala filen och alla repliker du exakt samma tillstånd. Allt är bra!

Koden nedan visar det korrekta sättet att uppdatera ett värde i en tillförlitlig samling:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue)
   {
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

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiera datatyper som inte kan ändras för att förhindra programmerare fel
Vi rekommenderar vill vi kompilatorn att rapportera fel när du skapar kod som mutates tillståndet för ett objekt som du ska överväga kan ändras av misstag. Men, C# kompilatorn har inte möjlighet att göra detta. Så för att undvika potentiella programmerare buggar, rekommenderar vi starkt att du definierar vilka du använder med tillförlitliga samlingar som inte kan ändras typer. Mer specifikt kan detta innebär att du har fästs mot core värdetyper (till exempel siffror [Int32, UInt64 osv.], DateTime, Guid, tidsintervall och liknande). Du kan också använda sträng. Det är bäst att undvika samlingsegenskaper som serialisering och avserialisering av dem kan försämra prestandan ofta. Men om du vill använda samlingsegenskaper rekommenderar vi användning av. oföränderligt samlingar nätverksbibliotek ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Det här biblioteket är tillgängliga för nedladdning från http://nuget.org. Vi rekommenderar också hur du skrivskyddar dina klasser och skrivskydda fält när det är möjligt.

Typen användarinformationen nedan visar hur du definierar en typ som inte kan ändras genom att dra nytta av ovan nämnda rekommendationer.

```csharp
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo
{
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;

   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) 
   {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context)
   {
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
   public UserInfo AddItemBidding(ItemId itemId)
   {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
```

ItemId typen är också en typ som inte kan ändras enligt nedan:

```csharp
[DataContract]
public struct ItemId
{
   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName)
   {
      Seller = seller;
      ItemName = itemName;
   }
}
```

## <a name="schema-versioning-upgrades"></a>Schemaversionshantering (uppgraderingar)
Tillförlitliga samlingar serialisera internt dina objekt med hjälp av. NET'S DataContractSerializer. Serialiserade objekt har sparats till den primära repliken lokal disk och skickas också till de sekundära replikerna. När din tjänst utvecklas, är det troligt att du vill ändra vilken typ av data (schema) din tjänst kräver. Metod för versionshantering av dina data med försiktighet. Först och främst måste alltid vara deserialisera gamla data. Det innebär mer specifikt deserialisering koden måste vara oändligt bakåtkompatibla: Version 333 av koden för tjänsten måste kunna tillämpas på data som placeras i en tillförlitlig samling av version 1 av koden för tjänsten 5 år sedan.

Koden är dessutom uppgraderade en uppgraderingsdomän i taget. Du kan alltså ha två olika versioner av koden för tjänsten som körs samtidigt under en uppgradering. Du måste slipper den nya versionen av koden för tjänsten att använda det nya schemat som äldre versioner av koden för tjänsten inte kanske kan hantera det nya schemat. Om det är möjligt bör du utforma varje version av din tjänst ska framåtkompatibla med en version. Det innebär mer specifikt att V1 av koden för tjänsten ska kunna ignorera eventuella schemaelement den inte uttryckligen hantera. Det måste dock kunna spara alla data som det inte uttryckligen känner och skriva dem igen ut när du uppdaterar en ordlista nyckeln eller värdet.

> [!WARNING]
> Medan du kan ändra schemat för en nyckel, måste du kontrollera att din nyckel hash-koden och är lika med algoritmer är stabil. Om du ändrar hur något av dessa algoritmer fungerar kan du inte leta upp nyckeln i en tillförlitlig ordlista någonsin igen.
>
>

Du kan också utföra vad vanligtvis kallas en uppgradering av två. Med två faser uppgraderingen måste uppgradera du din tjänst från V1 till V2: V2 innehåller koden som vet hur du arbetar med en ny schemaändring men den här koden kan inte köras. När V2-kod läser V1 data, körs på den och skriver V1-data. Sedan, när uppgraderingen är klar över alla uppgraderingsdomäner du kan på något sätt signalera att V2-instanser som körs att uppgraderingen har slutförts. (Det här är ett sätt att signalen lansera en uppgradering av configuration; det här är vad är detta en uppgradering av två faser.) Nu, V2-instanser kan läsa V1 data, konvertera den till V2 data, fungerar på det och skriva som V2-data. När andra instanser läser V2 data de behöver inte att konvertera den, de bara fungerar på den och skriva ut V2-data.

## <a name="next-steps"></a>Nästa steg
Läs om hur du skapar vidarebefordra kompatibel datakontrakt i [framåt-kompatibla datakontrakt](https://msdn.microsoft.com/library/ms731083.aspx)

Information om bästa metoder för versionshantering datakontrakt finns [Data kontrakt versionshantering](https://msdn.microsoft.com/library/ms731138.aspx)

Läs hur du implementerar version feltoleranta datakontrakt i [Version beräkningssystem serialisering återanrop](https://msdn.microsoft.com/library/ms733734.aspx)

Läs hur du ger en datastruktur som kan samverka mellan olika versioner i [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)