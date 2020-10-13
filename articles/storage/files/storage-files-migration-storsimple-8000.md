---
title: Migrering av StorSimple 8000-serien till Azure File Sync
description: Lär dig hur du migrerar en StorSimple 8100-eller 8600-apparat till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: be61a6e75c4aa9b5714ffbf3b4f19656b347c493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653255"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 och 8600-migrering till Azure File Sync

StorSimple 8000-serien representeras av antingen 8100 eller 8600 fysiska, lokala apparater och deras moln tjänst komponenter. Det är möjligt att migrera data från någon av dessa apparater till en Azure File Syncs miljö. Azure File Sync är standard och strategisk Azure-tjänst som StorSimple-apparater kan migreras till.

StorSimple 8000-serien kommer att uppnå [livs längd](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) i december 2022. Det är viktigt att börja planera migreringen så snart som möjligt. Den här artikeln innehåller de steg som krävs för att migrera till Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft strävar efter att hjälpa kunder i sin migrering. E-post AzureFilesMigration@microsoft . com för en anpassad migrations plan samt hjälp vid migreringen.

Azure File Sync är en moln tjänst från Microsoft, baserat på två huvud komponenter:

* Filsynkronisering och moln nivåer.
* Fil resurser som intern lagring i Azure, som kan nås via flera protokoll som SMB och fil REST. En Azure-filresurs är jämförbar med en fil resurs på en Windows-Server, som du kan montera som en nätverks enhet. Det stöder viktiga fil åter givning-aspekter som attribut, behörigheter och tidsstämpel. Med Azure-filresurser behöver inte längre ett program eller en tjänst för att tolka filer och mappar som lagras i molnet. Du kan komma åt dem internt över välkända protokoll och klienter som Windows Utforskaren. Detta gör Azure-fil resurser till den perfekta och mest flexibla metoden för att lagra generella fil Server data och vissa program data i molnet.

Den här artikeln fokuserar på stegen för migreringen. Om du innan du migrerar vill lära dig mer om Azure File Sync rekommenderar vi följande artiklar:

* [Azure File Sync-översikt](https://aka.ms/AFS "Översikt")
* [Guide för Azure File Sync distribution](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migreringsmål

Målet är att garantera integriteten hos produktions data och att garantera tillgängligheten. Den sistnämnda lösningen kräver att drift stoppen är minimal, så att den får plats i eller endast något högre än vanliga underhålls perioder.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 seriens migrerings Sök väg till Azure File Sync

En lokal Windows Server krävs för att köra en Azure File Sync-agent. Windows Server kan vara minst en 2012R2-Server, men det är helst en Windows Server 2019.

Det finns flera, alternativa sökvägar för migrering och det skulle skapa för lång tid för en artikel för att dokumentera alla och illustrera varför de är utsatta för risker eller nack delar som rekommenderas i den här artikeln.

![Översikt över migrering av StorSimple 8000-serien](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Översikt över StorSimple 8000-seriens migrering av stegen nedan i den här artikeln.")

Föregående bild illustrerar faser som motsvarar avsnitt i den här artikeln.
Vi använder en migrering på moln sidan för att undvika onödig återkallande av filer till din lokala StorSimple-installation. Den här metoden förhindrar påverkan på funktioner för lokal cachelagring eller användningen av nätverks bandbredd, vilket kan påverka produktions arbets belastningarna.
En migrering på moln sidan körs på en ögonblicks bild (en volym klon) av dina data. Därför är dina produktions data isolerade från den här processen tills de överflyttades i slutet av migreringen. Att arbeta av vad som egentligen är en säkerhets kopia, gör migreringen säker och lätt att upprepa, om du stöter på problem.

## <a name="considerations-around-existing-storsimple-backups"></a>Överväganden kring befintliga StorSimple säkerhets kopieringar

Med StorSimple kan du ta säkerhets kopior i form av volym kloner. I den här artikeln används en ny volym kloning för att migrera dina Live-filer.
Om du behöver migrera säkerhets kopior utöver dina real tids data gäller alla vägledningar i den här artikeln fortfarande. Den enda skillnaden är att i stället för att börja med en ny kloning av volymen börjar du med den äldsta säkerhets kopierings volymen som du behöver migrera.

Ordningen är följande:

* Bestäm den minsta uppsättning av volym kloner som du måste migrera. Vi rekommenderar att du behåller den här listan på ett minimum om möjligt, eftersom de fler säkerhets kopior som du migrerar längre än den totala migreringen tar.
* När du går igenom migreringsprocessen börjar du med den äldsta volymens klon som du vill migrera och vid varje efterföljande migrering använder du nästa äldsta.
* När varje volym klonad migrering slutförs måste du ta en ögonblicks bild av Azure-filresursen. [Ögonblicks bilder av Azure-filresurser](storage-snapshots-files.md) är hur du behåller säkerhets kopiorna av filerna och mappstrukturen för dina Azure-filresurser. Du behöver dessa ögonblicks bilder när migreringen är klar, för att se till att du har bevarat versioner av alla dina volym kloner när du går igenom migreringen.
* Se till att du tar ögonblicks bilder av Azure-filresurser för alla Azure-filresurser som hanteras av samma StorSimple-volym. Volym kloner finns på volym nivå, ögonblicks bilder av Azure-filresurser på delnings nivå. Du måste ta en ögonblicks bild av en resurs (på varje Azure-filresurs) när migreringen av en volym klon har avslut ATS.
* Upprepa migreringsprocessen för en volym kloning och ta bort ögonblicks bilder när varje volym klonas tills du får en ögonblicks bild av Live-data. Processen för att migrera en volym klon beskrivs i stegen nedan. 

Om du inte behöver flytta säkerhets kopiorna alls och kan starta en ny kedja av säkerhets kopior på Azure-filresursen efter migreringen av enbart Live-data, är det mycket bra att minska komplexiteten vid migreringen och hur lång tid migreringen tar. Du kan fatta beslutet om du vill flytta säkerhets kopior och hur många för varje volym (inte varje resurs) som du har i StorSimple.

## <a name="phase-1-get-ready"></a>Fas 1: Förbered dig

:::row:::
    :::column:::
        ![En bild som illustrerar en del av den tidigare översikts avbildningen som hjälper dig att fokusera på det här underavsnittet i artikeln.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Basen för migreringen är en volym kloning och en virtuell moln installation som kallas en StorSimple 8020.
Den här fasen fokuserar på distribution av dessa resurser i Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Distribuera en virtuell StorSimple 8020-apparat

Att distribuera en moln installation är en process som kräver säkerhet, nätverk och några andra överväganden.

> [!IMPORTANT]
> Följande guide innehåller några onödiga avsnitt. Läs och följ artikeln från början fram till "steg 3". Gå sedan tillbaka till den här artikeln. Du behöver inte slutföra "steg 3" eller något mer än det i hand boken för tillfället.

[Distribution av en virtuell StorSimple 8020-apparat](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Bestäm vilken volym klon som ska användas

När du är redo att påbörja migreringen är det första steget att ta en ny volym kloning – precis som du skulle göra för säkerhets kopiering – som fångar upp det aktuella läget för din StorSimple-moln lagring. Ta en klon för var och en av de StorSimple-volymer du har.
Om du behöver flytta säkerhets kopior är den första volym klonen som du använder inte en nyskapad klon, men den äldsta volym klonen (äldsta säkerhets kopian) måste migreras.
Mer information finns i avsnittet ["överväganden kring befintliga StorSimple säkerhets kopieringar"](#considerations-around-existing-storsimple-backups) .

> [!IMPORTANT]
> Följande guide innehåller några onödiga avsnitt. Läs och följ bara de steg som beskrivs i det länkade-till-avsnittet. Gå sedan tillbaka till den här artikeln. Du behöver inte följa avsnittet "nästa steg".

[Skapa en klon av en volym](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Använd volym kloningen

Den sista fasen i fas 1 är att göra den kloning av volymen som du har valt, tillgänglig på den virtuella 8020-enheten i Azure.

> [!IMPORTANT]
> Följande guide innehåller de steg som krävs, men även i slutet av en instruktion för att formatera volymen. **Formatera inte volymen** Läs och följ det länkade till "avsnitt 7" från början upp till-instruktionen: "10. För att formatera en enkel volym,... "  Stoppa innan du följer det här steget och återgår till den här artikeln.

[Montera en volym kloning på den virtuella 8020-enheten i Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Översikt över fas 1

Nu när du har slutfört fas 1 är du klar med följande:

* Distribuerade en virtuell StorSimple 8020-installation i Azure.
* Fastställde vilken volym kloning du påbörjar migreringen med.
* Montera dina volym kloner (en för varje Live-volym) till den virtuella StorSimple-enheten i Azure, med data som är tillgängliga för användning.

## <a name="phase-2-cloud-vm"></a>Fas 2: virtuell dator i molnet

:::row:::
    :::column:::
        ![Bild som visar att det nu är dags att etablera en virtuell dator och exponera volym kloningen (eller flera) till den virtuella datorn via iSCSI.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        När den första klonen är tillgänglig på den virtuella StorSimple 8020-enheten i Azure, är det nu dags att etablera en virtuell dator och exponera volym kloningen (eller flera) på den virtuella datorn via iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Distribuera en virtuell Azure-dator

Den virtuella Windows Server-datorn i Azure är precis som StorSimple 8020, en tillfällig infrastruktur som bara behövs under migreringen.
Konfigurationen av den virtuella dator som du distribuerar beror främst på antalet objekt (filer och mappar) som du kommer att synkronisera. Vi rekommenderar en högre prestanda konfiguration om du har problem.

En enda Windows Server kan synkronisera upp till 30 Azure-filresurser.
De specifikationer du bestämmer dig för måste omfatta varje resurs/sökväg eller roten för StorSimple-volymen och antalet objekt (filer och mappar).

Den totala storleken på data är mindre av en Flask hals – det är antalet objekt du behöver för att skräddarsy datorns specifikationer till.

* [Lär dig hur du ändrar storlek på en Windows-Server baserat på antalet objekt (filer och mappar) som du behöver synkronisera.](storage-sync-files-planning.md#recommended-system-resources)

    **Observera:** Den tidigare länkade artikeln visar en tabell med ett intervall för server minne (RAM). Orientera dig mot det stora antalet för den virtuella Azure-datorn. Du kan orientera dig mot det mindre antalet för din lokala dator.

* [Lär dig hur du distribuerar en virtuell Windows Server-dator.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Se till att den virtuella datorn har distribuerats i samma Azure-region som den virtuella StorSimple 8020-enheten. Om du som en del av den här migreringen, måste du också ändra regionen för dina moln data från den region som den lagras i idag, kan du göra det i ett senare steg när du etablerar Azure-filresurser.

> [!IMPORTANT]
> Ofta används en lokal Windows Server för att aktivera din lokala StorSimple-apparat. I en sådan konfiguration är det möjligt att aktivera funktionen "[datadeduplicering](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" på Windows Server. **Om du använde datadeduplicering med dina StorSimple-data, se till att du aktiverar datadeduplicering på den här virtuella Azure-datorn också.** Blanda inte ihop den här dedupliceringen på filnivå med StorSimples inbyggd block nivå deduplicering, för vilken ingen åtgärd krävs.

> [!IMPORTANT]
> Du kan optimera prestanda genom att distribuera en **snabb OS-disk** för din virtuella dator i molnet. Du kommer att lagra Sync-databasen på OS-disken för alla dina data volymer. Se dessutom till att du skapar en **stor OS-disk**. Beroende på antalet objekt (filer och mappar) på dina StorSimple-volymer kan operativ system disken behöva **flera hundra GIB** för att rymma Sync-databasen.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Exponera StorSimple 8020-volymer på den virtuella Azure-datorn

I den här fasen ansluter du en eller flera StorSimple-volymer från den virtuella 8020-enheten via iSCSI till den virtuella Windows Server-dator som du har etablerad.

> [!IMPORTANT]
> I följande artiklar fyller du bara i **Hämta privat IP för moln** installationen och **ansluter via iSCSI** -avsnitten och återgår till den här artikeln.

1. [Få en privat IP-adress för molninstallationen](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Anslut via iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Fas 2-Sammanfattning

Nu när du har slutfört fas 2 har du: 

* Etablerade en virtuell Windows Server-dator i samma region som 8020 Virtual StorSimple-enheten
* Exponerade alla tillämpliga volymer från 8020 till den virtuella Windows Server-datorn via iSCSI.
* Du bör nu se fil-och mappinnehåll när du använder Utforskaren på den virtuella servern på de monterade volymerna.

Fortsätt bara till fas 3 när du har slutfört de här stegen för alla volymer som behöver migreras.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fas 3: Konfigurera Azure-filresurser och Förbered dig för Azure File Sync

:::row:::
    :::column:::
        ![Illustration som visar behovet av att fastställa och etablera ett antal Azure-filresurser och skapa en Windows Server lokalt som en utbyte av StorSimple-enheter.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        I den här fasen ska du bestämma och tillhandahålla ett antal Azure-filresurser, skapa en Windows Server lokalt som en StorSimple-installation och konfigurera servern för Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mappa befintliga namn områden till Azure-filresurser

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Distribuera Azure-filresurser

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Om du behöver ändra Azure-regionen från den aktuella regionen som dina StorSimple-data finns i, etablerar du Azure-filresurserna i den nya region som du vill använda. Du fastställer regionen genom att välja den när du etablerar de lagrings konton som innehåller dina Azure-filresurser. Se till att även den Azure File Sync resurs som du kommer att etablera nedan finns i samma, ny region.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuera Azure File Sync moln resurs

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Om du behöver ändra Azure-regionen från den aktuella regionen som dina StorSimple-data finns i, har du etablerade lagrings kontona för dina Azure-filresurser i den nya regionen. Se till att du har valt samma region när du distribuerar den här tjänsten för synkronisering av lagring.

### <a name="deploy-an-on-premises-windows-server"></a>Distribuera en lokal Windows Server

* Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller fysisk server. Ett kluster för växling vid fel i Windows Server stöds också. Återanvänd inte servern du kanske har fram StorSimple 8100 eller 8600.
* Etablera eller Lägg till direktansluten lagring (DAS jämfört med NAS, vilket inte stöds).

Vi rekommenderar att du ger din nya Windows Server en lika stor eller större mängd lagrings utrymme än din StorSimple 8100-eller 8600-installation har lokalt tillgänglig för cachelagring. Du kommer att använda Windows Server på samma sätt som du använde StorSimple-installationen, om den har samma mängd lagrings utrymme som installationen, bör caching-upplevelsen vara liknande, om inte samma.
Du kan lägga till eller ta bort lagrings utrymme från Windows Server på. På så sätt kan du skala din lokala volym storlek och mängden lokal lagring som är tillgänglig för cachelagring.

### <a name="prepare-the-windows-server-for-file-sync"></a>Förbereda Windows Server för filsynkronisering

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurera Azure File Sync på Windows Server

Din registrerade lokala Windows Server måste vara klar och ansluten till Internet för den här processen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Se till att aktivera moln skiktning!** Moln lagring är en AFS-funktion som gör det möjligt för den lokala servern att ha mindre lagrings kapacitet än vad som lagras i molnet, men det fullständiga namn området är tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb, lokal åtkomst prestanda. En annan orsak till att aktivera moln nivåer i det här steget är att vi inte vill synkronisera fil innehåll i det här skedet. endast namn området ska flyttas vid denna tidpunkt.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fas 4: Konfigurera den virtuella Azure-datorn för synkronisering

:::row:::
    :::column:::
        ![Bild som visar hur du får den virtuella datorn som är ansluten via Azure File Sync och startar en första rundade flyttning av filer från dina StorSimple volym kloner.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Den här fasen gäller din virtuella Azure-dator med iSCSI-monterade, första volym klonen (s). Under den här fasen får du den virtuella datorn som är ansluten via Azure File Sync och startar en första rundade flyttning av filer från dina StorSimple volym kloner.
        
    :::column-end:::
:::row-end:::

Du har redan konfigurerat din lokala server som ska ersätta din StorSimple 8100-eller 8600-installation, för Azure File Sync. 

Att konfigurera den virtuella Azure-datorn är en nästan identisk process med ett ytterligare steg. Följande steg vägleder dig genom processen.

> [!IMPORTANT]
> Det är viktigt att den virtuella Azure-datorn **inte har kon figurer ATS med moln nivåer aktiverat!** Du kommer att byta ut volymen på den här servern med nyare volym kloner under migreringen. Moln nivåer har ingen förmån och kostnader för CPU-användning som du bör undvika.

1. [Distribuera AFS-agenten. (se föregående avsnitt)](#prepare-the-windows-server-for-file-sync)
2. [Förbereder den virtuella datorn för Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Konfigurera synkronisering](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Förbered den virtuella datorn för Azure File Sync

Azure File Sync används för att flytta filerna från de monterade iSCSI-StorSimple volymer till Azure-filresurser.
Under den här migreringsprocessen monterar du flera volym kloner på den virtuella datorn, under samma enhets beteckning. Azure File Sync måste konfigureras för att se nästa volym kloning som du har monterat som en nyare version av filerna och mapparna och uppdatera Azure-filresurser som är anslutna via Azure File Sync. 

> [!IMPORTANT]
> För att detta ska fungera måste en register nyckel anges på servern innan Azure File Sync konfigureras.

1. Skapa en ny katalog på den virtuella datorns systemen het. Azure File Sync information måste sparas där, i stället för på den monterade volymens kloner. Exempelvis: `"C:\syncmetadata"`
2. Öppna regedit och leta upp följande registrerings data fil: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Skapa en ny nyckel av typen sträng med namnet: ***MetadataRootPath***
4. Ange den fullständiga sökvägen till den katalog som du skapade på system volymen, till exempel: `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Konfigurera Azure File Sync på den virtuella Azure-datorn

Det här steget liknar föregående avsnitt, som beskriver hur du konfigurerar AFS på den lokala servern.

Skillnaden är att du inte måste aktivera moln skiktning på den här servern och att du måste kontrol lera att rätt mappar är anslutna till rätt Azure-filresurser. Annars matchar inte namngivningen av Azure-filresurser och data innehållet och det finns inget sätt att byta namn på moln resurser eller lokala mappar utan att konfigurera om synkroniseringen.

Se [föregående avsnitt om hur du konfigurerar Azure File Sync på en Windows Server](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Steg 4 – Sammanfattning

Nu kommer du att ha konfigurerat Azure File Sync på den virtuella Azure-datorn som du har monterat dina StorSimple volym kloner via iSCSI.
Data flödar nu från den virtuella Azure-datorn till de olika Azure-filresurserna och därifrån visas en fullständigt trött namnrymd på din lokala Windows Server.

> [!IMPORTANT]
> Se till att inga ändringar har gjorts eller att användar åtkomst har beviljats för Windows Server just nu.

Det kan ta lång tid att flytta data från den inledande volymen till Azure-filresurserna till Azure-filresurserna. Att uppskatta tiden det tar är knepigt och beror på många faktorer. Framför allt den hastighet som den virtuella Azure-datorn kan komma åt filer på StorSimple-volymer och hur snabbt Azure File Sync kan bearbeta de filer och mappar som behöver synkroniseras. 

Vi kan från upplevelsen förutsätta att bandbredden – den faktiska data storleken – spelar en underordnad roll. Tiden det här eller eventuella efterföljande migreringar tar är främst beroende av antalet objekt som kan bearbetas per sekund. Så till exempel 1 TiB med en 100 000-filer och mappar kommer sannolikt att avslutas långsammare än 1 TiB med endast 50 000 filer och mappar.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fas 5: iterera genom flera volym kloner

:::row:::
    :::column:::
        ![Bild som visar hur du minimerar nedtid genom att använda flera volym kloner och meddela när synkroniseringen är klar.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Den första synkroniseringen kan ta lång tid, vilket beskrivs i föregående fas. Dina användare och program har fortfarande åtkomst till den lokala StorSimple 8100-eller 8600-enheten. Det innebär att ändringar ackumuleras, och med varje dag större delta mellan Live-data och den ursprungliga volym kloningen, för närvarande migrering, formulär. I det här avsnittet får du lära dig hur du minimerar nedtid genom att använda flera volym kloner och meddela när synkroniseringen är klar.
    :::column-end:::
:::row-end:::

Migreringen är tyvärr inte i gång. Det innebär att ovannämnda delta i real tids data är en oundviklig konsekvens. Den goda nyheten är att du kan upprepa processen med att montera nya volym kloner. Varje volym klons delta blir progressivt mindre. Så småningom kommer synkroniseringen att slutföras inom en tids period som du bedömer som acceptabel för att ta bort användare och appar från den lokala Windows Server.

Upprepa följande steg tills synkroniseringen har slutförts tillräckligt snabbt för att du ska kunna ta användare och appar offline:

1. [Bestämma att synkroniseringen ska slutföras för en bestämd volym kloning.](#determine-when-sync-is-done)
2. [Ta en ny volym kloning (er) och montera den på den virtuella 8020-enheten.](#the-next-volume-clones)
3. [Avgör när synkroniseringen är färdig.](#determine-when-sync-is-done)
4. [Beskärnings strategi](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Nästa volym klon (er)

Vi har diskuterat att ta en volym klon (er) tidigare i den här artikeln.
Den här fasen har två åtgärder:

1. [Ta en volym kloning](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Montera den volymen kloning (se ovan)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Avgöra när synkroniseringen är färdig

När synkroniseringen är färdig kan du stoppa din tid mätning och bestämma om du behöver upprepa processen för att utföra en volym kloning och montera den eller om tidssynkroniseringen tog med den senaste volym klonen var tillräckligt liten.

För att avgöra om synkroniseringen är klar:

1. Öppna Loggboken och navigera till **program och tjänster**
2. Navigera och öppna **Microsoft\FileSync\Agent\Telemetry**
3. Leta efter den senaste **händelse 9102**som motsvarar en slutförd Sync-session
4. Välj **information** och bekräfta att **SyncDirection** -värdet har **laddats upp**
5. Kontrol lera **HResult** och bekräfta att det visar **0**. Det innebär att Sync-sessionen lyckades. Om HResult är ett värde som inte är noll uppstod ett fel vid synkroniseringen. Om **PerItemErrorCount** är större än 0, synkroniserades inte vissa filer eller mappar korrekt. Det är möjligt att ha ett HResult på 0, men ett PerItemErrorCount som är större än 0. I det här läget behöver du inte bekymra dig om PerItemErrorCount. Vi kommer att fånga dessa filer senare. Om det här antalet fel är viktigt, kan du kontakta kund support och be att bli ansluten till Azure File Sync produkt grupp för direkt vägledning om bästa, nästa steg.
6. Kontrol lera om det finns flera 9102-händelser med HResult 0 i en rad. Detta anger att synkroniseringen har slutförts för den här volym kloningen.

### <a name="cut-over-strategy"></a>Beskärnings strategi

1. Ta reda på om synkroniseringen från en volym klon är tillräckligt snabb nu. (Delta tillräckligt litet.)
2. Koppla från StorSimple-enheten.
3. En slutgiltig RoboCopy.

Mät tiden och ta reda på om synkronisering från en senaste volym klon kan slutföras inom ett tidsfönster som är tillräckligt litet, så att du kan ge dig som nedtid i systemet.

Nu är det dags att inaktivera användar åtkomst till StorSimple-enheten. Inga fler ändringar. Stillestånds tiden har påbörjats.
Du måste lämna enheten online och ansluten, men du måste nu förhindra ändringar på den.

I fas 6 kommer du att komma igång med alla delta i Live-data sedan den senaste volym kloningen.

## <a name="phase-6-a-final-robocopy"></a>Fas 6: en slutgiltig RoboCopy

Nu finns det två skillnader mellan din lokala Windows Server och StorSimple 8100-eller 8600-enheten:

1. Det kan finnas filer som inte har synkroniserats (se **PerItemErrors** från händelse loggen ovan)
2. StorSimple-installationen har ett ifyllt cacheminne jämfört med Windows Server bara ett namn område utan fil innehåll som lagras lokalt för tillfället.

![Bild som visar hur cacheminnet för Windows Server har hämtats till status för installationen och ser till att ingen fil ligger kvar bakom med en slutgiltig RoboCopy.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Vi kan hämta cacheminnet för Windows Server upp till status för installationen och se till att ingen fil är kvar bakom med en slutgiltig RoboCopy.

> [!CAUTION]
> Det är absolut nödvändigt att RoboCopy-kommandot som du följer är exakt enligt beskrivningen nedan. Vi vill bara kopiera filer som är lokala och filer som inte har flyttats genom volymen klon + synkronisering innan. Vi kan lösa problemen varför de inte synkroniseras senare, när migreringen är klar. (Se [Azure File Sync fel sökning](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Det är troligen inte längre utskrivbara tecken i fil namn som du inte saknar när de tas bort.)

RoboCopy-kommando:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Lägg

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Tillåter att RoboCopy körs med flera trådar. Standardvärdet är 8, Max är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Matar ut status till logg filen som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till konsol fönstret. Används tillsammans med utdata i en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhets kopierings program använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Gör det möjligt för RoboCopy att endast överväga delta mellan källan (StorSimple-enheten) och mål (Windows Server-katalog).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      åter givningen av fil kopian (standard är/COPY: DAT), kopierings flaggor: D = data, A = attribut, T = tidsstämplar, S = Security = NTFS ACL, O = ägar information, U = gransknings information
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiera ALL fil information (motsvarande/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      åter givning för kopian av kataloger (standard är/DCOPY: DA), kopierings flaggor: D = data, A = attribut, T = tidsstämplar
   :::column-end:::
:::row-end:::

Du bör köra detta RoboCopy-kommando för varje katalog på Windows-servern som mål, som du har konfigurerat med filsynkronisering till en Azure-fil.

Du kan köra flera av dessa kommandon parallellt.
När det här RoboCopy-steget har slutförts kan du tillåta att dina användare och appar får åtkomst till Windows Server, till exempel de gjorde StorSimple-apparaten.

Läs igenom Robocopy-loggfilerna för att se om filerna är kvar bakom. Om det skulle finnas problem kan du i de flesta fall lösa dem när migreringen är klar och användarna och apparna har publicerats på nytt till Windows Server. Om du behöver åtgärda eventuella problem gör du det innan fas 7.

Det krävs förmodligen att skapa SMB-resurser på den Windows-Server som du hade på StorSimple-data innan. Du kan läsa in det här steget och göra det tidigare för att inte förlora tiden här, men du måste se till att innan den här punkten är ändringar i filer som sker på Windows-servern.

Om du har en DFS-N-distribution kan du peka DFN-Namespaces till nya mappar för servermappar. Om du inte har en DFS-N-distribution, och du framförde 8100 8600-installationen lokalt med en Windows-Server, kan du ta bort servern från domänen och domän ansluta till din nya Windows Server med AFS till domänen, ge den samma server namn som den gamla servern och samma resurs namn, och sedan är övertagandet till den nya servern transparent för användarna , grup princip eller skript.

## <a name="phase-7-deprovision"></a>Fas 7: avetablering

Under den senaste fasen har du itererat över flera volym kloner och kan slutligen ta bort över användar åtkomsten till den nya Windows-servern efter att du StorSimple-installationen offline.

Nu kan du börja avetablera onödiga resurser.
Innan du börjar är det en bra idé att studera din nya Azure File Sync distribution i produktion, för en bit. Det ger dig alternativ för att åtgärda eventuella problem som kan uppstå.

När du är nöjd och har observerat din AFS-distribution under minst några få dagar, kan du börja med att avetablera resurser i följande ordning:

1. Stäng av den virtuella Azure-datorn som vi har använt för att flytta data från volym klonerna till Azure-filresurserna via filsynkronisering.
2. Gå till din resurs för Storage Sync-tjänsten i Azure och avregistrera den virtuella Azure-datorn. Det tar bort alla Sync-grupper.

    > [!WARNING]
    > **Se till att du väljer rätt dator.** Du har aktiverat den virtuella moln datorn, vilket innebär att den ska visas som den enda offline-servern i listan över registrerade servrar. Du måste inte välja den lokala Windows-servern i det här steget. då avregistreras den.

3. Ta bort virtuell Azure-dator och dess resurser.
4. Inaktivera den virtuella StorSimple-enheten 8020.
5. Avetablera alla StorSimple-resurser i Azure.
6. Dra ut den fysiska StorSimple från ditt data Center.

Migreringen är klar.

## <a name="next-steps"></a>Nästa steg

Bekanta dig med Azure File Sync. Särskilt med flexibiliteten i principer för moln nivåer.

Om du ser i Azure Portal eller från de tidigare händelserna synkroniseras vissa filer permanent inte, Läs fel söknings guiden för steg för att lösa problemen.

* [Azure File Sync översikt: aka.ms/AFS](https://aka.ms/AFS)
* [Moln nivå](storage-sync-cloud-tiering.md) 
* [Azure File Sync fel söknings guide](storage-sync-files-troubleshoot.md)
