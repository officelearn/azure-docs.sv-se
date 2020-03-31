---
title: Säkerhetskopiera och återställa Service Fabric
description: Konceptuell dokumentation för Säkerhetskopiering och återställning av Service Fabric, en tjänst för att konfigurera säkerhetskopiering av tillförlitliga tillståndskänsliga tjänster och reliable actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922783"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Säkerhetskopiera och återställa tillförlitliga tjänster och pålitliga aktörer
Azure Service Fabric är en plattform med hög tillgänglighet som replikerar tillståndet över flera noder för att upprätthålla den här hög tillgängligheten.  Även om en nod i klustret misslyckas fortsätter tjänsterna att vara tillgängliga. Även om denna inbyggda redundans som tillhandahålls av plattformen kan vara tillräcklig för vissa, är det i vissa fall önskvärt att tjänsten säkerhetskopierar data (till ett externt arkiv).

> [!NOTE]
> Det är viktigt att säkerhetskopiera och återställa dina data (och testa att det fungerar som förväntat) så att du kan återställa från scenarier för dataförlust.
> 

> [!NOTE]
> Microsoft rekommenderar att du använder [Periodisk säkerhetskopiering och återställning](service-fabric-backuprestoreservice-quickstart-azurecluster.md) för att konfigurera säkerhetskopiering av tillförlitliga tillståndstjänster och tillförlitliga aktörer. 
> 


En tjänst kanske till exempel vill säkerhetskopiera data för att skydda mot följande scenarier:

- I händelse av permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av replikerna av en tjänstpartition
- Administrativa fel där tillståndet av misstag tas bort eller skadas. Detta kan till exempel inträffa om en administratör med tillräcklig behörighet felaktigt tar bort tjänsten.
- Buggar i tjänsten som orsakar dataskador. Detta kan till exempel inträffa när en uppgradering av servicekod börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kan både koden och uppgifterna behöva återställas till ett tidigare tillstånd.
- Bearbetning av offlinedata. Det kan vara praktiskt att ha offlinebearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Med funktionen Säkerhetskopiering/återställning kan tjänster som bygger på API:et för tillförlitliga tjänster skapa och återställa säkerhetskopior. Api:erna för säkerhetskopiering som tillhandahålls av plattformen tillåter säkerhetskopiering(er) av en tjänstpartitions tillstånd, utan att blockera läs- eller skrivåtgärder. Återställnings-API:erna gör att en tjänstpartitions tillstånd återställs från en vald säkerhetskopia.

## <a name="types-of-backup"></a>Typer av säkerhetskopiering
Det finns två alternativ för säkerhetskopiering: Fullständig och Inkrementell.
En fullständig säkerhetskopia är en säkerhetskopia som innehåller alla data som krävs för att återskapa replikens tillstånd: kontrollpunkter och alla loggposter.
Eftersom den har kontrollpunkter och loggen kan en fullständig säkerhetskopia återställas av sig själv.

Problemet med fullständiga säkerhetskopior uppstår när kontrollpunkterna är stora.
En replik med 16 GB tillstånd har till exempel kontrollpunkter som summerar ungefär 16 GB.
Om vi har ett återställningspunktsmål på fem minuter, måste repliken säkerhetskopieras var femte minut.
Varje gång den säkerhetskopierar måste den kopiera 16 GB kontrollpunkter utöver 50 `CheckpointThresholdInMB`MB (konfigurerbart med hjälp av) värde av loggar.

