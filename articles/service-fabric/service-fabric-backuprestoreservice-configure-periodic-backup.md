---
title: Förstå konfiguration av regelbunden säkerhetskopiering
description: Använd Service Fabric:s periodiska säkerhetskopierings- och återställningsfunktion för att aktivera periodisk säkerhetskopiering av dina programdata.
author: hrushib
ms.topic: article
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 34c6495e094a1160f6ac75b9f098934d5cbce967
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610156"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Förstå periodisk konfiguration för säkerhetskopiering i Azure Service Fabric

Konfigurera periodisk säkerhetskopiering av dina tillförlitliga tillståndskänsliga tjänster eller reliable actors består av följande steg:

1. **Skapa principer för säkerhetskopiering:** I det här steget skapas en eller flera principer för säkerhetskopiering beroende på kraven.

2. **Aktivera säkerhetskopiering**: I det här steget associerar du principer för säkerhetskopiering som skapats i **steg 1** till de nödvändiga entiteterna, _Programmet,_ _Tjänsten_eller en _partition_.

## <a name="create-backup-policy"></a>Skapa princip för säkerhetskopiering

En principer för säkerhetskopiering består av följande konfigurationer:

* **Automatisk återställning vid dataförlust**: Anger om återställningen ska utlösas automatiskt med den senaste tillgängliga säkerhetskopian om partitionen skulle få en dataförlusthändelse.

* **Max inkrementella säkerhetskopior**: Definierar det maximala antalet inkrementella säkerhetskopior som ska göras mellan två fullständiga säkerhetskopior. Max inkrementella säkerhetskopior anger den övre gränsen. En fullständig säkerhetskopia kan göras innan det angivna antalet inkrementella säkerhetskopior har slutförts under något av följande villkor

    1. Repliken har aldrig tagit en fullständig säkerhetskopia eftersom den har blivit primär.

    2. Några av loggposterna sedan den senaste säkerhetskopieringen har trunkerats.

    3. Repliken passerade maxakaruleradebackuplogSizeInMB-gränsen.

* **Säkerhetskopieringsschema**: Den tid eller frekvens som du kan ta regelbundna säkerhetskopior. Man kan schemalägga säkerhetskopior för att vara återkommande med angivet intervall eller vid en fast tid dagligen / veckovis.

    1. **Frekvensbaserad säkerhetskopieringsschema:** Den här schematypen bör användas om det behövs för att ta säkerhetskopiering av data med fasta intervall. Önskat tidsintervall mellan två på varandra följande säkerhetskopior definieras med ISO8601-format. Frekvensbaserat säkerhetskopieringsschema stöder intervallupplösning till minuten.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Tidsbaserat säkerhetskopieringsschema**: Den här schematypen ska användas om behovet är att ta data som säkerhetskopieras vid specifika tider på dagen eller veckan. Schemafrekvenstyp kan antingen vara dagligen eller veckovis.
        1. ** _Dagligt_ tidsbaserat säkerhetskopieringsschema:** Den här schematypen ska användas om id-programmet behöver vidtas för att ta säkerhetskopiering av data vid specifika tidpunkter på dagen. Om du vill `ScheduleFrequencyType` ange detta anger du _dagligen_; och `RunTimes` inställd på en lista över önskad tid under dagen i ISO8601-format, datum som anges tillsammans med tiden kommer att ignoreras. Representerar `0001-01-01T18:00:00` till exempel _18:00_ varje dag och ignorerar datumdelen _0001-01-01_. Nedanstående exempel illustrerar konfigurationen för att utlösa daglig säkerhetskopiering klockan _9:00_ och _18:00_ varje dag.

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

        2. ** _Veckotidsbaserat_ säkerhetskopieringsschema:** Den här schematypen ska användas om id-programmet behöver vidtas för att ta säkerhetskopiering av data vid specifika tider på dagen. Om du vill `ScheduleFrequencyType` ange detta anger du _veckoföreslag_. inställd `RunDays` på en lista över dagar i en vecka `RunTimes` när säkerhetskopieringen måste utlösas och ställas in på en lista över önskad tid under dagen i ISO8601-format, kommer det datum som anges tillsammans med tiden att ignoreras. Lista över dagar i veckan när den periodiska säkerhetskopieringen ska utlösas. Nedanstående exempel illustrerar konfigurationen för att utlösa daglig säkerhetskopiering klockan _9:00_ och _18:00_ under måndag till fredag.

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

