---
title: Service Fabric-säkerhetskopiering och återställning | Microsoft Docs
description: Konceptuell dokumentation för Service Fabric-säkerhetskopiering och återställning
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: cd40f59cfa7846911c68206c3bc1e85a770b0fcc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670138"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Säkerhetskopiera och återställa Reliable Services och Reliable Actors
Azure Service Fabric är en plattform för hög tillgänglighet som replikerar tillståndet över flera noder att upprätthålla hög tillgänglighet.  Även om en nod i klustret misslyckas, fortsätter därför tjänsterna ska vara tillgängliga. Även om den här inbyggd redundans som tillhandahålls av plattformen är tillräcklig för vissa, i vissa fall är det lämpligt att tjänsten för att säkerhetskopiera data (till en extern lagring).

> [!NOTE]
> Det är viktigt att säkerhetskopiera och återställa data (och testa att den fungerar som förväntat) så att du kan återställa från data går förlorade.
> 

> [!NOTE]
> Microsoft rekommenderar att du använder [regelbunden säkerhetskopiering och återställning](service-fabric-backuprestoreservice-quickstart-azurecluster.md) för att konfigurera säkerhetskopiering av Reliable Stateful services och Reliable Actors. 
> 


En tjänst kan till exempel vill säkerhetskopiera data för att skydda mot följande scenarier:

- I händelse av permanent förlusten av en hela Service Fabric-kluster.
- Permanent förlust av en majoritet av replikeringar av en tjänstpartition
- Administrativa fel där tillståndet hämtar eller förstörs. Exempel: Detta kan inträffa om en administratör med tillräcklig behörighet för felaktigt tar bort tjänsten.
- Buggar i tjänsten som orsaka skadade data. Det kan exempelvis ske när en kod tjänsteuppgraderingen börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kanske både koden och data återställas till ett tidigare tillstånd.
- Offline databearbetning. Det kan vara praktiskt att ha offline bearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Funktionen för säkerhetskopiering/återställning tillåter tjänster som bygger på Reliable Services API för att skapa och återställa säkerhetskopior. De säkerhetskopierade API tillhandahålls av plattformen gör att säkerhetskopiorna av en tjänstpartition tillstånd utan blockerar Läs- eller skrivåtgärder. Återställningen API gör att en tjänstpartition tillstånd och kan inte återställas från en vald säkerhetskopia.

## <a name="types-of-backup"></a>Typer av säkerhetskopiering
Det finns två alternativ för säkerhetskopiering: Fullständiga och inkrementella.
En fullständig säkerhetskopia är en säkerhetskopia som innehåller alla data som krävs för att återskapa tillståndet för replikeringen: kontrollpunkter och alla loggposter.
En fullständig säkerhetskopia kan återställas ensamt eftersom den har kontrollpunkter och loggen.

Problem med fullständiga säkerhetskopieringar uppstår när kontrollpunkter är stora.
Till exempel en replik som har 16 GB tillstånd har kontrollpunkter som lägger upp cirka till 16 GB.
Om vi har ett Återställningspunktmål på fem minuter måste repliken säkerhetskopieras var femte minut.
Varje gång det säkerhetskopierar som behövs för att kopiera 16 GB kontrollpunkter utöver 50 MB (kan konfigureras med hjälp av `CheckpointThresholdInMB`) för loggar.