![Fullständigt exempel på säkerhetskopiering.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Lösningen på det här problemet är inkrementella säkerhetskopior, där säkerhetskopieringen bara innehåller de ändrade loggposterna sedan den senaste säkerhetskopieringen.

![Exempel på inkrementell säkerhetskopiering.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Eftersom inkrementella säkerhetskopior bara är ändringar sedan den senaste säkerhetskopian (inte innehåller kontrollpunkterna) tenderar de att vara snabbare men de kan inte återställas på egen hand.
För att återställa en inkrementell säkerhetskopiering krävs hela säkerhetskopieringskedjan.
En backup-kedja är en kedja av säkerhetskopior som börjar med en fullständig säkerhetskopiering och följs av ett antal sammanhängande inkrementella säkerhetskopior.

## <a name="backup-reliable-services"></a>Tillförlitliga tjänster för säkerhetskopiering
Tjänstförfattaren har full kontroll över när säkerhetskopior ska göras och var säkerhetskopior ska lagras.

För att starta en säkerhetskopia måste tjänsten anropa `BackupAsync`den ärvda medlemsfunktionen .  
Säkerhetskopior kan endast göras från primära repliker och de kräver att skrivstatus beviljas.

Som visas `BackupAsync` nedan, `BackupDescription` tar i ett objekt, där man kan ange en fullständig `Func<< BackupInfo, CancellationToken, Task<bool>>>` eller inkrementell säkerhetskopiering, samt en motringningsfunktion, som anropas när säkerhetskopieringsmappen har skapats lokalt och är redo att flyttas ut till någon extern lagring.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Begäran om att ta en `FabricMissingFullBackupException`inkrementell säkerhetskopia kan misslyckas med . Det här undantaget anger att en av följande saker händer:

- repliken har aldrig tagit en fullständig säkerhetskopia eftersom den har blivit primär,
- några av loggposterna sedan den senaste säkerhetskopieringen har trunkerats eller
- repliken `MaxAccumulatedBackupLogSizeInMB` passerade gränsen.

Användare kan öka sannolikheten för att kunna göra inkrementella säkerhetskopior genom att konfigurera `MinLogSizeInMB` eller `TruncationThresholdFactor`.
Om du ökar dessa värden ökar användningen per replikdisk.
Mer information finns i [Konfiguration av tillförlitliga tjänster](service-fabric-reliable-services-configuration.md)

`BackupInfo`ger information om säkerhetskopian, inklusive platsen för mappen där`BackupInfo.Directory`körningen sparade säkerhetskopian ( ). Motringningsfunktionen kan `BackupInfo.Directory` flytta till ett externt arkiv eller en annan plats.  Den här funktionen returnerar också en bool som anger om den kunde flytta säkerhetskopieringsmappen till målplatsen.

Följande kod visar hur `BackupCallbackAsync` metoden kan användas för att överföra säkerhetskopian till Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

I föregående exempel `ExternalBackupStore` är exempelklassen som används för att samverka med Azure Blob-lagring och `UploadBackupFolderAsync` är den metod som komprimerar mappen och placerar den i Azure Blob-arkivet.

Tänk på följande:

  - Det kan bara finnas en säkerhetskopiering under flygning per replik vid en given tidpunkt. Mer än `BackupAsync` ett samtal i `FabricBackupInProgressException` taget kommer att kasta för att begränsa inflight säkerhetskopior till en.
  - Om en replik växlar över medan en säkerhetskopia pågår kanske säkerhetskopieringen inte har slutförts. När redundansen är klar är det därför tjänstens ansvar att `BackupAsync` starta om säkerhetskopian genom att anropa vid behov.

## <a name="restore-reliable-services"></a>Återställa tillförlitliga tjänster
I allmänhet kan de fall då du kan behöva utföra en återställningsåtgärd delas in i någon av dessa kategorier:

  - Tjänstpartitionen förlorade data. Disken för två av tre repliker för en partition (inklusive den primära repliken) skadas eller rensas till exempel. Den nya primärt kan behöva återställa data från en säkerhetskopia.
  - Hela tjänsten går förlorad. En administratör tar till exempel bort hela tjänsten och tjänsten och data måste återställas.
  - Tjänsten replikerade skadade programdata (till exempel på grund av ett programfel). I det här fallet måste tjänsten uppgraderas eller återställas för att ta bort orsaken till skadan och icke-korrupta data måste återställas.

Även om många metoder är möjliga, `RestoreAsync` erbjuder vi några exempel på hur du använder för att återställa från ovanstående scenarier.

## <a name="partition-data-loss-in-reliable-services"></a>Partitionsdataförlust i tillförlitliga tjänster
I det här fallet skulle körningen automatiskt identifiera dataförlusten och anropa API:et. `OnDataLossAsync`

Tjänstförfattaren måste utföra följande för att återställa:

  - Åsidosätt metoden `OnDataLossAsync`för virtuell basklass .
  - Hitta den senaste säkerhetskopian på den externa platsen som innehåller tjänstens säkerhetskopior.
  - Hämta den senaste säkerhetskopian (och packa upp säkerhetskopian i säkerhetskopieringsmappen om den komprimerades).
  - Metoden `OnDataLossAsync` ger `RestoreContext`en . Anropa `RestoreAsync` API:et `RestoreContext`på den tillföljande .
  - Returnera sant om restaureringen var en framgång.

Följande är ett exempel `OnDataLossAsync` på implementering av metoden:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`skickas in `RestoreContext.RestoreAsync` till samtalet innehåller `BackupFolderPath`en medlem som heter .
När du återställer en `BackupFolderPath` enda fullständig säkerhetskopia bör den ställas in på den lokala sökvägen till mappen som innehåller hela säkerhetskopian.
När du återställer en fullständig säkerhetskopia och `BackupFolderPath` ett antal inkrementella säkerhetskopior bör du ställas in på den lokala sökvägen till mappen som inte bara innehåller hela säkerhetskopian, utan även alla inkrementella säkerhetskopior.
`RestoreAsync`samtal kan `FabricMissingFullBackupException` kasta `BackupFolderPath` om den medföljande inte innehåller en fullständig säkerhetskopia.
Det kan `ArgumentException` också `BackupFolderPath` kasta om har en bruten kedja av inkrementella säkerhetskopior.
Om den till exempel innehåller hela säkerhetskopian, den första inkrementella och den tredje inkrementella säkerhetskopieringen men ingen den andra inkrementella säkerhetskopieringen.

> [!NOTE]
> RestorePolicy är som standard säker.  Detta innebär `RestoreAsync` att API:et misslyckas med ArgumentException om den upptäcker att säkerhetskopieringsmappen innehåller ett tillstånd som är äldre än eller lika med tillståndet i den här repliken.  `RestorePolicy.Force`kan användas för att hoppa över denna säkerhetskontroll. Detta anges som en `RestoreDescription`del av .
> 

## <a name="deleted-or-lost-service"></a>Borttagen eller förlorad tjänst
Om en tjänst tas bort måste du först återskapa tjänsten innan data kan återställas.  Det är viktigt att skapa tjänsten med samma konfiguration, till exempel partitioneringsschema, så att data kan återställas sömlöst.  När tjänsten är uppe måste API:et för att återställa data (ovan)`OnDataLossAsync` anropas på varje partition av den här tjänsten. Ett sätt att uppnå detta är att använda [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) på varje partition.  

Från och med nu är implementeringen densamma som i ovanstående scenario. Varje partition måste återställa den senaste relevanta säkerhetskopian från det externa arkivet. En varning är att partitions-ID kan ha nu ändrats, eftersom körningen skapar partitions-ID dynamiskt. Tjänsten måste därför lagra lämplig partitionsinformation och tjänstnamn för att identifiera rätt senaste säkerhetskopian för att återställa från för varje partition.

> [!NOTE]
> Det rekommenderas inte `FabricClient.ServiceManager.InvokeDataLossAsync` att använda på varje partition för att återställa hela tjänsten, eftersom det kan skada ditt klustertillstånd.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikering av skadade programdata
Om den nyligen distribuerade programuppgraderingen har ett fel kan det orsaka dataskador. En programuppgradering kan till exempel börja uppdatera varje telefonnummerpost i en tillförlitlig ordlista med ett ogiltigt riktnummer.  I det här fallet replikeras de ogiltiga telefonnummer eftersom Service Fabric inte är medveten om vilken typ av data som lagras.

Det första du ska göra när du upptäcker en sådan flagrant bugg som orsakar datakorruption är att frysa tjänsten på programnivå och, om möjligt, uppgradera till den version av programkoden som inte har felet.  Men även efter att servicekoden har åtgärdats kan data fortfarande vara skadade och därför kan data behöva återställas.  I sådana fall kanske det inte är tillräckligt för att återställa den senaste säkerhetskopian, eftersom de senaste säkerhetskopiorna också kan vara skadade.  Således måste du hitta den sista säkerhetskopian som gjordes innan data blev skadade.

Om du inte är säker på vilka säkerhetskopior som är skadade kan du distribuera ett nytt Service Fabric-kluster och återställa säkerhetskopior av berörda partitioner precis som scenariot "Borttagen eller förlorad tjänst".  För varje partition börjar du återställa säkerhetskopiorna från det senaste till det minsta. När du hittar en säkerhetskopia som inte har skadan flyttar/tar du bort alla säkerhetskopior av den här partitionen som var nyare (än säkerhetskopian). Upprepa den här processen för varje partition. Nu, `OnDataLossAsync` när anropas på partitionen i produktionsklustret, kommer den senaste säkerhetskopian som hittades i det externa arkivet att vara den som plockas av ovanstående process.

Nu kan stegen i avsnittet "Borttagen eller förlorad tjänst" användas för att återställa tjänstens tillstånd till tillståndet innan buggykoden skadade tillståndet.

Tänk på följande:

  - När du återställer finns det en risk att säkerhetskopian som återställs är äldre än tillståndet för partitionen innan data förlorades. På grund av detta bör du återställa endast som en sista utväg för att återställa så mycket data som möjligt.
  - Strängen som representerar sökvägen till säkerhetskopian och sökvägarna för filer i säkerhetskopieringsmappen kan vara större än 255 tecken, beroende på FabricDataRoot-sökvägen och programtypsnamnets längd. Detta kan orsaka vissa .NET-metoder, till `Directory.Move`exempel , att kasta `PathTooLongException` undantaget. En lösning är att direkt anropa kernel32 `CopyFile`API:er, till exempel .

## <a name="back-up-and-restore-reliable-actors"></a>Säkerhetskopiera och återställa reliable actors


Reliable Actors Framework bygger på tillförlitliga tjänster. ActorService, som är värd för aktören/aktörerna, är en tillståndskänslig tillförlitlig tjänst. Därför är alla funktioner för säkerhetskopiering och återställning som är tillgängliga i Reliable Services också tillgängliga för reliable actors (förutom beteenden som är tillståndsspecifika). Eftersom säkerhetskopior kommer att tas per partition basis, stater för alla aktörer i den partitionen kommer att säkerhetskopieras (och restaurering är liknande och kommer att ske per partition basis). Om du vill utföra säkerhetskopiering/återställning bör tjänstägaren skapa en anpassad aktörstjänstklass som härleds från klassen ActorService och sedan gör säkerhetskopiering/återställning som liknar tillförlitliga tjänster enligt beskrivningen ovan i föregående avsnitt.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

När du skapar en anpassad aktörstjänstklass måste du också registrera det när du registrerar aktören.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Standardtillståndsprovidern för `KvsActorStateProvider`Reliable Actors är . Inkrementell säkerhetskopiering är `KvsActorStateProvider`inte aktiverad som standard för . Du kan aktivera inkrementell säkerhetskopiering genom att skapa `KvsActorStateProvider` med rätt inställning i konstruktorn och sedan skicka den till ActorService-konstruktorn enligt följande kodavsnitt:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

När inkrementell säkerhetskopiering har aktiverats kan du misslyckas med en inkrementell säkerhetskopiering med FabricMissingFullBackupException av något av följande skäl och du måste göra en fullständig säkerhetskopiering innan du tar inkrementella säkerhetskopieringar:

  - Repliken har aldrig tagit en fullständig säkerhetskopia sedan den blev primär.
  - Några av loggposterna har trunkerats sedan den senaste säkerhetskopieringen gjordes.

När inkrementell säkerhetskopiering är aktiverad använder `KvsActorStateProvider` du inte cirkulär buffert för att hantera dess loggposter och trunkerar den regelbundet. Om ingen säkerhetskopiering görs av användaren under en period av 45 minuter trunkerar systemet automatiskt loggposterna. Det här intervallet kan konfigureras genom att `logTruncationIntervalInMinutes` ange i `KvsActorStateProvider` konstruktorn (liknande när inkrementell säkerhetskopiering aktiveras). Loggposterna kan också trunkeras om primär replik behöver skapa en annan replik genom att skicka alla dess data.

När du återställer från en säkerhetskopieringskedja, liknande Reliable Services, bör BackupFolderPath innehålla underkataloger med en underkatalog som innehåller fullständig säkerhetskopiering och andra underkataloger som innehåller inkrementella säkerhetskopieringar. Återställnings-API:et kommer att kasta FabricException med lämpligt felmeddelande om valideringen av säkerhetskopieringskedjan misslyckas. 

> [!NOTE]
> `KvsActorStateProvider`ignorerar för närvarande alternativet RestorePolicy.Safe. Stöd för den här funktionen planeras i en kommande version.
> 

## <a name="testing-back-up-and-restore"></a>Testa säkerhetskopiering och återställning
Det är viktigt att se till att kritiska data säkerhetskopieras och kan återställas från. Detta kan göras genom `Start-ServiceFabricPartitionDataLoss` att anropa cmdlet i PowerShell som kan inducera dataförlust i en viss partition för att testa om data säkerhetskopiering och återställning funktionalitet för din tjänst fungerar som förväntat.  Det är också möjligt att programmässigt åberopa dataförlust och återställa från den händelsen också.

> [!NOTE]
> Du hittar ett exempel på en implementering av säkerhetskopierings- och återställningsfunktioner i webreferensappen på GitHub. Titta på `Inventory.Service` tjänsten för mer information.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Under huven: mer information om säkerhetskopiering och återställning
Här är lite mer information om säkerhetskopiering och återställning.

### <a name="backup"></a>Säkerhetskopiering
Reliable State Manager ger möjlighet att skapa konsekventa säkerhetskopior utan att blockera några läs- eller skrivåtgärder. För att göra det, använder den en kontrollpunkt och logg persistens mekanism.  Reliable State Manager tar suddiga (lätta) kontrollpunkter vid vissa punkter för att minska trycket från transaktionsloggen och förbättra återställningstiderna.  När `BackupAsync` anropas instruerar Reliable State Manager alla tillförlitliga objekt att kopiera sina senaste kontrollpunktsfiler till en lokal säkerhetskopieringsmapp.  Sedan kopierar Reliable State Manager alla loggposter, från "startpekaren" till den senaste loggposten i säkerhetskopieringsmappen.  Eftersom alla loggposter upp till den senaste loggposten ingår i säkerhetskopian och Reliable State Manager bevarar write-ahead-loggning, garanterar Reliable State Manager att alla transaktioner som har genomförts (har`CommitAsync` returnerats) inkluderas i säkerhetskopian.

Alla transaktioner som `BackupAsync` genomförs efter har anropats kan eller inte kan vara i säkerhetskopian.  När den lokala säkerhetskopieringsmappen har fyllts i av plattformen (det vill än lokal säkerhetskopiering slutförs av körningen) anropas tjänstens motringning för säkerhetskopiering.  Den här motringningen ansvarar för att flytta säkerhetskopieringsmappen till en extern plats, till exempel Azure Storage.

### <a name="restore"></a>Återställ
Reliable State Manager ger möjlighet att återställa från `RestoreAsync` en säkerhetskopia med hjälp av API.  
Metoden `RestoreAsync` på `RestoreContext` kan endast anropas inuti `OnDataLossAsync` metoden.
Den bool som `OnDataLossAsync` returneras av anger om tjänsten återställde sitt tillstånd från en extern källa.
Om `OnDataLossAsync` returnerandet är true återskapar Service Fabric alla andra repliker från den här primärt. Service Fabric säkerställer att repliker som tar emot `OnDataLossAsync` anrop först övergången till den primära rollen men inte beviljas lässtatus eller skrivstatus.
Detta innebär att för StatefulService-implementerare `RunAsync` `OnDataLossAsync` kommer inte att anropas förrän den har slutförts.
Sedan `OnDataLossAsync` kommer att åberopas på den nya primära.
Tills en tjänst slutför detta API framgångsrikt (genom att returnera sant eller falskt) och avslutar relevant omkonfiguration, kommer API:et att fortsätta anropas en i taget.

`RestoreAsync`först släpper alla befintliga tillstånd i den primära repliken som det anropades. Sedan skapar Reliable State Manager alla tillförlitliga objekt som finns i säkerhetskopieringsmappen. Därefter instrueras tillförlitliga objekt att återställa från sina kontrollpunkter i säkerhetskopieringsmappen. Slutligen återställer Reliable State Manager sitt eget tillstånd från loggposterna i säkerhetskopieringsmappen och utför återställning. Som en del av återställningsprocessen spelas åtgärder från "startpunkten" som har bekräftat loggposter i säkerhetskopieringsmappen upp till reliable-objekten. Det här steget säkerställer att det återställda tillståndet är konsekvent.

## <a name="next-steps"></a>Nästa steg
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Snabbstart för tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
  - [Meddelanden om tillförlitliga tjänster](service-fabric-reliable-services-notifications.md)
  - [Konfiguration av tillförlitliga tjänster](service-fabric-reliable-services-configuration.md)
  - [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Regelbunden säkerhetskopiering och återställning i Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

