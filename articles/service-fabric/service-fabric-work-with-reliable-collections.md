---
title: Arbeta med Reliable Collections
description: Lär dig de bästa metoderna för att arbeta med tillförlitliga samlingar i ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94836a37a62e3eeffb94d891980cc02694bd973e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409811"
---
# <a name="working-with-reliable-collections"></a>Arbeta med Reliable Collections
Service Fabric erbjuder en tillståndskänslig programmeringsmodell som är tillgänglig för .NET-utvecklare via Reliable Collections. Service Fabric tillhandahåller tillförlitlig ordlista och tillförlitliga köklasser. När du använder dessa klasser är ditt tillstånd partitionerat (för skalbarhet), replikerat (för tillgänglighet) och verkställts inom en partition (för ACID-semantik). Låt oss titta på en typisk användning av en tillförlitlig ordlista objekt och se vad det faktiskt gör.

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

Alla åtgärder på tillförlitliga ordlisteobjekt (förutom ClearAsync, som inte kan ångras), kräver ett ITransaction-objekt. Det här objektet har associerat med det alla ändringar som du försöker göra i någon tillförlitlig ordlista och /eller tillförlitliga köobjekt i en enda partition. Du hämtar ett ITransaction-objekt genom att anropa partitionens StateManagers CreateTransaction-metod.

I koden ovan skickas ITransaction-objektet till en tillförlitlig ordlistas AddAsync-metod. Internt tar ordlistemetoder som accepterar en nyckel ett läsar-/brännarelås som är associerat med nyckeln. Om metoden ändrar nyckelns värde, tar metoden ett skrivlås på nyckeln och om metoden bara läser från nyckelns värde, tas ett läslås på nyckeln. Eftersom AddAsync ändrar nyckelns värde till det nya, inst passerade värdet tas nyckelns skrivlås. Så om 2 (eller fler) trådar försöker lägga till värden med samma nyckel samtidigt, kommer en tråd att hämta skrivlåset och de andra trådarna blockerar. Som standard blockerar metoderna i upp till 4 sekunder för att hämta låset. efter 4 sekunder, metoderna kasta en TimeoutException. Metodöverbelastningar finns så att du kan skicka ett explicit timeout-värde om du föredrar det.

Vanligtvis skriver du din kod för att reagera på en TimeoutException genom att fånga den och försöka igen hela åtgärden (som visas i koden ovan). I min enkla kod, jag ringer bara Task.Delay passerar 100 millisekunder varje gång. Men i verkligheten kan du vara bättre att använda någon form av exponentiell back-off dröjsmål istället.

När låset har hämtats lägger AddAsync till nyckel- och värdeobjektreferenserna i en intern tillfällig ordlista som är associerad med ITransaction-objektet. Detta görs för att ge dig läs-din-egen-skriver semantik. Det vill säga när du har ringt AddAsync returnerar ett senare anrop till TryGetValueAsync (med samma ITransaction-objekt) värdet även om du ännu inte har bekräftat transaktionen. AddAsync serialiserar sedan dina nyckel- och värdeobjekt till bytematriser och lägger till dessa bytematriser i en loggfil på den lokala noden. Slutligen skickar AddAsync byte-matriserna till alla sekundära repliker så att de har samma nyckel-/värdeinformation. Även om nyckel-/värdeinformationen har skrivits till en loggfil, betraktas informationen inte som en del av ordlistan förrän transaktionen som de är associerade med har genomförts.

I koden ovan genomför anropet till CommitAsync alla transaktionens åtgärder. Specifikt lägger den till commit-information till loggfilen på den lokala noden och skickar även commit-posten till alla sekundära repliker. När en kvorum (majoritet) av replikerna har svarat, alla dataändringar anses permanenta och alla lås som är associerade med nycklar som manipulerades via ITransaction-objektet släpps så att andra trådar / transaktioner kan manipulera samma nycklar och deras värden.

Om CommitAsync inte anropas (vanligtvis på grund av att ett undantag genereras) tas objektet ITransaction bort. När du inaktiverar ett oengagerad ITransaction-objekt avbryter Service Fabric information till den lokala nodens loggfil och ingenting behöver skickas till någon av de sekundära replikerna. Och sedan, alla lås i samband med nycklar som manipulerades via transaktionen släpps.