![Fullständig säkerhetskopiering exempel.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Lösning på problemet är inkrementella säkerhetskopior, där säkerhetskopiera bara innehåller de ändrade posterna sedan den senaste säkerhetskopieringen.

![Inkrementell säkerhetskopiering exempel.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Eftersom inkrementella säkerhetskopieringar är endast ändringar sedan den senaste säkerhetskopieringen (inte inkluderar kontrollpunkter) är de brukar vara snabbare, men de kan inte återställas på egen hand.
Om du vill återställa en inkrementell säkerhetskopia krävs hela kedjan för säkerhetskopiering.
En säkerhetskopiering kedja är en kedja av säkerhetskopior som börjar med en fullständig säkerhetskopiering och följt av ett antal sammanhängande inkrementella säkerhetskopieringar.

## <a name="backup-reliable-services"></a>Säkerhetskopiering Reliable Services
Tjänsten författaren har fullständig kontroll över när du ska göra säkerhetskopior och där säkerhetskopiorna lagras.

Om du vill starta en säkerhetskopiering som tjänsten behöver anropa funktionen ärvda medlemmar `BackupAsync`.  
Säkerhetskopieringar kan göras från primära repliker och de kräver skrivning status som ska beviljas.

Som visas nedan, `BackupAsync` tar in en `BackupDescription` objekt, där en kan ange en fullständig eller inkrementell säkerhetskopiering, samt en återanropsfunktion, `Func<< BackupInfo, CancellationToken, Task<bool>>>` som anropas när mappen har skapats lokalt och är redo att flyttas till vissa extern lagring.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Begäran om att få en inkrementell säkerhetskopia kan misslyckas med `FabricMissingFullBackupException`. Det här undantaget anger att en av följande saker händer:

- repliken har aldrig tagit en fullständig säkerhetskopiering eftersom det har blivit primära
- några av loggposter eftersom den senaste säkerhetskopieringen har trunkerats eller
- repliken skickas den `MaxAccumulatedBackupLogSizeInMB` gränsen.

Användare kan öka sannolikheten för att kunna göra inkrementella säkerhetskopieringar genom att konfigurera `MinLogSizeInMB` eller `TruncationThresholdFactor`.
Öka dessa värden ökar den per replik diskanvändning.
Mer information finns i [Reliable Services-konfiguration](service-fabric-reliable-services-configuration.md)

`BackupInfo` innehåller information om säkerhetskopiering, inklusive platsen för den mapp där körningen sparade säkerhetskopieringen (`BackupInfo.Directory`). Återanropsfunktionen kan flytta den `BackupInfo.Directory` till en extern lagring eller en annan plats.  Den här funktionen returnerar också en bool som anger om det var kan flytta säkerhetskopieringsmappen till dess målplats.

Följande kod visar hur `BackupCallbackAsync` metoden kan användas för att ladda upp säkerhetskopian till Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

I föregående exempel `ExternalBackupStore` är klassen exemplet som används för gränssnittet med Azure Blob storage och `UploadBackupFolderAsync` är den metod som komprimerar mappen och placerar den i Azure Blob store.

Tänk på följande:

  - Det kan finnas endast en säkerhetskopieringsåtgärd pågående per replik vid en given tidpunkt. Mer än en `BackupAsync` samtal i taget genereras `FabricBackupInProgressException` att begränsa tillhörande säkerhetskopior till en.
  - Om en replik misslyckas över när en säkerhetskopiering pågår kanske säkerhetskopieringen inte har slutförts. Därför när redundansen är klar är det tjänstens ansvar att starta om säkerhetskopieringen genom att aktivera `BackupAsync` efter behov.

## <a name="restore-reliable-services"></a>Återställa Reliable Services
I allmänhet indelas fall när du kan behöva utföra en återställningsåtgärd i följande kategorier:

  - Tjänsten partitionera förlorade data. Disk för två av tre repliker för en partition (inklusive den primära repliken) hämtar skadad eller rensas. Den nya primärt kan behöva återställa data från en säkerhetskopia.
  - Hela tjänsten går förlorad. Exempelvis kan en administratör tar bort hela tjänsten och därmed tjänsten och data måste återställas.
  - Tjänsten replikerade skadad programdata (till exempel på grund av ett programfel). I det här fallet tjänsten måste uppgraderas eller återställas för att ta bort orsaken till felet och icke-skada data måste återställas.

På många sätt är möjligt, erbjuder vi några exempel på med `RestoreAsync` att komma tillrätta med scenarier ovan.

## <a name="partition-data-loss-in-reliable-services"></a>Partition dataförlust i Reliable Services
I det här fallet körningen skulle identifiera data går förlorade och automatiskt anropa den `OnDataLossAsync` API.

Tjänsten författare behöver för att utföra följande för att återställa:

  - Åsidosätt virtuella basklassmetoden `OnDataLossAsync`.
  - Hitta den senaste säkerhetskopian i den externa platsen som innehåller tjänstens säkerhetskopieringar.
  - Ladda ned den senaste säkerhetskopian (och packa säkerhetskopian i mappen om den har komprimerats).
  - Den `OnDataLossAsync` metoden ger ett `RestoreContext`. Anropa den `RestoreAsync` API på den angivna `RestoreContext`.
  - Returnera SANT om återställningen lyckades.

Följande är ett exempel på en implementering av den `OnDataLossAsync` metoden:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` överförda till den `RestoreContext.RestoreAsync` anropet innehåller en medlem som kallas `BackupFolderPath`.
När du återställer en enda fullständig säkerhetskopiering detta `BackupFolderPath` ska vara inställd på den lokala sökvägen till den mapp som innehåller din fullständig säkerhetskopiering.
När du återställer en fullständig säkerhetskopia och ett antal inkrementella säkerhetskopieringar `BackupFolderPath` ska vara inställd på den lokala sökvägen till den mapp som inte bara innehåller en fullständig säkerhetskopiering, men även alla de inkrementella säkerhetskopiorna.
`RestoreAsync` Anropet orsaka `FabricMissingFullBackupException` om den `BackupFolderPath` tillhandahålls inte innehåller en fullständig säkerhetskopia.
Den kan också ge `ArgumentException` om `BackupFolderPath` har en bruten kedja av inkrementella säkerhetskopieringar.
Om den innehåller en fullständig säkerhetskopiering, till exempel först inkrementell och den tredje inkrementell säkerhetskopieringen men inga andra inkrementell säkerhetskopiering.

> [!NOTE]
> RestorePolicy anges till säkra som standard.  Detta innebär att den `RestoreAsync` API misslyckas med ArgumentException om den upptäcker att mappen innehåller ett tillstånd som är äldre än eller lika med tillståndet i den här repliken.  `RestorePolicy.Force` kan användas för att hoppa över kontrollen säkerhet. Detta anges som en del av `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Tjänsten har tagits bort eller förlorade
Om en tjänst tas bort, måste du först återskapa tjänsten innan data kan återställas.  Det är viktigt att skapa en tjänst med samma konfiguration, till exempel partitioneringsschema så att data kan återställas sömlöst.  När tjänsten är igång, API för att återställa data (`OnDataLossAsync` ovan) måste anropas för varje partition för den här tjänsten. Ett sätt att uppnå detta är med hjälp av [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) på varje partition.  

Från och med nu är implementering samma som för scenariot ovan. Varje partition måste återställa den senaste relevanta säkerhetskopieringen från extern lagring. Ett villkor är att partitions-ID kan ha nu ändrats, eftersom körningen skapar partition ID: N dynamiskt. Tjänsten behöver därför att lagra lämplig partition information och tjänsten namn som identifierar den senaste korrekta säkerhetskopian att återställa från för varje partition.

> [!NOTE]
> Det rekommenderas inte att använda `FabricClient.ServiceManager.InvokeDataLossAsync` på varje partition att återställa hela tjänsten eftersom som kan skada din klustertillstånd.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikering av skadade programdata
Om den nyligen distribuerade programuppgraderingen har en bugg som kan orsaka att data skadas. En uppgradering av programmet kan till exempel börja uppdatera alla phone antalet poster i en tillförlitlig ordlista med ett ogiltigt riktnummer.  I det här fallet replikeras den ogiltiga telefonnummer eftersom Service Fabric inte är medveten om vilka slags data som lagras.

Det första du ska göra när du identifiera sådana en flagranta bugg som orsakar skadade data är att låsa tjänsten på programnivå och, om det är möjligt, uppgradera till version av den programkod som inte har programfelet.  Även efter kod som är fast, data kan fortfarande vara skadade och därför data kan behöva återställas.  I sådana fall kan kanske det inte räcker för att återställa den senaste säkerhetskopian eftersom de senaste säkerhetskopiorna kan också vara skadad.  Du har alltså att hitta den senaste säkerhetskopieringen gjordes innan data har skadats.

Om du inte är säker på vilken säkerhetskopior är skadade kan du distribuera ett nytt Service Fabric-kluster och återställer säkerhetskopior av berörda partitionerna precis som ovan ”har tagits bort eller förlorade service” scenario.  För varje partition börjar återställa säkerhetskopiorna från den senaste till minst. När du har hittat en säkerhetskopia som inte har skadat flytta/ta bort alla säkerhetskopior för den här partitionen som var (efter att säkerhetskopiering). Upprepa proceduren för varje partition. Nu när `OnDataLossAsync` anropas på partitionen i produktion-klustret, den senaste säkerhetskopieringen som finns i arkivet för externa blir det slutpunktsstatus av ovanstående procedur.

Nu kan stegen i ”har tagits bort eller förlorade tjänsten” avsnittet kan användas för att återställa tillståndet för tjänsten till tillståndet innan buggy koden skadat tillstånd.

Tänk på följande:

  - När du återställer finns det en risk att säkerhetskopian som återställs är äldre än tillståndet för partitionen innan data gick förlorade. Därför bör du återställa endast som en sista utväg återställa så mycket data som möjligt.
  - Den sträng som representerar sökvägen till mappen för säkerhetskopiering och sökvägar för filer på säkerhetskopieringsmappen kan vara större än 255 tecken, beroende på FabricDataRoot sökvägen och programtypen namnlängd. Detta kan orsaka vissa .NET-metoder som `Directory.Move`, utlöser den `PathTooLongException` undantag. En lösning är att anropa direkt kernel32-API: er, som `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Säkerhetskopiera och återställa Reliable Actors


Reliable Actors-ramverket är byggt på Reliable Services. ActorService som är värd för actor(s) är en tillståndskänslig tillförlitlig tjänst. Därför är alla säkerhetskopiering och återställning av tillgängliga funktioner i Reliable Services också tillgängligt för Reliable Actors (utom beteenden som är tillståndsprovider-specifik). Eftersom säkerhetskopieringar kommer att göras på basis av per partition, tillstånd för alla aktörer i att partition ska säkerhetskopieras (och återställning är liknande och ska ske på basis av per partition). Om du vill utföra säkerhetskopiering/återställning med tjänstens ägare ska skapa en anpassad aktören service klass som härleds från ActorService klass och gör säkerhetskopiering/återställning liknar Reliable Services enligt beskrivningen i föregående avsnitt.

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

När du skapar en anpassad aktörsklassen för tjänsten som du behöver registrera som även när du registrerar aktören.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Standardprovidern för tillstånd för Reliable Actors är `KvsActorStateProvider`. Inkrementell säkerhetskopiering är inte aktiverad som standard för `KvsActorStateProvider`. Du kan aktivera inkrementell säkerhetskopiering genom att skapa `KvsActorStateProvider` med lämplig inställning i dess konstruktorn och skicka den till ActorService konstruktorn som visas i följande kodavsnitt:

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

När inkrementell säkerhetskopiering har aktiverats, tar en inkrementell säkerhetskopia kan misslyckas med FabricMissingFullBackupException av någon av följande orsaker och du behöver ta en fullständig säkerhetskopia innan du tar inkrementella säkerhetskopiorna:

  - Repliken har aldrig tagit en fullständig säkerhetskopiering eftersom det blivit primära.
  - Vissa av posterna loggen har trunkerats eftersom senaste säkerhetskopian skapades.

När inkrementell säkerhetskopiering aktiveras `KvsActorStateProvider` använder inte cirkulär buffert för att hantera dess loggposter och trunkerar den med jämna mellanrum. Om ingen säkerhetskopia görs av användaren under 45 minuter, trunkerar systemet automatiskt loggposter. Det här intervallet kan konfigureras genom att ange `logTruncationIntervalInMinutes` i `KvsActorStateProvider` konstruktorn (ungefär som när du aktiverar inkrementell säkerhetskopiering). Loggposter kan också hämta trunkeras om primära repliken behöver skapa en annan replik genom att skicka alla data.

När du gör återställningen från en säkerhetskopiering kedja liknar Reliable Services i BackupFolderPath ska innehålla underkataloger med en underkatalog som innehåller fullständig säkerhetskopiering och andra underkataloger som innehåller inkrementella säkerhetskopiorna. Restore-API: et genereras FabricException med lämpligt felmeddelande om loggsäkerhetskopieringssekvensen verifieringen misslyckas. 

> [!NOTE]
> `KvsActorStateProvider` ignorerar för tillfället alternativet RestorePolicy.Safe. Stöd för den här funktionen är planerat i en kommande version.
> 

## <a name="testing-back-up-and-restore"></a>Testa säkerhetskopiera och Återställ
Det är viktigt att säkerställa att viktiga data säkerhetskopieras och återställas från. Detta kan göras genom att aktivera den `Start-ServiceFabricPartitionDataLoss` cmdlet i PowerShell som kan orsaka förlust av data i en viss partition att testa om data säkerhetskopiera och återställa funktioner för din tjänst fungerar som förväntat.  Det går också att anropa dataförlust och återställa från händelsen samt programmässigt.

> [!NOTE]
> Du kan hitta ett exempel på implementering av säkerhetskopiering och återställningsfunktionen i referens Webbappen på GitHub. Ta en titt på de `Inventory.Service` för mer information.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Under huven: Mer information om säkerhetskopiering och återställning
Här är några frågor om säkerhetskopiering och återställning.

### <a name="backup"></a>Backup
Reliable State Manager ger möjlighet att skapa programkonsekventa säkerhetskopior utan att blockera alla läs- eller skrivåtgärder. Om du vill göra det använder den en kontrollpunkts- och loggfiler persistence-mekanism.  Reliable State Manager tar fuzzy (enkel) kontrollpunkter vid vissa tidpunkter underlättar trycket från transaktionella loggen och förbättra återställningstiderna.  När `BackupAsync` anropas Reliable State Manager instruerar alla tillförlitliga objekt för att kopiera sina senaste kontrollpunktsfiler till en lokal mapp för säkerhetskopior.  Reliable State Manager kopierar sedan alla loggposter från ”start pointer” till den senaste loggposten på säkerhetskopieringsmappen.  Eftersom alla loggposter upp till den senaste loggposten ingår i säkerhetskopieringen och Reliable State Manager bevarar write-ahead loggning, Reliable State Manager garanterar att alla transaktioner som genomförs (`CommitAsync` har returnerat har ) som ingår i säkerhetskopian.

Alla transaktioner som sparar efter `BackupAsync` har anropats kan eller inte i säkerhetskopian.  När den lokala mappen för säkerhetskopiering har fyllts av plattformen (det vill säga lokal säkerhetskopia har avslutats av körningen), tjänstens säkerhetskopiering återanrop har anropats.  Den här återanrop ansvarar för att flytta säkerhetskopieringsmappen till en extern plats, till exempel Azure Storage.

### <a name="restore"></a>Återställ
Reliable State Manager ger möjlighet att återställa från en säkerhetskopia med hjälp av den `RestoreAsync` API.  
Den `RestoreAsync` metoden på `RestoreContext` kan endast anropas inuti den `OnDataLossAsync` metoden.
Bool som returneras av `OnDataLossAsync` anger om tjänsten återställts sitt tillstånd från en extern källa.
Om den `OnDataLossAsync` returnerar värdet är true, Service Fabric återskapar alla andra repliker från denna primära. Service Fabric säkerställer att repliker som ska ta emot `OnDataLossAsync` anropa första övergången till den primära rollen men inte beviljas läses status eller skriva status.
Detta innebär vid StatefulService implementerare `RunAsync` anropas inte förrän `OnDataLossAsync` har slutförts.
Sedan `OnDataLossAsync` kommer att anropas på den nya primärt.
Tills en tjänst har slutförts detta API har (genom att returnera true eller false) och är klar relevanta omkonfiguration, ska API: et hålla som anropas i taget.

`RestoreAsync` först utelämnar alla befintliga tillstånd i den primära repliken som den anropades på. Reliable State Manager skapar sedan alla tillförlitliga objekt som finns i mappen. Därefter instrueras tillförlitlig objekt att återställa från sina kontrollpunkter i mappen. Slutligen Reliable State Manager återställs den egna systemtillståndet från loggposter på säkerhetskopieringsmappen och utför återställningen. Som en del av återställningsprocessen, åtgärder som startar från ”Start” och som har allokerats loggposter på säkerhetskopieringsmappen spelas upp till tillförlitliga objekt. Det här steget säkerställer att den återställda är konsekvent.

## <a name="next-steps"></a>Nästa steg
  - [Tillförlitliga samlingar](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services-Snabbstart](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services-aviseringar](service-fabric-reliable-services-notifications.md)
  - [Reliable Services-konfiguration](service-fabric-reliable-services-configuration.md)
  - [Utvecklarreferens för tillförlitliga samlingar](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Regelbunden säkerhetskopiering och återställning i Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

