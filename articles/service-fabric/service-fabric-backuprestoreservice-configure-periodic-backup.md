---
title: Förstå konfigurationen av regelbunden säkerhetskopiering i Azure Service Fabric | Microsoft Docs
description: Använd Service Fabric regelbunden säkerhetskopiering och återställning av funktionen för att aktivera säkerhetskopiering av periodiska data för dina programdata.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: hrushib
ms.openlocfilehash: f2a1cd79a99e16460c96d28ebeb0a2bd68975361
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794251"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Förstå periodiska konfiguration av säkerhetskopiering i Azure Service Fabric

Konfigurera regelbunden säkerhetskopiering av din tillförlitliga tillståndskänsliga tjänster eller Reliable Actors består av följande steg:

1. **Skapa principer för säkerhetskopiering**: I det här steget skapas en eller flera principer för säkerhetskopiering beroende på kraven.

2. **Aktivera säkerhetskopiering**: I det här steget ska du associera principer för säkerhetskopiering som skapats i **steg 1** till nödvändiga entiteter _programmet_, _Service_, eller en _Partition_.

## <a name="create-backup-policy"></a>Skapa säkerhetskopieringsprincip

En princip för säkerhetskopiering består av följande konfigurationer:

* **Automatisk återställning på dataförlust**: Anger om du vill aktivera återställning som automatiskt med den senaste tillgängliga säkerhetskopian om partitionen påträffar en dataförlust inträffat.

* **Max inkrementella säkerhetskopieringar**: Definierar det maximala antalet säkerhetskopior som ska utföras mellan två fullständiga säkerhetskopieringar. Max inkrementella säkerhetskopior anger den övre gränsen. En fullständig säkerhetskopiering kan vidtas innan angivet antal inkrementella säkerhetskopieringar har utförts på något av följande villkor

    1. Repliken har aldrig tagit en fullständig säkerhetskopiering eftersom det har blivit primära.

    2. Några av loggposter eftersom den senaste säkerhetskopieringen har trunkerats.

    3. Repliken har godkänts MaxAccumulatedBackupLogSizeInMB gränsen.

* **Schema för säkerhetskopiering**: Den tidpunkt eller frekvens som du vill göra regelbundna säkerhetskopior. En kan schemalägga säkerhetskopieringar så att återkomma med angivna intervall eller på en fast tidpunkt varje dag / vecka.

    1. **Frekvensbaserad Säkerhetskopieringsschemat**: Den här schematypen bör användas om behovet av att säkerhetskopiera data med jämna tidsintervall. Önskat tidsintervall mellan två på varandra följande säkerhetskopior har definierats använder ISO8601-format. Frekvensbaserad schema för säkerhetskopiering har stöd för intervall lösning till och minut.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Tidsbaserade Säkerhetskopieringsschemat**: Den här schematypen bör användas om behovet av att säkerhetskopiera data vid specifika tidpunkter på dagen eller veckan. Frekvens schematyp kan antingen vara dagliga och veckovisa.
        1. **_Dagliga_ tidsbaserade Säkerhetskopieringsschemat**: Den här schematypen ska användas om behovet av id som ska ta säkerhetskopiering av data vid specifika tidpunkter på dagen. För att ange detta `ScheduleFrequencyType` till _dagliga_; och ange `RunTimes` till listan över önskade helst under dagen i ISO8601-format, datum som har angetts tillsammans med tiden kommer att ignoreras. Till exempel `0001-01-01T18:00:00` representerar _18:00:00_ varje dag, ignorerar DatumDel _0001-01-01_. Exemplet nedan visar att konfigurationen ska utlösaren daglig säkerhetskopiering på _9.00_ och _18:00:00_ varje dag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Varje vecka_ tidsbaserade Säkerhetskopieringsschemat**: Den här schematypen ska användas om behovet av id som ska ta säkerhetskopiering av data vid specifika tidpunkter på dagen. För att ange detta `ScheduleFrequencyType` till _veckovisa_; set `RunDays` till listan över dagar i veckan när säkerhetskopiering behöver utlöses och ange `RunTimes` till listan över önskade helst under dagen i ISO8601-format, datum anges tillsammans med tid kommer att ignoreras. Lista över de dagar i veckan när du ska aktivera regelbunden säkerhetskopiering. Exemplet nedan visar att konfigurationen ska utlösaren daglig säkerhetskopiering på _9.00_ och _18:00:00_ under måndag-fredag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Lagring för säkerhetskopior**: Anger platsen för att ladda upp säkerhetskopior. Storage kan antingen Azure-blobblagring eller filresurs.
    1. **Azure-blobblagring**: Den här typen av lagring måste väljas när behovet av att lagra genereras säkerhetskopior i Azure. Båda _fristående_ och _Azure-baserade_ kluster kan använda den här typen av lagring. Beskrivning för den här lagringstyp kräver anslutningssträng och namnet på behållaren där säkerhetskopieringar ska överföras. Om behållaren med det angivna namnet inte är tillgängligt, skapas det vid uppladdning av en säkerhetskopia.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Filresurs**: Den här typen av lagring måste väljas för _fristående_ kluster när behovet av att lagra data för säkerhetskopiering på plats. Beskrivning för den här lagringstyp kräver sökvägen till filresursen där säkerhetskopieringar ska överföras. Åtkomst till filresursen kan konfigureras med någon av följande alternativ
        1. _Integrerad Windows-autentisering_, där åtkomst till filresursen har angetts för alla datorer som hör till Service Fabric-klustret. I så fall, ange följande fält att konfigurera _filresursen_ baserat lagring av säkerhetskopior.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Skydda filresursen med användarnamn och lösenord_, där åtkomst till filresursen har angetts för specifika användare. Filspecifikationen dela lagring ger också möjlighet att ange sekundära användarnamn och sekundära lösenord för att ange autentiseringsuppgifter för hösten bak om autentiseringen misslyckas med primära användarnamn och lösenord som primär. I så fall, ange följande fält att konfigurera _filresursen_ baserat lagring av säkerhetskopior.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Se till att lagringstillförlitlighet uppfyller eller överskrider tillförlitlighet kraven för säkerhetskopierade data.