* **Lagring av säkerhetskopiering**: Anger platsen för överföring av säkerhetskopior. Lagring kan vara antingen Azure blob store eller filresurs.
    1. **Azure blob store**: Den här lagringstypen bör väljas när behovet är att lagra genererade säkerhetskopior i Azure. Både _fristående_ och _Azure-baserade_ kluster kan använda den här lagringstypen. Beskrivning för den här lagringstypen kräver anslutningssträng och namn på behållaren där säkerhetskopior måste överföras. Om behållaren med det angivna namnet inte är tillgänglig skapas den under överföringen av en säkerhetskopia.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Filresurs**: Den här lagringstypen bör väljas för _fristående_ kluster när det behövs för att lagra säkerhetskopiering av data lokalt. Beskrivning för den här lagringstypen kräver sökväg för fildelning där säkerhetskopior måste överföras. Åtkomst till filresursen kan konfigureras med något av följande alternativ
        1. _Integrerad Windows-autentisering_, där åtkomsten till filresursen tillhandahålls till alla datorer som tillhör Service Fabric-klustret. I det här fallet anger du följande fält för att konfigurera _fildelningsbaserad_ lagring av säkerhetskopiering.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Skydda filresursen med användarnamn och lösenord_, där åtkomsten till filresursen ges till specifika användare. Filresurslagringsspecifikation ger också möjlighet att ange sekundärt användarnamn och sekundärt lösenord för att tillhandahålla återgångsuppgifter om autentisering misslyckas med primärt användarnamn och primärt lösenord. I det här fallet anger du följande fält för att konfigurera _fildelningsbaserad_ lagring av säkerhetskopiering.

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
> Se till att lagringstillförlitligheten uppfyller eller överskrider tillförlitlighetskraven för säkerhetskopierade data.
>

* **Bevarandeprincip**: Anger principen om att behålla säkerhetskopior i den konfigurerade lagringen. Endast grundläggande bevarandeprincip stöds.
    1. **Grundläggande bevarandeprincip:** Den här bevarandeprincipen gör det möjligt att säkerställa optimal lagringsanvändning genom att ta bort säkerhetskopieringsfiler som inte längre krävs. `RetentionDuration`kan anges för att ställa in det tidsintervall för vilket säkerhetskopior måste behållas i lagringen. `MinimumNumberOfBackups`är en valfri parameter som kan anges för att säkerställa att det angivna antalet `RetentionDuration`säkerhetskopior alltid behålls oavsett . Nedan visas konfigurationen för att behålla säkerhetskopior i _10_ dagar och tillåter inte att antalet säkerhetskopior går under _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Aktivera periodisk säkerhetskopiering
När du har definierat säkerhetskopieringsprincipen för att uppfylla kraven för säkerhetskopiering av data bör säkerhetskopieringsprincipen associeras på lämpligt sätt antingen med ett _program_eller _en tjänst_eller en _partition_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarkisk spridning av säkerhetskopieringsprincip
I Service Fabric är relationen mellan program, tjänst och partitioner hierarkisk enligt vad som förklaras i [programmodellen](./service-fabric-application-model.md). Säkerhetskopieringsprincipen kan associeras antingen med ett _program,_ _en tjänst_eller en _partition_ i hierarkin. Säkerhetskopieringsprincipen sprider sig hierarkiskt till nästa nivå. Förutsatt att det bara finns en säkerhetskopieringsprincip som skapats och associerats med ett _program,_ kommer alla tillståndskänsliga partitioner som tillhör alla _tillförlitliga tillståndskänsliga tjänster_ och _reliable actors_ av _programmet_ att säkerhetskopieras med hjälp av säkerhetskopieringsprincipen. Eller om säkerhetskopieringsprincipen är associerad med en _tillförlitlig tillståndskänslig tjänst,_ kommer alla dess partitioner att säkerhetskopieras med hjälp av säkerhetskopieringsprincipen.