## <a name="volatile-reliable-collections"></a>Flyktiga tillförlitliga samlingar 
I vissa arbetsbelastningar, till exempel en replikerad cache, kan tillfällig dataförlust tolereras. Om du undviker att data blir beständiga på disken kan det ge bättre fördröjningar och dataflöde när du skriver till Tillförlitliga ordlistor. Avvägningen för en brist på uthållighet är att om kvorumförlust inträffar, kommer fullständig dataförlust att inträffa. Eftersom kvorumförlust är en sällsynt händelse kan den ökade prestandan vara värd den sällsynta risken för dataförlust för dessa arbetsbelastningar.

För närvarande är flyktigt stöd endast tillgängligt för tillförlitliga ordlistor och tillförlitliga köer och inte ReliableConcurrentQueues. Se listan över [varningar för](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections) att informera ditt beslut om huruvida du ska använda flyktiga samlingar.

Om du vill aktivera flyktigt ```HasPersistedState``` stöd i tjänsten ```false```ställer du in flaggan i tjänsttypsdeklarationen på , så här:
```xml
<StatefulServiceType ServiceTypeName="MyServiceType" HasPersistedState="false" />
```

>[!NOTE]
>Befintliga kvarstående tjänster kan inte göras volatila, och vice versa. Om du vill göra det måste du ta bort den befintliga tjänsten och sedan distribuera tjänsten med den uppdaterade flaggan. Detta innebär att du måste vara villig att ådra ```HasPersistedState``` dig fullständig dataförlust om du vill ändra flaggan. 

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Vanliga fallgropar och hur man undviker dem
Nu när du förstår hur de tillförlitliga samlingarna fungerar internt, låt oss ta en titt på några vanliga missbruk av dem. Se koden nedan:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // AddAsync serializes the name/user, logs the bytes,
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property's value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
```

När du arbetar med en vanlig .NET-ordlista kan du lägga till en nyckel/ett värde i ordlistan och sedan ändra värdet för en egenskap (till exempel LastLogin). Den här koden fungerar dock inte korrekt med en tillförlitlig ordlista. Kom ihåg från den tidigare diskussionen, anropet till AddAsync serialiserar nyckel-/värdeobjekten till bytematriser och sparar sedan matriserna i en lokal fil och skickar dem även till de sekundära replikerna. Om du senare ändrar en egenskap ändrar detta egenskapens värde i minnet. Det påverkar inte den lokala filen eller de data som skickas till replikerna. Om processen kraschar, vad som finns i minnet kastas bort. När en ny process startar eller om en annan replik blir primär, är det gamla egenskapsvärdet det som är tillgängligt.

Jag kan inte nog betona hur lätt det är att göra den typ av misstag som visas ovan. Och, du kommer bara lära sig om misstaget om / när processen går ner. Det korrekta sättet att skriva koden är helt enkelt att vända de två raderna:


```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync();
}
```

Här är ett annat exempel som visar ett vanligt misstag:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
   ConditionalValue<User> user = await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue)
   {
      // The line below updates the property's value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync();
   }
}
```

Återigen, med vanliga .NET ordböcker, koden ovan fungerar bra och är ett vanligt mönster: utvecklaren använder en nyckel för att slå upp ett värde. Om värdet finns ändrar utvecklaren en egenskaps värde. Men med tillförlitliga samlingar uppvisar den här koden samma problem som redan diskuterats: **du får inte ändra ett objekt när du har gett det till en tillförlitlig samling.**

Det korrekta sättet att uppdatera ett värde i en tillförlitlig samling, är att få en referens till det befintliga värdet och överväga objektet som avses i denna referens oföränderlig. Skapa sedan ett nytt objekt som är en exakt kopia av det ursprungliga objektet. Nu kan du ändra tillståndet för det nya objektet och skriva det nya objektet i samlingen så att det blir serialiserad till byte arrayer, läggas till den lokala filen och skickas till replikerna. När du har begått ändringen eller objekten har in-memory-objekten, den lokala filen och alla repliker exakt tillstånd. Allt är bra!

Koden nedan visar det korrekta sättet att uppdatera ett värde i en tillförlitlig samling:

```csharp
using (ITransaction tx = StateManager.CreateTransaction())
{
   // Use the user's name to look up their data
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

      // Update the key's value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);
      await tx.CommitAsync();
   }
}
```

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definiera oföränderliga datatyper för att förhindra programmerarfel
Helst vill vi att kompilatorn ska rapportera fel när du av misstag producerar kod som muterar tillståndet för ett objekt som du ska betrakta oföränderligt. Men C # kompilatorn inte har förmågan att göra detta. För att undvika potentiella programmerarfel rekommenderar vi därför starkt att du definierar de typer du använder med tillförlitliga samlingar som oföränderliga typer. Detta innebär att du håller dig till kärnvärdetyper (till exempel siffror [Int32, UInt64, etc.], DateTime, Guid, TimeSpan och liknande). Du kan också använda String. Det är bäst att undvika insamling egenskaper som serialisering och deserializing dem kan ofta skada prestanda. Men om du vill använda samlingsegenskaper rekommenderar vi starkt att du använder . NET:s oföränderliga samlingsbibliotek ([System.Collections.Immutable](https://www.nuget.org/packages/System.Collections.Immutable/)). Det här biblioteket kan https://nuget.orghämtas från . Vi rekommenderar också att du förseglar dina klasser och gör fälten skrivskyddade när det är möjligt.

Användarinfo-typen nedan visar hur du definierar en oföränderlig typ som utnyttjar ovannämnda rekommendationer.

```csharp
[DataContract]
// If you don't seal, you must ensure that any derived classes are also immutable
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

ItemId-typen är också en oföränderlig typ som visas här:

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

## <a name="schema-versioning-upgrades"></a>Schemaversionering (uppgraderingar)
Internt serialiserar tillförlitliga samlingar dina objekt med . NET:s DataContractSerializer. Serialiserade objekt sparas till den primära replikens lokala disk och överförs också till sekundära repliker. När tjänsten förfaller är det troligt att du vill ändra vilken typ av data (schema) som tjänsten kräver. Närma sig versionshantering av dina data med stor omsorg. Först och främst måste du alltid kunna deserialisera gamla data. Detta innebär att din avserialiseringskod måste vara oändligt bakåtkompatibel: Version 333 av din servicekod måste kunna fungera på data som placeras i en tillförlitlig samling efter version 1 av din tjänstkod för 5 år sedan.

Dessutom uppgraderas servicekoden en uppgraderingsdomän i taget. Så under en uppgradering har du två olika versioner av din servicekod som körs samtidigt. Du måste undvika att den nya versionen av tjänstkoden använder det nya schemat eftersom gamla versioner av tjänstkoden kanske inte kan hantera det nya schemat. När det är möjligt bör du utforma varje version av din tjänst så att den är framåtkompatibel med en version. Detta innebär att V1 i tjänstkoden ska kunna ignorera alla schemaelement som den inte uttryckligen hanterar. Den måste dock kunna spara alla data som den inte uttryckligen känner till och skriva tillbaka dem när du uppdaterar en ordlistenyckel eller ett värde.

> [!WARNING]
> Även om du kan ändra schemat för en nyckel måste du se till att nyckelns hash-kod och algoritmer är lika med. Om du ändrar hur någon av dessa algoritmer fungerar, kommer du inte att kunna slå upp nyckeln i den tillförlitliga ordlistan någonsin igen.
> .NET-strängar kan användas som en nyckel men använda själva strängen som nyckeln – använd inte resultatet av String.GetHashCode som nyckel.

Du kan också utföra vad som vanligtvis kallas en två uppgradering. Med en tvåfasuppgradering uppgraderar du tjänsten från V1 till V2: V2 innehåller koden som vet hur du hanterar den nya schemaändringen men den här koden körs inte. När V2-koden läser V1-data fungerar den på den och skriver V1-data. Sedan, när uppgraderingen är klar över alla uppgraderingsdomäner, kan du på något sätt signalera till de V2-instanser som körs att uppgraderingen är klar. (Ett sätt att signalera detta är att distribuera en konfigurationsuppgradering, det är det som gör detta till en tvåfasuppgradering.) Nu kan V2-instanserna läsa V1-data, konvertera dem till V2-data, arbeta på den och skriva ut dem som V2-data. När andra instanser läser V2-data behöver de inte konvertera dem, de fungerar bara på den och skriver ut V2-data.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar framåtkompatibla datakontrakt finns i [Vidarebefordrankompatibla datakontrakt](https://msdn.microsoft.com/library/ms731083.aspx)

Information om hur du gör det bästa sättet att ta reda på de bästa metoderna för versionshantering av datakontrakt finns i [Versionshantering av data](https://msdn.microsoft.com/library/ms731138.aspx)

Mer information om hur du implementerar versionstoleranta datakontrakt finns i [Versionstoleranta serialiseringsåterknäst](https://msdn.microsoft.com/library/ms733734.aspx)

Mer information om hur du tillhandahåller en datastruktur som kan samverka mellan flera versioner finns [i IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx)