>

* **Bevarandeprincip**: Anger principen om du vill behålla säkerhetskopior i det konfigurerade lagringsutrymmet. Endast grundläggande bevarandeprincip stöds.
    1. **Grundläggande bevarandeprincip**: Den här bevarandeprincip tillåter för att säkerställa optimal lagringsanvändning genom att ta bort säkerhetskopior som krävs inga fler. `RetentionDuration` Du kan ange att ange det tidsintervall som krävs säkerhetskopior ska behållas i lagringen. `MinimumNumberOfBackups` är en valfri parameter som kan anges för att se till att det angivna antalet säkerhetskopior bevaras alltid oavsett den `RetentionDuration`. Exemplet nedan visar konfigurationen om du vill behålla säkerhetskopior under _10_ dagar och tillåter inte antalet säkerhetskopieringar under _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

> [!IMPORTANT]
> Se till att kvarhållningsvaraktighetens i bevarandeprincipen är konfigurerad för att vara mindre än 24 dagar eller be om det skulle resultera i Backup Restore-tjänsten för att gå till kvorum förlust efter replikeringsredundans på grund av ett problem i körningen.

## <a name="enable-periodic-backup"></a>Aktivera regelbunden säkerhetskopiering
När du har definierat principen för säkerhetskopiering för att uppfylla krav för säkerhetskopiering av data, principen för säkerhetskopiering ska kopplas korrekt antingen en _programmet_, eller _service_, eller en _partition_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarkisk spridningen av princip för säkerhetskopiering
I Service Fabric relationen mellan program, tjänst och partitioner är hierarkiska enligt beskrivningen i [programmodell](./service-fabric-application-model.md). Princip för säkerhetskopiering kan vara antingen associerat med en _programmet_, _service_, eller en _partition_ i hierarkin. Princip för säkerhetskopiering sprider hierarkiskt till nästa nivå. Om vi antar att det finns endast en princip för säkerhetskopiering som associeras med en _programmet_, alla tillståndskänsliga partitioner som hör till alla _tillförlitliga tillståndskänsliga tjänster_ och _Reliable Actors_ av den _program_ kommer att säkerhetskopieras med principen för säkerhetskopiering. Eller om principen för säkerhetskopiering är associerad med en _tillståndskänslig och tillförlitlig tjänst_, alla dess partitioner kommer att säkerhetskopieras med principen för säkerhetskopiering.

### <a name="overriding-backup-policy"></a>Åsidosätta princip för säkerhetskopiering
Det kan finnas ett scenario där säkerhetskopiering av data med samma schema för säkerhetskopiering krävs för alla program utom specifika tjänster-tjänster där du behöver är att ha säkerhetskopiering av data med hjälp av högre frekvens schemat eller tar säkerhetskopiering till ett annat lagringskonto eller filresursen. Om du vill hantera sådana scenarier, tillhandahåller återställningsverktyg service kontor och sprids principen i tjänsten och partition omfånget. När principen för säkerhetskopiering är associerade på _service_ eller _partition_, åsidosätter spridda säkerhetskopieringsprincip, om sådant finns.