### <a name="overriding-backup-policy"></a>Åsidosättning av säkerhetskopieringsprincipen
Det kan finnas ett scenario där säkerhetskopiering av data med samma säkerhetskopieringsschema krävs för alla tjänster i programmet, med undantag för specifika tjänster där behovet är att ha säkerhetskopiering av data med högre frekvensschema eller att säkerhetskopiera till ett annat lagringskonto eller Fildelning. För att hantera sådana scenarier tillhandahåller säkerhetskopieringstjänsten möjlighet att åsidosätta spridd princip vid tjänst- och partitionsomfattning. När säkerhetskopieringsprincipen är associerad vid _tjänsten_ eller _partitionen_åsidosätter den eventuella principer för spridd säkerhetskopiering.

### <a name="example"></a>Exempel

I det här exemplet används inställningar med två program, _MyApp_A_ och _MyApp_B_. Ansökan _MyApp_A_ innehåller två tillförlitliga tillståndskänsliga tjänster, _SvcA1_ & _SvcA3_, och en pålitlig aktör tjänst, _ActorA2_. _SvcA1_ innehåller tre partitioner medan _ActorA2_ och _SvcA3_ innehåller två partitioner vardera.  Application _MyApp_B_ innehåller tre Tillförlitliga tillståndskänsliga tjänster, _SvcB1,_ _SvcB2_och _SvcB3_. _SvcB1_ och _SvcB2_ innehåller två partitioner vardera medan _SvcB3_ innehåller tre partitioner.

Anta att dessa programs krav för säkerhetskopiering av data är följande

1. MyApp_A
    1. Skapa daglig säkerhetskopiering av data för alla partitioner av alla _tillförlitliga tillståndskänsliga tjänster_ och _tillförlitliga aktörer_ som tillhör programmet. Ladda upp säkerhetskopierade data till plats _BackupStore1_.

    2. En av tjänsterna, _SvcA3,_ kräver säkerhetskopiering av data varje timme.

    3. Datastorleken i _partitionen SvcA1_P2_ är mer än förväntat och dess säkerhetskopieringsdata bör lagras på olika lagringsplatser _BackupStore2_.

2. MyApp_B
    1. Skapa säkerhetskopiering av data varje söndag klockan 8:00 för alla partitioner i _SvcB1-tjänsten._ Ladda upp säkerhetskopierade data till plats _BackupStore1_.

    2. Skapa säkerhetskopiering av data varje dag klockan 8:00 för partition _SvcB2_P1_. Ladda upp säkerhetskopierade data till plats _BackupStore1_.

