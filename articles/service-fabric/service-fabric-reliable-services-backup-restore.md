---
title: Service Fabric-säkerhetskopiering och återställning | Microsoft Docs
description: Konceptuell dokumentationen för Service Fabric-säkerhetskopiering och återställning
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: dd8042620b6b9829e49f3124ecdee1c038f8c12f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="back-up-and-restore-reliable-services-and-reliable-actors"></a>Säkerhetskopiera och återställa Reliable Services och Reliable Actors
Azure Service Fabric är en plattform för hög tillgänglighet som replikerar tillståndet över flera noder för att upprätthålla hög tillgänglighet.  Även om en nod i klustret misslyckas därför fortsätter tjänster att vara tillgängliga. Den här inbyggda redundans som tillhandahålls av plattformen kan vara tillräcklig för vissa, i vissa fall är det lämpligt att tjänsten för att säkerhetskopiera data (till en extern butik).

> [!NOTE]
> Det är viktigt att säkerhetskopiera och återställa dina data (och testa att det fungerar som förväntat) så att du kan återställa från data går förlorade.
> 
> 

En tjänst kan till exempel vill säkerhetskopiera data för att skydda mot följande scenarier:

- Om en hel Service Fabric-klustret permanent förlorade.
- Permanent förlorade en majoritet av replikerna för en partition för tjänsten
- Administrativa fel där tillståndet hämtar eller förstörs. T.ex, kan detta inträffa om en administratör med tillräcklig behörighet för felaktigt tar bort tjänsten.
- Programfel i tjänsten som kan orsakar att data skadas. Det kan till exempel hända när en tjänst koduppgradering startas felaktiga data skrivs till en tillförlitlig samling. I sådana fall kanske både koden och data återställas till ett tidigare tillstånd.
- Offline databearbetning. Det kan vara praktiskt att ha offline bearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Funktionen säkerhetskopiering/återställning kan tjänster som bygger på tillförlitliga Services-API för att skapa och återställa säkerhetskopior. Säkerhetskopiering API: erna som tillhandahålls av plattformens Tillåt säkerhetskopiorna av en tjänst partition tillstånd, utan blockerar Läs- eller skrivåtgärder. Återställningen API: er kan en tjänst partition tillstånd och kan inte återställas från en säkerhetskopia av valda.

## <a name="types-of-backup"></a>Typer av säkerhetskopiering
Det finns två alternativ för säkerhetskopiering: fullständig och inkrementell.
En fullständig säkerhetskopia är en säkerhetskopia som innehåller de data som krävs för att återskapa tillståndet för repliken: kontrollpunkter och alla loggposter.
Eftersom den har kontrollpunkter och loggfilen, kan du återställa en fullständig säkerhetskopia ensamt.

Problem med fullständiga säkerhetskopieringar uppstår när kontrollpunkter är stora.
Till exempel en replik som har 16 GB tillstånd har kontrollpunkter som lägga upp cirka till 16 GB.
Om vi har ett mål för återställningspunkt fem minuter måste repliken säkerhetskopieras var femte minut.
Varje gång den säkerhetskopierar som behövs för att kopiera 16 GB kontrollpunkter förutom 50 MB (kan konfigureras med hjälp av `CheckpointThresholdInMB`) med loggar.