### <a name="example"></a>Exempel

Det här exemplet använder installationen med två program, _MyApp_A_ och _MyApp_B_. Programmet _MyApp_A_ innehåller två tillförlitliga tillståndskänsliga tjänster _SvcA1_ & _SvcA3_, och en Reliable Actor-tjänst, _ActorA2_. _SvcA1_ innehåller tre partitioner vid _ActorA2_ och _SvcA3_ innehåller två partitioner.  Programmet _MyApp_B_ innehåller tre tillförlitliga tillståndskänsliga tjänster, _SvcB1_, _SvcB2_, och _SvcB3_. _SvcB1_ och _SvcB2_ innehåller två partitioner varje vid _SvcB3_ innehåller tre partitioner.

Anta att dessa program behov för säkerhetskopiering av data är följande

1. MyApp_A
    1. Skapa daglig säkerhetskopiering av data för alla partitioner i alla _Reliable Stateful services_ och _Reliable Actors_ som hör till programmet. Överföra säkerhetskopieringsdata till platsen _BackupStore1_.

    2. En av tjänsterna, _SvcA3_, kräver varje timme för säkerhetskopiering av data.

    3. Datastorlek i partition _SvcA1_P2_ är högre än förväntat och dess säkerhetskopierade data ska lagras till annan lagringsplats _BackupStore2_.

2. MyApp_B
    1. Skapar säkerhetskopior av data varje söndag kl 8:00 för alla partitioner i _SvcB1_ service. Överföra säkerhetskopieringsdata till platsen _BackupStore1_.

    2. Skapar säkerhetskopior av data varje dag kl 8:00 för partitionen _SvcB2_P1_. Överföra säkerhetskopieringsdata till platsen _BackupStore1_.

