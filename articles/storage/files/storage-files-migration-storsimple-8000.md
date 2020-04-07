---
title: StorSimple 8000-seriens migrering till Azure File Sync
description: Lär dig hur du migrerar en StorSimple 8100- eller 8600-apparat till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f0c4da7caf71670746e84d5cfaa457ebae57156
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755042"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 och 8600 migrering till Azure File Sync

StorSimple 8000-serien representeras av antingen 8100 eller de 8600 fysiska, lokala apparaterna och deras molntjänstkomponenter. Det är möjligt att migrera data från någon av dessa enheter till en Azure File Sync-miljö. Azure File Sync är standard- och strategisk azure-tjänst som StorSimple-enheter kan migreras till.

StorSimple 8000-serien kommer att nå sin [uttjänt](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) i december 2022. Det är viktigt att börja planera din migrering så snart som möjligt. Den här artikeln innehåller nödvändiga steg i bakgrundskunskap och migreringar för en lyckad migrering till Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft har åtagit sig att hjälpa kunder i deras migrering. E-post AzureFilesMigration@microsoft .com för en anpassad migreringsplan samt hjälp under migreringen.

Azure File Sync är en Microsoft-molntjänst som baseras på två huvudkomponenter:

* Filsynkronisering och molnnivådelning.
* Filresurser som inbyggt lagringsutrymme i Azure, som kan nås via flera protokoll som SMB och file REST. En Azure-filresurs kan jämföras med en filresurs på en Windows Server som du kan montera som en nätverksenhet. Den stöder viktiga filåtergivningsaspekter som attribut, behörigheter och tidsstämplar. Med Azure-filresurser behöver du inte längre ett program eller en tjänst tolka de filer och mappar som lagras i molnet. Du kan komma åt dem internt via välbekanta protokoll och klienter som Utforskaren. Det gör Azure-fil delar den perfekta och mest flexibla metoden för att lagra filserverdata för allmänt ändamål samt vissa programdata i molnet.

Den här artikeln fokuserar på migreringsstegen. Om du innan du migrerar vill veta mer om Azure File Sync rekommenderar vi följande artiklar:

* [Synkronisering av Azure-filer – översikt](https://aka.ms/AFS "Översikt")
* [Azure File Sync - distributionsguide](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migreringsmål

Målet är att garantera produktionsdatas integritet och garantera tillgänglighet. Den senare kräver att hålla stilleståndstiden till ett minimum, så att den kan passa in i eller bara något överstiga vanliga underhållsfönster.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Migreringssökvägen för StorSimple 8000-serien till Azure File Sync

En lokal Windows-server krävs för att köra en Azure File Sync-agent. Windows Server kan vara minst en 2012R2-server men är helst en Windows Server 2019.

Det finns många alternativa migreringsvägar och det skulle skapa för lång tid av en artikel för att dokumentera dem alla och illustrera varför de bär risk eller nackdelar över den väg vi rekommenderar som bästa praxis i den här artikeln.

![Översikt över migreringsfaser i StorSimple 8000-serien](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000-serien migrationsväg översikt över faserna längre nedan i den här artikeln.")

Den föregående bilden visar faser som motsvarar avsnitt i den här artikeln.
Vi använder en migrering på molnsidan för att undvika onödigt återkallande av filer till din lokala StorSimple-apparat. Den här metoden undviker att påverka lokala cachelagringsbeteende eller användning av nätverksbandbredd, som antingen kan påverka dina produktionsarbetsbelastningar.
En migrering på molnsidan arbetar på en ögonblicksbild (en volymklon) av dina data. Så dina produktionsdata är isolerade från denna process - tills cut-over i slutet av migreringen. Arbeta bort av vad som i huvudsak är en säkerhetskopia, gör migreringen säker och lätt repeterbar, om du stöter på några svårigheter.

## <a name="considerations-around-existing-storsimple-backups"></a>Överväganden kring befintliga StorSimple-säkerhetskopior

StorSimple kan du ta säkerhetskopior i form av volym kloner. I den här artikeln används en ny volymklon för att migrera dina livefiler.
Om du behöver migrera säkerhetskopior utöver dina livedata gäller fortfarande all vägledning i den här artikeln. Den enda skillnaden är att istället för att börja med en ny volym klon, kommer du att börja med den äldsta backup volym klon du behöver migrera.

Sekvensen är följande:

* Bestäm den minsta uppsättning volymkloner som du måste migrera. Vi rekommenderar att du håller den här listan till ett minimum om möjligt, eftersom ju fler säkerhetskopior du migrerar ju längre den övergripande migreringen tar.
* När du går igenom migreringsprocessen börjar du med den äldsta volymklon som du tänker migrera och använder nästa äldsta på varje efterföljande migrering.
* När varje volymklonmigrering har slutförts måste du ta en ögonblicksbild av Azure-filresurs. [Ögonblicksbilder av Azure-filresurs](storage-snapshots-files.md) är hur du behåller säkerhetskopior av filerna och mappstrukturen för dina Azure-filresurser. Du behöver dessa ögonblicksbilder när migreringen är klar för att säkerställa att du har bevarat versioner av var och en av dina volymkloner när du går igenom migreringen.
* Se till att du tar ögonblicksbilder av Azure-filresurs för alla Azure-filresurser som betjänas av samma StorSimple-volym. Volym kloner är på volymnivå, Azure filresurs ögonblicksbilder är på resursnivå. Du måste ta en ögonblicksbild av resursen (på varje Azure-filresurs) när migreringen av en volymklon är klar.
* Upprepa migreringsprocessen för en volymklon och ta ögonblicksbilder av resurser efter varje volymklon tills du fastnar i en ögonblicksbild av livedata. Processen att migrera en volymklon beskrivs i faserna nedan. 

Om du inte behöver flytta säkerhetskopior alls och kan starta en ny kedja av säkerhetskopior på Azure-filresurssidan efter att migreringen av endast live-data har gjorts, är det fördelaktigt att minska komplexiteten i migreringen och hur lång tid migreringen tar. Du kan fatta beslutet om du vill flytta säkerhetskopior eller inte och hur många för varje volym (inte varje resurs) du har i StorSimple.

## <a name="phase-1-get-ready"></a>Fas 1: Gör dig redo

:::row:::
    :::column:::
        ![En bild som illustrerar en del av den tidigare översiktsbilden som hjälper till att fokusera det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Grunden för migreringen är en volymklon och en virtuell molninstallation, kallad StorSimple 8020.
Den här fasen fokuserar på distribution av dessa resurser i Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Distribuera en virtuell StorSimple 8020-apparat

Distribuera en molninstallation är en process som kräver säkerhet, nätverk och några andra överväganden.

> [!IMPORTANT]
> Följande guide innehåller några onödiga avsnitt. Läs och följ artikeln från början fram till "Steg 3". Gå sedan tillbaka till den här artikeln. Du behöver inte slutföra "Steg 3" eller något utöver det i den guiden, just nu.

[Driftsättning av en virtuell StorSimple 8020-apparat](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Bestäm en volymklon som ska användas

När du är redo att påbörja migreringen är det första steget att ta en ny volymklon - precis som för säkerhetskopiering - som fångar det aktuella tillståndet för din StorSimple-molnlagring. Ta en klon för var och en av de StorSimple-volymer du har.
Om du är i behov av att flytta säkerhetskopior, då den första volymen klon du använder är inte en nyskapad klon men den äldsta volymen klon (äldsta backup) du behöver migrera.
Se avsnittet ["Överväganden kring befintliga StorSimple-säkerhetskopior"](#considerations-around-existing-storsimple-backups) för detaljerad vägledning.

> [!IMPORTANT]
> Följande guide innehåller några onödiga avsnitt. Läs och följ bara stegen som beskrivs i avsnittet länkad till. Gå sedan tillbaka till den här artikeln. Du behöver inte följa avsnittet "Nästa steg".

[Skapa en klon av en volym](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Använd volymklonen

Den sista fasen av fas 1 är att göra volymen klon du har valt, tillgänglig på 8020 virtuella installationen i Azure.

> [!IMPORTANT]
> Följande guide innehåller de nödvändiga stegen, men också - i slutet - en instruktion för att formatera volymen. **FORMATERA INTE VOLYMEN** Läs och följ det länkade till "avsnitt 7" från början till instruktionen: "10. För att formatera en enkel volym, ..."  Stoppa innan du följer det här steget och återgå till den här artikeln.

[Montera en volymklon på den virtuella 8020-installationen i Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Sammanfattning av fas 1

Nu när du har slutfört fas 1 har du gjort följande:

* Distribuerade en virtuell StorSimple 8020-installation i Azure.
* Bestämd vilken volymklon du ska börja migreringen med.
* Monterade dina volymkloner (en för varje strömvolym) till den virtuella StorSimple-installationen i Azure, med dess data tillgängliga för vidare användning.

## <a name="phase-2-cloud-vm"></a>Fas 2: Virtuell moln

:::row:::
    :::column:::
        ![En bild som illustrerar en del av den tidigare översiktsbilden som hjälper till att fokusera det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        När den första klonen är tillgänglig på den virtuella storsimple 8020-installationen i Azure är det nu dags att etablera en virtuell dator och exponera volymklonen (eller flera) till den virtuella datorn över iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Distribuera en virtuell Azure-dator

Den virtuella windows server-datorn i Azure är precis som StorSimple 8020, en tillfällig infrastruktur som bara är nödvändig under migreringen.
Konfigurationen av den virtuella datorn som du distribuerar beror främst på antalet objekt (filer och mappar) som du ska synkronisera. Vi rekommenderar att du går med en konfiguration med högre prestanda om du har några problem.

En enda Windows Server kan synkronisera upp till 30 Azure-filresurser.
De specifikationer du bestämmer dig för måste omfatta varje resurs / sökväg eller roten till StorSimple volymen och räkna objekt (filer och mappar).

Den totala storleken på data är mindre av en flaskhals - det är antalet objekt du behöver för att skräddarsy maskinen specifikationer till.

* [Lär dig hur du ã¶r storlek på en Windows Server baserat på antalet objekt (filer och mappar) som du behöver synkronisera.](storage-sync-files-planning.md#recommended-system-resources)

    **Observera:** Den tidigare länkade artikeln visar en tabell med ett intervall för serverminne (RAM). Orientera mot det stora antalet för Den virtuella Azure-datorn. Du kan orientera dig mot det mindre numret för din lokala maskin.

* [Lär dig hur du distribuerar en Virtuell Windows Sever-dator.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Kontrollera att den virtuella datorn distribueras i samma Azure-region som den virtuella storsimple 8020-installationen. Om du som en del av den här migreringen också behöver ändra regionen för dina molndata från den region som lagras i dag, kan du göra det i ett senare steg, när du etablerar Azure-filresurser.

> [!IMPORTANT]
> Ofta används en lokal Windows Server för att fronta den lokala StorSimple-apparaten. I en sådan konfiguration är det möjligt att aktivera funktionen "[Data deduplication](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" på den Windows Server. **Om du har använt dataduplicering med dina StorSimple-data, se till att du aktiverar dataduplicering även på den här Azure VM.** Blanda inte ihop den här dedupliceringen på filnivå med StorSimples inbyggd deduplicering på blocknivå, för vilken ingen åtgärd är nödvändig.

> [!IMPORTANT]
> För att optimera för prestanda, distribuera en **snabb OS-disk** för din moln-VM. Du lagrar synkroniseringsdatabasen på OS-disken för alla dina datavolymer. Se dessutom till att du skapar en **stor OS-disk**. Beroende på antalet objekt (filer och mappar) på Dina StorSimple-volymer kan OS-disken behöva **flera hundra GiB utrymme** för att rymma synkroniseringsdatabasen.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Exponera StorSimple 8020-volymerna för Den virtuella Azure-datorn

I den här fasen ansluter du en eller flera StorSimple-volymer från den virtuella 8020-installationen via iSCSI till den Windows Server-virtuella dator som du har etablerat.

> [!IMPORTANT]
> I följande artiklar kan du bara fylla i **get private IP för molninstallationen** och Anslut över **iSCSI-avsnitt** och återgå till den här artikeln.

1. [Få en privat IP-adress för molninstallationen](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Anslut över iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Sammanfattning av fas 2

Nu när du har slutfört fas 2 har du: 

* Etablerat en virtuell Windows Server-dator i samma region som den virtuella 8020 virtuella StorSimple-installationen
* Exponerade alla tillämpliga volymer från 8020 till Windows Server VM över iSCSI.
* Du bör nu se fil- och mappinnehåll när du använder Utforskaren på den virtuella servern på de monterade volymerna.

Fortsätt bara till fas 3 när du har slutfört dessa steg för alla volymer som behöver migrering.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fas 3: Konfigurera Azure-filresurser och gör dig redo för Azure File Sync

:::row:::
    :::column:::
        ![En bild som illustrerar en del av den tidigare översiktsbilden som hjälper till att fokusera det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        I den här fasen kommer du att bestämma och etablera ett antal Azure-filresurser, skapa en Windows Server lokalt som en StorSimple-uppsättning ersättning och konfigurera den servern för Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mappa dina befintliga namnområden till Azure-filresurser

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Distribuera Azure-filresurser

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Om du behöver ändra Azure-regionen från den aktuella regionen som dina StorSimple-data finns i, etablerar du Azure-filresurserna i den nya region som du vill använda. Du bestämmer regionen genom att välja den när du etablerar lagringskontona som innehåller dina Azure-filresurser. Se till att även Azure File Sync-resursen som du ska etablera nedan är i samma nya region.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuera molnresursen Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Om du behöver ändra Azure-regionen från den aktuella regionen som dina StorSimple-data finns i, har du etablerat lagringskontona för dina Azure-filresurser i den nya regionen. Kontrollera att du har valt samma region när du distribuerar den här lagringssynkroniseringstjänsten.

### <a name="deploy-an-on-premises-windows-server"></a>Distribuera en lokal Windows Server

* Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller en fysisk server. Ett Windows Server-felväxlingskluster stöds också. Återanvänd inte servern som du kanske har fronting StorSimple 8100 eller 8600.
* Etablera eller lägg till direktansluten lagring (DAS jämfört med NAS, som inte stöds).

Det är bäst att ge din nya Windows Server en lika stor eller större mängd lagringsutrymme än vad som är till 8100- eller 8600-8600-apparat lokalt tillgängligt för cachelagring. Du kommer att använda Windows Server på samma sätt som du använde StorSimple-apparaten, om den har samma mängd lagringsutrymme som enheten bör cachelagringsupplevelsen vara liknande, om inte densamma.
Du kan lägga till eller ta bort lagringsutrymme från Windows Server efter behag. På så sätt kan du skala upp den lokala volymstorleken och mängden lokal lagring som är tillgänglig för cachelagring.

### <a name="prepare-the-windows-server-for-file-sync"></a>Förbereda Synkroniseringen av Windows Server för fil

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurera Synkronisering av Azure-filer på Windows Server

Din registrerade lokala Windows Server måste vara klar och ansluten till internet för den här processen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Var noga med att aktivera molnnivåhantering!** Molnnivådelning är AFS-funktionen som gör att den lokala servern har mindre lagringskapacitet än vad som lagras i molnet, men som ändå har det fullständiga namnområdet tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb, lokal åtkomstprestanda. En annan anledning att aktivera molnnivåhantering i det här steget är att vi inte vill synkronisera filinnehåll i det här skedet, bara namnområdet bör flyttas just nu.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fas 4: Konfigurera Den virtuella Azure-datorn för synkronisering

:::row:::
    :::column:::
        ![En bild som illustrerar en del av den tidigare översiktsbilden som hjälper till att fokusera det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Den här fasen gäller din virtuella Azure-dator med iSCSI-monterade, första volymklon(er). Under den här fasen kommer du att ansluta den virtuella datorn via Azure File Sync och starta en första omgång av att flytta filer från din StorSimple volym klon (s).
        
    :::column-end:::
:::row-end:::

Du har redan konfigurerat din lokala server som ersätter storsimple 8100- eller 8600-installationen för Azure File Sync. 

Konfigurera Azure VM är en nästan identisk process, med ytterligare ett steg. Följande steg guidar dig genom processen.

> [!IMPORTANT]
> Det är viktigt att den virtuella **Azure-datorn inte är konfigurerad med molnnivåbaserad aktiverad!** Du kommer att utbyta volymen på den här servern med nyare volym kloner under hela migreringen. Molnnivådelning har ingen nytta och omkostnader för CPU-användning som du bör undvika.

1. [Distribuera AFS-agenten. (se föregående avsnitt)](#prepare-the-windows-server-for-file-sync)
2. [Förbereda den virtuella datorn för Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Konfigurera synkronisering](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Gör den virtuella datorn redo för Azure File Sync

Azure File Sync används för att flytta filerna från de monterade iSCSI StorSimple-volymerna till azure-filresurserna för mål.
Under den här migreringsprocessen ska du montera flera volymkloner till den virtuella datorn under samma enhetsbeteckning. Azure File Sync måste konfigureras för att se nästa volymklon som du har monterat som en nyare version av filerna och mapparna och uppdatera Azure-filresurserna som är anslutna via Azure File Sync. 

> [!IMPORTANT]
> För att detta ska fungera måste en registernyckel anges på servern innan Azure File Sync har konfigurerats.

1. Skapa en ny katalog på systemenheten för den virtuella datorn. Azure File Sync-information måste sparas där i stället för på de monterade volymklonerna. Exempel: `"C:\syncmetadata"`
2. Öppna regedit och leta upp följande registreringsdatafil:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Skapa en ny nyckel av typen String med namnet: ***MetadataRootPath***
4. Ange den fullständiga sökvägen till den katalog som du skapade på systemvolymen, till exempel:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Konfigurera Azure File Sync på den virtuella Azure-datorn

Det här steget liknar föregående avsnitt, som beskriver hur du konfigurerar AFS på den lokala servern.

Skillnaden är att du inte får aktivera molnnivåhantering på den här servern och att du måste se till att rätt mappar är anslutna till rätt Azure-filresurser. Annars matchar inte namngivningen av Azure-filresurser och datainnehåll och det finns inget sätt att byta namn på molnresurserna eller lokala mappar utan att konfigurera om synkroniseringen.

Se [föregående avsnitt om hur du konfigurerar Azure File Sync på en Windows Server](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Sammanfattning av steg 4

Nu har du konfigurerat Azure File Sync på den Virtuella Azure-datorn som du har monterat dina StorSimple-volymkloner via iSCSI.
Data flödar nu från den virtuella Azure-datorn till de olika Azure-filresurserna och därifrån visas ett helt trött namnområde på din lokala Windows Server.

> [!IMPORTANT]
> Kontrollera att inga ändringar har gjorts eller att användaråtkomst beviljas windowsservern just nu.

De första volymklondata som flyttas via Azure-datorn till Azure-filresurserna kan ta lång tid, potentiellt veckor. Uppskatta den tid detta kommer att ta är knepigt och beror på många faktorer. Framför allt den hastighet med vilken Azure VM kan komma åt filer på StorSimple-volymerna och hur snabbt Azure File Sync kan bearbeta de filer och mappar som behöver synkroniseras. 

Av erfarenhet kan vi anta att bandbredden - alltså den faktiska datastorleken - spelar en underordnad roll. Den tid som den här eller efterföljande migreringsrundan tar beror oftast på antalet objekt som kan bearbetas per sekund. Så till exempel 1 TiB med en 100.000 filer och mappar kommer sannolikt att sluta långsammare än 1 TiB med endast 50.000 filer och mappar.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fas 5: Iterera genom kloner av flera volymer

:::row:::
    :::column:::
        ![En bild som illustrerar en del av den tidigare översiktsbilden som hjälper till att fokusera det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Som diskuterats i föregående fas kan den första synkroniseringen ta lång tid. Dina användare och program har fortfarande åtkomst till den lokala StorSimple 8100- eller 8600-apparaten. Det innebär att förändringar ackumuleras, och med varje dag ett större delta mellan levande data och den ursprungliga volymen klon, är du för närvarande migrering, former. I det här avsnittet får du lära dig hur du minimerar driftstopp genom att använda flera volymkloner och berätta när synkroniseringen är klar.
    :::column-end:::
:::row-end:::

Tyvärr är migreringsprocessen inte omedelbart. Det innebär att det ovan nämnda deltat till levande data är en oundviklig konsekvens. Den goda nyheten är att du kan upprepa processen att montera nya volym kloner. Varje volymklons delta kommer att vara gradvis mindre. Så småningom kommer en synkronisering att avslutas inom en tid som du anser vara acceptabelt för att ta användare och appar offline för att klippa över till din lokala Windows-server.

Upprepa följande steg tills synkroniseringen har slutförts med en tillräckligt snabb tid som du känner dig bekväm med att koppla från användare och appar:

1. [Bestäm synkroniseringen är klar för en viss volymklon.](#determine-when-sync-is-done)
2. [Ta en ny volym klon (s) och montera den på 8020 virtuell installation.](#the-next-volume-clones)
3. [Bestäm när synkroniseringen är klar.](#determine-when-sync-is-done)
4. [Cut-over strategi](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Nästa volym klon (er)

Vi har diskuterat att ta en volym klon (s) tidigare i denna artikel.
Denna fas har två åtgärder:

1. [Ta en volymklon](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Montera den volymklonen (se ovan)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Ta reda på när synkroniseringen är klar

När synkroniseringen är klar kan du stoppa tidsmätningen och avgöra om du behöver upprepa processen att ta en volymklon och montera den eller om den tidssynkronisering tog med den senaste volymklonen var tillräckligt liten.

För att avgöra synkronisering är klar:

1. Öppna Loggboken och navigera till **Program och tjänster**
2. Navigera och öppna **Microsoft\FileSync\Agent\Telemetri**
3. Leta efter den senaste **händelsen 9102**, vilket motsvarar en slutförd synkroniseringssession
4. Välj **Information** och bekräfta att **SyncDirection-värdet** är **Ladda upp**
5. Kontrollera **HResult** och bekräfta att den visar **0**. Det innebär att synkroniseringssessionen lyckades. Om HResult är ett värde som inte är noll uppstod ett fel vid synkroniseringen. Om **PerItemErrorCount** är större än 0 synkroniserades inte vissa filer eller mappar korrekt. Det är möjligt att ha en HResult på 0 men en PerItemErrorCount som är större än 0. Nu behöver du inte oroa dig för PerItemErrorCount. Vi kommer att fånga dessa filer senare. Om antalet fel är betydande, tusentals artiklar, kontakta kundsupport och be att vara ansluten till Azure File Sync produktgrupp för direkt vägledning om bästa, nästa faser.
6. Kontrollera att se flera 9102 händelser med HResult 0 i rad. Detta indikerar att synkroniseringen är klar för den här volymklonen.

### <a name="cut-over-strategy"></a>Cut-over strategi

1. Ta reda på om synkronisering från en volymklon är tillräckligt snabb nu. (Delta tillräckligt liten.)
2. Koppla från StorSimple-apparaten.
3. En sista RoboCopy.

Mät tiden och avgöra om synkronisering från en ny volymklon kan avslutas inom ett tillräckligt litet tidsfönster, som du har råd med som driftstopp i systemet.

Det är nu dags att inaktivera användaråtkomst till StorSimple-apparaten. Inga fler ändringar. Stilleståndstiden har börjat.
Du måste lämna apparaten online och ansluten, men måste nu förhindra ändringar på den.

I fas 6 kommer du ikapp med alla delta i live-data sedan den senaste volymen klon.

## <a name="phase-6-a-final-robocopy"></a>Fas 6: En slutlig RoboCopy

Nu finns det två skillnader mellan den lokala Windows Server och StorSimple 8100- eller 8600-apparaten:

1. Det kan finnas filer som inte har synkroniserats (se **PerItemErrors** från händelseloggen ovan)
2. StorSimple-enheten har en ifylld cache jämfört med Windows Server bara ett namnområde utan att filinnehåll lagras lokalt just nu.

![En bild som illustrerar en del av den tidigare översiktsbilden som hjälper till att fokusera det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Vi kan föra cachen av Windows Server upp till tillståndet för apparaten och se till att ingen fil lämnas kvar med en slutlig RoboCopy.

> [!CAUTION]
> Det är absolut nödvändigt att RoboCopy kommandot du följer, är exakt som beskrivs nedan. Vi vill bara kopiera filer som är lokala och filer som inte har flyttats genom metoden volymklon+sync tidigare. Vi kan lösa problemen varför de inte synkroniserade senare, när migreringen är klar. (Se [felsökning av Azure-filsynkronisering](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Det är troligen oskrivbara tecken i filnamn som du inte missar när de tas bort.)

RoboCopy, kommando:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Bakgrund:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Gör det möjligt för RoboCopy att köra flertrådade. Standard är 8, max är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Utdatastatus till LOG-fil som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till konsolfönstret. Används tillsammans med utdata till en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhetskopieringsprogram skulle använda. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR (MIR)
   :::column-end:::
   :::column span="1":::
      Gör det möjligt för RoboCopy att endast överväga deltan mellan källan (StorSimple-apparat) och målet (Windows Server-katalog).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      återgivning av filkopian (standard är /COPY:DAT), kopiera flaggor: D=Data, A=Attribut, T=Tidsstämplar, S=Security=NTFS-ACL:er, O=Ägarinformation, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      KOPIERA ALL filinformation (motsvarande /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      återgivning för kopian av kataloger (standard är /DCOPY:DA), kopiera flaggor: D=Data, A=Attribut, T=Tidsstämplar
   :::column-end:::
:::row-end:::

Du bör köra det här RoboCopy-kommandot för var och en av katalogerna på Windows Server som ett mål, som du har konfigurerat med filsynkronisering till en Azure-fil.

Du kan köra flera av dessa kommandon parallellt.
När detta RoboCopy-steg är klart kan du tillåta användare och appar att komma åt Windows Server som de gjorde i StorSimple-apparaten tidigare.

Läs robocopy loggfilen (s) för att se om filer har lämnats kvar. Om det skulle finnas problem kan du i de flesta fall lösa dem när migreringen är klar och användarna och apparna har hemtrevs till Din Windows Server igen. Om du behöver åtgärda eventuella problem gör du det före fas 7.

Det är troligt att skapa SMB-resurser på Windows Server som du hade på StorSimple data innan. Du kan front-load detta steg och göra det tidigare för att inte förlora tid här, men du måste se till att innan denna punkt, inga ändringar av filer sker på Windows-servern.

Om du har en DFS-N-distribution kan du peka DFN-namnområdena på de nya servermappplatserna. Om du inte har en DFS-N-distribution och du har frontat din 8100 8600-apparat lokalt med en Windows Server, kan du ta bort servern från domänen och domänen ansluta till din nya Windows-server med AFS till domänen, ge den samma servernamn som den gamla servern och samma resursnamn, sedan förblir cut-over till den nya servern transparent för användarna , grupprincip eller skript.

## <a name="phase-7-deprovision"></a>Fas 7: Avetablera

Under den sista fasen har du itererat genom flera volym kloner, och så småningom kunde skära över användaråtkomst till den nya Windows Server efter att ha tagit dig StorSimple apparaten offline.

Du kan nu börja avetablera onödiga resurser.
Innan du börjar är det en bra idé att observera din nya Azure File Sync-distribution i produktion, för lite. Det ger dig alternativ för att åtgärda eventuella problem som kan uppstå.

När du är nöjd och har observerat din AFS-distribution i minst några dagar kan du börja avetablera resurser i den här ordningen:

1. Inaktivera den virtuella Azure-datorn som vi har använt för att flytta data från volymklonerna till Azure-filresurserna via filsynkronisering.
2. Gå till din Storage Sync Service-resurs i Azure och avregistrera Den virtuella Azure-datorn. Som tar bort den från alla synkroniseringsgrupper.

    > [!WARNING]
    > **SE TILL att du väljer rätt maskin.** Du har stängt av moln-VM, det betyder att den ska visas som den enda offlineservern i listan över registrerade servrar. Du får inte välja den lokala Windows Server i det här steget, om du gör det avregistrerar den.

3. Ta bort Azure VM och dess resurser.
4. Inaktivera den virtuella StorSimple-8020-apparaten.
5. Avetablera alla StorSimple-resurser i Azure.
6. Koppla ur Den fysiska storsimpleapparaten från ditt datacenter.

Din migrering är klar.

## <a name="next-steps"></a>Nästa steg

Bekanta dig med Azure File Sync. Särskilt med flexibiliteten i molnnivåprinciper.

Om du ser i Azure-portalen, eller från tidigare händelser, att vissa filer permanent inte synkroniseras, går du igenom felsökningsguiden för steg för att lösa dessa problem.

* [Översikt över Synkronisering av Azure-filer: aka.ms/AFS](https://aka.ms/AFS)
* [Molnnivådelning](storage-sync-cloud-tiering.md) 
* [Felsökningsguide för Azure File Sync](storage-sync-files-troubleshoot.md)