![Fullständig säkerhetskopiering exempel.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Lösning på problemet är inkrementella säkerhetskopieringar där säkerhetskopian innehåller endast de ändrade posterna sedan den senaste säkerhetskopieringen.

![Exempel för inkrementell säkerhetskopiering.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Eftersom säkerhetskopior är endast ändringar sedan den senaste säkerhetskopieringen (inte omfattar kontrollpunkter) är de brukar gå snabbare, men kan inte återställas på egen hand.
Om du vill återställa en inkrementell krävs säkerhetskopiering hela kedjan.
En säkerhetskopiering kedja följt av ett antal sammanhängande inkrementella säkerhetskopieringar är en kedja av säkerhetskopior som börjar med en fullständig säkerhetskopia.

## <a name="backup-reliable-services"></a>Säkerhetskopiering Reliable Services
Tjänsten författaren har fullständig kontroll över när säkerhetskopior och där säkerhetskopiorna lagras.

Om du vill starta en säkerhetskopiering tjänsten måste anropa funktionen ärvda medlemmen `BackupAsync`.  
Säkerhetskopieringar kan göras från primära repliker och de behöver skrivstatus för att beviljas åtkomst.

Enligt nedan, `BackupAsync` tar in en `BackupDescription` objekt, där en kan ange en fullständig eller inkrementell säkerhetskopiering, samt en Återanropsfunktionen, `Func<< BackupInfo, CancellationToken, Task<bool>>>` som anropas när mappen har skapats lokalt och är redo att flytta till vissa extern lagring.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Begäran om att få en inkrementell säkerhetskopia kan misslyckas med `FabricMissingFullBackupException`. Det här undantaget anger att en av följande händer:

- repliken har aldrig tagit en fullständig säkerhetskopiering eftersom det har blivit primära,
- Vissa av loggposter sedan den senaste säkerhetskopieringen har trunkerats eller
- repliken som skickades i `MaxAccumulatedBackupLogSizeInMB` gränsen.

Användare kan öka sannolikheten för att kunna göra inkrementella säkerhetskopieringar genom att konfigurera `MinLogSizeInMB` eller `TruncationThresholdFactor`.
Observera att öka dessa värden ökar den per replik diskanvändning.
Mer information finns i [Reliable Services-konfiguration](service-fabric-reliable-services-configuration.md)

`BackupInfo` innehåller information om säkerhetskopiering, inklusive sökvägen till mappen där körningsmiljön sparade säkerhetskopieringen (`BackupInfo.Directory`). Återanropsfunktionen kan flytta den `BackupInfo.Directory` till en annan plats eller en extern butik.  Den här funktionen returnerar också bool som anger om kunde för att flytta säkerhetskopieringsmappen till dess målplats.

Följande kod visar hur `BackupCallbackAsync` metoden kan användas för att ladda upp säkerhetskopian till Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

I föregående exempel `ExternalBackupStore` är exempel klass som används för gränssnittet med Azure Blob storage och `UploadBackupFolderAsync` är den metod som komprimerar mappen och placerar den i Azure Blob store.

Tänk på följande:

  - Det kan finnas endast en säkerhetskopiering relä per replikering vid en given tidpunkt. Mer än en `BackupAsync` samtal i taget genereras `FabricBackupInProgressException` att begränsa inflight säkerhetskopieringar till en.
  - Om en replik misslyckas över när en säkerhetskopiering pågår kan säkerhetskopieringen inte har slutförts. Därför när redundansväxlingen är klar, är det tjänstens ansvar för att starta om säkerhetskopieringen genom att anropa `BackupAsync` efter behov.

## <a name="restore-reliable-services"></a>Återställa Reliable Services
I allmänhet indelas fall när du kan behöva utföra återställningsåtgärder i dessa kategorier:

  - Tjänsten partitionera förlorade data. Disken för två av tre repliker för en partition (inklusive den primära repliken) hämtar skadad eller rensas. Den nya primärt kan behöva återställa data från en säkerhetskopia.
  - Hela tjänsten går förlorad. Till exempel en administratör tar bort hela tjänsten och därmed tjänsten och data ska återställas.
  - Tjänsten replikerade skadad programdata (t.ex, på grund av ett fel i programmet). I det här fallet har tjänsten ska uppgraderas eller för att ta bort orsaken till felet har återställts och icke skadade data måste återställas.

Medan flera metoder är möjligt, vi erbjuder några exempel på med `RestoreAsync` att återställa från ovannämnda scenarier.

## <a name="partition-data-loss-in-reliable-services"></a>Partitionen dataförlust i Reliable Services
I det här fallet körningsmiljön skulle automatiskt identifiera data går förlorade och anropa den `OnDataLossAsync` API.

Tjänsten författare behöver för att utföra följande för att återställa:

  - Åsidosätta metoden virtuella basklass `OnDataLossAsync`.
  - Hitta den senaste säkerhetskopian i den externa platsen som innehåller säkerhetskopior av tjänsten.
  - Hämta den senaste säkerhetskopian (och packa säkerhetskopieringen i mappen om den har komprimerats).
  - Den `OnDataLossAsync` metoden ger ett `RestoreContext`. Anropa den `RestoreAsync` API på angiven `RestoreContext`.
  - Returnera true om återställningen lyckades.

Följande är exempel på implementering av den `OnDataLossAsync` metoden:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` skickade till den `RestoreContext.RestoreAsync` anrop innehåller en medlem som kallas `BackupFolderPath`.
När du återställer en fullständig säkerhetskopia, detta `BackupFolderPath` ska anges till den lokala sökvägen till den mapp som innehåller en fullständig säkerhetskopiering.
När du återställer en fullständig säkerhetskopia och ett antal inkrementella säkerhetskopieringar `BackupFolderPath` ska anges till den lokala sökvägen till den mapp som inte bara innehåller en fullständig säkerhetskopiering, men även alla de inkrementella säkerhetskopiorna.
`RestoreAsync` Anropet kan utlösa `FabricMissingFullBackupException` om den `BackupFolderPath` som inte innehåller en fullständig säkerhetskopia.
Den kan också ge `ArgumentException` om `BackupFolderPath` har brutits kedja för säkerhetskopior.
Om den innehåller en fullständig säkerhetskopiering, till exempel först inkrementell och tredje inkrementell säkerhetskopiering men inga andra inkrementell säkerhetskopiering.

> [!NOTE]
> RestorePolicy anges till säkra som standard.  Detta innebär att den `RestoreAsync` API misslyckas med ArgumentException om den upptäcker att mappen innehåller ett tillstånd som är äldre än eller lika med det tillstånd som finns i den här repliken.  `RestorePolicy.Force` kan användas för att hoppa över kontrollen säkerhet. Det har angetts som en del av `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Borttagna eller förlorade service
Om en tjänst har tagits bort, måste du först återskapa tjänsten innan data kan återställas.  Det är viktigt att skapa tjänsten med samma konfiguration, till exempel partitioneringsschema så att data kan återställas utan problem.  När tjänsten är igång API för att återställa data (`OnDataLossAsync` ovan) måste anropas för varje partition för den här tjänsten. Ett sätt att uppnå detta är med hjälp av `[FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx)` för varje partition.  

Från och med nu är implementering samma som scenariot ovan. Varje partition måste återställa den senaste relevanta säkerhetskopieringen från extern butik. En begränsning är att partitions-ID kan ha nu ändrats, eftersom partition ID: N skapas dynamiskt i körningsmiljön. Tjänsten måste därför att lagra lämplig information och tjänsten partitionsnamnet att identifiera den senaste korrekta säkerhetskopian att återställa från en för varje partition.

> [!NOTE]
> Det rekommenderas inte att använda `FabricClient.ServiceManager.InvokeDataLossAsync` på varje partition för att återställa hela tjänsten eftersom som kan skada din klustertillstånd.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikering av skadade programdata
Om uppgraderingen nyligen distribuerade program har ett fel som kan medföra att data skadas. En uppgradering av programmet kan till exempel börja uppdatera alla phone antalet poster i en tillförlitlig ordlista med ett ogiltigt riktnummer.  I det här fallet replikeras ogiltiga telefonnummer sedan Service Fabric inte är medveten om vilka slags data som lagras.

Det första du ska göra när du identifiera sådana ett flagranta fel som orsakar skadade data är att låsa tjänsten på programnivå och, om möjligt, uppgradera till version av den programkod som inte har programfelet.  Även efter kod som har åtgärdats, data kan fortfarande vara skadad, och därmed data kan behöva återställas.  I sådana fall kan det inte räcker för att återställa den senaste säkerhetskopian eftersom de senaste säkerhetskopiorna kan också vara skadad.  Du har alltså att hitta den senaste säkerhetskopieringen gjordes innan data har skadats.

Om du inte är säker på vilken säkerhetskopior är skadade kan du distribuera ett nytt Service Fabric-kluster och återställa säkerhetskopior av berörda partitioner precis som anges ovan ”Deleted eller förlorade service” scenario.  För varje partition startar återställa säkerhetskopior från den senaste till minst. När du har hittat en säkerhetskopiering som inte har fel flyttning/ta bort alla säkerhetskopior för den här partitionen som var (än att säkerhetskopiering). Upprepa proceduren för varje partition. Nu när `OnDataLossAsync` anropas på partitionen i produktion klustret den senaste säkerhetskopian finns i arkivet för externa blir en utvald av ovanstående procedur.

Nu steg ”Deleted eller förlorade service” avsnitt kan användas för att återställa status för tjänsten till läget innan buggy kod skadad tillståndet.

Tänk på följande:

  - När du återställer finns det en risk att säkerhetskopian som återställs är äldre än tillståndet för partitionen innan data gick förlorade. Därför bör du återställa endast som en sista utväg återställa så mycket som möjligt av informationen.
  - Den sträng som representerar sökvägen till säkerhetskopian och sökvägar för filer i mappen kan vara större än 255 tecken, beroende på FabricDataRoot sökvägen och programtyp namnlängd. Detta kan orsaka vissa .NET-metoder som `Directory.Move`, för att utlösa den `PathTooLongException` undantag. En lösning som direkt anropar kernel32 API: er, som `CopyFile`.

## <a name="backup-and-restore-reliable-actors"></a>Säkerhetskopiering och återställning Reliable Actors


Tillförlitliga aktörer Framework bygger på Reliable Services. ActorService som är värd för actor(s) är en tillståndskänslig tillförlitlig tjänst. Därför måste är alla säkerhetskopiering och återställning av tillgängliga funktioner i Reliable Services också tillgänglig för Reliable Actors (utom beteenden som är specifika för tillståndsprovidern). Eftersom säkerhetskopior kommer att vidtas på grundval av per partition, tillstånd för alla aktörer i att partition ska säkerhetskopieras (och återställningen liknar och sker på grundval av per partition). Om du vill utföra säkerhetskopiering/återställning med tjänstens ägare ska skapa en anpassad aktören service-klass som härleds från klassen ActorService och gör sedan säkerhetskopiering/återställning liknar Reliable Services enligt beskrivningen ovan i föregående avsnitt.

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

När du skapar en anpassad aktören tjänstklass som du behöver registrera som samt när du registrerar aktören.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Standardprovidern för tillstånd för Reliable Actors är `KvsActorStateProvider`. Inkrementell säkerhetskopiering är inte aktiverad som standard för `KvsActorStateProvider`. Du kan aktivera inkrementell säkerhetskopiering genom att skapa `KvsActorStateProvider` med lämplig inställning i sin konstruktor och skicka den till ActorService konstruktorn enligt följande kodavsnitt:

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

När inkrementell säkerhetskopiering har aktiverats, tar en inkrementell säkerhetskopia kan misslyckas med FabricMissingFullBackupException av något av följande skäl och du behöver utföra en fullständig säkerhetskopiering innan du tar inkrementella säkerhetskopiorna:

  - Repliken har aldrig tagit en fullständig säkerhetskopiering eftersom den blev primära.
  - Vissa av posterna loggen har trunkerats eftersom senaste säkerhetskopian skapades.

När inkrementell säkerhetskopiering är aktiverad `KvsActorStateProvider` använder inte cirkulär buffert för att hantera dess loggposter och trunkerar den regelbundet. Om ingen säkerhetskopia är upptaget av användaren under 45 minuter, trunkerar loggposter automatiskt i systemet. Intervallet kan konfigureras genom att ange `logTrunctationIntervalInMinutes` i `KvsActorStateProvider` konstruktor (liknar när du aktiverar inkrementell säkerhetskopiering). Loggposter kan också hämta trunkeras om primär replik måste skapa en annan replik genom att skicka alla data.

När du gör återställningen från en säkerhetskopiering kedja liknar Reliable Services BackupFolderPath ska innehålla underkataloger med en underkatalog med fullständig säkerhetskopiering och andra underkataloger som innehåller inkrementella säkerhetskopiorna. Återställ API genereras FabricException med ett meddelande om loggsäkerhetskopieringssekvensen verifieringen misslyckas. 

> [!NOTE]
> `KvsActorStateProvider` ignorerar för tillfället alternativet RestorePolicy.Safe. Stöd för den här funktionen är planerad i en kommande version.
> 

## <a name="testing-backup-and-restore"></a>Testa säkerhetskopiering och återställning
Det är viktigt att säkerställa att viktiga data säkerhetskopieras och återställas från. Detta kan göras genom att anropa den `Start-ServiceFabricPartitionDataLoss` cmdlet i PowerShell som kan orsaka dataförlust i en viss partition för att kontrollera om data säkerhetskopiera och återställa funktioner för tjänsten fungerar som förväntat.  Det är också möjligt att anropa förlust av data och återställa från händelsen samt programmässigt.

> [!NOTE]
> Du kan hitta ett exempel på implementering av säkerhetskopiering och återställning av funktioner i referens webbprogrammet på GitHub. Tittar du på den `Inventory.Service` tjänsten för mer information.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Under huven: Mer information om säkerhetskopiering och återställning
Här är några mer information om säkerhetskopiering och återställning.

### <a name="backup"></a>Backup
Tillförlitliga Tillståndshanterarens ger möjlighet att skapa konsekvent säkerhetskopieringar utan att blockera alla läs- eller skrivåtgärder. Om du vill göra det, använder den en mekanism för datapersistence kontrollpunkts- och loggfiler.  Tillförlitliga Tillståndshanterarens tar fuzzy (lightweight) kontrollpunkter vid vissa tidpunkter för att avlasta trycket från transaktionella loggen och förbättra återställningstiden.  När `BackupAsync` anropas, tillförlitlig Tillståndshanterarens instruerar alla tillförlitliga objekt för att kopiera sina senaste kontrollpunktsfiler till en lokal mapp.  Tillförlitliga Tillståndshanterarens kopierar sedan alla loggposter från ”start pekaren” till den senaste loggposten i säkerhetskopieringsmappen.  Eftersom alla loggposter upp till den senaste loggposten ingår i säkerhetskopieringen och tillförlitlig Tillståndshanterarens bevarar write-ahead loggning, tillförlitlig Tillståndshanterarens garanterar att alla transaktioner som genomförs (`CommitAsync` har returnerat har) ingår i säkerhetskopian.

En transaktion som sparar efter `BackupAsync` har anropats kanske eller kanske inte i säkerhetskopian.  När den lokala mappen för säkerhetskopiering har fyllts av plattformen (det vill säga lokal säkerhetskopia har avslutats av körningen), tjänstens säkerhetskopiering återanropet anropas.  Den här återanrop ansvarar för att flytta säkerhetskopieringsmappen till en extern plats, till exempel Azure Storage.

### <a name="restore"></a>Återställ
Tillförlitliga Tillståndshanterarens ger dig möjlighet att återställa från en säkerhetskopia med hjälp av den `RestoreAsync` API.  
Den `RestoreAsync` metod på `RestoreContext` kan endast anropas inuti den `OnDataLossAsync` metoden.
Bool som returneras av `OnDataLossAsync` anger om tjänsten har återställts dess tillstånd från en extern källa.
Om den `OnDataLossAsync` returnerar true, Service Fabric återskapar alla andra repliker från denna primära Server. Service Fabric säkerställer att repliker som tar emot `OnDataLossAsync` anropa första övergången till den primära rollen men inte beviljas läses status eller skriva status.
Detta innebär att för StatefulService implementerare `RunAsync` inte anropas förrän `OnDataLossAsync` har slutförts.
Sedan `OnDataLossAsync` kommer att anropas på den nya primärt.
Tills en tjänst är slutförd detta API har (genom att returnera true eller false) och är klar relevanta omkonfiguration, kommer API: et hålla som anropas i taget.

`RestoreAsync` först utelämnar alla befintliga tillstånd i den primära repliken som den anropades på. Sedan skapar tillförlitliga Tillståndshanterarens alla tillförlitliga objekt som finns i mappen. Sedan instrueras tillförlitliga objekt att återställa från deras kontrollpunkter i mappen. Slutligen tillförlitliga Tillståndshanterarens återställer dess egna tillstånd från loggposter i mappen och utför återställningen. Som en del av återställningsprocessen spelas åtgärder som startar från ”Start” som har commit loggposter i mappen tillförlitliga objekt. Det här steget säkerställer att den återställda är konsekvent.

## <a name="next-steps"></a>Nästa steg
  - [Tillförlitliga samlingar](service-fabric-work-with-reliable-collections.md)
  - [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services-meddelanden](service-fabric-reliable-services-notifications.md)
  - [Tillförlitliga tjänstkonfiguration](service-fabric-reliable-services-configuration.md)
  - [För utvecklare för tillförlitlig samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Regelbunden säkerhetskopiering och återställning i Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