För att åtgärda dessa behov för säkerhetskopiering av data, säkerhetskopieringspolicyer BP_1 att BP_5 skapas och säkerhetskopiering är aktiverat på följande sätt.
1. MyApp_A
    1. Skapa säkerhetskopieringsprincip, _BP_1_, med frekvensbaserad Säkerhetskopieringsschemat där frekvensen är inställd på 24 timmar. och lagring som konfigurerats för att använda lagringsplats för säkerhetskopior _BackupStore1_. Aktivera den här principen för program, _MyApp_A_ med [aktivera programmet Säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API. Den här åtgärden aktiverar säkerhetskopiering av data med hjälp av säkerhetskopieringsprincipen _BP_1_ för alla partitioner i _Reliable Stateful services_ och _Reliable Actors_ som hör till programmet  _MyApp_A_.

    2. Skapa säkerhetskopieringsprincip, _BP_2_, med frekvensbaserad Säkerhetskopieringsschemat där frekvensen är inställd på 1 timme. och lagring som konfigurerats för att använda lagringsplats för säkerhetskopior _BackupStore1_. Aktivera den här principen för tjänsten _SvcA3_ med [aktivera tjänsten säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Den här åtgärden åsidosätts spridda principen _BP_1_ genom att uttryckligen har aktiverats säkerhetskopieringsprincip _BP_2_ för alla partitioner i tjänsten _SvcA3_ leder till säkerhetskopiering av data med hjälp av backup principen _BP_2_ för de här partitionerna.

    3. Skapa säkerhetskopieringsprincip, _BP_3_, med frekvensbaserad Säkerhetskopieringsschemat där frekvensen är inställd på 24 timmar. och lagring som konfigurerats för att använda lagringsplats för säkerhetskopior _BackupStore2_. Aktivera den här principen för partitionen _SvcA1_P2_ med [aktivera Partition säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API. Den här åtgärden åsidosätts spridda principen _BP_1_ genom att uttryckligen har aktiverats säkerhetskopieringsprincip _BP_3_ för partitionen _SvcA1_P2_.

2. MyApp_B
    1. Skapa säkerhetskopieringsprincip, _BP_4_med tidsbaserade Säkerhetskopieringsschemat där schematyp frekvensen är inställd på veckovis, kör dagar är inställd på söndag och körtider anges till 8:00:00. Lagring som konfigurerats för att använda lagringsplats för säkerhetskopior _BackupStore1_. Aktivera den här principen för tjänsten _SvcB1_ med [aktivera tjänsten säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Den här åtgärden aktiverar säkerhetskopiering av data med hjälp av säkerhetskopieringsprincipen _BP_4_ för alla partitioner i tjänsten _SvcB1_.

    2. Skapa säkerhetskopieringsprincip, _BP_5_, med tidsbaserade Säkerhetskopieringsschemat där schematyp frekvensen är inställd på varje dag och körtider anges till 8:00:00. Lagring som konfigurerats för att använda lagringsplats för säkerhetskopior _BackupStore1_. Aktivera den här principen för partitionen _SvcB2_P1_ med [aktivera Partition säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API. Den här åtgärden aktiverar säkerhetskopiering av data med hjälp av säkerhetskopieringsprincipen _BP_5_ för partitionen _SvcB2_P1_.

Följande diagram visar uttryckligen aktiverade principer för säkerhetskopiering och sprids principer för säkerhetskopiering.

![Service Fabric-Programhierarki][0]

## <a name="disable-backup"></a>Inaktivera säkerhetskopiering
Principer för säkerhetskopiering kan inaktiveras när det finns inget behov av att säkerhetskopierade data. Säkerhetskopiera principen är aktiverad på en _programmet_ kan endast inaktiveras på samma _programmet_ med [inaktivera säkerhetskopia på programnivå](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API, säkerhetskopiering principen är aktiverad på en _service_ kan inaktiveras på samma _service_ med [inaktivera tjänsten Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) API, och säkerhetskopiera principen är aktiverad på en _partition_ kan inaktiveras på samma _partition_ med [inaktivera Backup för Partition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) API.

* Inaktiverar princip för säkerhetskopiering för en _program_ stoppar alla regelbundna säkerhetskopior som sker till följd av spridningen av principen för säkerhetskopiering till tillförlitliga tillståndskänslig tjänstpartitioner eller Reliable Actor-partitioner.

* Inaktiverar princip för säkerhetskopiering för en _service_ stoppar alla regelbundna säkerhetskopior som sker till följd av spridningen av den här säkerhetskopieringsprincipen till partitioner av den _service_.

* Inaktiverar princip för säkerhetskopiering för en _partition_ stoppar alla regelbunden säkerhetskopiering sker på grund av principen för säkerhetskopiering på partitionen.

* När du inaktiverar säkerhetskopiering för en entity(application/service/partition) `CleanBackup` kan anges till _SANT_ att ta bort alla säkerhetskopior i konfigurerade lagringsutrymmet.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Pausa och återuppta säkerhetskopiering
Vissa situationen begära tillfälligt uppehåll i regelbunden säkerhetskopiering av data. I sådana fall, beroende på krav, inaktivera backup API kan användas på en _programmet_, _Service_, eller _Partition_. Regelbunden säkerhetskopiering inaktiveringen är transitiva över underträd med programmets hierarkin från det datum då den används. 

* När inaktiveringen används på en _programmet_ med [pausa programmet Säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API och sedan alla tjänster och partitioner under det här programmet är pausat för regelbunden säkerhetskopiering av data.

* När inaktiveringen används på en _Service_ med [pausa tjänsten Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API och sedan alla partitioner under den här tjänsten pausas för regelbunden säkerhetskopiering av data.

* När inaktiveringen används på en _Partition_ med [pausa Partition säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API, kommer dess pausar partitioner under den här tjänsten pausas för regelbunden säkerhetskopiering av data.

När behovet av inaktiveringen är klar kan sedan säkerhetskopieringen periodiska data återställas med hjälp av respektive återuppta säkerhetskopiering API. Regelbunden säkerhetskopiering måste det köras på samma _programmet_, _service_, eller _partition_ där det pausades.

* Om inaktivering har tillämpats på en _programmet_, och sedan återupptas med [återuppta programmet Säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API. 

* Om inaktivering har tillämpats på en _Service_, och sedan återupptas med [återuppta tjänsten säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API.

* Om inaktivering har tillämpats på en _Partition_, och sedan återupptas med [återuppta Partition säkerhetskopiering](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API.

### <a name="difference-between-suspend-and-disable-backups"></a>Skillnaden mellan att pausa och inaktivera säkerhetskopiering
Inaktivera backup bör användas när säkerhetskopieringar inte längre behövs för ett visst program, tjänst eller partition. En kan infact anropa inaktivera backup begäran tillsammans med ren säkerhetskopior parameter ska vara sant vilket betyder att alla befintliga säkerhetskopior tas bort. Dock pausa ska användas i situationer där en vill inaktivera säkerhetskopior tillfälligt som när lokal disk blir full eller ladda upp säkerhetskopiering misslyckas på grund av kända nätverksproblem osv. 

Medan inaktivera kan anropas endast på en nivå som har tidigare aktiverats för säkerhetskopiering explicilty men inaktivering kan tillämpas på vilken nivå som har aktiverats för säkerhetskopiering antingen direkt eller via arv / hierarki. Om säkerhetskopiering aktiveras på programnivå, en till exempel anropa inaktivera endast på programnivå men pausa anropas på program, tjänster eller partition under programmet. 

## <a name="auto-restore-on-data-loss"></a>Automatisk återställning på dataförlust
Tjänsten partitionen kan förlora data på grund av oväntade fel. Disk för två av tre repliker för en partition (inklusive den primära repliken) hämtar skadad eller rensas.

När Service Fabric upptäcker att partitionen är i förlust av data, den anropar `OnDataLossAsync` gränssnitt metoden på partitionen och förväntar sig partition att vidta nödvändiga åtgärder för att starta från data går förlorade. I så fall, om den effektiva säkerhetskopieringsprincipen på partitionen har `AutoRestoreOnDataLoss` -flaggan inställd på `true` och sedan återställningen aktiveras automatiskt med senaste tillgängliga säkerhetskopian för den här partitionen.

## <a name="get-backup-configuration"></a>Hämta konfiguration av säkerhetskopiering
Separat API: er är tillgängliga för att hämta information om konfiguration av säkerhetskopiering på en _programmet_, _service_, och _partition_ omfång. [Hämta programinformation säkerhetskopiering Configuration](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [hämta konfigurationsinformation för tjänsten Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo), och [hämta konfigurationsinformation för Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) är dessa API: er respektive. Dessa API: er returnera huvudsakligen gäller principen för säkerhetskopiering, omfång som principen för säkerhetskopiering är tillämpad och säkerhetskopiering inaktivering information. Följande är en kort beskrivning av resultaten som returneras av dessa API: er.

- Konfiguration av säkerhetskopiering programinformation: innehåller information om säkerhetskopieringspolicyn som tillämpas på programmet och alla över ridden principer på tjänster och partitioner som hör till programmet. Den innehåller även inaktivering information för programmet och den services och partitioner.

- Konfigurationsinformation för backup-tjänsten: innehåller information om gällande princip för säkerhetskopiering på tjänsten och scope på som den här principen har tillämpats och alla över ridden principer på dess partitioner. Den innehåller också informationen om inaktivering för tjänsten och dess partitioner.

- Partitionera konfigurationsinformation för säkerhetskopiering: innehåller information om gällande princip för säkerhetskopiering på partitionen och omfånget som den här principen har tillämpats. Den innehåller också informationen om inaktivering för partitionerna.

## <a name="list-available-backups"></a>Lista tillgängliga säkerhetskopior

Tillgängliga säkerhetskopior kan anges med hjälp av hämta API för säkerhetskopiering. Resultatet av API-anrop omfattar information om säkerhetskopiering-objekt som är relaterade till alla säkerhetskopior som är tillgängliga på den lagring av säkerhetskopior, som konfigureras i gäller principen för säkerhetskopiering. Olika varianter av detta API tillhandahålls för att lista tillgängliga säkerhetskopior som hör till ett program, tjänst eller partition. Dessa API: er stöder komma den _senaste_ tillgängliga säkerhetskopiering av alla tillämpliga partitioner eller filtrering av säkerhetskopior baserat på _startdatum_ och _slutdatum_.

Dessa API: er har också stöd för sidbrytning av resultaten när _MaxResults_ parametern anges till noll positivt heltal API: et och returnerar sedan högsta _MaxResults_ säkerhetskopiera info-objekt. Om, det finns mer information om säkerhetskopiering objekt än den _MaxResults_ värde, returneras ett fortsättningstoken. Giltig fortsättning parameter-token som kan användas för att hämta nästa uppsättning resultat. När giltig fortsättning token värdet skickas till nästa anrop av API, returnerar API: et nästa uppsättning resultat. Inga fortsättningstoken inkluderas i svaret när alla tillgängliga resultat returneras.

Följande är kort information om stöds varianter.

- [Hämta säkerhetskopiering programlista](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Returnerar en lista över säkerhetskopior som är tillgängliga för varje partition som hör till angivna Service Fabric-program.

- [Hämta Service säkerhetskopiering lista](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): Returnerar en lista över säkerhetskopior som är tillgängliga för varje partition som hör till angivna Service Fabric-tjänst.
 
- [Hämta säkerhetskopiering Partitionslista](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Returnerar en lista över säkerhetskopior som är tillgängliga för den angivna partitionen.

## <a name="next-steps"></a>Nästa steg
- [REST API-referens för Backup restore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png