För att åtgärda dessa krav för säkerhetskopiering av data är säkerhetskopieringsprinciper BP_1 till BP_5 skapas och säkerhetskopiering aktiveras på följande sätt.
1. MyApp_A
    1. Skapa säkerhetskopieringsprincip, _BP_1_, med frekvensbaserat säkerhetskopieringsschema där frekvensen är inställd på 24 timmar. och lagring av säkerhetskopior som konfigurerats för att använda _lagringsplatsen BackupStore1_. Aktivera den här principen för program _MyApp_A_ med hjälp av [Aktivera API för säkerhetskopiering av program.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) Den här åtgärden möjliggör säkerhetskopiering av data med hjälp av säkerhetskopieringsprincip _BP_1_ för alla partitioner av _tillförlitliga tillståndstjänster_ och _tillförlitliga aktörer_ som tillhör program _MyApp_A_.

    2. Skapa säkerhetskopieringsprincip, _BP_2_, med frekvensbaserat säkerhetskopieringsschema där frekvensen är inställd på 1 timmar. och lagring av säkerhetskopior som konfigurerats för att använda _lagringsplatsen BackupStore1_. Aktivera den här principen för tjänsten _SvcA3_ med hjälp av Aktivera API [för säkerhetskopiering av tjänst.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) Den här åtgärden åsidosätter spridd princip _BP_1_ genom att uttryckligen aktiverad princip för säkerhetskopiering _BP_2_ för alla partitioner av tjänsten _SvcA3_ som leder till säkerhetskopiering av data med hjälp av säkerhetskopieringsprincip _BP_2_ för dessa partitioner.

    3. Skapa säkerhetskopieringsprincip, _BP_3_, med frekvensbaserat säkerhetskopieringsschema där frekvensen är inställd på 24 timmar. och lagring av säkerhetskopior som konfigurerats för att använda _lagringsplatsen BackupStore2_. Aktivera den här principen för partition _SvcA1_P2_ med hjälp av Aktivera API [för partitionssäkerhetssäkerhet.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Den här åtgärden åsidosätter spridd princip _BP_1_ genom att uttryckligen aktiverad princip för säkerhetskopiering _BP_3_ för partition _SvcA1_P2_.

2. MyApp_B
    1. Skapa säkerhetskopieringsprincip, _BP_4_, med tidsbaserat säkerhetskopieringsschema där schemafrekvenstypen är inställd på veckotyp, kördagar är inställda på söndag och körningstider är inställda på 8:00. Säkerhetskopieringslagring konfigurerad för att använda _lagringsplats BackupStore1_. Aktivera den här principen för tjänsten _SvcB1_ med hjälp av Aktivera API [för säkerhetskopiering av tjänst.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) Den här åtgärden möjliggör säkerhetskopiering av data med hjälp av säkerhetskopieringsprincip _BP_4_ för alla partitioner av tjänsten _SvcB1_.

    2. Skapa säkerhetskopieringsprincip, _BP_5_, med tidsbaserat säkerhetskopieringsschema där schemafrekvenstypen är inställd på daglig och körtider är inställt på 8:00. Säkerhetskopieringslagring konfigurerad för att använda _lagringsplats BackupStore1_. Aktivera den här principen för partition _SvcB2_P1_ med hjälp av Aktivera API [för partitionssäkerhetssäkerhet.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Den här åtgärden möjliggör säkerhetskopiering av data med hjälp av principer för säkerhetskopiering _BP_5_ för partition _SvcB2_P1_.

I följande diagram visas uttryckligen aktiverade principer för säkerhetskopiering och spridade principer för säkerhetskopiering.

![Programhierarki för tjänstinfrastruktur][0]

## <a name="disable-backup"></a>Inaktivera säkerhetskopiering
Principer för säkerhetskopiering kan inaktiveras när du inte behöver säkerhetskopiera data. Säkerhetskopieringsprincipen aktiverad vid ett _program_ kan endast inaktiveras på samma _program_ med inaktivera api för säkerhetskopiering av [program,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) säkerhetskopieringsprincipen aktiverad vid en _tjänst_ kan inaktiveras på samma _tjänst_ med hjälp av [Inaktivera tjänstsäkerhetssäkerhets-API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) och säkerhetskopieringsprincipen aktiverad vid en _partition_ kan inaktiveras på samma _partition_ med [inaktivera partitionssäkerhets-API.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup)

* Inaktivera säkerhetskopieringsprincipen för ett _program_ stoppar alla periodiska säkerhetskopieringar av data som sker som ett resultat av spridning av säkerhetskopieringsprincipen till tillförlitliga tillståndskänsliga tjänstpartitioner eller tillförlitliga aktörspartitioner.

* Inaktivera säkerhetskopieringsprincipen för en _tjänst_ stoppar alla periodiska säkerhetskopieringar av data som sker som ett resultat av spridning av den här säkerhetskopieringsprincipen till partitionerna för _tjänsten_.

* Inaktivera säkerhetskopieringsprincipen för en _partition_ stoppar all periodisk säkerhetskopiering av data som sker på grund av säkerhetskopieringsprincipen på partitionen.

* När du inaktiverar säkerhetskopiering för en `CleanBackup` entitet (program/tjänst/partition) kan du ställas in på _true_ för att ta bort alla säkerhetskopior i konfigurerad lagring.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Pausa & återuppta säkerhetskopieringen
En viss situation kan kräva tillfällig avstängning av periodisk säkerhetskopiering av data. I en sådan situation, beroende på kravet, kan pausa API för säkerhetskopiering användas vid ett _program,_ _tjänst_eller _partition_. Periodisk avstängning för säkerhetskopiering är transitivt över underträdet i programmets hierarki från den punkt den tillämpas. 

* När avstängning tillämpas på ett _program_ med [Suspend Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API, pausas alla tjänster och partitioner under det här programmet för regelbunden säkerhetskopiering av data.

* När avstängning tillämpas på en _tjänst_ med [suspend service backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API, pausas alla partitioner under den här tjänsten för regelbunden säkerhetskopiering av data.

* När avstängning tillämpas på en _partition_ med [suspend partition backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API, pausas partitioner under den här tjänsten pausas för regelbunden säkerhetskopiering av data.

När behovet av avstängning är över kan den periodiska säkerhetskopieringen återställas med respektive API för säkerhetskopiering av återupptare. Periodisk säkerhetskopiering måste återupptas vid samma _program,_ _tjänst_eller _partition_ där den avbröts.

* Om avstängning tillämpades vid ett _program_bör den återupptas med [API för återställning av program.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) 

* Om avstängning tillämpades på en _tjänst_bör den återupptas med API för säkerhetskopiering av [återställning av återställning av tjänsten.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup)

* Om avstängning tillämpades på en _partition_bör den återupptas med [API för återställningspartitionssäkerhet.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup)

### <a name="difference-between-suspend-and-disable-backups"></a>Skillnaden mellan Pausa och Inaktivera säkerhetskopior
Inaktivera säkerhetskopiering bör användas när säkerhetskopior inte längre krävs för ett visst program, en viss tjänst eller en viss partition. Man kan åberopa inaktivera begäran om säkerhetskopiering tillsammans med ren säkerhetskopior parameter vara sant vilket skulle innebära att alla befintliga säkerhetskopior tas bort också. Men paus ska användas i scenarier där man vill stänga av säkerhetskopior tillfälligt som när den lokala disken blir full eller ladda upp säkerhetskopiering misslyckas på grund av kända nätverksproblem etc. 

Även inaktivera kan anropas endast på en nivå som tidigare aktiverats för säkerhetskopiering uttryckligen men avstängning kan tillämpas på alla nivåer som för närvarande är aktiverat för säkerhetskopiering antingen direkt eller via arv / hierarki. Om säkerhetskopiering till exempel är aktiverat på programnivå kan man bara inaktivera inaktivera på programnivå, men paus kan anropas vid program, någon tjänst eller partition under det programmet. 

## <a name="auto-restore-on-data-loss"></a>Automatisk återställning vid dataförlust
Tjänstpartitionen kan förlora data på grund av oväntade fel. Disken för två av tre repliker för en partition (inklusive den primära repliken) skadas eller rensas till exempel.

När Service Fabric upptäcker att partitionen är `OnDataLossAsync` i dataförlust anropas gränssnittsmetoden på partitionen och förväntar sig att partitionen vidtar nödvändiga åtgärder för att komma ut ur dataförlust. I det här fallet, om den `AutoRestoreOnDataLoss` effektiva säkerhetskopieringsprincipen på partitionen har flagga inställd på `true` då återställningen utlöses automatiskt med hjälp av senaste tillgängliga säkerhetskopia för den här partitionen.

## <a name="get-backup-configuration"></a>Hämta konfiguration för säkerhetskopiering
Separata API:er görs tillgängliga för att hämta konfigurationsinformation för säkerhetskopiering vid ett _program,_ _tjänst_och partitionsomfattning. _partition_ [Hämta konfigurationsinformation för programsäkerhet,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)hämta konfigurationsinformation för [säkerhetskopiering av tjänsten](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)och hämta [konfigurationsinformation för partitionssäkerhetskopior](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) är dessa API:er. I huvudsak returnerar dessa API:er den tillämpliga säkerhetskopieringsprincipen, omfattning där säkerhetskopieringsprincipen tillämpas och information om avstängning av säkerhetskopiering. Följande är en kort beskrivning om returnerade resultat av dessa API: er.

- Konfigurationsinformation för säkerhetskopiering av program: innehåller information om säkerhetskopieringsprincipen som tillämpas vid programmet och alla överliggande principer för tjänster och partitioner som tillhör programmet. Den innehåller också avstängningsinformation för programmet och it-tjänster och partitioner.

- Konfigurationsinformation för säkerhetskopiering av tjänsten: innehåller information om effektiv säkerhetskopieringsprincip till tjänst och det omfång vid vilket den här principen tillämpades och alla överridna principer vid dess partitioner. Den innehåller också avstängningsinformation för tjänsten och dess partitioner.

- Konfigurationsinformation för partitionssäkerhetskopiering: innehåller information om effektiv säkerhetskopieringsprincip vid partitionen och det omfång som den här principen tillämpades på. Den innehåller också avstängningsinformation för partitionerna.

## <a name="list-available-backups"></a>Lista tillgängliga säkerhetskopior

Tillgängliga säkerhetskopior kan visas med Api för hämta säkerhetskopior. Resultatet av API-anropet innehåller informationsobjekt för säkerhetskopiering som är relaterade till alla säkerhetskopior som är tillgängliga vid säkerhetskopieringslagringen, som är konfigurerad i tillämplig princip för säkerhetskopiering. Olika varianter av det här API:et tillhandahålls för att lista tillgängliga säkerhetskopior som tillhör ett program, en tjänst eller en partition. Dessa API:er stöder att du får den _senaste_ tillgängliga säkerhetskopian av alla tillämpliga partitioner eller filtrering av säkerhetskopior baserat på _startdatum_ och _slutdatum_.

Dessa API:er stöder också sidnumrering av resultaten, när parametern _MaxResults_ är inställd på icke-noll positivt heltal returnerar API:et maximalt _MaxResults-informationsobjekt_ för säkerhetskopiering. Om det finns fler tillgängliga objekt för säkerhetskopieringsinformation än _maxresults-värdet_ returneras en fortsättningstoken. Giltig parameter för fortsättningstoken kan användas för att hämta nästa uppsättning resultat. När giltigt tokenvärde för fortsättning skickas till nästa anrop av API:et returnerar API:et nästa uppsättning resultat. Ingen fortsättningstoken ingår i svaret när alla tillgängliga resultat returneras.

Nedan följer den korta informationen om varianter som stöds.

- [Hämta programsäkerhetslista:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist)Returnerar en lista över säkerhetskopior som är tillgängliga för varje partition som tillhör det angivna Service Fabric-programmet.

- [Hämta servicesäkerhetslista:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist)Returnerar en lista över säkerhetskopior som är tillgängliga för varje partition som tillhör given Service Fabric-tjänst.
 
- [Hämta partitionssäkerhetslista:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist)Returnerar en lista över säkerhetskopior som är tillgängliga för den angivna partitionen.

## <a name="next-steps"></a>Nästa steg
- [REST API-referens för reservåteråterställning](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
